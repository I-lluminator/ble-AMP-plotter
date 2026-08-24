# AD5941 安培法 BLE 网页

## 功能

- 通过Web Bluetooth连接BLE透明串口模块。
- 实时解析9字节A5安培电流帧并绘图。
- 读取和设置`SensorBias`及采样周期。
- START/STOP命令包含CRC16、序列号、800 ms超时和最多2次重试。
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

注意：为兼容原工程及常见BLE透传模块，固件默认USART1波特率为9600，BLE模块UART侧必须配置一致。若要持续传输100 Hz数据，建议先把BLE模块改为115200，再把`Hardware/Bluetooth.h`中的`BT_UART_BAUDRATE`同步改为115200。
