# Uso (y búsqueda) de samples

Si aún no tiene una colleción de samples a mano, puede encontrar algunas muestras (suficientes para seguir la documentación y el tutorial) aquí: https://github.com/the-drunk-coder/megra-public-samples

Mégra actualmente solo admite muestras en formato FLAC (wav está en desarrollo).

Coloque las muestras en la carpeta (la carpeta se creará en el primer inicio):

* Linux: ~/.config/megra/samples.
* Windows: C:\Users\<username>\AppData\Roaming\parkellipsen\megra\config\samples
* macOS: /Users/<username>/Library/Application Support/de.parkellipsen.megra/samples
	
Ahora tendrá un evento de sonido para cada muestra. Eso significa que, si tiene una carpeta llamada bd en la carpeta de muestras, puede llamarla así:

```
;; llame a esto en el editor de Mégra (coloque el cursor entre paréntesis y teclea "Ctrl+Return")
(once (bd))
```

También se puede buscar por palabra clave... si tiene un sample llamada `jazz.flac` en su carpeta `bd`, puede llamarla así:

```
(once (bd 'jazz))
```

Los nombres de archivo están separados en palabras clave por espacios en blanco, guiones, guiones bajos y puntos. Por lo tanto, si su muestra se llama `ultimate jazz-bassdrum.latest.flac`, las palabras clave serán `ultimate`, `jazz`, `bassdrum` y `latest`.

Si no proporciona ninguna palabra clave, se elige una muestra aleatoria de la carpeta. Las muestras fuera de la carpeta de muestras mencionada anteriormente no se pueden llamar.

Actualmente no hay forma de especificar carpetas de muestras personalizadas, pero también puede cargar muestras individuales en un conjunto a mano usando
```
(load-sample :set '<set> :path "<path-to-sample>")
```. 

Estos no necesitan estar en la carpeta de samples, la ruta puede apuntar a cualquier lugar.

Como se mencionó anteriormente, asegúrese de configurar su sistema de audio a la samplerate de tus samples, de lo contrario, la carga de las samples será lenta debido al resampling.
