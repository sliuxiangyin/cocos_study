# GPIO_Mode 
这是 STM32 标准外设库中定义的 GPIO 模式枚举类型，决定了引脚的输入输出方式。不同场景需要使用不同的 GPIO 模式。

---

## ✅ 各模式用途一览：

| 模式                      | 名称     | 中文说明                 | 常用场景               |
| ----------------------- | ------ | -------------------- | ------------------ |
| `GPIO_Mode_AIN`         | 模拟输入   | 模拟输入模式（Analog Input） | 连接 ADC（模拟传感器、电压检测） |
| `GPIO_Mode_IN_FLOATING` | 浮空输入   | 无上拉/下拉的输入            | 接收外部信号（按钮、信号线）     |
| `GPIO_Mode_IPD`         | 下拉输入   | 内部下拉电阻               | 防止输入悬空（默认是 0）      |
| `GPIO_Mode_IPU`         | 上拉输入   | 内部上拉电阻               | 防止输入悬空（默认是 1）      |
| `GPIO_Mode_Out_OD`      | 开漏输出   | 可以拉低，不能输出高  | I²C 通信、外部电平交互      |
| `GPIO_Mode_Out_PP`      | 推挽输出   | 正常输出高/低电平 | 控制 LED、继电器、数码管等    |
| `GPIO_Mode_AF_OD`       | 复用开漏输出 | 外设用、开漏模式             | I²C（复用为外设功能）       |
| `GPIO_Mode_AF_PP`       | 复用推挽输出 | 外设用、推挽模式             | USART、SPI 等通信引脚    |

---

## 🧠 实际使用建议：

### 1. 控制 LED

```c
GPIO_Mode_Out_PP   // 推挽输出
```

### 2. 按键输入（无外接上下拉）

```c
GPIO_Mode_IPU      // 上拉输入（常用）
```

### 3. 模拟传感器输入（ADC）

```c
GPIO_Mode_AIN
```

### 4. I2C 通信

```c
GPIO_Mode_AF_OD    // 复用开漏输出
```

### 5. USART 通信

```c
GPIO_Mode_AF_PP    // 复用推挽输出  
```

---

## ⚠️ 特别说明：

* 如果你使用某些外设（USART、SPI、I2C），必须用 **AF（复用）模式**，否则它不能正常使用。
* 开漏模式通常需要外部上拉电阻，否则输出可能不稳定。
* 推挽输出是最常用、最简单的输出模式。

---


你贴出的这些是 STM32 **每个引脚的编号宏定义**，用于告诉 STM32 你想操作哪一个 GPIO 引脚（Pin）。
这些**和模式（`GPIO_Mode_XXX`）是分开的**：

* `GPIO_Pin_X` 是说 **哪个引脚**。
* `GPIO_Mode_XXX` 是说 **这个引脚干什么用、怎么用**。

---

# GPIO_Pin

## ✅ 举例说明：

### 例1：控制 LED（连接在 `PC13`）

```c
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_13;         // 选 PC13 引脚
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;   // 推挽输出模式
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
GPIO_Init(GPIOC, &GPIO_InitStructure);             // 初始化 PC13
```

### 例2：按键输入（连接在 `PA0`，内部上拉）

```c
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;          // 选 PA0 引脚
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;      // 上拉输入
GPIO_Init(GPIOA, &GPIO_InitStructure);             // 初始化 PA0
```

---

## 🧠 总结关系：

| 你想干什么    | 选择哪个 `GPIO_Pin_X`       | 配套的 `GPIO_Mode_XXX` 模式          |
| -------- | ----------------------- | ------------------------------- |
| 控制 LED   | `GPIO_Pin_13` 等         | `GPIO_Mode_Out_PP`              |
| 按键输入     | `GPIO_Pin_0`、`1`...     | `GPIO_Mode_IPU` 或 `IN_FLOATING` |
| 读传感器     | `GPIO_Pin_1` 等          | `GPIO_Mode_AIN`                 |
| I2C 通信   | `GPIO_Pin_6/7`（PB6/7）   | `GPIO_Mode_AF_OD`               |
| USART 通信 | `GPIO_Pin_9/10`（PA9/10） | `GPIO_Mode_AF_PP`               |

---

## ✅ 支持多个引脚一起初始化

你可以用位或 `|` 同时初始化多个引脚，例如：

```c
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0 | GPIO_Pin_1 | GPIO_Pin_2;
```

