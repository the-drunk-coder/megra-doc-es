# Generadores de secuencias & contextos

Ahora tenemos que poner las cosas en contexto. Todo en Mégra se ejecuta dentro de un contexto donde se sincronizan diferentes secuencias (o generadores de secuencias).
Es por eso que es un "Sync-conteXt" o "sx" para abreviar. "Sincronización" aquí se refiere a que los generadores se inician al mismo tiempo.
Si corren a diferentes velocidades (más sobre eso más adelante), no permanecerán sincronizados.

Un contexto de sincronización necesita un **nombre** y una **bandera** que le indique si debe ejecutarse o no.

Todo lo que sigue son **generadores de secuencia**. Un **generador de secuencias** genera una secuencia de eventos de sonido (o control), de ahí el nombre.

```lisp
;; Esto es un comentario... todo detrás ;; será ignorado.

;; Este es un contexto de sincronización. Captura los eventos de sonido generados por los generadores de secuencia.
;; Los nombres en Mégra comienzan con ', por lo que el nombre aquí es solo 'contexto'. Esto se hereda de
;; familia Lisp de lenguajes de programación.
;; El indicador (booleano) es #t (verdadero, true) o #f (falso) y determina si el contexto
;; esta activo o no.

(sx 'context #t ;; <-- sx <name> <flag>
  (cyc 'cycle-generator "bd ~ hats ~ sn ~ hats ~")) ;; <-- esto genera una secuencia de eventos
```

Puede colocar varios generadores de secuencias en el mismo contexto, de modo que todos se ejecuten sincronizados:
Por defecto, los nuevos generadores (que no estaban presentes en el contexto anterior) se **sincronizan**
en un **evento no silencioso**.

```lisp
(sx 'context #t ;; <-- ¡pon esto en #f para silenciar este contexto!
  (cyc 'percussion-generator "~ ~ ~ risset:'a5 ~ ~ risset:'c6 ~")
  (cyc 'bassline-generator "saw:'a1 ~ saw:'ds2 saw:'e2 ~ ~ saw:'c3 ~")
  (cyc 'beat-generator "bd ~ hats ~ sn ~ hats ~")) 
```

También puede sincronizar varios contextos:

```lisp
(sx 'context-a #t ;; <-- ejecutar esto primero
  (cyc 'percussion-generator "~ ~ ~ risset:'a5 ~ ~ risset:'c6 ~")
  (cyc 'beat-generator "bd ~ hats ~ sn ~ hats ~"))

(sx 'context-b #t :sync 'context-a ;; <-- ejecuta esto cuando lo creas conveniente
  (cyc 'bassline-generator "saw:'a1 ~ saw:'ds2 saw:'e2 ~ ~ saw:'c3 ~"))

(clear) ;; clear stops everything
```

Hay más cosas geniales que puedes hacer solo con el contexto de sincronización, como
**soloing** y **blocking**:

```lisp
(sx 'context #t :solo 'beat-generator ;; <-- puede solo generadores individuales...
  (cyc 'percussion-generator "~ ~ ~ risset:'a5 ~ ~ risset:'c6 ~")
  (cyc 'bassline-generator "saw:'a1 ~ saw:'ds2 saw:'e2 ~ ~ saw:'c3 ~")
  (cyc 'beat-generator "bd ~ hats ~ sn ~ hats ~"))

(sx 'context #t :block 'beat-generator ;; <-- ... o bloquearlos!
  (cyc 'percussion-generator "~ ~ ~ risset:'a5 ~ ~ risset:'c6 ~")
  (cyc 'bassline-generator "saw:'a1 ~ saw:'ds2 saw:'e2 ~ ~ saw:'c3 ~")
  (cyc 'beat-generator "bd ~ hats ~ sn ~ hats ~"))

(sx 'context #t :block 'sn ;; <-- También puede bloquear o solo un tipo de evento especial.
  (cyc 'percussion-generator "~ ~ ~ risset:'a5 ~ ~ risset:'c6 ~")
  (cyc 'bassline-generator "saw:'a1 ~ saw:'ds2 saw:'e2 ~ ~ saw:'c3 ~")
  (cyc 'beat-generator "bd ~ hats ~ sn ~ hats ~"))

(sx 'context #t :solo 'sn 'risset ;; <-- También puede bloquear o solo varias etiquetas.
  (cyc 'percussion-generator "~ ~ ~ risset:'a5 ~ ~ risset:'c6 ~")
  (cyc 'bassline-generator "saw:'a1 ~ saw:'ds2 saw:'e2 ~ ~ saw:'c3 ~")
  (cyc 'beat-generator "bd ~ hats ~ sn ~ hats ~"))

(clear)
```

**TECH BACKGROUND:**

También puede pensar en el contexto de sincronización como un sumidero para eventos de sonido. Cada evento sonoro
que emite el generador tiene un montón de etiquetas, que contienen el nombre del
generador, el tipo de evento y, en el caso de eventos de muestra, las etiquetas de búsqueda (más sobre esto más adelante).
Las palabras clave bloque y solo activan filtros que actúan sobre estas etiquetas.
