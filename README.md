# STM
### ✅ **STM32 Timers (Basic to Advanced) – Step by Step Guide for STM32F407**

STM32F407 has multiple **timers (TIM1-TIM14)**. Here's a step-by-step guide to use **Timers with STM32CubeIDE**.

---

## ✅ **Step 1: Timer Types in STM32F407**

| Timer      | Type             | Features                                    |
| ---------- | ---------------- | ------------------------------------------- |
| TIM1, TIM8 | Advanced-control | PWM, complementary outputs (motor control)  |
| TIM2-TIM5  | General-purpose  | PWM, Input Capture, Output Compare, Encoder |
| TIM9-TIM14 | Basic timers     | Simple time base, no PWM                    |

---

## ✅ **Step 2: Timer Basic Time Delay Example**

### ➡ **Goal**: Generate 1 second periodic interrupt using **TIM2**.

---

### ✅ **Step 3: CubeMX Timer Configuration**

1. **Enable TIM2** in CubeMX.
2. **Mode:** Select **Internal Clock**.
3. **Parameter Settings:**

   * **Prescaler**: Divide the clock to a slower speed.
   * **Counter Period**: Defines the timer's period.

For example:

* APB1 Timer Clock = 84 MHz
* Prescaler = 8399 → 84MHz / (8399 +1) = 10 kHz
* Period = 9999 → 10kHz / 10000 = 1 Hz → 1 second.

4. Enable **Update Event Interrupt (TIM2 global interrupt)**.

---

### ✅ **Step 4: Generate Code**

* CubeMX will auto-generate **MX\_TIM2\_Init()**.

---

### ✅ **Step 5: Write Code to Start Timer in Interrupt Mode**

In `main.c`:

```c
HAL_TIM_Base_Start_IT(&htim2);
```

In `main.c` under **User Code 4**:

```c
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if(htim->Instance == TIM2)
    {
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12); // Toggle LED on PD12 every 1s
    }
}
```

---

### ✅ **Step 6: Enable Timer Interrupts**

Make sure NVIC for TIM2 is enabled automatically by CubeMX:

```c
HAL_NVIC_SetPriority(TIM2_IRQn, 0, 0);
HAL_NVIC_EnableIRQ(TIM2_IRQn);
```

---

### ✅ **Step 7: Output -**

LED toggles every 1 second via Timer interrupt.

---

## ✅ **Bonus: PWM with Timer**

1. Enable Timer (e.g. TIM3).
2. In **Configuration**, enable **PWM Generation CH1**.
3. Set frequency via Prescaler & Period.
4. Generate code.
5. Start PWM:

```c
HAL_TIM_PWM_Start(&htim3, TIM_CHANNEL_1);
```

---

## ✅ **Summary of Timer Use Cases**

| Use Case       | Function                  |
| -------------- | ------------------------- |
| Time delay     | `HAL_TIM_Base_Start_IT()` |
| PWM            | `HAL_TIM_PWM_Start()`     |
| Input Capture  | `HAL_TIM_IC_Start()`      |
| Encoder        | `HAL_TIM_Encoder_Start()` |
| Output Compare | `HAL_TIM_OC_Start()`      |

---

If you want:

* ✅ **PWM LED Brightness Control Example**
* ✅ **Input Capture for Frequency Measurement**
* ✅ **Timer with DMA**
* ✅ **Encoder Interface with Timer**

👉 **Let me know which one you need!**
<img width="1485" height="635" alt="image" src="https://github.com/user-attachments/assets/273d7ac1-7dc1-4776-a3e8-4ed041689a37" />

✅ Now your **Clock Configuration is set to Maximum Performance on STM32F407**.

### 🔍 **Clock Settings Summary:**

| Parameter                         | Value                           |
| --------------------------------- | ------------------------------- |
| **PLL Source**                    | HSI (16 MHz)                    |
| **PLL M**                         | 8                               |
| **PLL N**                         | 168                             |
| **PLL P**                         | 2                               |
| **SYSCLK (System Clock)**         | **168 MHz (Max for STM32F407)** |
| **AHB Prescaler**                 | 1                               |
| **HCLK (Core Clock)**             | 168 MHz                         |
| **APB1 Prescaler**                | /4                              |
| **APB1 Peripheral Clock (PCLK1)** | 42 MHz                          |
| **APB1 Timer Clock**              | **84 MHz**                      |
| **APB2 Prescaler**                | /2                              |
| **APB2 Peripheral Clock (PCLK2)** | 84 MHz                          |
| **APB2 Timer Clock**              | **168 MHz**                     |

---

### ✅ **Timers Impact**

| Timer Group     | Timer Names            | Timer Clock |
| --------------- | ---------------------- | ----------- |
| **APB1 Timers** | TIM2-TIM5, TIM12-TIM14 | **84 MHz**  |
| **APB2 Timers** | TIM1, TIM8, TIM9-TIM11 | **168 MHz** |

Thus, your timers can now count up to these input clocks. You can precisely configure:

```
Timer Frequency = Timer Clock / (Prescaler + 1) / (Period + 1)
```

---

### ✅ **Example: For TIM2 (APB1 Timer, 84 MHz)**

If you want 1 Hz:

```c
Prescaler = 8399;
Period = 9999;
// Timer frequency = 84 MHz / (8399+1) / (9999+1) = 1 Hz
```

### ✅ **Example: For TIM1 (APB2 Timer, 168 MHz)**

If you want 1 kHz:

```c
Prescaler = 167;
Period = 999;
// Timer frequency = 168 MHz / (167+1) / (999+1) = 1 kHz
```

---

### ✅ **Summary**

You have now:

* Maximum CPU performance (168 MHz).
* APB1 Timer clock = 84 MHz
* APB2 Timer clock = 168 MHz

---

If you want:

* **Complete timer config code**
* **PWM signal generation**
* **Input capture to measure frequency**

👉 Let me know which one you want!

