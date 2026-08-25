# AD5941 安培法 BLE 网页

## 功能

- 通过Web Bluetooth连接BLE透明串口模块。
- 实时解析9字节A5安培电流帧并绘图。
- 读取和设置`SensorBias`及采样周期。
- START/STOP命令包含CRC16、序列号、800 ms超时和最多2次重试。
- 每条命令先发送10字节`0xFF`并等待30 ms，可唤醒处于STOP模式的STM32。
- 自动扩展16位采样点编号，支持长时间连续绘图。

## 参数协议

`SET_AMP_CONFIG (0x10)`的4字节Payload：

| 偏移 | 类型 | 内容 |
|---:|---|---|
| 0 | `int16 LE` | SensorBias，单位mV，范围-600～600 |
| 2 | `uint16 LE` | SamplePeriod，单位ms，范围10～30000 |

`GET_AMP_CONFIG (0x11)`无请求Payload，成功响应返回相同的4字节参数。

## 数据帧

```text
A5 + point(u16 LE) + current_nA(i32 LE) + checksum + 5A
```

## 使用

1. 将本目录部署到HTTPS静态网站。
2. 使用支持Web Bluetooth的安卓Chrome打开。
3. 根据BLE模块修改FFE0/FFE1 UUID。
4. 连接后读取参数，停止状态下应用新参数，再启动检测。

固件默认USART1波特率为115200，HC-08的UART侧必须配置一致。低功耗远程唤醒时应把HC-08配置为仍可发现、连接的MODE1；STM32停止检测并空闲3秒后关闭OLED显示并进入STOP，网页命令的唤醒前导码通过PA10/EXTI10将其唤醒。
