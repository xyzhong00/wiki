__#WIFI__

<div class="bi-table">
  <table>
    <colgroup>
      <col width="90px" />
      <col width="90px" />
    </colgroup>
    <tbody>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">API</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">说明</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WIFI.scan(cb）</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能：扫描wifi网络，获取当前环境中的热点信息. </div>
          <div data-type="p"></div>
          <div data-type="p">参数：cb :扫描完成后的回调函数; </div>
          <div data-type="p">   cb(result)参数说明：result是一个json数组。</div>
          <div data-type="p">通过result[i].ssid, result[i].channel, result[i].rssi, result[i].auth, result[i].encry访问成员。</div>
          <div data-type="p"></div>
          <div data-type="p">返回值：0: 执行成功。 </div>
          <div data-type="p">             -1:参数错误。</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WIFI.connect(ssid,pwd,cb)</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能：通过指定的ssid和pwd连接wifi热点； </div>
          <div data-type="p"></div>
          <div data-type="p">参数: ssid为wifi热点的名称，pwd为wifi热点的密码，cb为连接成功/失败的callback回调函数。 </div>
          <div data-type="p"> </div>
          <div data-type="p">返回值：0开始连接。-1代表参数错误。 </div>
          <div data-type="p">cb（State）{} 的参数State是一个字符串，State为“CONNECTED“代表连接成功。“DISCONNECT“代表连接失败，如密码错误。</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WIFI.getip()</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能：获取wif的ip地址。 </div>
          <div data-type="p"> </div>
          <div data-type="p">参数​：无</div>
          <div data-type="p"></div>
          <div data-type="p">返回值：点分格式的String字符串。</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WIFI.getmac()</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能​：获取WIFI的mac地址。 </div>
          <div data-type="p"> ​</div>
          <div data-type="p">参数​：无 </div>
          <div data-type="p"> ​</div>
          <div data-type="p">返回值​：mac地址，String字符串。</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WIFI.getchannel()</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能：获取WIFI的信道。 </div>
          <div data-type="p"> </div>
          <div data-type="p">参数：无 </div>
          <div data-type="p"></div>
          <div data-type="p">返回值：channel： 整型。-1：获取失败</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WIFI.setchannel()</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能：设置WIFI的信道； </div>
          <div data-type="p"> </div>
          <div data-type="p">参数 :无 </div>
          <div data-type="p"></div>
          <div data-type="p">返回值：0：设置成功。-1：设置失败。</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WIFI.poweroff()</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能：关闭WIFI。</div>
          <div data-type="p"></div>
          <div data-type="p">参数​：无</div>
          <div data-type="p"></div>
          <div data-type="p">返回值：0：设置成功。-1：设置失败。</div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p">WIFI.poweron()</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能：打开WIFI。 参数​：无 返回值：0：设置成功。-1：设置失败。

          </div>
        </td>
      </tr>
      <tr>
        <td rowspan="1" colSpan="1">
          <div data-type="p"></div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p"></div>
        </td>
      </tr>
    </tbody>
  </table>
</div>

__JS 调用WIFI模块示例：__
```javascript
var ssid = "Xiaomi_296E_rock";
var passwd = "rockzhou";
WIFI.connect(ssid,passwd,function(state){console.log('wifi state:'+state);
var ip = WIFI.getip();
console.log('WIFI state getip ='+ip);
var mac = WIFI.getmac();
console.log('WIFI.getmac:'+mac);
var channel = WIFI.getchannel();
console.log('WIFI.getchannel:'+channel);
if (state == 'CONNECTED'){
	HTTP.request("http://www.baidu.com",function(result){
		console.log('http requst reulst=:'+result);
	});
}
});
```

运行结果log如下：
```plain
BoneEngine > WIFI.scan callback enter,ssid=WAVLINK_25A7 chanenl=0 
BoneEngine > WIFI.scan callback enter,ssid=Xiaomi_A288 chanenl=0 
BoneEngine > WIFI.scan callback enter,ssid=abcdef chanenl=0 
# I (3033) wifi: n:10 0, o:1 0, ap:255 255, sta:10 0, prof:1
I (4023) wifi: state: init -> auth (b0)
I (4063) wifi: state: auth -> assoc (0)
I (4093) wifi: state: assoc -> run (10)
I (4373) wifi: connected with Xiaomi_296E_rock, channel 10
BoneEngine > wifi state:CONNECTED 
BoneEngine > WIFI state getip =192.168.8.140 
BoneEngine > WIFI.getmac:0z\30:AE:A4:44:7A:5C 
BoneEngine > WIFI.getchannel:10 
```

 