USB协议枚举分析
所有的USB通信的数据流都是由主控器发起，其它从设备进行响应。
从设备的描述符
完整的设备描述符和配置描述符见代码片段



HID键盘 一次完整枚举过程及数据：
以下描述中“主机”指的是PC的USB主控制器，“设备”指插入USB接口的设备。
1.	Reset
复位设备

2.	Suspended

3.	Reset

4.	High speed Detection Handshake

5.	GetDescriptor (Device)
枚举的第一个请求中，主机试图获取设备描述符来确定默认设备(端点0)的最大包大小。
主控制器发出包：80 06 00 01 00 00 40 00，其中设备描述符中40给出当前主控制器允许接收最大包大小为64个字节。
从设备应答包：
返回部分设备描述符中数据：12 01 00 02 00 00 00 40， 其中12给出设备描述符总长度，之后主控制复位从设备后，再次根据12长度获取设备全部设备描述符。
获取设备描述符不全，无关紧要，主要获取从设备设备描述符总长度即可。

主控制器发出包80 06 00 01 00 00 40 00


|Setup request||||
|:--|:--|--:|--:|
|Name|Value|Dec|Hex|
|bmRequestType.Recipient|Device|0|0x00|
|bmRequestType.Type|Standard|0|0x00|
|bmRequestType.Direction|Device-to-host|1|0x01|
|bRequest|GET_DESCRIPTOR|6|0x06|
|wValue|DEVICE|256|0x0100|
|wIndex|Zero|0|0x0000|
|wLength|64|64|0x0040|


| Name                    | Value          |  Dec |     Hex |
| :---------------------- | :------------- | ---: | ------: |
| bmRequestType.Recipient | Device         |    0 |    0x00 |
| bmRequestType.Type      | Standard       |    0 |    0x00 |
| bmRequestType.Direction | Device-to-host |    1 |    0x01 |
| bRequest                | GET_DESCRIPTOR |    6 |    0x06 |
| wValue                  | DEVICE         |   256|  0x0100 |
| wIndex                  | Zero           |    0 |  0x0000 |
| wLength                 | 64             |   64 |  0x0040 |












### 设备描述符
```c
//USB Standard Device Descriptor
uint8_t USBD_DeviceDesc[18] =
{
	0x12,       /*bLength*/
              /*描述符大小*/
	0x01, 			/*bDescriptorType*/
              /*设备描述符,固定0x01*/
	0x00, 0x02, /*bcdUSB*/
              /*USB规范发布号0002=2.0, 0110-1.1*/
	0x00,       /*bDeviceClass*/
              /*类型代码=0所有接口在描述符里并且每个接口独立,=1-F表示不接口,=FF自定义*/
	0x00,       /*bDeviceSubClass*/
              /*子类型代码,bDeviceClass 为0, 该值为0, 否则USB-IF组织定义的编码*/
	0x00,       /*bDeviceProtoco*/
              /*协议代码(由USB分配),如果使用USB-IF组织定义的协议就需要设置这里的值,否则直接设置为0.*/
               /*如果厂商自己定义的可以设置为FFH*/
	0x40,      	/*bMaxPacketSize*/
              /*端点0最大分组大小,有效值8,16,32,64*/
	0x3c, 0x41, /*idVendor*/
              /*供应商ID(由USB分配)*/
	0x07, 0x21, /*idVendor*/
              /*产品ID(由厂家分配)*/
	0x02, 0x05, /*bcdDevice*/
              /*设备出产编码.由厂家自行设置*/
	0x01,       /*Index of manufacturer  string*/
              /*厂商描述符字符串索引．索引到对应的字符串描述符．为０则表示没有*/
	0x02,       /*Index of product string*/
              /*产品描述符字符串索引*/
	0x00,       /*Index of serial number string*/
              /*设备序列号字符串索引*/
	0x01        /*bNumConfigurations*/
              /*配置字符串的个数*/
} ;
```

### 配置描述符
```C
//USB HID device Configuration Descriptor 
static uint8_t USBD_HID_CfgDesc[34] =
{
  //Configuration Descriptor
  0x09,	      /* bLength: Configuration Descriptor size*/
              /*描述符长度*/
  0x02,	      /* bDescriptorType: Configuration*/
              /*配置描述符,固定0x02*/
  0x22, 0x00, /* wTotalLength: Bytes returned*/
              /*返回总长度*/
  0x01,	      /*bNumInterfaces: 1 interface*/
              /*配置接口描述符数量*/
  0x01,	      /*bConfigurationValue: Configuration value*/
              /*作为Set Configuration的一个参数选择配置值*/
  0x00,       /*iConfiguration: Index of string descriptor describing the configuration*/
              /*描述该配置字符串描述符的索引*/
  0xa0,       /*bmAttributes: bus powered and Support Remote Wake-up*/
              /*供电模式选择．Bit4-0保留，D7:总线供电，D6:自供电，D5:远程唤醒*/
  0xFa,       /*MaxPower 100 mA: this current is used for detecting Vbus*/
              /*提供USB设备最大电流．以2mA为单位*/
  
  //Interface Descriptor
  0x09,       /*bLength: Interface Descriptor size*/
              /*描述符长度*/
  0x04,	      /*bDescriptorType: Interface descriptor type*/
              /*接口描述符,固定0x04*/
  0x00,       /*bInterfaceNumber: Number of Interface*/
              /*接口编号*/
  0x00,       /*bAlternateSetting: Alternate setting*/
              /*备用接口描述符编号*/
  0x01,       /*bNumEndpoints*/
              /*端点数量*/
  0x03,       /*bInterfaceClass*/
              /*接口类型,(由USB分配),0x03 = HID*/
  0x01,       /*bInterfaceSubClass*/
              /*子类型(由USB分配) 1=BOOT, 0=no boot*/
  0x01,       /*nInterfaceProtocol*/
              /*接口协议,(由USB分配) 0=none, 1=keyboard, 2=mouse*/
  0x00,       /*iInterface: Index of string descriptor*/
  
  /******************** Descriptor of Joystick Keyboard HID ********************/
  0x09,       /*bLength: HID Descriptor size*/
  0x21,       /*bDescriptorType: HID*/
  0x10,       /*bcdHID: HID Class Spec release number*/
  0x01,
  0x00,       /*bCountryCode: Hardware target country*/
  0x01,       /*bNumDescriptors: Number of HID class descriptors to follow*/
  0x22,       /*bDescriptorType*/
  0x41,	      /*wItemLength: Total length of Report descriptor*/
  0x00,
  
  //Endpoint Descriptor
  0x07,       /*bLength: Endpoint Descriptor size*/
              /*描述符长度*/
  0x05,       /*bDescriptorType:*/
              /*端点描述符类型,固定0x05*/
  0x81,       /*bEndpointAddress: Endpoint Address (IN)*/
              /*USB设备的端点地址.Bit7:IN(1)/OUT(0)方向,如果是控制端点可以忽略,Bit6-4:保留,BIt3-0:端点号*/
  0x03,       /*bmAttributes: Interrupt endpoint*/
              /*端点属性.Bit7-2保留,BIt1-0:控制(00),同步(01),批量(02),中断(03)*/
  0x08, 0x00, /*wMaxPacketSize: 4 Byte max ,端点接收或发送的最大数据大小*/
  0x0a,       /*bInterval: Polling Interval (10 ms)*/
              /*轮训数据传送端点的时间间隔,对于批量传送和控制传送的端点忽略.对于同步传送的端点,必须为1*/
              /*对于中断传送的端点，范围为1-255*/
};





```
