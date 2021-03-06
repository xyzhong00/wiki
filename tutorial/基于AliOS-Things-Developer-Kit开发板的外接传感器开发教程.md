- 开发板配置
- 代码下载
- 代码移植
- 功能调试
- 驱动代码提交

## 1 开发板配置
我们验证选用的开发板是基于STM32L496VGTx芯片研发的一款物联网开发板。其内核为ARM 32位Cortex-M4 CPU，最高80MHZ的主频率，1MB的闪存，320KB的SRAM，最多支持136个高速IO口，还支持SPI,CAN,I2C,I2S,USB,UART等常用的外设接口。

***调试时，请使用右上角的USB1 ST_Link接口***

![](https://i.imgur.com/ColtF57.png)

单板的背面有arduino接口，当前验证使用的外接sensor主要基于I2C总线进行连接。
SCL以及SDA的高电平为3V

![](https://i.imgur.com/nfThGYH.png)

developer kit开发板环境配置请参考以下链接，建议采用一键式安装：

https://github.com/alibaba/AliOS-Things/wiki/AliOS-Things-Studio

## 2 代码下载
代码下载前，请确认已在github注册账号，链接及注册流程如下：
- https://github.com/

**为了便于后续的代码的审核提交，注册github账号时请使用本公司的邮箱**
![](https://i.imgur.com/q0JhzHJ.png)

打开以下代码链接后，可以通过以下方式下载代码。首先选择代码分支；
- https://github.com/andy2012zwj/AliOS-Things/tree/aos_udata

![](https://i.imgur.com/7LjS6d4.png)

然后选择zip格式下载；
![](https://i.imgur.com/AoAtQN8.png)

## 3 代码移植
### 3.1 驱动代码集成
请参考以下链接完成uData架构下传感器驱动的移植：https://github.com/alibaba/AliOS-Things/wiki/AliOS-Things-uData-Sensor-Driver-Porting-Guide.zh
### 3.2 总线配置
在developer kit板上，我们是通过外接I2C3连接传感器，需要注意的地方是总线配置,port口为3，从设备的地址为8bit：
```
i2c_dev_t  ####_ctx = {
    .port = 3, /*developer kit上外接I2C的port为3*/
    .config.dev_addr = 0x5D<<1, /* 从设备I2C地址，8bit */
};

```
### 3.3 服务订阅
如果需要在串口查看调试信息，则需要在udata_sample函数中，修改函数udata_sample中的订阅的传感器service类型（路径：example\uDataapp\uData-example.c）；压力传感器如下所示：
```
int udata_sample(void)
{
    int ret = 0;

    aos_register_event_filter(EV_UDATA, uData_report_demo, NULL);

    ret = uData_subscribe(UDATA_SERVICE_BARO);/*UDATA_SERVICE_BARO为压力传感器对应的service 类型*/
    if (ret != 0) {
        LOG("%s %s %s %d\n", uDATA_STR, __func__, ERROR_LINE, __LINE__);
        return -1;
    }

    return 0;
}

```

```
/*service 类型*/
typedef enum 
{
 UDATA_SERVICE_ACC = 0,     /* Accelerometer */ 
 UDATA_SERVICE_MAG,         /* Magnetometer */
 UDATA_SERVICE_GYRO,        /* Gyroscope */
 UDATA_SERVICE_ALS,         /* Ambient light sensor */
 UDATA_SERVICE_PS,          /* Proximity */
 UDATA_SERVICE_BARO,        /* Barometer */
 UDATA_SERVICE_TEMP,        /* Temperature  */
 UDATA_SERVICE_UV,          /* Ultraviolet */
 UDATA_SERVICE_HUMI,        /* Humidity */
 UDATA_SERVICE_HALL,        /* HALL sensor */
 UDATA_SERVICE_HR,          /* Heart Rate sensor */
 UDATA_SERVICE_PEDOMETER,   
 UDATA_SERVICE_PDR,     
 UDATA_SERVICE_VDR,
 UDATA_SERVICE_GPS,
 
 UDATA_MAX_CNT, 
}udata_type_e;
```

## 4 功能调试
下面以developer kit板为例说明linkkit用例的调试过程。
#### 4.1 编译
example\uDataapp目录下已集成了相关的用例代码，2、3两个章节完成配置修改后，执行以下命令则可以编译用例
aos make udataapp@developerkit
![](https://i.imgur.com/6CwKRXU.png)
编译完成后，生成的可执行文件为out\udataapp@developerkit\binary\udataapp@developerkit.bin
![](https://i.imgur.com/1k2DCSk.png)
#### 4.2 文件烧录
本示例采用ST-LINK工具烧写bin文件，用户也可参考developer kit板环境配置说明中的其他方法；
![](https://i.imgur.com/gB0snoy.png)
#### 4.3 用例执行
通过串口连接单板（串口速率为115200）
烧录完成后，复位单板，开始运行；如果配置流程没有错误，则可以在串口看到sensor通过udata上报的数据。

![](https://i.imgur.com/sXXs2SJ.png)
其中物理传感器对应的服务类型，请参考结构体udata_type_e；

物理传感器的上报的数据单位，请参考以下链接中的《传感器数据单位》章节

https://github.com/alibaba/AliOS-Things/wiki/AliOS-Things-uData-Sensor-Driver-Porting-Guide.zh

## 5 驱动代码提交
如果功能测试完成无误，则可以参考以下链接中外部代码提交方式，向AliOS Things提交代码和入申请：

https://github.com/alibaba/AliOS-Things/wiki/contributing.zh

待AliOS对其做相关的认证后，则可以集成到AliOS Things中。
