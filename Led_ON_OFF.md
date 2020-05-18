## LED ON_OFF Con maquinas de estado

En este documento se describira el funcionamiento de una maquina de estado y su implementacion.

El ejercicio que se desarrollara es el siguiente

![Led_ON_OFF](https://github.com/vagudelop/Sstemas-en-tiempo-real/blob/master/Imagenes/Led_ON_OFF.JPG)

Este consta de 4 estados y 2 transciones

Para su desarrollo primero definimos los estados y las transiciones, esto lo hacemos en el main.h del proyecto
```
/* Define to prevent recursive inclusion -------------------------------------*/
#ifndef __MAIN_H
#define __MAIN_H

#ifdef __cplusplus
extern "C" {
#endif

/* Includes ------------------------------------------------------------------*/
#include "stm32l4xx_hal.h"

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */

/* USER CODE END Includes */

/* Exported types ------------------------------------------------------------*/
/* USER CODE BEGIN ET */

enum states {LED_ON_DOWN, LED_ON_UP, LED_OFF_DOWN, LED_OFF_UP} current_state;
//estas variables se toman como {0,1,2,3}
enum inputs {PB_DOWN,PB_UP} current_input;
//estas variables se toman como {0,1}

/* USER CODE END ET */
```
Y tambien definimos variables y funciones globales 
```
/* USER CODE BEGIN EFP */
//se define la variable get input que nos indica si el pulsador fue oprimido o no
int8_t get_input(void);
//se define la funcion set_output que me dice si el led se enciende o no dependiendo del estado del pulsador
void set_output(int8_t current_state);

/* USER CODE END EFP */
```
Ahora en el main.c ponemos la estructura de la maquina como tal

```
int main(void)
{

  /* USER CODE BEGIN 2 */
  uint8_t cuenta = 0;//uint8_t ->sin signo de 8 bits, cuenta la cantidad de pulsaciones que hay
  current_state = LED_OFF_UP; // se define el primer estado
  /* USER CODE END 2 */
  

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */
	  current_input = get_input();//se pregunta por el estado del pulsador
	  	  	switch(current_state)//El switch nos sirve para ejecutar diferentes casos en función del resultado
			//de una expresión o variable en este caso el current_state - 4 diferentes estados-
	  	        {
	  	  	case LED_ON_UP:
	  	  		switch(current_input)//aca se pregunta por el estado del pulsador -2 diferentes estados-
	  	  		{
	  	  		case PB_DOWN:
	  	  	 	  current_state = LED_ON_DOWN;
	  	  		  cuenta++;
	  	  		  break;
	  	  		default:
	  	  		  current_state = LED_ON_UP;
	  	  		}
	  	  		break;
	  	       case LED_ON_DOWN:
	  	  		switch(current_input)//aca se pregunta por el estado del pulsador -2 diferentes estados-
	  	  		{
	  	  		case PB_DOWN:
	  	  	 	  current_state = LED_ON_DOWN;
	  	  		  break;
	  	  		default:
	  	  		  current_state = LED_OFF_UP;
	  	  		}
	  	  		break;
	  	  	case LED_OFF_UP:
	  	  		switch(current_input)//aca se pregunta por el estado del pulsador -2 diferentes estados-
	  	  		{
	  	  		case PB_DOWN:
	  	  		  current_state = LED_OFF_DOWN;
	  	  		  cuenta++;
	  	  		  break;
	  	  		default:
	  	  		  current_state = LED_OFF_UP;
	  	  		}
	  	  		break;
	  	  	case LED_OFF_DOWN:
	  	  		switch(current_input)//aca se pregunta por el estado del pulsador -2 diferentes estados-
	  	  		{
	  	  		case PB_DOWN:
	  	  		  current_state = LED_OFF_DOWN;
	  	  		  break;
	  	  		default:
	  	  		  current_state = LED_ON_UP;
	  	  		}
	  	  		break;
	  	  	  }
	  	  	set_output(current_state);
    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}

```
Por ultimo se configuran las funciones y/o variables
```

/* USER CODE BEGIN 4 */
int8_t get_input(void) //estado del pulsador
{
	if (!HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin))
		return PB_DOWN;
	else
		return PB_UP;
}

void set_output(int8_t current_state) //prende o no el led dependiendo del estado en que este
{
	switch(current_state)
	{
	case LED_ON_UP:
	case LED_ON_DOWN:
		HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
		//HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_SET);
		break;
	case LED_OFF_UP:
	case LED_OFF_DOWN:
		//HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
		HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_RESET);
		break;
	}

}

/* USER CODE END 4 */
```
