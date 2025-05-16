# 1. STM32 外设是什么？
在STM32的上下文中，外设（Peripheral） 是指集成在微控制器（MCU）芯片内部的专用硬件功能模块，它们用于与外部设备或环境交互，或提供特定的内部功能。外设不是独立的“设备”，而是STM32芯片的一部分，通过寄存器编程来控制其行为。

---


外设是STM32内部的硬件功能模块，例如：
• 通信接口：USART、SPI、I2C、CAN、USB（用于串行通信）

• 定时器/计数器：TIM1~TIM17（用于PWM、输入捕获、定时中断等）

• 模拟外设：ADC、DAC（模数/数模转换）

• GPIO：通用输入输出引脚（直接控制高低电平）

• 中断控制器：NVIC（管理中断优先级）

• DMA：直接内存访问（不经过CPU高速搬运数据）

• 时钟系统：RCC（控制内部时钟源和分频）


这些外设通过总线（AHB/APB） 连接到CPU内核，开发者通过配置它们的寄存器来使用其功能。



**总结**
• STM32外设是芯片内部的硬件功能模块，通过寄存器控制。  

• 外设与外部设备通过GPIO、SPI等接口连接。  

• 使用外设需：1）开启时钟，2）配置寄存器，3）处理中断/DMA（可选）。  









这是 STM32 标准外设库（Standard Peripheral Library）中用于 **开启外设时钟** 的函数。STM32 的外设运行前必须先给它供电，也就是 **打开对应的时钟开关（Clock Enable）**。

---

### 🔧 函数原型：

```c
void RCC_AHBPeriphClockCmd(uint32_t RCC_AHBPeriph, FunctionalState NewState);
void RCC_APB2PeriphClockCmd(uint32_t RCC_APB2Periph, FunctionalState NewState);
void RCC_APB1PeriphClockCmd(uint32_t RCC_APB1Periph, FunctionalState NewState);
```

这些函数的作用都是一样的：

> **打开或关闭某个外设的时钟，使能它工作。**

---

### 🧱 区别是“属于哪个总线”：

| 函数名                      | 控制哪条总线上的外设？       | 常见外设举例                             |
| ------------------------ | ----------------- | ---------------------------------- |
| `RCC_AHBPeriphClockCmd`  | **AHB** 总线（高级总线）  | DMA、FSMC、CRC                       |
| `RCC_APB2PeriphClockCmd` | **APB2** 总线（高速外设） | GPIOA\~GPIOE、AFIO、USART1、ADC、SPI1  |
| `RCC_APB1PeriphClockCmd` | **APB1** 总线（低速外设） | USART2~~USART3、I2C、SPI2、TIM2~~TIM7 |

---

### 🧩 参数说明：

* `RCC_xxxPeriph`：要控制的外设时钟，比如 `RCC_APB2Periph_GPIOC`
* `NewState`：

  * `ENABLE`：开启时钟（外设开始工作）
  * `DISABLE`：关闭时钟（省电但不能用）

---

### ✅ 举例说明：

#### 开启 GPIOC 的时钟（属于 APB2）：

```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
```

#### 开启 USART2 的时钟（属于 APB1）：

```c
RCC_APB1PeriphClockCmd(RCC_APB1Periph_USART2, ENABLE);
```

#### 开启 DMA1 的时钟（属于 AHB）：

```c
RCC_AHBPeriphClockCmd(RCC_AHBPeriph_DMA1, ENABLE);
```

---

### 💡 为什么要这样分？

STM32 的外设被划分到不同的总线上，按访问速度和功能分类。

* AHB：速度最高（比如 DMA）
* APB2：次高（GPIO、SPI1、USART1）
* APB1：最慢（USART2、SPI2、TIM2）

---

很好，这是理解 STM32 架构的关键一步！下面我来一步一步给你解释：

---

## ✅ 什么是“总线”？

在 STM32（以及其他微控制器）中，\*\*总线（Bus）\*\*是一个数据传输通道，用于连接 **CPU（主控）和外设（比如 GPIO、串口、ADC 等）**。

你可以把它想象成一条高速公路：

* **CPU = 发货中心**
* **外设 = 各个工厂或商店**
* **总线 = 运输通道**

不同的“高速公路”有不同的限速（访问速度）和优先级，所以 STM32 把外设按功能和性能划分到了不同的总线上。

---

## ✅ STM32F103 的总线划分：

STM32F103 使用 3 种主要的总线：

| 总线名称     | 英文全称                      | 特点/用途        |
| -------- | ------------------------- | ------------ |
| **AHB**  | Advanced High-speed Bus   | 高速总线，速度最高    |
| **APB2** | Advanced Peripheral Bus 2 | 比较快，连接“重要外设” |
| **APB1** | Advanced Peripheral Bus 1 | 普通外设，速度较慢    |

---

## ✅ 常用外设所属总线分类表（STM32F103 系列）：

| 外设/模块             | 所属总线 | 时钟使能宏示例                            |
| ----------------- | ---- | ---------------------------------- |
| **GPIOA\~GPIOE**  | APB2 | `RCC_APB2Periph_GPIOA`             |
| **AFIO**（重映射）     | APB2 | `RCC_APB2Periph_AFIO`              |
| **USART1**        | APB2 | `RCC_APB2Periph_USART1`            |
| **USART2/3**      | APB1 | `RCC_APB1Periph_USART2` / `USART3` |
| **SPI1**          | APB2 | `RCC_APB2Periph_SPI1`              |
| **SPI2**          | APB1 | `RCC_APB1Periph_SPI2`              |
| **ADC1 / ADC2**   | APB2 | `RCC_APB2Periph_ADC1` / `ADC2`     |
| **TIM1**（高级定时器）   | APB2 | `RCC_APB2Periph_TIM1`              |
| **TIM2\~TIM7**    | APB1 | `RCC_APB1Periph_TIM2` 等            |
| **I2C1 / I2C2**   | APB1 | `RCC_APB1Periph_I2C1` / `I2C2`     |
| **DMA1**          | AHB  | `RCC_AHBPeriph_DMA1`               |
| **FSMC（外部存储控制器）** | AHB  | `RCC_AHBPeriph_FSMC`               |
| **CRC**（校验单元）     | AHB  | `RCC_AHBPeriph_CRC`                |

---

## ✅ 举个例子：

### 如果你想使用 `USART2`：

1. 它属于 `APB1` 总线
2. 你就写：

```c
RCC_APB1PeriphClockCmd(RCC_APB1Periph_USART2, ENABLE);
```

### 如果你想使用 `GPIOC`：

1. 它属于 `APB2` 总线
2. 你就写：

```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
```

---

## 总结记忆口诀：

* **GPIO、ADC、SPI1、USART1、AFIO 在 APB2**
* **USART2/3、SPI2、I2C、普通定时器在 APB1**
* **DMA、FSMC、CRC 在 AHB**

---


