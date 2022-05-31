# Configuración Audio

Como se mencionó, la versión predeterminada de Mégra solo funciona con un tamaño de bloque (*blocksize* o *buffer size*) fijo de 512, por lo que si esa es la versión que instaló, asegúrese de que el tamaño de bloque de su sistema sea 512.

Cualquier frecuencia de muestreo debería funcionar, pero tenga en cuenta que todas las muestras que utilice se volverán a muestrear a la frecuencia de muestreo actual si no coinciden, lo que podría aumentar el tiempo de carga. Recomiendo usar la frecuencia de muestreo en la que se almacenan sus muestras.

## Ringbuffer Versions

Tanto para macOS como para Windows, hay versiones que tienen el sufijo `_ringbuffer`. Estas versiones pueden adaptarse a una amplia gama de tamaños de búfer de audio, por lo que si no está seguro de cuál es el tamaño del búfer de audio de su sistema (también conocido como tamaño de bloque), utilícelos.

En Windows, es necesario usar esa versión porque la API de audio WASAPI usa tamaños de búfer variables.

No existe tal versión para Linux porque con JACK, es muy fácil establecer el tamaño de bloque correcto.
