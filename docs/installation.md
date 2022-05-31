# Installación

Hay tres modos de instalar Mégra. ¡Antes de comenzar, asegúrese de leer el capítulo sobre la configuración de audio!

## Archivos binarios

Esta es la forma más fácil si está interesado en usar Mégra. Como Mégra es un programa autónomo e independiente, no hay mucho más que configurar. Simplemente descargue la última versión del [release page](https://github.com/the-drunk-coder/megra.rs/releases/).

[Mégra 0.0.4](https://github.com/the-drunk-coder/megra.rs/releases/tag/0.0.4)

## Con Cargo

Si ya usa `rust` y `cargo`, puede instalar la última versión desde *crates.io*.

En una terminal, escriba:

```
cargo install megra_rs
```

En Windows, necesitará la función de memoria intermedia, así que escriba:

```bash
cargo install megra_rs --features ringbuffer
```

Lea el capítulo sobre la configuración de audio para ver lo que hace la función del *ringbuffer*.

## Desde el codigo de fuente

Descarga o clona el repositorio. En la carpeta del repositorio, escriba...

```
cargo run --release -- -e -o 2ch
```



