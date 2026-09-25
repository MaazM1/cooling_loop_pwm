# STM32 FreeRTOS & Hardware PWM Guide

Documentation for setting up STM32 IDE, installing FreeRTOS, and configuring hardware PWM. Includes sample programs and basics of FreeRTOS

---

## 1. Installing FreeRTOS on STM32

To smoothly upload the software to the STM3, we use the official **STM32CubeIDE**. Setting up cross-compiler with VScode is a bit tricky but it's doable. 

### Download and Install STMCubeIDE
1. Navigate to official download page [ST STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html).
2. Scroll down, select your OS and install
3. Just follow the installation steps and launch it

---
## 2. Project Initialization in STM32CubeMX

STMCubeMX handles the hardware peripheral initialization code generation. This guide uses the **NUCLEO-F446RE** for reference, but it's the same concept for any other STM board. Any code, setting or hardware pin specific to the F446RE will be mentioned and explained how to navigate it to your specific board. 

### 2.1 Selecting your board
1. Open STM32CubeMX (Not the IDE)
2. Select the *Board Selector Tab* 
3. In the *Commercial Part Number* Box Search for and select **NUCLEO-F446RE** or your specific board
4. Select start project. When prompted with the board project options menu, select the blue *unselect all* box, and click ok. It should load a pinout view with your selected board, and now we can configure the timer peripherals and installing RTOS

### 2.2  Configuring Peripherals and installing FreeRTOS

1. Click on the categories panel if it's not selected already and click on timers to expand the list. 
2. Click on TIM3, a new *TIM3 Mode and Configuration* menu will play. Set clock source to Internal clock, and set Channel 1 to PWM Generation CH1. Setting the clock source to internal clock makes it so it counts ticks from the microcontroller itself, which we will use in PWM.                




---

## 2. Hardware Configuration (Nucleo-F446RE)

| Parameter | Configuration | Details |
| :--- | :--- | :--- |
| **Timer** | `TIM3` | General-purpose 16-bit timer |
| **Channel** | `Channel 1` | Configured as `PWM Generation CH1` |
| **GPIO Pin** | `PA6` | Morpho / Arduino header pin `D12` (Alternate Function AF2) |
| **Prescaler ($PSC$)** | `83` | Scales timer clock down (e.g., $84\text{ MHz} \rightarrow 1\text{ MHz}$ tick rate) |
| **Counter Period ($ARR$)** | `1000` | $1000$ counts per cycle $\rightarrow 1\text{ kHz}$ base frequency |
| **Counter Mode** | `Up` | Counts $0 \rightarrow ARR$ |

---

## 3. Peripheral Initialization

Peripherals must be started **after** the hardware init functions (`MX_TIM3_Init()`) and **before** starting the FreeRTOS scheduler (`osKernelStart()`).

In `Core/Src/main.c`:

```c
/* USER CODE BEGIN 2 */
// Enable Timer 3 Channel 1 PWM output and start hardware counter
HAL_TIM_PWM_Start(&htim3, TIM_CHANNEL_1);
/* USER CODE END 2 */