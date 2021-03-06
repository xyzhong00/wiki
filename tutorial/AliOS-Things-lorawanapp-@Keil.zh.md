[EN](AliOS-Things-lorawanapp-@Keil) | 中文

## 在介绍之前大家做一些相关准备：
- Keil v5.25.2.0(当前使用版本）


- lora网络（注意相关硬件要接好天线）

  - End point
    - MXCHIP MK3047							*1
  - Gateway
    - M1301 Gateway module(GTI 光通国际）   	*1
      - Raspberry Pi                                                        *1
  - Lora Server
    - [LoRa App Server](https://docs.loraserver.io/lora-app-server/overview/): 这里是一个开源的server包，可以自行下载搭建

- 5VDC/2ADC电源（网关供电）   *1

- J-Link                                             *1

- micro-USB线                                *1

- 串口终端：这里使用X-Shell

- lora和LoRaWAN基础知识

- 当然还有最关键的[AliOS-Things](https://github.com/alibaba/AliOS-Things)了， 直接戳有电梯:smile:

  ​

## 开始

### 节点 End point
1. Keil工程位置：aos/projects/Keil/lorawanapp
2. 工程结构：

   ![lorawan_keil](https://img.alicdn.com/tfs/TB1X53omRfH8KJjy1XbXXbLdXXa-363-740.png)

   结构分析：
   - board/eml3047： 板级的资源配置
   - device/eml3047_lwran:：板级对应lora的接口
   - device/sx127x：lora radio部分
   - example：示例代码，aos初始化等
   - kernel/protocols：LoRaWAN协议栈
   - kernel/rhino：aos内核部分
   - kernel/vcall：aos api定义了系统应该提供的编程接口
   - platform/arch：硬件内核架构，这里是armv6 cortex m0
   - platform/mcu：对应mcu的底层驱动，这里主要来自于ST
3. 编译并烧录到目标板
4. 打开X-Shell，找到对应串口，连接串口，串口参数如下：

   ![串口](https://img.alicdn.com/tfs/TB1ldupi3vD8KJjy0FlXXagBFXa-206-154.png)

5. 连接后串口终端显示如下, 看到rssi，snr的数据表明已经节点已经连接到Lorawan网络开始接收到Gateway的数据。

   ![sl2](https://img.alicdn.com/tfs/TB1kuuKi3DD8KJjy0FdXXcjvXXa-848-759.png)

6. 以上烧录的是默认代码，LoRaWAN的参数如下：

   |    参数    | 值 |
   | ---------- | --- |
   | 入网方式    |  OTAA |
   | DevEui     |  31-35-37-31-50-37-7B-18（目前设定是根据板子创建相当于板子的MAC） |
   | AppEui     |01-01-01-01-01-01-01-01|
   | AppKey     |2B 7E 15 16 28 AE D2 A6 AB F7 15 88 09 CF 4F 3C|
   | Class      | A|

   以上前四个参数可以在aos/board/eml3047/inc/commissioning.h中修改：

```
/**
 * When set to 1 the application uses the Over-the-Air activation procedure
 * When set to 0 the application uses the Personalization activation procedure
 */
#define OVER_THE_AIR_ACTIVATION                     1

/**
 * Mote device IEEE EUI (big endian)
 *
 * \remark see STATIC_DEVICE_EUI comments
 */
#define LORAWAN_DEVICE_EUI                          { 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01 }


/**
 * Application IEEE EUI (big endian)
 */
#define LORAWAN_APPLICATION_EUI                     { 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01, 0x01 }

/**
 * AES encryption/decryption cipher application key
 */
#define LORAWAN_APPLICATION_KEY                     { 0x2B, 0x7E, 0x15, 0x16, 0x28, 0xAE, 0xD2, 0xA6, 0xAB, 0xF7, 0x15, 0x88, 0x09, 0xCF, 0x4F, 0x3C }


```

​	节点类型可以在aos/example/lorawanapp/lorawanapp.c中修改：

```
/**
 * Initialises the Lora Parameters
 */
static LoRaParam_t LoRaParamInit = {
    TX_ON_TIMER,
    APP_TX_DUTYCYCLE,
    CLASS_A,
    LORAWAN_ADR_ON,
    DR_0,
    LORAWAN_PUBLIC_NETWORK,
    JOINREQ_NBTRIALS
}
```

### 网关 Gateway

1. 网关通电保持工作（这里无需设置，只需通电工作即可，详细可以了解LoRaWAN的协议）

### 服务器 Server

1. 配置server端

   - 创建应用，相关参数可按图勾选

     ![s0](https://img.alicdn.com/tfs/TB1NSlOi3vD8KJjSsplXXaIEFXa-1480-271.png)

     ![s2](https://img.alicdn.com/tfs/TB1yehVi0zJ8KJjSspkXXbF7VXa-1221-807.png)

   - 在应用下创建节点，按照串口终端的打印信息填写

     ![s3](https://img.alicdn.com/tfs/TB1mfRZi0fJ8KJjy0FeXXXKEXXa-1494-312.png)

     ![s4](https://img.alicdn.com/tfs/TB1RTV8i8HH8KJjy0FbXXcqlpXa-1313-828.png)

   - 入网过程开始后，终端节点会被激活，会生成相应的Key

     ![s5](https://img.alicdn.com/tfs/TB1hK4Si_TI8KJjSsphXXcFppXa-1477-666.png)

   - 入网后会开始数据交互，服务器端在Raw frame logs下面会看到

     ![s6](https://img.alicdn.com/tfs/TB1BvRZi0fJ8KJjy0FeXXXKEXXa-1474-665.png)

2. Server端发送数据

   - 打开server的api页面，找到Internal下的Post接口，在body框内填入用户名和密码，默认为admin，admin，点击Try it out!按钮生成Token

     ![api1](https://img.alicdn.com/tfs/TB1Ki9hi8TH8KJjy0FiXXcRsXXa-1221-727.png)

   - 在下方Response Body中找到jwt字段，这个就是我们要用的Token

     ![api01](https://img.alicdn.com/tfs/TB128F7i22H8KJjy0FcXXaDlFXa-1198-206.png)

   - Copy Token到页面右上方的空格里，如下：

     ![api02](https://img.alicdn.com/tfs/TB1n8dNi4TI8KJjSspiXXbM4FXa-1252-112.png)

   - 找到DownlinkQueue接口，在body内的devEUI字段填入节点对应的devEUI，注意格式。在data字段里填入测试要用的数据，注意这里需要使用[base64](https://www.base64encode.org/)对原始数据encode再填入。在fPort内填入100，然后点击Try it out!按钮发送数据

     ![api2](https://img.alicdn.com/tfs/TB1_1GJi_vI8KJjSspjXXcgjXXa-1221-779.png)

   - 节点正常会收到数据并在串口打印出来，至此基于OTAA，Class A的节点入网，收发数据，主动传输的系统示例就结束了。

3. 如果大家想尝试ABP, Class C（这里只支持Class A和C）等不同的配置，可以自行修改上述提到的代码和server端的节点设置。
