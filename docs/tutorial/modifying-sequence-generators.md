# Modificación de generadores de secuencias

Una de las características interesantes de Mégra es que puedes modificar los propios generadores, o su velocidad y orden de ejecución, nuevamente, tanto en tiempo discreto (pasos) como probabilísticamente.

Para obtener una lista completa de los modificadores que se pueden aplicar, consulte la sección "Modificadores del generador" en la referencia de la función.

## apple - basado en la probabilidad

```lisp
(sx 'ba #t 
  (apple :p 20 (haste 2 0.5) ;; <-- con una probabilidad del 20%, acelerar en 2 pasos, reducir la duración a la mitad
    (cyc 'go "sqr:120 sqr:180 ~ sqr:80")))
```

Esto solo modifica la velocidad de ejecución. También puede modificar el orden de ejecución.
En el siguiente ejemplo, saltamos 2 pasos hacia adelante con una probabilidad del 9% o retrocedemos 2 pasos con una probabilidad del 10%.

```lisp
(sx 'ba #t 
  (apple :p 10 (rewind 2) :p 9 (skip 2) 
    (cyc 'go "sqr:120 sqr:180 ~ sqr:80")))
```

Finalmente, puede modificar el generador en sí agregando nueva información basada en su historial:

```
(sx 'ba #t
  (apple :p 10 (grow 0.5 :method 'flower) 
    (cyc 'ta "sqr:'c2 sqr:'a3 sqr:'e3 sqr:'f4"))) ;; <-- puedes usar nombres de notas !
```

La función 'grow' toma un evento de sonido del generador, agrega una cierta cantidad de variación (el primer argumento, donde 0.0 significa que no hay variación y 1.0 significa mucha variación).

Déjelo correr por un tiempo y luego verifique cómo se ve:

```
(export-dot "grown" :live 'ba 'ta)
```

## every - basado en pasos

La versión basada en pasos en realidad cumple ambos roles:

```lisp
(sx 'ba #t 
  (every :n 12 (haste 2 0.5) ;; <-- cada 12 pasos, acelerar por 2 pasos, reducir la duración a la mitad
    (cyc 'go "sqr:120 sqr:180 ~ sqr:80")))
```
