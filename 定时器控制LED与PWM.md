> #         **重庆交通大学信息科学与工程学院**
>
> #             **《嵌入式系统开发》课程**
>
> #                      作业报告（第7周）

**班 级： <span class="underline"> 通信工程2001</span>**

**姓名-学号 ： <span class="underline"> 阎桂董-632007030622 </span>**

**实验项目名称： <span class="underline"> 作业题目 </span>**

**实验项目性质： <span class="underline"> 设计性 </span>**

**实验所属课程： <span class="underline">《嵌入式系统开发》 </span>**

**实验室(中心)： <span class="underline"> 南岸校区语音大楼 </span>**

**指 导 教 师 ： <span class="underline">娄路 </span>**

**完成时间： <span class="underline"> 2022</span> 年 <span class="underline"> 10</span> 月 <span class="underline"> 30</span> 日**

------

<div STYLE="page-break-after: always;"></div>

<div STYLE="page-break-after: always;"></div>

**一、实验内容和任务**

STM32定时器控制LED灯闪烁及PWM练习

**二、实验要求**

1\. 分组要求：每个学生独立完成，即1人1组。

2\. 程序及报告文档要求：具有较好的可读性，如叙述准确、标注明确、截图清晰等。

3.项目代码上传github，同时把项目完整打包为zip文件，与实验报告（Markdown源码及PDF文件）、作业博客地址一起提交到学习通。

三. **实验过程介绍 **

<div STYLE="page-break-after: always;"></div>1、请设置一个5秒的定时器，每隔5秒从串口发送“hello windows！”；同时设置一个2秒的定时器，让LED等周期性地闪烁。

2、使用TIM3和TIM4，分别输出一个PWM波形，PWM的占空比随时间变化，去驱动你外接的一个LED以及最小开发板上已焊接的LED（固定接在 PC13 GPIO端口），实现2个 LED呼吸灯的效果。

# 一、STM32定时器控制LED灯闪烁

## 1、相关配置

### 1）配置RCC和SYS

![img](file:///C:\Users\ASUS\Documents\Tencent Files\1943459926\Image\C2C\~V6UPWDWIRTHAWO_(KJ`)Q7.png)

![image-20221030144217801](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030144217801.png)

### 2）配置IO

配置PC15，并命名为D1。

![image-20221030144350446](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030144350446.png)

### 3）配置定时器

选定时器2，配置Clock Source为Internal Clock，分频系数为71技术周期5000，然后设置为能够自动重载

![image-20221030151243536](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030151243536.png)

![image-20221030161128100](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030161128100.png)

### 4）配置中断

![image-20221030161302446](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030161302446.png)

![image-20221030161344089](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030161344089.png)

### 5）时钟配置

![image-20221030152259073](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030152259073.png)

### 6）配置USART

![image-20221030161513854](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030161513854.png)

## 2、生成文件

![image-20221030152526270](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030152526270.png)

![image-20221030152618467](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030152618467.png)

## 3、代码

 定时器

```
HAL_TIM_Base_Start_IT(&htim2);
HAL_TIM_Base_Start_IT(&htim3);

```

串口

```
uint8_t hello[20]="hello windows！\r\n";
HAL_UART_Transmit(&huart1,hello,20,100000);
```

控制灯闪烁和串口

```
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
	static uint32_t time_cnt =0;
	static uint32_t time_cntck =0;
	if(htim->Instance == TIM2)
	{
		if(++time_cnt >= 400)
		{
			time_cnt =0;
			HAL_GPIO_TogglePin(GPIOA,GPIO_PIN_1);
		}
	}
	if(htim->Instance == TIM3)
	{
		if(++time_cntck >= 1000)
		{
			time_cntck =0;
    HAL_UART_Transmit(&huart1,hello,20,100000);
		}
			
	}
}
```

总代码

```
/* USER CODE BEGIN Header */
/**
  ******************************************************************************
  * @file           : main.c
  * @brief          : Main program body
  ******************************************************************************
  * @attention
  *
  * Copyright (c) 2022 STMicroelectronics.
  * All rights reserved.
  *
  * This software is licensed under terms that can be found in the LICENSE file
  * in the root directory of this software component.
  * If no LICENSE file comes with this software, it is provided AS-IS.
  *
  ******************************************************************************
  */
/* USER CODE END Header */
/* Includes ------------------------------------------------------------------*/
#include "main.h"
#include "tim.h"
#include "usart.h"
#include "gpio.h"

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */

/* USER CODE END Includes */

/* Private typedef -----------------------------------------------------------*/
/* USER CODE BEGIN PTD */

/* USER CODE END PTD */

/* Private define ------------------------------------------------------------*/
/* USER CODE BEGIN PD */
/* USER CODE END PD */

/* Private macro -------------------------------------------------------------*/
/* USER CODE BEGIN PM */

/* USER CODE END PM */

/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */

/* USER CODE END PV */

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
static void MX_NVIC_Init(void);
/* USER CODE BEGIN PFP */

/* USER CODE END PFP */

/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */

/* USER CODE END 0 */

/**
  * @brief  The application entry point.
  * @retval int
  */
  uint8_t hello[20]="hello windows!\r\n";
int main(void)
{
  /* USER CODE BEGIN 1 */

  /* USER CODE END 1 */

  /* MCU Configuration--------------------------------------------------------*/

  /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
  HAL_Init();

  /* USER CODE BEGIN Init */

  /* USER CODE END Init */

  /* Configure the system clock */
  SystemClock_Config();

  /* USER CODE BEGIN SysInit */

  /* USER CODE END SysInit */

  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  MX_TIM2_Init();
  MX_TIM3_Init();
  MX_USART1_UART_Init();

  /* Initialize interrupts */
  MX_NVIC_Init();
	HAL_TIM_Base_Start_IT(&htim2);
	HAL_TIM_Base_Start_IT(&htim3);

  /* USER CODE BEGIN 2 */

  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}

/**
  * @brief System Clock Configuration
  * @retval None
  */
void SystemClock_Config(void)
{
  RCC_OscInitTypeDef RCC_OscInitStruct = {0};
  RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

  /** Initializes the RCC Oscillators according to the specified parameters
  * in the RCC_OscInitTypeDef structure.
  */
  RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSE;
  RCC_OscInitStruct.HSEState = RCC_HSE_ON;
  RCC_OscInitStruct.HSEPredivValue = RCC_HSE_PREDIV_DIV1;
  RCC_OscInitStruct.HSIState = RCC_HSI_ON;
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
  RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSE;
  RCC_OscInitStruct.PLL.PLLMUL = RCC_PLL_MUL9;
  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
  {
    Error_Handler();
  }

  /** Initializes the CPU, AHB and APB buses clocks
  */
  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
                              |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV2;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_2) != HAL_OK)
  {
    Error_Handler();
  }
}

/**
  * @brief NVIC Configuration.
  * @retval None
  */
static void MX_NVIC_Init(void)
{
  /* TIM2_IRQn interrupt configuration */
  HAL_NVIC_SetPriority(TIM2_IRQn, 0, 0);
  HAL_NVIC_EnableIRQ(TIM2_IRQn);
  /* TIM3_IRQn interrupt configuration */
  HAL_NVIC_SetPriority(TIM3_IRQn, 0, 0);
  HAL_NVIC_EnableIRQ(TIM3_IRQn);
}

/* USER CODE BEGIN 4 */
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
	static uint32_t time_cnt =0;
	static uint32_t time_cntck =0;
	if(htim->Instance == TIM2)
	{
		if(++time_cnt >= 400)
		{
			time_cnt =0;
			HAL_GPIO_TogglePin(D1_GPIO_Port,D1_Pin);
		}
	}
	if(htim->Instance == TIM3)
	{
		if(++time_cntck >= 1000)
		{
			time_cntck =0;
    HAL_UART_Transmit(&huart1,hello,20,100000);
		}
			
	}
}

/* USER CODE END 4 */
/**
  * @brief  This function is executed in case of error occurrence.
  * @retval None
  */
void Error_Handler(void)
{
  /* USER CODE BEGIN Error_Handler_Debug */
  /* User can add his own implementation to report the HAL error return state */
  __disable_irq();
  while (1)
  {
  }
  /* USER CODE END Error_Handler_Debug */
}

#ifdef  USE_FULL_ASSERT
/**
  * @brief  Reports the name of the source file and the source line number
  *         where the assert_param error has occurred.
  * @param  file: pointer to the source file name
  * @param  line: assert_param error line source number
  * @retval None
  */
void assert_failed(uint8_t *file, uint32_t line)
{
  /* USER CODE BEGIN 6 */
  /* User can add his own implementation to report the file name and line number,
     ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */
  /* USER CODE END 6 */
}
#endif /* USE_FULL_ASSERT */


```

## 4、编译

![image-20221030162801226](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030162801226.png)

## 5、烧录

![image-20221030163722538](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030163722538.png)

## 6、效果

![Screenshot_2022_1030_163502](D:\qq\MobileFile\Screenshot_2022_1030_163502.gif)

# 二、呼吸灯

## 1、HAL库建立

### 1）配置

1）SYS和RCC配置和上面定时器控制LED一样，这里就不多说

2）定时器配置

![image-20221030170907999](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030170907999.png)

3）配置中断

![image-20221030171117644](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030171117644.png)

![image-20221030171128827](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030171128827.png)

4）配置USART

![image-20221030171218817](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030171218817.png)

5）配置时钟

![image-20221030171301371](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030171301371.png)

2、项目配置

![image-20221030171448394](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030171448394.png)

![image-20221030171506136](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030171506136.png)

## 2、代码

初始值设为10

```
uint16_t duty_num = 10;
```

开始TIM2的通道2，输出PWM

```
HAL_TIM_PWM_Start(&htim2,TIM_CHANNEL_2);
```

每隔50毫秒，占空比加10，如果超过500（也就是PWM周期），自动变成0

```
HAL_Delay(50);
		duty_num = duty_num + 10;
		if(duty_num > 500)
		{
			duty_num = 0;
		}
		__HAL_TIM_SetCompare(&htim2,TIM_CHANNEL_2,duty_num);
```

总代码如下

```
/* USER CODE BEGIN Header */
/**
  ******************************************************************************
  * @file           : main.c
  * @brief          : Main program body
  ******************************************************************************
  * @attention
  *
  * Copyright (c) 2022 STMicroelectronics.
  * All rights reserved.
  *
  * This software is licensed under terms that can be found in the LICENSE file
  * in the root directory of this software component.
  * If no LICENSE file comes with this software, it is provided AS-IS.
  *
  ******************************************************************************
  */
/* USER CODE END Header */
/* Includes ------------------------------------------------------------------*/
#include "main.h"
#include "tim.h"
#include "usart.h"
#include "gpio.h"

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */

/* USER CODE END Includes */

/* Private typedef -----------------------------------------------------------*/
/* USER CODE BEGIN PTD */

/* USER CODE END PTD */

/* Private define ------------------------------------------------------------*/
/* USER CODE BEGIN PD */
/* USER CODE END PD */

/* Private macro -------------------------------------------------------------*/
/* USER CODE BEGIN PM */

/* USER CODE END PM */

/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */

/* USER CODE END PV */
uint16_t duty_num = 10;
/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
static void MX_NVIC_Init(void);
/* USER CODE BEGIN PFP */

/* USER CODE END PFP */

/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */

/* USER CODE END 0 */

/**
  * @brief  The application entry point.
  * @retval int
  */
int main(void)
{
  /* USER CODE BEGIN 1 */

  /* USER CODE END 1 */

  /* MCU Configuration--------------------------------------------------------*/

  /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
  HAL_Init();

  /* USER CODE BEGIN Init */

  /* USER CODE END Init */

  /* Configure the system clock */
  SystemClock_Config();

  /* USER CODE BEGIN SysInit */

  /* USER CODE END SysInit */

  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  MX_TIM2_Init();
  MX_USART1_UART_Init();

  /* Initialize interrupts */
  MX_NVIC_Init();
  /* USER CODE BEGIN 2 */
  HAL_TIM_PWM_Start(&htim2,TIM_CHANNEL_2);
  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
		HAL_Delay(50);
		duty_num = duty_num + 10;
		if(duty_num > 500)
		{
			duty_num = 0;
		}
		__HAL_TIM_SetCompare(&htim2,TIM_CHANNEL_2,duty_num);
  }
  /* USER CODE END 3 */
}

/**
  * @brief System Clock Configuration
  * @retval None
  */
void SystemClock_Config(void)
{
  RCC_OscInitTypeDef RCC_OscInitStruct = {0};
  RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

  /** Initializes the RCC Oscillators according to the specified parameters
  * in the RCC_OscInitTypeDef structure.
  */
  RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSE;
  RCC_OscInitStruct.HSEState = RCC_HSE_ON;
  RCC_OscInitStruct.HSEPredivValue = RCC_HSE_PREDIV_DIV1;
  RCC_OscInitStruct.HSIState = RCC_HSI_ON;
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
  RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSE;
  RCC_OscInitStruct.PLL.PLLMUL = RCC_PLL_MUL9;
  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
  {
    Error_Handler();
  }

  /** Initializes the CPU, AHB and APB buses clocks
  */
  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
                              |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV2;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_2) != HAL_OK)
  {
    Error_Handler();
  }
}

/**
  * @brief NVIC Configuration.
  * @retval None
  */
static void MX_NVIC_Init(void)
{
  /* TIM2_IRQn interrupt configuration */
  HAL_NVIC_SetPriority(TIM2_IRQn, 0, 0);
  HAL_NVIC_EnableIRQ(TIM2_IRQn);
}

/* USER CODE BEGIN 4 */

/* USER CODE END 4 */

/**
  * @brief  This function is executed in case of error occurrence.
  * @retval None
  */
void Error_Handler(void)
{
  /* USER CODE BEGIN Error_Handler_Debug */
  /* User can add his own implementation to report the HAL error return state */
  __disable_irq();
  while (1)
  {
  }
  /* USER CODE END Error_Handler_Debug */
}

#ifdef  USE_FULL_ASSERT
/**
  * @brief  Reports the name of the source file and the source line number
  *         where the assert_param error has occurred.
  * @param  file: pointer to the source file name
  * @param  line: assert_param error line source number
  * @retval None
  */
void assert_failed(uint8_t *file, uint32_t line)
{
  /* USER CODE BEGIN 6 */
  /* User can add his own implementation to report the file name and line number,
     ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */
  /* USER CODE END 6 */
}
#endif /* USE_FULL_ASSERT */


```

## 3、编译并烧录

![image-20221030171928626](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030171928626.png)

![image-20221030172248081](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030172248081.png)

## 4、效果图

![Screenshot_2022_1030_172356](D:\qq\MobileFile\Screenshot_2022_1030_172356.gif)

## 5、输出PWM波形

![image-20221030174807315](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221030174807315.png)

# 总结

让我理解了用控制器控制LED灯闪烁，明白了其中原理

通过在STM32上配置PWM，了解了其中配置与认知，以及PWM的出现对这一系列单片机起着很重要的作用
