[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=19144195&assignment_repo_type=AssignmentRepo)
# Lab03: Visualización de Datos en Raspberry Pi Zero W

## Integrantes
[Jhonatan Rodriguez Grisales](https://github.com/Jhonatan-3011)


[Nelson Prada](https://github.com/nelson18prada)

[Miguel Angel Bernal](https://github.com/Miguelbernalradio) 

## Documentación
 Descripción General

• En el archivo [monitor_temp.py](/src/monitor_temp.py) encontraran la clase MonitorTemperaturaRPI está diseñada para realizar un seguimiento continuo y visual del comportamiento térmico de la CPU de una Raspberry Pi. Este sistema combina la captura de datos de temperatura a través de comandos del sistema operativo con su registro persistente en un archivo CSV y una representación gráfica en tiempo real usando la biblioteca matplotlib. A través de un enfoque orientado a objetos, el monitoreo se mantiene activo mientras se visualiza la ventana gráfica, permitiendo observar cómo evoluciona la temperatura a lo largo del tiempo con una frecuencia configurable. La arquitectura de la clase permite un manejo eficiente de los datos mediante una ventana deslizante y ofrece tolerancia a errores, permitiendo que el sistema continúe funcionando incluso si ocurren fallos puntuales en la lectura de la temperatura.

Atributos de la Clase

• duracion_max:
Tiempo máximo en segundos para conservar los datos en memoria y en la gráfica.

• intervalo:
Intervalo de muestreo en segundos entre cada lectura de temperatura.

• archivo_csv:
Ruta y nombre del archivo CSV donde se almacenan las lecturas de tiempo y temperatura.

• tiempos:
Lista de tiempos relativos (desde el inicio de la medición).

• temperaturas:
Lista de temperaturas en grados Celsius correspondientes a cada punto en tiempos.

• inicio:
Tiempo absoluto de inicio del monitoreo (segundos desde época).

• fig, ax:
Objetos para graficar en tiempo real la evolución de la temperatura.

Métodos

• __init__(self, duracion_max=60, intervalo=0.5, archivo_csv="temperaturas.csv")
Inicializa todos los atributos, configura la gráfica en modo interactivo (plt.ion()), y crea un archivo CSV con encabezados si no existe.


leer_temperatura(self)

• Ejecuta el comando vcgencmd measure_temp mediante subprocess.
Extrae y convierte la cadena resultante a un valor numérico.
En caso de error, captura la excepción y retorna None.


actualizar_datos(self)

• Calcula el tiempo transcurrido desde el inicio.
Llama a leer_temperatura para obtener la temperatura actual.

Si la lectura es válida:

• Añade los datos a las listas tiempos y temperaturas.
Escribe los datos en el archivo CSV (modo append).
Elimina datos antiguos que exceden el límite de duracion_max para mantener una ventana de tiempo deslizante.


graficar(self)

• Limpia el gráfico actual.
Dibuja la serie temporal de temperatura usando matplotlib.
Configura títulos, etiquetas y grilla.
Actualiza el lienzo de la figura para reflejar los nuevos datos.


ejecutar(self)

• Inicia un bucle principal que se ejecuta mientras la ventana de la gráfica esté abierta.

En cada iteración:

• Llama a actualizar_datos y graficar. 
Espera el intervalo definido (self.intervalo) antes de repetir.
Maneja interrupciones por teclado (KeyboardInterrupt) para una salida controlada.
Finaliza cerrando la figura y desactivando el modo interactivo de matplotlib.

Concretamente lo que se lo agrego al codigo principal fue:

• Importación del módulo csv:
Se añadió import csv para permitir la escritura de datos en un archivo .csv.

• Nuevo parámetro archivo_csv en el constructor:
Se añadió el argumento archivo_csv="temperaturas.csv" para definir el nombre del archivo donde se guardan los datos.

• Creación del archivo .csv con encabezados:
Dentro del __init__, se abre el archivo CSV en modo escritura ('w') y se escriben los encabezados: "Tiempo (s)", "Temperatura (°C)".

• Escritura de datos en el archivo CSV en actualizar_datos():
Cada vez que se actualiza la temperatura, se añade una línea con el tiempo y la temperatura al archivo .csv.

## Diagrama de flujo 

![Diagrama de flujo](/diagrama_flujo.png)

Explicacion del diagrama de flujo

El diagrama de flujo describe un proceso secuencial para monitorear y registrar temperaturas, comenzando con la inicialización del sistema y la creación de un archivo CSV para almacenar los datos. A continuación, se activa una gráfica interactiva para visualizar las mediciones en tiempo real. El sistema calcula el tiempo transcurrido y realiza lecturas de temperatura, verificando su validez antes de proceder con el almacenamiento y la representación gráfica.

Si la lectura es válida, los datos se guardan en el archivo CSV y se actualizan en la gráfica, lo que permite un seguimiento continuo y preciso. En caso contrario, el proceso omite estos pasos y avanza directamente a la espera de un intervalo de tiempo predefinido antes de repetir el ciclo. Esta validación asegura que solo se registren datos correctos, manteniendo la integridad del sistema.

El flujo opera en un ciclo repetitivo, donde cada iteración incluye la lectura, validación, almacenamiento y visualización de datos, seguida de una pausa controlada. Este enfoque es común en sistemas de adquisición de datos, donde la periodicidad y la precisión son esenciales. La combinación de almacenamiento en CSV y visualización gráfica facilita tanto el análisis posterior como el monitoreo en tiempo real.

Finalmente, el proceso puede terminar manualmente o bajo ciertas condiciones, cerrando el archivo CSV y deteniendo la gráfica. Este diseño modular y eficiente es ideal para aplicaciones de monitoreo ambiental, industrial o científico, donde la recolección y el análisis de datos son fundamentales para la toma de decisiones.

## Preguntas

1. ¿Qué función cumple ```plt.fignum_exists(self.fig.number)``` en el ciclo principal?

La función plt.fignum_exists(self.fig.number) comprueba si la ventana de la gráfica sigue abierta. Si el usuario cierra la ventana mientras el programa está en ejecución, esta función devuelve False, lo que provoca que el bucle principal se detenga de manera controlada. Es un control de presencia: mientras la ventana esté abierta, el monitoreo continúa; cuando se cierra, el programa finaliza. Se evitan errores y se previene que el programa siga funcionando innecesariamente.

2. ¿Por qué se usa ```time.sleep(self.intervalo)``` y qué pasa si se quita?

La instrucción time.sleep(self.intervalo) regula la frecuencia con la que se leen y grafican los datos. Esta pausa evita la sobrecarga del procesador por lecturas y gráficos constantes. Sin esta pausa, el código se ejecuta a la máxima velocidad, lo que puede saturar el sistema, calentar más la Raspberry Pi y acumular datos innecesarios en el archivo CSV en poco tiempo, generando ruido en vez de información útil.

3. ¿Qué ventaja tiene usar ```__init__``` para inicializar listas y variables?

El método __init__ inicializa listas y variables desde que se crea el objeto. Establece parámetros como el tiempo máximo de monitoreo, la frecuencia de actualización y el nombre del archivo CSV. Prepara los elementos gráficos y las estructuras de datos. El objeto queda listo desde el inicio, evitando errores por variables no definidas o estructuras mal configuradas.

4. ¿Qué se está midiendo con ```self.inicio = time.time()```?

Se está registrando el tiempo exacto (en segundos) desde el inicio de la ejecución del programa o de una determinada operación. Esto permite calcular cuánto tiempo ha pasado desde ese momento, lo cual es útil para medir duración o sincronizar procesos.

5. ¿Qué hace exactamente ```subprocess.check_output(...)```?

Este comando ejecuta una instrucción o programa en la línea de comandos del sistema operativo desde Python y devuelve su salida como texto. Es útil para obtener datos de otros programas externos, como sensores o comandos del sistema.

6. ¿Por qué se almacena ```ahora = time.time() - self.inicio``` en lugar del tiempo absoluto?

Se hace para obtener el tiempo transcurrido desde el inicio del programa, lo cual es más útil para gráficos o análisis relativos. El tiempo absoluto es una fecha fija, mientras que el tiempo transcurrido ayuda a visualizar cómo evolucionan los datos en el tiempo.

7. ¿Por qué se usa ```self.ax.clear()``` antes de graficar?

Se usa para limpiar el gráfico anterior antes de dibujar uno nuevo, evitando que los datos se superpongan. Así, cada actualización muestra solo la información más reciente de forma clara y ordenada.

8. ¿Qué captura el bloque ```try...except``` dentro de ```leer_temperatura()```?

Este bloque captura errores que puedan ocurrir al intentar leer la temperatura, como fallos de conexión o errores del sensor. Así se evita que el programa se detenga inesperadamente y se puede manejar el error de forma controlada.

9. ¿Cómo podría modificar el script para guardar las temperaturas en un archivo .```csv```?

Puedes abrir un archivo en modo de escritura (with open("datos.csv", "a")) y usar csv.writer o simplemente escribir una línea con archivo.write(f"{tiempo},{temperatura}\n") dentro del ciclo donde se lee la temperatura. Así cada dato se guarda en formato separado por comas.

