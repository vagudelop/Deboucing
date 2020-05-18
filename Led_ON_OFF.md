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

