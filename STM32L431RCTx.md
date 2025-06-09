好的，遵照您的要求，我已将您提供的所有图片资料（金葫芦AHL-STM32L431核心板的原理图、引脚图、功能表等）进行系统性地整理、分析和汇编，形成一份内容详尽、结构清晰的综合技术手册。

---

# 金葫芦 AHL-STM32L431 核心板综合技术手册

## 目录
1.  [产品概述](#1-产品概述)
    *   [1.1 产品简介](#11-产品简介)
    *   [1.2 主要特性](#12-主要特性)
2.  [硬件系统详解](#2-硬件系统详解)
    *   [2.1 MCU最小系统 (STM32L431RCT6)](#21-mcu最小系统-stm32l431rct6)
    *   [2.2 电源模块](#22-电源模块)
    *   [2.3 USB转双串口模块 (CH342F)](#23-usb转双串口模块-ch342f)
    *   [2.4 板载外设与接口](#24-板载外设与接口)
        *   [2.4.1 TSI触摸按键模块](#241-tsi触摸按键模块)
        *   [2.4.2 测温模块](#242-测温模块)
        *   [2.4.3 LED三色灯](#243-led三色灯)
        *   [2.4.4 SWD调试接口](#244-swd调试接口)
        *   [2.4.5 复位电路](#245-复位电路)
        *   [2.4.6 MCU I/O引出接口](#246-mcu-io引出接口)
3.  [引脚定义与功能](#3-引脚定义与功能)
    *   [3.1 核心板引脚布局图 (68-Pin LCC)](#31-核心板引脚布局图-68-pin-lcc)
    *   [3.2 核心板引脚功能详表](#32-核心板引脚功能详表)
4.  [附录](#4-附录)
    *   [附录A: STM32L431RCT6 (LQFP64) 芯片引脚功能表](#附录a-stm32l431rct6-lqfp64-芯片引脚功能表)

---

## 1. 产品概述

### 1.1 产品简介
金葫芦 AHL-STM32L431 (IoT-GEC) 是一款基于 STMicroelectronics (意法半导体) 公司的高性能低功耗微控制器 **STM32L431RCT6** 设计的核心板。它采用68-Pin LCC封装，尺寸紧凑，集成了电源管理、USB通信、调试接口以及多种常用外设，旨在为物联网（IoT）、嵌入式系统开发、教学和快速原型验证提供一个功能强大且易于使用的平台。

### 1.2 主要特性
*   **核心处理器**: STM32L431RCT6, ARM Cortex-M4 内核, FPU, 80MHz 主频。
*   **板载存储**: 256KB Flash, 64KB SRAM。
*   **供电方式**: 支持5V输入，板载LDO转换为3.3V为MCU和外设供电。
*   **USB通信**: 板载CH342F芯片，通过USB Type-C接口扩展出两个UART串口，方便与PC或其他主机通信。
*   **板载外设**:
    *   1个TSI电容式触摸按键。
    *   1个NTC热敏电阻用于温度检测。
    *   1个RGB三色LED，用于状态指示。
    *   1个复位按键。
*   **调试接口**: 标准的4线SWD（Serial Wire Debug）接口，用于程序下载和在线调试。
*   **接口扩展**: 所有可用的MCU I/O口均通过LCC封装的引脚引出，方便用户根据需求连接至底板或外围电路。
*   **封装形式**: 68-pin LCC (Leadless Chip Carrier) 封装，适合SMT贴片安装。

## 2. 硬件系统详解

### 2.1 MCU最小系统 (STM32L431RCT6)
核心板围绕STM32L431RCT6构建，包含了MCU运行所需的基本电路。
*   **时钟**:
    *   **高速外部时钟 (HSE)**: PTH0 (PH0/OSC_IN) 和 PTH1 (PH1/OSC_OUT) 引脚用于连接外部高速晶振（原理图上未预留元件位置，一般使用内部高速时钟HSI）。
    *   **低速外部时钟 (LSE)**: PTC14 (PC14/OSC32_IN) 和 PTC15 (PC15/OSC32_OUT) 引脚已连接32.768kHz晶振(Y1)，用于RTC（实时时钟）功能。
*   **电源**: VDD/VDDA引脚均已连接滤波电容，保证系统稳定运行。VSSA/VREF-接地，VDDA/VREF+接3.3V，为ADC等模拟外设提供参考电压。
*   **启动配置**: PH3/BOOT0引脚接地，默认从主闪存（Main Flash memory）启动。



### 2.2 电源模块
*   **稳压芯片**: 采用T301 (SOT-23-5封装) LDO稳压器。
*   **功能**: 将外部输入的5V电压（P5V）转换为稳定的3.3V电压（P3V3），为主控MCU及板上其他芯片供电。
*   **电路**: 输入端C301 (1uF) 和输出端C302 (2.2uF) 为滤波电容，保证电源的纯净和稳定。



### 2.3 USB转双串口模块 (CH342F)
*   **核心芯片**: CH342F，一颗高速USB总线转接芯片，支持USB转双串口。
*   **接口**: 通过板载的USB Type-C接口（TAPE_C）连接到PC。
*   **串口映射**:
    *   **UART0**: 连接到MCU的 **PTA2 (TXD0)** 和 **PTA3 (RXD0)**。
    *   **UART2**: 连接到MCU的 **PTC10 (TXD1)** 和 **PTC11 (RXD1)**。
*   **功能**: 极大地简化了开发过程中的日志打印、数据收发和命令行交互，无需额外连接USB转TTL模块。



### 2.4 板载外设与接口

#### 2.4.1 TSI触摸按键模块
*   **原理**: 利用MCU的TSI（Touch Sensing Interface）功能。
*   **连接**: MCU的 **PTD2** 引脚连接至一块独立的PCB覆铜区域（触摸片 P203），通过检测电容变化实现触摸感应。
*   **应用**: 可作为用户交互的输入按键。



#### 2.4.2 测温模块
*   **核心元件**: NTC（负温度系数）热敏电阻 R202。
*   **原理**: R202与固定电阻R201（10KΩ）串联分压，随着环境温度变化，R202的阻值会发生显著变化，从而导致分压点的电压变化。
*   **连接**: 分压点连接至MCU的 **PTB0** (ADC1_IN15) 引脚，通过ADC采样该电压值，即可计算出当前温度。



#### 2.4.3 LED三色灯
*   **类型**: 共阳极RGB LED。
*   **控制引脚**:
    *   **红色(R)**: 由 **PTB7** 控制。
    *   **绿色(G)**: 由 **PTB8** 控制。
    *   **蓝色(B)**: 由 **PTB9** 控制。
*   **驱动方式**: MCU引脚输出低电平点亮对应颜色的LED，可通过PWM实现不同颜色和亮度的混合。



#### 2.4.4 SWD调试接口
*   **接口**: P8，一个标准的2.54mm间距4-Pin排针接口。
*   **引脚定义**:
    1.  **P5V**: 5V电源输出。
    2.  **SWD DIO**: **PTA13** (JTMS-SWDIO)
    3.  **SWD CLK**: **PTA14** (JTCK-SWCLK)
    4.  **GND**: 地。
*   **功能**: 用于连接ST-Link、J-Link等调试器，进行程序烧录和在线调试。



#### 2.4.5 复位电路
*   **按键**: S1 (RESET)，一个瞬时按压开关。
*   **功能**: 按下该按键可使MCU的NRST引脚产生一个低电平脉冲，实现硬件复位。



#### 2.4.6 MCU I/O引出接口
核心板通过J1, J2, J3, J4四个虚拟连接器将MCU的I/O口引出至LCC封装的焊盘上。具体对应关系见[3.2 核心板引脚功能详表](#32-核心板引脚功能详表)。

## 3. 引脚定义与功能

### 3.1 核心板引脚布局图 (68-Pin LCC)
下图展示了AHL-STM32L431核心板的68个引脚的物理位置和丝印标识。



### 3.2 核心板引脚功能详表
下表详细列出了核心板68个引脚的编号、丝印、对应的STM32引脚及其主要复用功能。

| GEC 引脚号 | 丝印 | STM32 引脚 | 主要复用功能与说明 |
| :--- | :--- | :--- | :--- |
| 1 | GND | GND | 地 |
| 2 | GPIOC_0/PTC9 | PTC9 | TSC_G4_IO4 / SDMMC1_D1 |
| 3 | GPIOC_1/PTC8 | PTC8 | TSC_G4_IO3 / SDMMC1_D0 |
| 4 | GPIOC_2/PTC7 | PTC7 | TSC_G4_IO2 / SDMMC1_D7 |
| 5 | GP10C_3/PTC6 | PTC6 | TSC_G4_IO1 / SDMMC1_D6 |
| 6 | GP10C_4/ADC_PIN7/PTC5 | PTC5 | COMP1_INP / ADC1_IN14 / WKUP5 / USART3_RX |
| 7 | GP10C_5/ADC_PIN6/PTC4 | PTC4 | COMP1_INM / ADC1_IN13 / USART3_TX |
| 8 | UARTA_RX/ADC_PIN1/PTA3 | PTA3 | **UART2_RX (板载CH342)** / COMP2_INP / ADC1_IN8 |
| 9 | GND | GND | 地 |
| 10 | UARTA_TX/ADC_PINO/PTA2 | PTA2 | **UART2_TX (板载CH342)** / COMP2_INM / ADC1_IN7 |
| 11 | GPIOC_6/ADC_PIN5/PTB1 | PTB1 | COMP1_INM / ADC1_IN16 / TIM1_CH3N / USART3_RTS_DE |
| 12 | GPIOC_7/ADC_PIN4/PTB0 | PTB0 | **ADC1_IN15 (板载测温)** / TIM1_CH2N / SPI1_NSS / USART3_CK |
| 13 | UARTC_RX/PTC11 | PTC11 | **UART3_RX (板载CH342)**, SPI3_MISO (BIOS保留串口更新用) |
| 14 | UARTC_TX/PTC10 | PTC10 | **UART3_TX (板载CH342)**, SPI3_SCK |
| 15 | GPIOB_0/ADC_PIN9/SPIB_MOSI/PTA7 | PTA7 | ADC1_IN12 / TIM1_CH1N / I2C3_SCL / SPI1_MOSI |
| 16 | GPIOB_1/SPIB_MISO/PTA6 | PTA6 | ADC1_IN11 / TIM1_BKIN / SPI1_MISO / COMP1_OUT |
| 17 | GND | GND | 地 |
| 18 | GND | GND | 地 |
| 19 | GNSS-ANT | N/A | GPS/北斗天线接入 (需要对应底板设计) |
| 20 | GND | GND | 地 |
| 21 | GPIOB_2/ADC_PIN8/SPIB_SCK/PTA5 | PTA5 | COMP1_INM / ADC1_IN10 / DAC1_OUT2 / TIM2_CH1 / SPI1_SCK |
| 22 | GPIOB_3/SPIB_PCS/PTA15 | PTA15 | JTDI / TIM2_CH1 / USART2_RX / SPI1_NSS / SPI3_NSS |
| 23 | GPIOB_4/PTB3 | PTB3 | JTDO-TRACESWO / TIM2_CH2 / SPI1_SCK / SPI3_SCK |
| 24 | GPIOB_5/PTB4 | PTB4 | NJTRST / I2C3_SDA / SPI1_MISO / SPI3_MISO |
| 25 | GPIOB_6/PTB5 | PTB5 | LPTIM1_IN1 / I2C1_SMBA / SPI1_MOSI / SPI3_MOSI |
| 26 | GND | GND | 地 |
| 27 | GPIOB_7/PTB6 | PTB6 | COMP2_INP / LPTIM1_ETR / I2C1_SCL / USART1_TX |
| 28 | GPIOB_8/SPIA_MISO/PTB14 | PTB14 | TIM1_CH2N / I2C2_SDA / SPI2_MISO / USART3_RTS_DE |
| 29 | GPIOB_9/SPIA_MOSI/PTB15 | PTB15 | RTC_REFIN / TIM1_CH3N / SPI2_MOSI |
| 30 | GPIOB_10/SPIA_SCK/PTB13 | PTB13 | TIM1_CH1N / I2C2_SCL / SPI2_SCK / USART3_CTS |
| 31 | GPIOB_11/SPIA_PCS/PTB12 | PTB12 | TIM1_BKIN / I2C2_SMBA / SPI2_NSS / USART3_CK |
| 32 | GND | GND | 地 |
| 33 | MAIN-ANT | N/A | 主天线接入 (需要对应底板设计) |
| 34 | GND | GND | 地 |
| 35 | GND | GND | 地 |
| 36 | P3V3_ME | 3.3V | 3.3V 电源输出 (监测用) |
| 37 | GPIOB_12/PTA8 | PTA8 | MCO / TIM1_CH1 / USART1_CK |
| 38 | GPIOB_13/I2CB_SDA/PTB11 | PTB11 | TIM2_CH4 / I2C2_SDA / USART3_RX |
| 39 | GPIOB_14/I2CB_SCL/PTB10 | PTB10 | TIM2_CH3 / I2C2_SCL / SPI2_SCK / USART3_TX |
| 40 | GPIOB_15/ADC_PIN11/PTA4 | PTA4 | COMP1_INM / ADC1_IN9 / DAC1_OUT1 / SPI1_NSS / SPI3_NSS |
| 41 | GPIOC_8/PTC15 | PTC15 | **OSC32_OUT (LSE)** / ADC1_IN1 / PC15 |
| 42 | GPIOC_9/PTC14 | PTC14 | **OSC32_IN (LSE)** / ADC1_IN0 / PC14 |
| 43 | GND | GND | 地 |
| 44 | GPIOC_10/ADC_PIN3/PTA1 | PTA1 | OPAMP1_VINM / COMP1_INP / ADC1_IN6 / TIM2_CH2 |
| 45 | GPIOC_11/ADC_PIN2/PTA0 | PTA0 | OPAMP1_VINP / COMP1_INM / ADC1_IN5 / RTC_TAMP2 / WKUP1 |
| 46 | GPIOC_12/ADC_PIN13/I2CA_SDA/PTC1 | PTC1 | ADC1_IN2 / LPTIM1_OUT / I2C3_SDA / LPUART1_TX |
| 47 | GPIOC_13/ADC_PIN14/I2CA_SCL/PTC0 | PTC0 | ADC1_IN1 / LPTIM1_IN1 / I2C3_SCL / LPUART1_RX |
| 48 | GPIOC_14/ADC_PIN15/PTC2 | PTC2 | ADC1_IN3 / LPTIM1_IN2 / SPI2_MISO |
| 49 | GPIOC_15/ADC_PIN16/PTC3 | PTC3 | ADC1_IN4 / LPTIM1_ETR / SPI2_MOSI |
| 50 | RESET | NRST | **硬件复位引脚** |
| 51 | GND | GND | 地 |
| 52 | GND | GND | 地 |
| 53 | - | - | (空引脚) |
| 54 | GPIODA_0/PTB9 | PTB9 | **LED_BLUE**, IR_OUT / I2C1_SDA / SPI2_NSS / CAN1_TX |
| 55 | GPIODA_1/PTB8 | PTB8 | **LED_GREEN**, I2C1_SCL / CAN1_RX / SDMMC1_D4 (BIOS指示) |
| 56 | GPIODA_2/PTB7 | PTB7 | **LED_RED**, COMP2_INM / PVD_IN / LPTIM1_IN2 / I2C1_SDA |
| 57 | RESET | NRST | **硬件复位引脚** (与Pin 50内部相连) |
| 58 | GPIODA_3/PTA13 | PTA13 | **SWD_DIO**, JTMS-SWDIO / IR_OUT |
| 59 | GPIODA_4/PTD2 | PTD2 | **TSI触摸**, USART3_RTS_DE / TSC_SYNC / SDMMC1_CMD |
| 60 | GND | GND | 地 |
| 61 | GPIOD_SWD_DIO/PTC12 | PTC12 | SPI3_MOSI / USART3_CK / TSC_G3_IO4 / SDMMC1_CK |
| 62 | GPIOD_SWD_CLK/PTA14 | PTA14 | **SWD_CLK**, JTCK-SWDCLK / LPTIM1_OUT / I2C1_SMBA |
| 63 | GND | GND | 地 |
| 64 | 3.3V | 3.3V | 3.3V 电源输出 (150mA Max) |
| 65 | GND | GND | 地 |
| 66 | 5V | 5V | 5V 电源输入 |
| 67 | 5V | 5V | 5V 电源输入 |
| 68 | GND | GND | 地 |

---

## 4. 附录

### 附录A: STM32L431RCT6 (LQFP64) 芯片引脚功能表
此表为MCU芯片本身的引脚定义，供高级用户参考。

| 引脚号 | 引脚名 | 复用功能 | 备注 |
| :--- | :--- | :--- | :--- |
| 1 | VBAT | VBAT | RTC和备份寄存器电源 |
| 2 | PTC13 | RTC_TAMP1/RTC_TS/RTC_OUT/WKUP2 | |
| 3 | PTC14 | OSC32_IN | 外接低速晶振引脚 |
| 4 | PTC15 | OSC32_OUT | 外接低速晶振引脚 |
| 5 | PTH0 | OSC_IN | 外接高速晶振引脚 |
| 6 | PTH1 | OSC_OUT | 外接高速晶振引脚 |
| 7 | NRST | NRST | 复位引脚，需上拉 |
| 8 | PTC0 | ADC1_IN1/LPTIM1_IN1/I2C3_SCL/LPUART1_RX/LPTIM2_IN1 | |
| 9 | PTC1 | ADC1_IN2/LPTIM1_OUT/I2C3_SDA/LPUART1_TX | |
| 10 | PTC2 | ADC1_IN3/LPTIM1_IN2/SPI2_MISO | |
| 11 | PTC3 | ADC1_IN4/LPTIM1_ETR/SPI2_MOSI, SAI1_SD_A/LPTIM2_ETR | |
| 12 | VSSA | VREF- | A/D 电源地 |
| 13 | VDDA | VREF+ | A/D 电源 |
| 14 | PTA0 | OPAMP1_VINP/COMP1_INM/ADC1_IN5/RTC_TAMP2/WKUP1/TIM2_CH1/... | |
| 15 | PTA1 | OPAMP1_VINM/COMP1_INP/ADC1_IN6/TIM2_CH2/I2C1_SMBA/SPI1_SCK/... | |
| 16 | PTA2 | COMP2_INM/ADC1_IN7/WKUP4/LSCO/TIM2_CH3/USART2_TX/... | |
| 17 | PTA3 | OPAMP1_VOUT/COMP2_INP/ADC1_IN8/TIM2_CH4/USART2_RX/... | |
| 18 | VSS | VSS | 数字地 |
| 19 | VDD | VDD | 数字电源 |
| 20 | PTA4 | COMP1_INM/COMP2_INM/ADC1_IN9/DAC1_OUT1/SPI1_NSS/... | |
| 21 | PTA5 | COMP1_INM/COMP2_INM/ADC1_IN10/DAC1_OUT2/TIM2_CH1/... | |
| 22 | PTA6 | ADC1_IN11/TIM1_BKIN/SPI1_MISO/COMP1_OUT/USART3_CTS/... | |
| 23 | PTA7 | ADC1_IN12/TIM1_CH1N/I2C3_SCL/SPI1_MOSI/... | |
| 24 | PTC4 | COMP1_INM/ADC1_IN13/USART3_TX | |
| 25 | PTC5 | COMP1_INP/ADC1_IN14/WKUP5/USART3_RX | |
| 26 | PTB0 | ADC1_IN15/TIM1_CH2N/SPI1_NSS/USART3_CK/... | |
| 27 | PTB1 | COMP1_INM/ADC1_IN16/TIM1_CH3N/USART3_RTS_DE/... | |
| 28 | PTB2 | COMP1_INP/RTC_OUT/LPTIM1_OUT/I2C3_SMBA | |
| 29 | PTB10 | TIM2_CH3/I2C2_SCL/SPI2_SCK/USART3_TX/LPUART1_RX/... | |
| 30 | PTB11 | TIM2_CH4/I2C2_SDA/USART3_RX/LPUART1_TX/... | |
| 31 | VSS | VSS | 数字地 |
| 32 | VDD | VDD | 数字电源 |
| 33 | PTB12 | TIM1_BKIN/TIM1_BKIN_COMP2/I2C2_SMBA/SPI2_NSS/... | |
| 34 | PTB13 | TIM1_CH1N/I2C2_SCL/SPI2_SCK/USART3_CTS/... | |
| 35 | PTB14 | TIM1_CH2N/I2C2_SDA/SPI2_MISO/USART3_RTS_DE/... | |
| 36 | PTB15 | RTC_REFIN/TIM1_CH3N/SPI2_MOSI/TSC_G1_IO4/... | |
| 37 | PTC6 | TSC_G4_IO1/SDMMC1_D6 | |
| 38 | PTC7 | TSC_G4_IO2/SDMMC1_D7 | |
| 39 | PTC8 | TSC_G4_IO3/SDMMC1_D0 | |
| 40 | PTC9 | TSC_G4_IO4/SDMMC1_D1 | |
| 41 | PTA8 | MCO/TIM1_CH1/USART1_CK/SWPMI1_IO/SAI1_SCK_A/... | |
| 42 | PTA9 | TIM1_CH2/I2C1_SCL/USART1_TX/SAI1_FS_A/TIM15_BKIN | |
| 43 | PTA10 | TIM1_CH3/I2C1_SDA/USART1_RX/SAI1_SD_A | |
| 44 | PTA11 | TIM1_CH4/TIM1_BKIN2/SPI1_MISO/COMP1_OUT/USART1_CTS/... | |
| 45 | PTA12 | TIM1_ETR/SPI1_MOSI/USART1_RTS_DE/CAN1_TX | |
| 46 | PTA13 | JTMS-SWDIO/IR_OUT/SWPMI1_TX/SAI1_SD_B | SWDIO |
| 47 | VSS | VSS | 数字地 |
| 48 | VDD | VDD | 数字电源 |
| 49 | PTA14 | JTCK-SWDCLK/LPTIM1_OUT/I2C1_SMBA/SWPMI1_RX/... | SWDCLK |
| 50 | PTA15 | JTDI/TIM2_CH1/TIM2_ETR/USART2_RX/SPI1_NSS/... | |
| 51 | PTC10 | SPI3_SCK/USART3_TX/TSC_G3_IO2/SDMMC1_D2 | |
| 52 | PTC11 | SPI3_MISO/USART3_RX/TSC_G3_IO3/SDMMC1_D3 | |
| 53 | PTC12 | SPI3_MOSI/USART3_CK/TSC_G3_IO4/SDMMC1_CK | |
| 54 | PTD2 | USART3_RTS_DE/TSC_SYNC/SDMMC1_CMD | |
| 55 | PTB3 | COMP2_INM/JTDO-TRACESWO/TIM2_CH2/... | |
| 56 | PTB4 | COMP2_INP/NJTRST/I2C3_SDA/SPI1_MISO/... | |
| 57 | PTB5 | LPTIM1_IN1/I2C1_SMBA/SPI1_MOSI/SPI3_MOSI/... | |
| 58 | PTB6 | COMP2_INP/LPTIM1_ETR/I2C1_SCL/USART1_TX/... | |
| 59 | PTB7 | COMP2_INM/PVD_IN/LPTIM1_IN2/I2C1_SDA/... | |
| 60 | PTH3 | BOOT0 | 启动设置，接地 |
| 61 | PTB8 | I2C1_SCL/CAN1_RX/SDMMC1_D4/SAI1_MCLK_A/TIM16_CH1 | |
| 62 | PTB9 | IR_OUT/I2C1_SDA/SPI2_NSS/CAN1_TX/SDMMC1_D5/SAI1_FS_A | |
| 63 | VSS | VSS | 数字地 |
| 64 | VDD | VDD | 数字电源 |