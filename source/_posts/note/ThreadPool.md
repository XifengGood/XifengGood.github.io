---
title: ThreadPool
excerpt: note
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-08-01 22:19:24
---
### 
<!-- more -->
``` C++
#pragma once
#include <vector>
#include <string>
#include <thread>
#include <queue>
#include <condition_variable>
#include <functional>
#include <future>

class ThreadPool
{
public:
    void DoTask()
    {
        while (true)
        {
            std::function<void()> task;
            {
                std::unique_lock<std::mutex> lock{queue_mutex};
                condition.wait(lock,
                               [this]
                               { return stop || !tasks.empty(); });
                if (stop && tasks.empty())
                {
                    return;
                }
                task = std::move(tasks.front());
                tasks.pop();
            }
            task();
        }
    }

    ThreadPool(size_t threads) : stop(false)
    {
        for (size_t i = 0; i < threads; ++i)
        {
            workers.emplace_back(std::bind(&ThreadPool::DoTask, this));
        }
    }

    ~ThreadPool()
    {
        {
            std::unique_lock<std::mutex> ul{queue_mutex};
            stop = true;
        }
        condition.notify_all();
        for (auto &worker : workers)
        {
            worker.join();
        }
    }

    template <class F, class... Args>
    auto enqueue(F &&f, Args &&...args) -> std::future<typename std::result_of<F(Args...)>::type>
    {
        using return_type = typename std::result_of<F(Args...)>::type;

        auto task = std::make_shared<std::packaged_task<return_type()>>(
            std::bind(std::forward<F>(f), std::forward<Args>(args)...));

        std::future<return_type> res = task->get_future();
        {
            std::unique_lock<std::mutex> lock{queue_mutex};
            if (stop)
            {
                throw std::runtime_error("enqueue on stopped ThreadPool");
            }
            tasks.emplace(
                [task]()
                {
                    (*task)();
                });
        }
        condition.notify_one();
        return res;
    }

private:
    std::vector<std::thread> workers;
    std::queue<std::function<void()>> tasks;
    std::mutex queue_mutex;
    std::condition_variable condition;
    bool stop;
};
```
[^1]: