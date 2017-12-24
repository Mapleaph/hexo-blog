title: Windows Power Management
date: 2015-11-22 11:52:36
categories: Encyclopedia
---

## 系统电源状态

| Status        | Description                                        |
|---------------|----------------------------------------------------|
| S0(Working)   | CPU 全功率运行                                     |
| S4(Hibernate) | 系统停止，RAM 被保存到磁盘                         |
| S5(Shutdown)  | 系统停止并关闭，需要完全引导以恢复操作             |
| S1(Sleeping)  | CPU 停止，RAM 被刷新                               |
| S2(Sleeping)  | CPU 不通电，RAM 被刷新                             |
| S3(Sleeping)  | CPU 不通电，RAM 处于低速刷新模式，电源功率输出降低 |

## 设备电源状态

| Status | Description                                |
|--------|--------------------------------------------|
| D0     | 设备全功率运行                             |
| D3     | 设备没有电，环境丢失                       |
| D1     | 设备处于低功率运行模式，设备环境可能被保留 |
| D2     | 设备处于低功率运行模式，设备环境可能无效   |

**操作系统不直接处理设备的电源状态，而是由驱动程序专门来处理。设备至少要支持 D0 和 D3 两种状态。**