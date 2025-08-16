---
title: backend
excerpt: note
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-08-13 08:27:23
---
# 目前的后端框架
## 1. Java 生态
- Spring Boot 3.2

  - 企业级首选，支持虚拟线程（Project Loom）、GraalVM 原生编译，适用于金融、医疗等高合规性场景1。

  - 集成 AI 模块（Spring AI），可自动生成 CRUD 代码，提升开发效率1。

- Quarkus 3.0

  - 云原生优化，冷启动时间 <10ms，适合微服务与边缘计算1。

  - 响应式架构（Mutiny 库）提升数据库查询吞吐量1。

## 2. Python 生态
- Django 4.2

  - 全栈框架，内置 ORM、Admin 面板，适合 CMS、ERP 等 CRUD 密集型应用57。

  - 新增异步视图支持，优化复杂查询性能3。

- FastAPI 0.100

  - 高性能异步 API 框架，基于 Pydantic 自动生成 OpenAPI 文档，适合 AI 模型部署与金融科技14。

  - 单实例 QPS 达 3000+，内存占用仅为 Django 的 1/51。

- Flask

  - 轻量级微框架，灵活扩展，适合快速原型开发7。

  - 2025 年强化异步支持，优化高并发微服务架构3。

## 3. Go 生态
- Gin 1.9

  - 高性能微服务框架，路由解析速度 50 万次/秒，内存占用仅 2MB1。

  - 深度集成 Kubernetes，支持 Pod 级自动扩缩容1。

- Fiber 2.0

  - 基于 Gorilla Mux 重构，内存安全，支持 WebAssembly 跨平台部署1。

## 4. Node.js 生态
- Express.js

  - 最基础的 Web 框架，插件生态丰富，适合中小型 API 开发2。

- NestJS

  - 模块化架构，支持 TypeScript、GraphQL 和微服务，适合企业级应用2。

- Fastify

  - 高性能 Web 框架，自动 JSON Schema 优化，适合高并发 API 网关2。

## 5. Rust 生态
- Actix Web 4.0

  - 极致性能，10 万 QPS 下内存占用仅 760KB，适合支付系统与边缘计算1。

- Axum 1.0

  - 函数式编程风格，组合式路由设计，提升 API 开发效率1。

## 6. .NET 生态
- ASP.NET Core

  - 跨平台支持，集成 Razor Pages、SignalR，适合企业级 Web 应用9。

## 7. PHP 生态
- Laravel

  - MVC 架构，适合 CMS 和电子商务平台9。

## 8. 其他新兴框架
- Deno 2.0（Node.js 替代）

  - 支持 WebGPU 加速，优化 3D 渲染性能1。

## 总结
不同框架适用于不同场景：

- 企业级系统：Spring Boot、Django、NestJS

- 高性能微服务：Gin、Actix Web、FastAPI

- 云原生 & 边缘计算：Quarkus、Rust 框架

- 快速开发 & 全栈：Django、Laravel

# C++ 后端框架

C++ 虽然是系统级语言，但在高性能后端开发中也有不少成熟的框架，尤其在游戏服务器、高频交易、实时通信、搜索引擎等对性能要求极高的领域。以下是主流的 C++ 后端框架：

## 1. 通用 Web 框架
- Drogon

  - 特点：基于 C++14/17 的异步高性能框架，支持 HTTP/HTTPS/WebSocket，集成 ORM（对标 Python 的 SQLAlchemy）。

  - 性能：单机轻松支撑 10万+ QPS，延迟 <1ms，适合金融 API 或实时服务。

  - 案例：阿里云部分高并发网关、量化交易系统。

- Crow

  - 特点：轻量级（头文件库），类似 Python Flask 的语法，适合快速开发 REST API。

  - 缺点：功能较基础，适合小型项目。

- Pistache

  - 特点：RESTful 风格，支持协程，代码简洁，适合中等规模服务。

## 2. 高性能 RPC/微服务框架
- gRPC (C++ 实现)

  - 特点：Google 开源的跨语言 RPC 框架，基于 HTTP/2 和 Protobuf，性能远超 REST。

  - 场景：微服务通信（如游戏服务器集群）。

- Seastar

  - 特点：Facebook 开源的异步框架，专为 100万+ 并发连接设计，无锁架构。

  - 用途：分布式数据库（如 ScyllaDB）、实时消息推送。

- brpc (百度开源)

  - 特点：百度内部广泛使用的 RPC 框架，支持多种协议（HTTP/RTMP/Redis），延迟低至 0.5ms。

  - 优势：内置负载均衡、熔断机制，适合工业级应用。

## 3. 网络库（需自行封装业务逻辑）
- Boost.Beast

  - 特点：基于 Boost.Asio 的 HTTP/WebSocket 库，灵活但需手动处理路由等逻辑。

  - 适用：需要极致定制的场景（如自定义协议）。

- libuv

  - 特点：Node.js 的底层库，事件驱动，适合实现自定义高性能服务器。

## 4. 游戏/实时领域专用
- ENet

  - 特点：轻量级 UDP 网络库，适合实时多人游戏（Minecraft 同款）。

- uWebSockets

  - 特点：WebSocket 优化库，支持 100万+ 并发连接，内存占用极低。

## 为什么 C++ 框架较少？
1. 开发效率低：手动管理内存、缺乏内置生态（如 ORM、身份验证），适合特定高性能场景。

2. 生态分散：Java/Python 有统一框架（Spring/Django），而 C++ 更倾向“组合库”模式。

## 选型建议
- 需要 HTTP API：Drogon（功能全）、Crow（简单）

- 微服务/RPC：gRPC、brpc

- 超高性能：Seastar、Boost.Beast

- 游戏/实时：ENet、uWebSockets