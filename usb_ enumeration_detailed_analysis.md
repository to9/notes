USB协议枚举分析
所有的USB通信的数据流都是由主控器发起，其它从设备进行响应。
从设备的描述符
完整的设备描述符和配置描述符见代码片段



HID键盘 一次完整枚举过程及数据：
以下描述中“主机”指的是PC的USB主控制器，“设备”指插入USB接口的设备。
## 1.	Reset
复位设备

## 2.	Suspended

## 3.	Reset

## 4.	High speed Detection Handshake

## 5.	GetDescriptor (Device)
枚举的第一个请求中，主机试图获取设备描述符来确定默认设备(端点0)的最大包大小。
主控制器发出包：80 06 00 01 00 00 40 00，其中设备描述符中40给出当前主控制器允许接收最大包大小为64个字节。
从设备应答包：
返回部分设备描述符中数据：12 01 00 02 00 00 00 40， 其中12给出设备描述符总长度，之后主控制复位从设备后，再次根据12长度获取设备全部设备描述符。
获取设备描述符不全，无关紧要，主要获取从设备设备描述符总长度即可。

主控制器发出包80 06 00 01 00 00 40 00

Setup request:

| Name                    | Value          |  Dec |     Hex |
| :---------------------- | :------------- | ---: | ------: |
| bmRequestType.Recipient | Device         |    0 |    0x00 |
| bmRequestType.Type      | Standard       |    0 |    0x00 |
| bmRequestType.Direction | Device-to-host |    1 |    0x01 |
| bRequest                | GET_DESCRIPTOR |    6 |    0x06 |
| wValue                  | DEVICE         |   256|  0x0100 |
| wIndex                  | Zero           |    0 |  0x0000 |
| wLength                 | 64             |   64 |  0x0040 |

从设备应答包：12 01 00 02 00 00 00 40 

Device descriptor:

| Name               | Value                               | Dec | Hex    |
|:-------------------|:------------------------------------|----:|-------:|
| bLength            | Invalid, might be 18                | 18  | 0x12   |
| bDescriptorType    | DEVICE                              | 1   | 0x01   |
| bcdUSB             | 2                                   | 512 | 0x0200 |
| bDeviceClass       | Class defined at interface level    | 0   | 0x00   |
| bDeviceSubClass    | Subclass defined at interface level | 0   | 0x00   |
| bDeviceProtocol    | None                                | 0   | 0x00   |
| bMaxPacketSize0    | 64                                  | 64  | 0x40   |
| idVendor           | Not available                       | ?   | ?      |
| idProduct          | Not available                       | ?   | ?      |
| bcdDevice          | Not available                       | ?   | ?      |
| iManufacturer      | Not available                       | ?   | ?      |
| iProduct           | Not available                       | ?   | ?      |
| iSerialNumber      | Not available                       | ?   | ?      |
| bNumConfigurations | Not available                       | ?   | ?      |

## 6.	Reset

## 7.	High speed Detection Handshake

## 8.	SetAddress (1)
主控制器分配从设备设备的设备地址。范围为0 – 127。
主控制器发出包：00 05 01 00 00 00 00 00，告知从设备当前从设备地址为1，同设备在其他电脑可能分配地址00 05 16 00 00 00 00 00， 地址22。

分配USB设备的地址，每个USB设备都需要分配一个唯一的地址才能进行通讯，与网卡的MAC地址类似。USB总线上最大可以有127个设备。

SetAddress ：
包格式00 05 XX XX 00 00 00 00，其中XX XX 为2字节设备地址，地址范围0 – 128

主控制器发出包：00 05 01 00 00 00 00 00

Setup request:

| Name                    | Value          | Dec  | Hex  |
|:------------------------|:---------------|-----:|-----:|
| bmRequestType.Recipient | Device         |    0 | 0x00 |
| bmRequestType.Type      | Standard       |    0 | 0x00 |
| bmRequestType.Direction | Host-to-device |    0 | 0x00 |
| bRequest                | SET_ADDRESS    |    5 | 0x05 |
| wValue                  | Address 1      |    1 | 0x01 |
| wIndex                  | Zero           |    0 | 0x00 |
| wLength                 | 0              |    0 | 0x00 |

## 9.	GetDescriptor (Device)
主控制器根据之前发出“获取设备描述符”包，得知设备(端点0)最大包大小。再次获取从设备的全部”设备描述符”数据包。

主控制器发出包：
80 06 00 01 00 00 12 00，其中12 00给出主控制器一包数据允许接收大小。也就是说从设备发送数据不应大于该值18字节(USB使用LSB在前MSB在后方式)。

从设备应答包：
根据主控制器允许接收最大包大小进行发送数据，12 01 00 02 00 00 00 40 3C 41 07 21 02 05 01 02 00 01

主控制器发出包：80 06 00 01 00 00 12 00

Setup request:

| Name                    | Value          | Dec    | Hex    |
|:------------------------|:---------------|-------:|-------:|
| bmRequestType.Recipient | Device         |    0   | 0x00   |
| bmRequestType.Type      | Standard       |    0   | 0x00   |
| bmRequestType.Direction | Device-to-host |    1   | 0x01   |
| bRequest                | GET_DESCRIPTOR |    6   | 0x06   |
| wValue                  | DEVICE         |    256 | 0x0100 |
| wIndex                  | Zero           |    0   | 0x0000 |
| wLength                 | 18             |    18  | 0x0012 |

从设备应答包：12 01 00 02 00 00 00 40 

| Name                    | Value                               | Dec    | Hex    |
|:------------------------|:------------------------------------|-------:|-------:|
| bLength                 | 18                                  | 18     | 0x12   |
| bDescriptorType         | DEVICE                              | 1      | 0x01   |
| bcdUSB                  | 2                                   | 512    | 0x0200 |
| bDeviceClass            | Class defined at interface level    | 0      | 0x00   |
| bDeviceSubClass         | Subclass defined at interface level | 0      | 0x00   |
| bDeviceProtocol         | None                                | 0      | 0x00   |
| bMaxPacketSize0         | 64                                  | 64     | 0x40   |
| idVendor                | Dell Inc.                           | 16,700 | 0x413C |
| idProduct               | 0x2107                              | 8,455  | 0x2107 |
| bcdDevice               | 5.0.2                               | 1,282  | 0x0502 |
| iManufacturer           | 1                                   | 1      | 0x01   |
| iProduct                | 2 "Joystick in FS Mode"             | 2      | 0x02   |
| iSerialNumber           | 0                                   | 0      | 0x00   |
| bNumConfigurations      | 1                                   | 　     | 　     |


## 10.	GetDescriptor (Configuration)
主控制器发出包：80 06 00 02 00 00 09 00获取部分设备“配置描述符大小信息”，从设备应收到后给出该应答包，其包大小不应该大于主控制器待能接收最大长度09 00(9) 字节数据。从设发送配置描述符前9字节信息09 02 22 00 01 01 00 A0 FA。前9字节中第3和4字节wTotalLength包含了当前从设备“配置描述符”大小信息。之后设备会再一次获取该从设备“配置描述符”数据，其获取长度根据第一次返回的长度适当调整主控制器允许接收数据长度包80 06 00 02 00 00 FF 00，主控制器允许接收最大长度为FF 00(255字节)。

主控制器发出包：80 06 00 02 00 00 09 00

Setup request:

| Name                    | Value             | Dec    | Hex    |
|:------------------------|:------------------|-------:|-------:|
| bmRequestType.Recipient | Device            | 0      | 0x00   |
| bmRequestType.Type      | Standard          | 0      | 0x0    |
| bmRequestType.Direction | Device-to-host    | 1      | 0x1    |
| bRequest                | GET_DESCRIPTOR    | 6      | 0x06   |
| wValue                  | CONFIGURATION (0) | 512    | 0x0200 |
| wIndex                  | Zero              | 0      | 0x0000 |
| wLength                 | 9                 | 9      | 0x0009 |

从设备应答包：09 02 22 00 01 01 00 A0 FA

| Name                       | Value             | Dec    | Hex    |
|:---------------------------|:------------------|-------:|-------:|
| bLength                    | Valid             | 9      | 0x09   |
| bDescriptorType            | CONFIGURATION     | 2      | 0x02   |
| wTotalLength               | 34 bytes          | 34     | 0x0022 |
| bNumInterface              | 1                 | 1      | 0x01   |
| bConfigurationValue        | 1                 | 1      | 0x01   |
| iConfiguration             | 0                 | 0      | 0x00   |
| bmAttributes. Reserved     | Zero              | 0      | 0x00   |
| bmAttributes. RemoteWakeup | Supported         | 1      | 0x1    |
| bmAttributes. SelfPowered  | No, Bus Powered   | 0      | 0x0    |
| bmAttributes. Reserved7    | One               | 1      | 0x1    |
| bMaxPower                  | 500 mA            | 250    | 0xFA   |

## 11.	GetDescriptor (Configuration)

主控制器发出包：80 06 00 02 00 00 FF 00

Setup request:

| Name                    | Value             | Dec    | Hex    | 
|:------------------------|:------------------|-------:|-------:| 
| bmRequestType.Recipient | Device            | 0      | 0x00   | 
| bmRequestType.Type      | Standard          | 0      | 0x0    | 
| bmRequestType.Direction | Device-to-host    | 1      | 0x1    | 
| bRequest                | GET_DESCRIPTOR    | 6      | 0x06   | 
| wValue                  | CONFIGURATION (0) | 512    | 0x0200 | 
| wIndex                  | Zero              | 0      | 0x0000 | 
| wLength                 | 255               | 255    | 0x00FF | 





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
