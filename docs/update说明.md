# 水务控制器接口说明
## 1. 蓝牙服务接口
### 1.1 电源控制
```c
void bt_power_on(void);
void bt_power_off(void);
```
### 1.2 蓝牙是否连接
```c
// true->connected, false->not connected
bool bt_is_connected(void);
```
### 1.3 蓝牙数据收发
蓝牙数据收发通过串口3实现，因此与串口收发函数相同，串口号为LPUART3_PORT_IDX。
```c
int imxrt_uart_read(int uart_idx, uint8_t *buf, uint16_t buf_sz, uint32_t timeout);
int imxrt_uart_write(int uart_idx, uint8_t *buf, uint16_t len);
```
## 2. lora服务接口
### 2.1 电源控制
```c
void lora_power_on(void);
void lora_power_off(void);
```

### 2.2 lora数据收发接口
lora数据收发通过串口5实现，因此与串口收发函数相同，串口号为LPUART5_PORT_IDX。具体数据的协议需按照lora模块的说明进行。
```c
int at_command(int port_idx, char *cmd, int cmd_len);
int imxrt_uart_read(int uart_idx, uint8_t *buf, uint16_t buf_sz, uint32_t timeout);
int imxrt_uart_write(int uart_idx, uint8_t *buf, uint16_t len);
```
## 3. 4G模块服务
### 3.1 电源控制
```c
void ec20_power_on(void);
void ec20_power_off(void);
```

### 3.2 PPP拨号接口
```c
int ppp_init(void);
```

## 4. 时间服务
### 4.1 RTC
```c
int rtc_read_time(struct tm *tm);
int rtc_write_time(struct tm *tm);
```
### 4.2 系统时间
```c
void sys_get_time(struct timeval *tv);
void sys_set_time(struct timeval *tv);
```

## 5. ADS1115接口(4-20mA/0-10V)
```c
int ads111x_read(void);
```

## 6. 在线升级服务

### 6.1 Flash地址空间分布

用于启动的NOR Flash共有8MB空间，为了程序升级的可靠性将整个程序分为3个部分，第一部分是引导程序uboot，主要功能是校验并加载APP程序；第二部分和第三部分都是APP程序，用于保证升级出现异常时（如：掉电等）设备能正常启动并再次进行升级。

| 程序名称 | 地址范围              |
| -------- | --------------------- |
| uboot(512K)    | 0x00000000~0x00080000 |
|APP Image1(2M)| 0x00080000~0x00280000 |
|APP Image2(2M)| 0x00280000~0x00480000 |

### 6.2 引导过程

![image-20201012204559600](C:\Users\ZXJ\Documents\markdown\update说明.assets\image-20201012204559600.png)

### 6.3 升级过程

为了验证程序的正确性需要对生成的bin文件增加CRC校验，然后APP程序通过通信方式接收升级程序，并烧写到相应的Flash地址空间。

对bin文件增加校验的操作如下:

```shell
mkimage -A arm -T firmware -C none -O Linux -a 0x80000000 -e 0x80000004 -n "app for rt1052" -d rt1052.bin rt1052-app.bin
```

### 6.4 Flash 接口函数

```c

typedef struct
{
    uint32_t version;
    status_t (*init)(uint32_t instance, flexspi_nor_config_t *config);
    status_t (*write_page)(uint32_t instance, flexspi_nor_config_t *config, uint32_t dst_addr, const uint32_t *src);
    status_t (*erase_all)(uint32_t instance, flexspi_nor_config_t *config);
    status_t (*erase)(uint32_t instance, flexspi_nor_config_t *config, uint32_t start, uint32_t lengthInBytes);
    status_t (*read)(uint32_t instance, flexspi_nor_config_t *config, uint32_t *dst, uint32_t addr, uint32_t lengthInBytes);
    void (*clear_cache)(uint32_t instance);
    status_t (*xfer)(uint32_t instance, void *xfer);
    status_t (*update_lut)(uint32_t instance, uint32_t seqIndex, const uint32_t *lutBase, uint32_t seqNumber);
} flexspi_nor_driver_interface_t;
```

具体用法见update.c中示例。
