# MATT 模型灯光控制系统

微信小程序通过 BLE 直连 ESP32-C3FH4 控制板，控制拼装模型（高达 / 假面骑士）内部的 LED 与 WS2812 灯带。后端不参与实时控制，只做配置中心。

## 仓库结构

| 目录 | 内容 |
|---|---|
| `docs/specs/` | 规格说明书 |
| `firmware/` | ESP-IDF 固件（ESP32-C3FH4） |
| `firmware/main/protocol/` | 协议编解码（平台无关，可主机测试） |
| `firmware/main/effect/` | 场景与灯效合成（平台无关，可主机测试） |
| `firmware/main/driver/` | RMT / PWM / GPIO 驱动封装 |
| `firmware/main/ble/` | GATT 服务与特征 |
| `firmware/main/storage/` | NVS 持久化 |
| `firmware/test_host/` | 主机端 Unity 单元测试 |
| `miniprogram/` | 微信小程序（含灯效编排器） |
| `backend/` | NestJS + TypeORM + MySQL |
| `admin-web/` | Vue 3 + Element Plus 后台 |

## 关键约束

- **纯 BLE 直连**：没有 WiFi、没有 MQTT、没有云端实时链路。
- **8 通道上限**，任意通道可配成普通 LED 或 WS2812。
- **WS2812 走 RMT 软件串行轮转刷新**（C3 只有 2 个 RMT TX），每条灯带刷新率随之下降。
- **场景最多 8 个**，每场景 8 通道 × 8 段 × 8 关键帧。
- 编排器只在**小程序**里；后台只读 + 模板库 + 管理。

## 文档

- [设计规格](docs/specs/matt-lighting-system.md)

## 开发顺序

1. 固件最小可跑：GATT 骨架 + 8 通道驱动 + 直接设色 → 通用 BLE 工具点亮
2. 固件协议层完整：即时指令 + 状态读回 + 配置同步分片
3. 小程序控制页
4. 固件场景引擎 + NVS 持久化
5. 小程序编排器
6. 后端
7. Vue 后台
8. OTA over BLE
