# Antirrebote
En este documento se realiza un ejercicio el cual consiste en realizar un circuito antrebote por medio de maquinas de estado.
El ejercicio se puede encontrar [aqui](https://www.eeweb.com/profile/tommyg/articles/debouncing-push-buttons-using-a-state-machine-approach).

Vamos a tener 4 estados los cuales son:

•**En espera:** esperando que se active cualquier botón del puerto.

•**Detectado:** se ha activado un botón para que se ingrese este estado y los valores del puerto de los botones se registran con Temp_Press. Cada 10 ms, el puerto del botón se vuelve a comprobar y, si bien sigue siendo el mismo valor, se incrementa un contador. En lenguaje de máquina de estados, la variable se conoce como "una variable de estado extendida".

•**Espera para liberar:** si el contador alcanza un valor mínimo predefinido, 'MIN_BUTTON_COUNT', Temp_press ahora se considera válido y se ingresa el estado WaitForRelease para esperar a que se libere el botón antes de que se actualice la variable, Button_Press, que contiene el valor final del botón.

•**Actualización:** el botón se ha liberado y, por lo tanto, el valor final Button_Press se actualiza con el valor temporal sin rebote 'Temp_Press'.

Y un unico evento o transicion:

•El diagrama de la máquina de estados representa esta vez como la ocurrencia de un evento **TICK ( TICK_E )**.

Se van a tener dos variables, Button_Press y Temp_Press , se definen con este tipo; Button_Press mantiene los valores finales para los botones después de que se hayan eliminado, mientras que Temp_Press mantiene valores intermedios para los botones durante la eliminación de rebotes.

El diagrama de la maquina de estados es:
![diagrama](https://github.com/vagudelop/Deboucing/blob/master/Imagenes/diagrama.JPG)

Una vez claros los estados y los eventos vamos a realizar la definicion de estos estados y eventos en el **main.h**, asi:

```
/* USER CODE BEGIN ET */
//Defino la cantidad maxima de estados y de transiciones para las dimensiones de la tabla de estados
#define MAX_ESTADOS 4
#define MAX_TRANSI 1
//Establezco la definicion de los estados y transiciones
enum estados {en_espera, detectado, espera_p_liberar, actualizacion} estado_actual;
enum transiciones {tick_e} transicion_actual;

typedef void (*transition)();
uint64_t tick_time;
```
En el main.c ponemos:
```
/* USER CODE BEGIN 0 */

uint8_t en_espera_(void);
void detectado_(void);
void espera_p_liberar_(void);
void actualizacion_(void);

//creamos la tabla de estados
transition state_table[MAX_ESTADOS][MAX_TRANSI] = {
	//	tick_e
		{en_espera_},
		{detectado_},
		{espera_p_liberar_},
		{actualizacion_},
};


/* USER CODE END 0 */

int main(void)
{
  
  //defino el temporizador que me cuenta 10ms y el contador que cuenta el numero de pulsaciones
  uint16_t c=0;
  uint16_t t=10;


  //se establece el estado inicial
  estado_actual =  en_espera;
  /* USER CODE END 2 */
  int8_t inicial;
  inicial = HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin);
  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */
	  transicion_actual = en_espera_() ;
	  if( estado_actual>=0 && estado_actual <= MAX_ESTADOS &&
	 	 transicion_actual >= 0 && transicion_actual <= MAX_TRANSI){
	 	 state_table[MAX_ESTADOS][MAX_TRANSI]();
    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}


uint8_t en_espera_ (){
	// es este estado se espera que se oprima el boton
	//por esto se pregunta si se detecto una pulsacion
	//y al detectarse se activa la transicion

	if (!HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin)){
		return tick_e;
	}else{
		estado_actual =  en_espera;
	}
}

void detectado_(void){
	// Se define la variable temp_press que guarda el valor del puerto del botn
	uint8_t temp_press;
	temp_press = HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin);
	if (tick_time % t == 0){
		if(temp_press==inicial){
			c++;
		}
		else if (temp_press!=inicial){
			estado_actual = detectado;
		}
		if (c>=10){
			c=0;
			estado_actual= espera_p_liberar;
		}
	}

}

void espera_p_liberar_(void){
	if(HAL_GPIO_ReadPin(B1_GPIO_Port, B1_Pin)){
		estado_actual = actualizacion;
	}
}

void actualizacion (void){
	estado_actual = en_espera;
}
```
Y por ultimo en **stm32l4xx_it.c** ubicado en la carpeta **src** en la parte del **void SysTick_Handler(void)**, ponemos :
```
/* USER CODE BEGIN SysTick_IRQn 0 */
  tick_time++;
  /* USER CODE END SysTick_IRQn 0 */
```
