# Antirrebote
En este documento se realiza un ejercicio el cual consiste en realizar un circuito antrebote por medio de maquinas de estado.
El ejercicio se puede encontrar [aqui](https://www.eeweb.com/profile/tommyg/articles/debouncing-push-buttons-using-a-state-machine-approach).

Vamos a tener 4 estados los cuales son:

•En espera: esperando que se active cualquier botón del puerto.
•Detectado: se ha activado un botón para que se ingrese este estado y los valores del puerto de los botones se registran con Temp_Press. Cada 10 ms, el puerto del botón se vuelve a comprobar y, si bien sigue siendo el mismo valor, se incrementa un contador. En lenguaje de máquina de estados, la variable se conoce como "una variable de estado extendida".
•Espera para liberar: si el contador alcanza un valor mínimo predefinido, 'MIN_BUTTON_COUNT', Temp_press ahora se considera válido y se ingresa el estado WaitForRelease para esperar a que se libere el botón antes de que se actualice la variable, Button_Press, que contiene el valor final del botón.
•Actualización: el botón se ha liberado y, por lo tanto, el valor final Button_Press se actualiza con el valor temporal sin rebote 'Temp_Press'.

Y un unico evento o transicion:

•El diagrama de la máquina de estados representa esta vez como la ocurrencia de un evento TICK ( TICK_E ).

Se van a tener dos variables, Button_Press y Temp_Press , se definen con este tipo; Button_Press mantiene los valores finales para los botones después de que se hayan eliminado, mientras que Temp_Press mantiene valores intermedios para los botones durante la eliminación de rebotes.
