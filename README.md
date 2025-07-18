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

✅ Sure! Here's a **Step-by-Step Guide to Configure Timer on STM32F407** (with output demonstration).

---

## ✅ **Objective**

We will:

1. Configure **TIM2** to generate a **1-second time base interrupt**.
2. Toggle an **LED on Discovery board (PD12)** on each interrupt (blink every 1s).

---

## ✅ **Step 1: CubeMX Configuration**

1. **Open CubeMX** (via STM32CubeIDE).
2. Select your **STM32F407VG** MCU or Discovery board.

### 🟩 **GPIO**

* Enable **GPIOD PIN 12** → Mode: **Output Push-Pull**.

### 🟦 **Timer (TIM2)**

1. Enable **TIM2 → Mode: Internal Clock**.
2. Click **Parameter Settings**:

   * **Prescaler:** `8399`
   * **Counter Period (ARR):** `9999`
   * **Clock Division:** `No Division`

> This config results in:

```
84 MHz / (8399 + 1) = 10 kHz
10 kHz / (9999 + 1) = 1 Hz (1s interval)
```

3. Enable **TIM2 global interrupt** under **NVIC Settings**.

### 🟧 **Clock Config**

* Ensure the clock is set to **168 MHz (SYSCLK)**.

### ✅ Click **Project → Generate Code**.

---

## ✅ **Step 2: Generated Functions**

CubeMX will generate **MX\_TIM2\_Init()**, which sets the timer.

---

## ✅ **Step 3: Modify `main.c`**

### 🔹 Add to **USER CODE 2**:

```c
HAL_TIM_Base_Start_IT(&htim2);
```

### 🔹 Implement **Callback for Timer Interrupt**

In **USER CODE 4**:

```c
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if(htim->Instance == TIM2)
    {
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);  // Toggle LED
    }
}
```

### ✅ Your `main.c` Example:

```c
#include "main.h"

TIM_HandleTypeDef htim2;

int main(void)
{
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  MX_TIM2_Init();

  HAL_TIM_Base_Start_IT(&htim2);  // Start timer with interrupt

  while (1)
  {
    // Main loop does nothing, LED toggling is in interrupt
  }
}

void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if(htim->Instance == TIM2)
    {
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);
    }
}
```

---

## ✅ **Step 4: Build and Flash**

* Build the project.
* Flash to your STM32F407 Discovery board.
* You should see **LD4 (Green LED on PD12)** blink **once every 1 second**.

---

## ✅ **Bonus: Adjust Timer Frequency**

* Change **Prescaler / Period** to adjust blink rate.
* For faster blinking (e.g. 0.5s), reduce period.

---

## ✅ **Summary of Steps**

| Step | Action                                    |
| ---- | ----------------------------------------- |
| 1    | Configure GPIO & Timer via CubeMX         |
| 2    | Enable NVIC interrupt for TIM2            |
| 3    | Start Timer Interrupt in `main.c`         |
| 4    | Implement `HAL_TIM_PeriodElapsedCallback` |
| 5    | Flash & Observe LED blinking              |

---

If you want:

* ✅ **PWM generation on Timer**
* ✅ **Input Capture to measure frequency**
* ✅ **Timer + UART print on interval**

👉 Let me know!
✅ Yes, your code is **correct and complete** for generating a **1-second periodic interrupt using TIM2** that toggles **PD12 LED** on the STM32F407 Discovery board.

### 🔎 **Quick Verification**

* **TIM2 Init:**

```c
htim2.Init.Prescaler = 8399;
htim2.Init.Period = 9999;
```

✔ This gives 1Hz (1s) interrupt.

* **Timer Started in Interrupt Mode:**

```c
HAL_TIM_Base_Start_IT(&htim2);
```

✔ Correct.

* **LED GPIO PD12:**

```c
GPIO_PIN_12 initialized as OUTPUT_PP.
```

* **Callback:**

```c
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if(htim->Instance == TIM2)
    {
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);
    }
}
```

✔ Correctly toggles the LED.

---

### ✅ **Bonus Suggestion**

If you want to confirm via **UART** too, you can add:

```c
#include <stdio.h>
#include <string.h>

extern UART_HandleTypeDef huart2;  // Ensure UART2 is initialized in CubeMX.

void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if(htim->Instance == TIM2)
    {
        HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_12);

        char msg[] = "Timer Interrupt Triggered\r\n";
        HAL_UART_Transmit(&huart2, (uint8_t*)msg, strlen(msg), HAL_MAX_DELAY);
    }
}
```

> This will print on UART whenever the timer interrupt fires.

---

✅ **Result Expected:** LED on **PD12** blinks every **1 second**.

If you want:

* Timer frequency variations
* PWM generation
* Input capture

👉 Tell me and I’ll share complete examples.
✅ Here's a **step-by-step guide to generate PWM using Timer on STM32 (STM32F407)** to **dim an LED**.

---

## ✅ **Step 1: CubeMX / STM32CubeIDE Configuration**

1. **Enable Timer (e.g., TIM3) in PWM Generation Mode**

   * In **Timers**, select **TIM3 → Channel 1 → PWM Generation CH1**.
   * Select an output pin, e.g., **PA6 (TIM3\_CH1)**.

2. **Configure Timer Settings**

   * Prescaler: e.g., **83** → gives 1 MHz timer clock (from 84 MHz APB1 Timer Clock).
   * Period: e.g., **999** → gives a PWM frequency of 1 kHz.

3. **Configure GPIO**

   * The chosen pin (e.g., **PA6**) is automatically set as **AF mode**.

4. **Initialize the Timer in PWM mode**

---

## ✅ **Step 2: Generated Code**

### In `MX_TIM3_Init()`

```c
static void MX_TIM3_Init(void)
{
    TIM_OC_InitTypeDef sConfigOC = {0};

    htim3.Instance = TIM3;
    htim3.Init.Prescaler = 83;    // 84MHz / (83 + 1) = 1MHz
    htim3.Init.CounterMode = TIM_COUNTERMODE_UP;
    htim3.Init.Period = 999;      // 1MHz / (999 + 1) = 1kHz PWM frequency
    htim3.Init.ClockDivision = TIM_CLOCKDIVISION_DIV1;
    htim3.Init.AutoReloadPreload = TIM_AUTORELOAD_PRELOAD_DISABLE;
    HAL_TIM_PWM_Init(&htim3);

    sConfigOC.OCMode = TIM_OCMODE_PWM1;
    sConfigOC.Pulse = 499;        // 50% Duty cycle initially
    sConfigOC.OCPolarity = TIM_OCPOLARITY_HIGH;
    sConfigOC.OCFastMode = TIM_OCFAST_DISABLE;
    HAL_TIM_PWM_ConfigChannel(&htim3, &sConfigOC, TIM_CHANNEL_1);
}
```

---

## ✅ **Step 3: Start PWM in `main.c`**

```c
MX_TIM3_Init();    // Initialize TIM3 PWM
HAL_TIM_PWM_Start(&htim3, TIM_CHANNEL_1);   // Start PWM on Channel 1
```

---

## ✅ **Step 4: Change Brightness Dynamically**

You can adjust the **PWM duty cycle** dynamically like this:

```c
__HAL_TIM_SET_COMPARE(&htim3, TIM_CHANNEL_1, duty_cycle);
```

Where `duty_cycle` varies from **0 to 999** (for 0% to 100%).

Example:

```c
for(uint16_t duty = 0; duty <= 999; duty+=10)
{
    __HAL_TIM_SET_COMPARE(&htim3, TIM_CHANNEL_1, duty);
    HAL_Delay(10);
}
```

This will **gradually increase the brightness of the LED**.

---

## ✅ **Summary of Configuration**

| Parameter     | Value                          |
| ------------- | ------------------------------ |
| Timer Clock   | 84 MHz                         |
| Prescaler     | 83                             |
| Period        | 999                            |
| PWM Frequency | 1 kHz                          |
| Duty Cycle    | 0-100% via compare value 0-999 |

---

## ✅ **Bonus Tip**

For smooth fading, implement a **duty cycle ramp-up/down** in a loop with a small delay.

---

If you want I can generate the **full code template** or help with **multiple PWM channels / higher frequency / specific LED pins**.
