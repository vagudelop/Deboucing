## LED ON_OFF Con maquinas de estado-tablas

En este documento se describira el funcionamiento de una maquina de estado y su implementacion.

El ejercicio que se desarrollara es el siguiente

![Led_ON_OFF](https://github.com/vagudelop/Sstemas-en-tiempo-real/blob/master/Imagenes/Led_ON_OFF.JPG)

Este consta de 4 estados y 2 transciones y se va a realizar utilizando tablas para el desarrollo de la maquina de estado
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

//Defino la cantidad maxima de estados y de transiciones para las dimensiones de la tabla de estados
#define MAX_ESTADOS 4
#define MAX_ENTRADAS_TRANSICIONES 2

enum states {LED_ON_DOWN, LED_ON_UP, LED_OFF_DOWN, LED_OFF_UP} current_state;
//estas variables se toman como {0,1,2,3}
enum inputs {PB_DOWN,PB_UP} current_input;
//estas variables se toman como {0,1}
```

Y tambien definimos variables, funciones globales y ademas definimos la tabla de estados
```
//creo la tabla de estados
typedef void (*transition)();


/* Exported functions prototypes ---------------------------------------------*/
void Error_Handler(void);

/* USER CODE BEGIN EFP */
//se define la variable get input que nos indica si el pulsador fue oprimido o no
int8_t get_input(void);
//se definen las funciones que voy a usar para cambiar de estado dependiendo de la transicion
void PB_ABAJO (void);//Funcion para cuando el pulsador esta presionado
void PB_ARRIBA (void);//Funcion para cuando el pulsador no esta presionado
/* USER CODE END EFP */
```
Ahora en el main.c ponemos la estructura de la maquina como tal

```
int main(void)
{
  
  /* USER CODE BEGIN 2 */
  //Se crea la tabla para la maquina de estados
  transition state_table[MAX_ESTADOS][MAX_ENTRADAS_TRANSICIONES] = {
  //	PB_DOWN	PB_UP
  		{PB_ABAJO,PB_ARRIBA},	// LED_ON_UP
  		{PB_ABAJO,PB_ARRIBA},	// LED_ON_DOWN
  		{PB_ABAJO,PB_ARRIBA},	// LED_OFF_UP
  		{PB_ABAJO,PB_ARRIBA}};// LED_OFF_DOWN


  current_state = LED_OFF_UP; // se define el primer estado
  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */
	  if( current_input>=0 && current_input <= MAX_ESTADOS &&
	  	  	  current_state >= 0 && current_state <= MAX_ENTRADAS_TRANSICIONES){
	  		  state_table[current_state][current_input]();
	  	  }
//recordemos que curren_input y current_states toman valores de, {0,1} y {0,1,2,3} respectivamente
//siendo para current_input 0->PB_DOWN , 1->PB_UP
	   //para current_state 0->LED_ON_DOWN, 1->LED_ON_UP, 2->LED_OFF_DOWN, 3->LED_OFF_UP
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


//Funcion para cuando el pulsador esta presionado
void PB_ABAJO(){

	if (current_input == PB_DOWN){
		if (current_state == LED_ON_UP){
			current_state = LED_ON_DOWN;
			}else if (current_state == LED_OFF_UP){
			current_state= LED_OFF_DOWN;
		    }
	}
}


//Funcion para cuando el pulsador no esta presionado
void PB_ARRIBA(){

	if (current_input == PB_UP){
		if(current_state == LED_ON_DOWN){
			current_state = LED_OFF_UP;
			HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, RESET);
		}else if (current_state == LED_OFF_DOWN){
			current_state = LED_ON_UP;
			HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, SET);
		}
	}

}

/* USER CODE END 4 */
```
