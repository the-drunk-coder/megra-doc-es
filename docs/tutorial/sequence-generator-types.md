# Tipos de generadores de secuencias

Si el contexto de sincronización es un receptor de eventos, entonces necesitamos una fuente. Eso es lo que
los generadores de secuencia son para.
Ya hemos visto un tipo de generador, el generador de ciclo. Hay varios más.
Cada generador de secuencias es fundamentalmente una *cadena de Markov* que se comporta de cierta manera,
dependiendo de cómo se cree. Un generador de secuencias puede comportarse de forma **no determinista**, pero
no necesariamente tiene que hacerlo.

**TIP:**
Puede visualizar las cadenas de Markov, ya que se pueden representar fácilmente como un gráfico.
¡Para ese fin, necesita graphviz (https://graphviz.org/) instalado! (más adelante a medida que avanzamos)

Veamos algunos generadores ahora. Para obtener una lista completa, consulte la sección 'Generador de secuencias'
en la función de referencia a continuación. Como ya se mencionó, cada generador necesita un identificador.
Esto es para que podamos dirigirnos a ellos para su visualización y mantener su estado sobre el
varias ejecuciones.

## El generador de núcleo

.. o 'nuc', para abreviar ...

```lisp
(sx 'ctx #t ;; <-- pitido continuo
  (nuc 'core :dur 400 ;; <-- este argumento de palabra clave controla el intervalo de tiempo.
  ;; ¡Se puede utilizar con todos los generadores de secuencias!
    (sine 440)
    ;;(sine 885) ;; <-- puedes pasar uno o varios eventos, descomentar para probar
    ))

(clear)
```

Este es el generador más simple, simplemente repite los eventos que se le dan una y otra vez en un intervalo de tiempo constante. Cada generador tiene un montón de argumentos de palabras clave, como puede ver arriba.

## El generador de ciclos

Ya hemos visto esto anteriormente en la introducción de contextos. El generador cyc crea, lo has adivinado, ciclos, aunque hay más, como veremos.

```lisp
;; Aquí hay un ciclo básico:
(sx 'trololo #t ;; <-- there's no need to name every context 'context' ... you don't use 'password' for all your passwords, do you ?
  (cyc 'bells "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5"))
```

```lisp
;; Si quieres que el generador sea más rápido, puedes modificar el intervalo de tiempo:
(sx 'trololo # ;; mucho mas rapido !
  (cyc 'bells :dur 100 "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5"))

(sx 'trololo # ;; mucho mas lento !
  (cyc 'bells :dur 400 "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5")) 
```

La función 'cyc' toma una cadena como argumento que describe un ciclo. Si ya conoce TidalCycles, el concepto se toma prestado de eso. Es un pequeño lenguaje de secuenciación propio.

Hasta ahora todo ha sido muy determinista, entonces, ¿de qué se trata toda la charla estocástica en el archivo Léame?
Bueno, echa un vistazo a esto:

```lisp
(sx 'trololo #t 
  (cyc 'bells :rep 70 :max-rep 4 "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5")) 
```

No suena tan predecible, ¿verdad? Entonces, ¿qué hacen los argumentos de palabras clave? La palabra clave 'rep' define
la probabilidad de que un evento se repita (70% de probabilidad en este caso). La palabra clave 'max-rep' especifica el número máximo de repeticiones.

Por el bien de la visualización, reduzcamos el número de repeticiones:

```lisp
(sx 'trololo #t 
  (cyc 'bells :rep 70 :max-rep 2 "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5")) 
```

Ahora, veamos cómo se ve esta cadena de Markov como un gráfico:

```lisp
(export-dot "trololo" :live 'trololo 'bells)
```

Ahora encontrará un archivo llamado "trololo_trololo_bells.dot" en la carpeta desde la que inició Mégra.
Ejecute `$ neato -Tsvg trololo_trololo_bells.dot -o trololo_trololo_bells.svg` para generar un archivo SVG que se puede ver en su navegador. Deberías ver la cadena de markov representada como un gráfico, donde puedes ver todos
las repeticiones, etc. ¡Pruebe diferentes configuraciones y vea cómo se ven!

Por su capacidad para crear secuencias repetitivas, el generador cyc es perfecto para crear latidos y así sucesivamente.
Para obtener una descripción completa, consulte la entrada en la referencia de la función.

## The Inference Generator

The cyc generator is good to create more or less repetetive sequences from abstract descriptions (the cyc language),
But what if you want something more controlled ? If you want to create generators from a set of rules, the 'infer' 
generator will take them and infer a generator from them. This is also a good opportunity to explain what the markov
chains (of probabilistic finite automata, PFA for short) are doing.

If you're into generative music, you probably know already what a Markov Chain is, as it is a fairly common structure 
in that domain. If not, here's a simple explanation. Even if you know them already, I'd recommend reading the following 
part to get to know the specific "flavour" of Markov Chains employed by Mégra.

Imagine you want to create a simple boom-bap style beat, with just a bassdrum, a snare and some hi-hats. Only half the 
time the hi-hat should be in between the bassdrum and the snare.

In slightly more precise terms, we could describe the beat with the following rules. The natural language description 
is a bit tedious, but bear with me here. More concise descriptions will follow!

1. Start with a bassdrum.
2. There's a 50% chance that a snare will follow the bassdrum, after 400 milliseconds.
3. There's a 50% chance that a hi-hat will follow the bassdrum, after 200 milliseconds.
4. After every snare will follow a bassdrum, after 400 milliseconds.
5. After every hi-hat will follow a snare, after 200 milliseconds.

```lisp
(sx 'boom #t 
  (infer 'bap ;; <- this creates the generator (infers it from rules)
    :events 'b (bd) 's (sn) 'h (hats) ;; <- here's the event mapping ... pretty prosaic ...
    :rules 
    (rule 'b 's 50 400) ;; here's the rules ... format: (rule 'source 'target probability duration)
    (rule 'b 'h 50 200) 
    (rule 's 'b 100 400) 
    (rule 'h 's 100 200)))

;; Visualize it:

(export-dot "beat" :live 'boom 'bap)
;; neato -Tsvg beat_bap_boom.dot -o beat_bap_boom.svg

(clear)

;; You can also introduce repetitions:
(sx 'boom #t 
  (infer 'bap ;; <- this creates the generator (infers it from rules)
    :events 'b (bd) 's (sn) 'h (hats) ;; <- here's the event mapping ... pretty prosaic ...
    :rules 
    (rule 'b 's 50 400)
    (rule 'b 'h 50 200) 
    (rule 's 'b 100 400) 
    (rule 'h 's 10 200)
    (rule 'h 'h 90 50) ;; repeat the hihat with a 90% chance, and really quickly
    (rule 'hhhh 'b 100 200) ;; maximum repetition number: 4
    ))
```

Visualize again: 
```
(export-dot "beat" :live 'boom 'bap)
$ neato -Tsvg beat_bap_boom.dot -o beat_bap_boom.svg
```

TECH BACKGROUND:
Here you can see the "variable-order" part of variable-order markov chains. You can specify the
required memory length when necessary, i.e. to define the number of repetitions. 

A bit like irregular trap, no ?

## The Learning Generator

Markov chains cannot only be inferred from rules, but also learned from a sample. Here's where
a bit of old-fashioned machine learning comes in. 

So if instead of an exact sequence you need "something like" the sample, you can learn the pattern
from the sample. Durations are fixed for this method.

```lisp
(sx 'boom #t 
  (learn 'bap ;; <- this creates the generator (learns from a sample)
    :events 'b (bd) 's (sn) 'h (hats) 'r (risset 'c5)
    :sample "bsbs~~~bsbsb~~hbhb~rbrb~~" ;; <-- hack in some sample string .. '~' stands for silence
    ))

(clear)
```
