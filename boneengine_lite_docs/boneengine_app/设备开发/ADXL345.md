## #设备功能

adxl345是一款具有数字加速度计功能低功耗传感器，分辨率可以达到13bit，测量范围在16g,通过I2C或SPI协议进行数据交互，这里我们会定时读取传感器的加速度数据，并上报到LD云端平台；


## #硬件资源

DevelopKit上有I2C的外扩端口，同时也需要额外的adxl345模块，所需的硬件资源如图1所示：


![menu.saveimg.savepath20180521170714.jpg | left | 407x442](https://cdn.yuque.com/lark/0/2018/jpeg/66207/1526893748661-0f5e31d9-ea89-481d-a134-187ee43241fd.jpeg "")

图1

DevelopKit外扩的I2C各个引脚定义如上图1所示，两者之间的接线是：

* <span data-type="color" style="color:#F5222D">ADXL345模块的VCC连接DevelopKit的VCC引脚；</span>
* <span data-type="color" style="color:#F5222D">ADXL345模块的GND连接DevelopKit的GND引脚；</span>
* <span data-type="color" style="color:#F5222D">ADXL345模块的SCL连接DevelopKit的SCL引脚；</span>
* <span data-type="color" style="color:#F5222D">ADXL345模块的SDA连接DevelopKit的SDA引脚；</span>


## #软件设计
根据adxl345的数据手册，加速度计的x、y、z轴的值分别存放在寄存器0x32、0x34、0x36中，寄存器0x2D、0x31、0x2C和配置及使能相关，在初始化传感器的时候要对其进行配置；


```javascript
/*DevelopKit开发板配置文件*/
{
  "I2C": [
    {
      "id":"adxl345",
      "port":2,
      "address_width":7,
      "freq":200000,
      "mode":1,
      "dev_addr":166
    }
  ]
}


/*adxl345.js*/
......
/*读取加速度计数据*/
adxl345.getAcc = function(){

    var acc = [0,0,0];
    if(0 == this.isInited){
        this.init_config();
        this.isInited = 1;   
    }
    var gx = this.read_two(this.xLowReg);
    var gy = this.read_two(this.yLowReg);
    var gz = this.read_two(this.zLowReg);

    acc[0] = gx;
    acc[1] = gy;
    acc[2] = gz;

    return acc;
};

......


/*index.js*/
......
/*发送数据到云端*/
var postEvent = function(val) {
    var obj={};

    var id;
    var attrs = device.properties;
    obj['Gx'] = val[0];
    obj['Gy'] = val[1];
    obj['Gz'] = val[2];
    var event = device.events[0];
    device.update(event, obj);
};
......
```


## #运行验证

设备运行并接入LD一站式开发平台后，设备上传的数据将显示在界面上：



![1.png | left | 683x198](https://cdn.yuque.com/lark/0/2018/png/66207/1526894442058-e5f2b026-c800-4dbb-ae91-b9137490447f.png "")


## #代码仓库
```git
http://gitlab.alibaba-inc.com/Gravity/gravity_lite/tree/master/devices/adxl345
```

 