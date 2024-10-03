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
El filtro utilizado en este laboratorio fue diseñado de acuerdo con los parámetros vistos en clase:

- **Tipo de filtro**: [Butterworth/Chebyshev, etc.]
- **Frecuencia de corte**: 45 Hz (pasa altas) y 400 Hz (pasa bajas).
- **Orden del filtro**: [X], calculado en base a [justificación técnica].
- **Tamaño del rizado (ripple)**: [X dB], si aplica.

**Cálculos para el orden del filtro**:
Se realizaron cálculos para justificar el orden y las frecuencias de corte, tomando en cuenta las características del ruido y las señales de interés.

## 4. Aplicación del aventanamiento
Se aplicó una ventana de Hanning para mejorar el análisis espectral de la señal. La ventana fue seleccionada debido a su capacidad para reducir las fugas espectrales.

- **Tipo de ventana**: Hanning.
- **Tamaño de la ventana**: 256 muestras.
- **Forma de la ventana**:

  ![Ventana de Hanning](ruta_a_imagen_ventana.png)

- **Comparación antes y después de la convolución**:

  - Antes del aventanamiento: ![Señal antes](ruta_a_imagen_antes.png)
  - Después del aventanamiento: ![Señal después](ruta_a_imagen_despues.png)

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
Para evaluar la fatiga muscular, se realizó un análisis estadístico utilizando la **prueba de medias**:

- **Hipótesis nula (H0)**: No hay cambios significativos en la media de la frecuencia.
- **Hipótesis alternativa (H1)**: Hay un cambio significativo en la media de la frecuencia.

Los resultados mostraron un [aumento/disminución] en la frecuencia media conforme el músculo se fatigaba. Se concluye que [conclusión basada en el test].

## 7. Conclusiones
En este laboratorio, se logró procesar y analizar la señal EMG del flexor profundo de los dedos, identificando contracciones musculares y observando la fatiga muscular. Las herramientas estadísticas aplicadas permitieron evaluar los cambios en las frecuencias dominantes, lo que confirma la hipótesis de que la fatiga afecta las características espectrales de la señal.

## 8. Anexos
- [Link al código utilizado](ruta_a_codigo_github.py)
- [Archivo de datos EMG utilizado](ruta_a_datos_EMG.txt)

## 9. Referencias
- [Documentación relacionada con el laboratorio](ruta_a_documento_tecnico.pdf)
- [Guía de Prácticas de Laboratorio, Señales EMG](ruta_a_guia_practicas.pdf)

---

## Instrucciones para reproducir
1. Clonar el repositorio.
2. Instalar las dependencias especificadas en el archivo `requirements.txt`.
3. Ejecutar el archivo `LABORATORIO3.py` para procesar y analizar la señal.

---

## Comentarios adicionales
Este informe está autocontenido y no requiere de enlaces externos adicionales para su comprensión. Todos los datos, códigos y gráficas están incluidos directamente en este repositorio para facilitar la reproducibilidad del trabajo.

