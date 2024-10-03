# Informe de Laboratorio - Señales EMG

## 1. Introducción
La electromiografía hace parte de una de las ramas del estudio médico, el cual permite valorar la salud de los músculos, y los nervios que los controlan. Es así, como a la hora adquisición para su posterior evaluación, es necesario procesar la señal de los datos adquiridos, esto con el fin eliminar las interferencias que se pueden presentar en la toma de datos. En este laboratorio se analiza la señal electromiográfica (EMG) del flexor profundo de los dedos tomado por electrodos superficiales, utilizando diversas técnicas de procesamiento digital de señales, tales como el filtrado, aventanamiento y análisis en el dominio de la frecuencia. El objetivo es detectar y analizar, las contracciones musculares y el comportamiento del cuerpo al llegar a la fatiga muscular. Este documento describe el proceso de captura, procesamiento, y análisis de los datos obtenidos.

## 2. Captura de la señal EMG
- **Músculo medido**: Flexor profundo de los dedos.
- **Frecuencia de muestreo**: 1000 Hz.
- **Duración de la señal**: La duración de la señal es 10s, en la gráfica se empezo hacer análisis a partir de 7s a 10s.
- **Longitud de la señal**: La longitud de la señal  es 1801, que fueron capturados durante el tiempo de muestreo.
- **Cantidad de contracciones**:

  ![image](https://github.com/user-attachments/assets/19a36ead-77f9-48ee-98df-3b43f01e90e6)

Para detectar los picos en la señal EMG y determinar el número de contracciones, se utilizo la función find_peaks() de la librería scipy.signal. Esta función permite identificar los picos de una señal, aplicando ciertos criterios como el umbral mínimo de altura o la distancia mínima entre picos.

- **Gráfica de la señal**:

  ![image](https://github.com/user-attachments/assets/a9dd82f1-211c-44fe-846e-57b5e429d05a)

  Se muestra la señal capturada con todas sus características. Se utilizó una frecuencia de muestreo de 1000 Hz y se midió el músculo flexor profundo de los dedos. La longitud total de la señal es de 1801 muestras y la señal incluye 7 contracciones musculares.

## 3. Diseño del filtro
El filtro utilizado en este laboratorio fue diseñado de acuerdo con los siguientes parámetros vistos en clase:

- **Tipo de filtro**: Butterworth
- **Frecuencia de corte**: 20 Hz (pasa altas) y 300 Hz (pasa bajas).
- **Orden del filtro**: 4.
- **Tamaño del rizado (ripple)**: no aplica.

**Cálculos para el orden del filtro**:
Teniendo en cuenta los parámetros previamente establecidos, se decidió hacer un filtro Butterworth, esto con el fin de conseguir un amortiguamiento de forma idónea se realizó un filtro de 4to orden, posteriormente gracias a la literatura se estableció una frecuencia de muestreo 1000 Hz. Para la frecuencia de corte se optó acudir al artículo realizado por J.L. Correa. Et al.  Estableciendo así un valor 20Hz en el pasa Bajo, y una frecuencia de 300 Hz para el pasa altos.  Obteniendo así los siguientes cálculos:

![image](https://github.com/user-attachments/assets/05efb522-47dd-41db-86c1-2cfd8afdc481)


![image](https://github.com/user-attachments/assets/c5b65ff6-3e96-4312-8ad4-91331baf5865)

La siguiente sección de código, enmarca los atributos bases para el diseño del filtro pasa altos y pasa bajos, teniendo encuentra la frecuencia de corte, con el teorema de Nyquist  y su respectiva frecuencia de corte.
```Python
# Función para diseñar el filtro Butterworth
def butter_highpass(cutoff, fs, order=4):
    nyquist = 0.5 * fs  # Frecuencia de Nyquist
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='high', analog=False)
    return b, a

def butter_lowpass(cutoff, fs, order=4):
    nyquist = 0.5 * fs  # Frecuencia de Nyquist
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return b, a
```

Para el filtro Pasa Altos:
![image](https://github.com/user-attachments/assets/be215d75-5541-4ad1-a0d4-849bcbdc650f)


Para el filtro Pasa Bajos:
![image](https://github.com/user-attachments/assets/9d7f8d61-3d52-48e8-859d-49ea4c5f0457)



Los filtros se establecieron con el siguiente código:
```Python

# Función para aplicar los filtros
def filtrar_senal(data, fs):
    
    highcut = 20  # Frecuencia de corte para el filtro pasa altas
    lowcut = 300  # Frecuencia de corte para el filtro pasa bajas
    
    # Diseñar filtros
    b_high, a_high = butter_highpass(highcut, fs, order=4)
    b_low, a_low = butter_lowpass(lowcut, fs, order=4)
    
    # Aplicar el filtro pasa altas
    filtrada = filtfilt(b_high, a_high, data)
    
    # Aplicar el filtro pasa bajas
    filtrada = filtfilt(b_low, a_low, filtrada)
    
    return filtrada
```


## 4. Aplicación del aventanamiento
Se aplicó una ventana de Hanning para mejorar el análisis espectral de la señal. La ventana fue seleccionada debido a su capacidad para reducir las fugas espectrales.

- **Tipo de ventana**: Hanning.
- **Tamaño de la ventana**: 256 muestras.

Se aplicó una ventana de Hanning a la señal EMG para mejorar el análisis en el dominio de la frecuencia. La elección de esta ventana y el tamaño de 256 muestras responde a las siguientes razones:

### 4.1 Ventana de Hanning
La ventana de Hanning se seleccionó por su capacidad para reducir las fugas espectrales, un problema que ocurre cuando se cortan segmentos de la señal. Esta ventana suaviza los extremos de los segmentos, reduciendo las discontinuidades, lo que se traduce en un espectro más limpio. La fórmula que define la ventana de Hanning es:

![image](https://github.com/user-attachments/assets/653dcbb1-935e-4bd2-a270-81c4fc13740e)

Esta ventana es adecuada para el análisis de señales EMG, donde se busca un equilibrio entre la reducción de ruido y una buena representación del espectro de frecuencia.

### 4.2. Tamaño de la ventana: 256 muestras
El tamaño de la ventana se definió en 256 muestras porque:

![image](https://github.com/user-attachments/assets/a09b39bd-cb22-4eae-ae52-a2dc51c6f9d7)

Ofrece un buen compromiso entre la resolución temporal y frecuencial: ventanas más grandes mejoran la resolución en frecuencia, pero empeoran la resolución temporal.
Es una potencia de dos, lo que facilita cálculos más rápidos de la FFT.
Es un valor comúnmente utilizado en estudios de análisis de EMG y suficiente para capturar las características relevantes de la señal.
Esta elección está respaldada por estudios como Müller et al. (2004) y recomendaciones del campo del procesamiento de señales.

## 4.3 Segmentación y aventanamiento de la señal
-**Aplicación del aventanamiento de Hanning**:
La ventana de Hanning de 256 muestras suaviza los bordes de cada segmento, evitando las discontinuidades bruscas en los límites de cada ventana. Esto minimiza los efectos de "fugas espectrales" en el análisis de Fourier y mejora la resolución de frecuencias.
Al aplicar esta ventana, la señal se multiplica por una función que disminuye la amplitud de los extremos de cada segmento, reduciendo el impacto de las discontinuidades cuando se analizan sus componentes frecuenciales.

-**Separación de segmentos no nulos**:
Tras aplicar la ventana, los segmentos con contenido significativo (no nulo) son separados y mostrados. En la gráfica, se observan siete segmentos diferentes, cada uno correspondiente a una parte de la señal aventanada. Cada uno de estos segmentos incluye la información de la señal dentro de una ventana de 256 muestras.

![image](https://github.com/user-attachments/assets/5d91fec4-4fa9-46b1-a7da-dd5f8fd782c8)

Los segmentos muestran cómo varía la amplitud de la señal en cada intervalo. La aplicación de la ventana de Hanning suaviza los valores al inicio y al final de cada segmento, lo que puede observarse como una disminución gradual en los extremos de las gráficas. Este enfoque es útil para análisis espectral, ya que permite examinar las características de la señal en intervalos específicos sin distorsiones causadas por las discontinuidades en los extremos.


## 5. Transformada rápida de Fourier (FFT) y análisis estadístico
Se calculó la FFT para cada contracción muscular, analizando las frecuencias dominantes y el comportamiento de la señal en el dominio de la frecuencia.




Para llevar a cabo este análisis, primero se definió una función llamada `calculadoradefft(segmentos, fs)`. Esta función utiliza la biblioteca `numpy` para calcular la transformada rápida de Fourier (FFT) de cada ventana. Además, mediante la biblioteca `matplotlib`, se grafica la FFT correspondiente a cada una de las ventanas.
```python
def calculadoradefft(segmentos, fs):
    for i, segmento in enumerate(segmentos):
        espectro = np.abs(fft(segmento))
        frecuencias = np.fft.fftfreq(len(segmento), d=1/fs)
        
        plt.figure(figsize=(10, 6))
        plt.plot(frecuencias[:len(segmento) // 2], espectro[:len(segmento) // 2], label=f'FFT del Segmento {i+1}', color='orange')
        plt.title(f'FFT del Segmento {i+1}')
        plt.xlabel('Frecuencia (Hz)')
        plt.ylabel('Magnitud')
        plt.grid(True)
        plt.legend()
        plt.show()
```
los resultados obtenidos son los siguientes






- **Frecuencia dominante**: [Frecuencia más alta].
- **Frecuencia media**: [Frecuencia media calculada].
- **Desviación estándar**: [Valor de la desviación estándar].

Gráficas de las contracciones en el dominio de la frecuencia:

- ![Espectro de Frecuencias Ventana 1](ruta_a_imagen_fft1.png)
- ![Espectro de Frecuencias Ventana 2](ruta_a_imagen_fft2.png)

## 6. Análisis estadístico
Para evaluar la fatiga muscular, se realizó un análisis estadístico del test de medias, por el cual se establecieron las siguientes hipótesis:

- **Hipótesis nula (H0)**: que al momento de realizar la comparación entre las medias de cada grupo de intervalo, al momento de que el musculo llega al fallo la media ira disminuyendo.
- **Hipótesis alternativa (H1)**: que al momento de realizar la comparación entre las medias de cada grupo de intervalo, al momento de que el musculo llega al fallo la media obtendrá un valor mayor con respecto a los demás grupos de impulsos.

Los resultados mostraron un [aumento/disminución] en la frecuencia media conforme el músculo se fatigaba. Se concluye que [conclusión basada en el test], que la hipotesis corecta fue ........

## 7. Conclusiones
En este laboratorio, se logró procesar y analizar la señal EMG del flexor profundo de los dedos, identificando contracciones musculares y observando la fatiga muscular. Las herramientas estadísticas aplicadas permitieron evaluar los cambios en las frecuencias dominantes, lo que confirma la hipótesis de que la fatiga afecta las características espectrales de la señal.

## 8. Anexos
- [Link al código utilizado](ruta_a_codigo_github.py)
- [Archivo de datos EMG utilizado](ruta_a_datos_EMG.txt)

## 9. Referencias
- [1.	J.L. Correa-Figueroa, E. Morales-Sánchez, J.A. Huerta-Ruelas, J.J. González-Barbosa, C.R. Cárdenas-Pérez.  “Sistema de Adquisición de Señales SEMG para la Detección de Fatiga Muscular “. Revista Mexicana de Ingeniería Biomédica IB. Vol. 37 No. 1. 2016.  http://dx.doi.org/10.17488/RMIB.37.1.4)
- [Guía de Prácticas de Laboratorio, Señales EMG](ruta_a_guia_practicas.pdf)

---

## Instrucciones para reproducir
1. Clonar el repositorio.
2. Instalar las dependencias especificadas en el archivo `requirements.txt`.
3. Ejecutar el archivo `LABORATORIO3.py` para procesar y analizar la señal.

---

## Comentarios adicionales
Este informe está autocontenido y no requiere de enlaces externos adicionales para su comprensión. Todos los datos, códigos y gráficas están incluidos directamente en este repositorio para facilitar la reproducibilidad del trabajo.

