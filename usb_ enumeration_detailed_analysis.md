

```C
//USB HID device Configuration Descriptor 
static uint8_t USBD_HID_CfgDesc[34] =
{
  //Configuration Descriptor
  0x09,	      /* bLength: Configuration Descriptor size ,描述符长度*/
  0x02,	      /* bDescriptorType: Configuration ,配置描述符,固定0x02*/
  0x22, 0x00, /* wTotalLength: Bytes returned,返回总长度*/
  0x01,	      /*bNumInterfaces: 1 interface,配置接口描述符数量*/
  0x01,	      /*bConfigurationValue: Configuration value*/
              /*作为Set Configuration的一个参数选择配置值*/
  0x00,       /*iConfiguration: Index of string descriptor describing the configuration*/
              /*描述该配置字符串描述符的索引*/
  0xa0,       /*bmAttributes: bus powered and Support Remote Wake-up*/
              /*供电模式选择．Bit4-0保留，D7:总线供电，D6:自供电，D5:远程唤醒*/
  0xFa,       /*MaxPower 100 mA: this current is used for detecting Vbus*/
            /*提供USB设备最大电流．以2mA为单位*/
  
  //Interface Descriptor
  0x09,       /*bLength: Interface Descriptor size,描述符长度*/
  0x04,	      /*bDescriptorType: Interface descriptor type,接口描述符,固定0x04*/
  0x00,       /*bInterfaceNumber: Number of Interface,接口编号*/
  0x00,       /*bAlternateSetting: Alternate setting,备用接口描述符编号*/
  0x01,       /*bNumEndpoints,端点数量*/
  0x03,       /*bInterfaceClass,接口类型,(由USB分配),0x03 = HID*/
  0x01,       /*bInterfaceSubClass,子类型(由USB分配) 1=BOOT, 0=no boot*/
  0x01,       /*nInterfaceProtocol,接口协议,(由USB分配) 0=none, 1=keyboard, 2=mouse*/
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
  0x07,       /*bLength: Endpoint Descriptor size,描述符长度*/
  0x05,       /*bDescriptorType:,端点描述符类型,固定0x05*/
  0x81,       /*bEndpointAddress: Endpoint Address (IN)*/
              /*USB设备的端点地址.Bit7:IN(1)/OUT(0)方向,如果是控制端点可以忽略*/
              /*Bit6-4:保留,BIt3-0:端点号*/
  0x03,       /*bmAttributes: Interrupt endpoint*/
              /*端点属性.Bit7-2保留,BIt1-0:控制(00),同步(01),批量(02),中断(03)*/
  0x08, 0x00, /*wMaxPacketSize: 4 Byte max ,端点接收或发送的最大数据大小*/
  0x0a,       /*bInterval: Polling Interval (10 ms)*/
              /*轮训数据传送端点的时间间隔,对于批量传送和控制传送的端点忽略.*/
              /*对于同步传送的端点,必须为1,对于中断传送的端点，范围为1-255*/
};





```
