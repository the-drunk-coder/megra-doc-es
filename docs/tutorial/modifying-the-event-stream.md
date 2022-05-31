# Modifying the Event Stream

Every generator emits a stream of sound events that flow towards the sink (sx). The events can be 
manipulated along the way, either in a probabilistic or in a step-based fashion.

You can also filter for certain event types.

## pear - probability-based

The 'pear' function lets you control sound event parameters in a probabilistic fashion.

```lisp
(sx 'boom #t 
  (pear (lvl 0.4) (rev 0.2) ;; <-- the pear function lets you control parameters of the sound events
    (cyc 'bap "bd ~ hats ~ sn ~ hats ~")))

(sx 'boom #t
  (pear :p 20 (rev 0.3) ;; <-- you can set probabilities for the modification to be applied.
    (cyc 'bap "bd ~ hats ~ sn ~ hats ~")))
```

Everything after the ':p' keyword will be applied with a probability of 20%
The ':p' keyword in Mégra denotes a **probability**, which ranges from **0 to 100**. 

```lisp
(sx 'boom #t
  (pear :p 20 (rev 0.3) :for 'sn :p 40 (del 0.5) ;; <-- you can also specify selectors. 
    (cyc 'bap "bd ~ hats ~ sn ~ hats ~")))

;; Everything after the ':for' keyword will only be applied to events that contain the specified tag.

(clear)
```

**TECH BACKGROUND**

The parameters you see in "pear" represent a kind of "incomplete" sound event that 
doesn't contain enough information to actually be interpreted as sound.
For example, you could create a cycle like this:

```lisp
(sx 'ba #t ;; <-- this one only generates level events
  (cyc 'fa "lvl:0.1 lvl:0.3 lvl:0.6 lvl:0.4"))
```

as the receiver doesn't really know what to do with it, it interprets it as a sine.

This allows you to combine a sound generator with one or many "abstract" event sequence generator
in a serialist manner:

```lisp
(sx 'ba #t
  (cmp ;; <-- the compose function composes the subsequent generators 
    (cyc 'lvls "lvl:0.1 lvl:0.2 lvl:0.2 lvl:0.1 lvl:0.3 lvl:0.05") ;; <-- this loop controls the the levels
    (cyc 'pitches "freq:1000 freq:500 freq:2000") ;; <-- this one the pitches
    (cyc 'sound "risset ~ saw risset sqr"))) ;; <-- this one the type of sound
```

Apart from that, parameter events are not static:

```lisp
(sx 'boom #t
  (pear (lvl (bounce 0.0 0.5)) ;; <-- bounce is a kind of semi-recified sine wave ...  
    (cyc 'bap "bd ~ hats ~ sn ~ hats ~")))
```

The 'bounce' parameter fades in and out ...

Find a list of dynamic parameters (discrete parameter modifiers) in 
the reference documentation !

## every - step-based

The 'every' function applies a modifier every ':n' steps.

```lisp
(sx 'boom #t
  (every :n 10 (rev 0.4)  ;; <-- everything after the ':n' keyword is applied every n steps
    (cyc 'bap "bd ~ hats ~ sn ~ hats ~ ")))

(sx 'boom #t
  (every :n 10 (rev 0.4) (del 0.5) ;; <-- you can specify multiple parameters to be changed
    (cyc 'bap "bd ~ hats ~ sn ~ hats ~ ")))

(sx 'boom #t
  (every :n 12 (rev 0.4) :n 10 (del 0.3) ;; <-- you can also specify multiple n
    (cyc 'bap "bd ~ hats ~ sn ~ hats ~ ")))

(sx 'boom #t
  (every :n 5 (rev 0.6) :for 'sn 'hats :n 7 (del 0.6) ;; <-- same as with pear, you can specify selectors
    (cyc 'bap "bd ~ hats ~ sn ~ hats ~ ")))

(clear)
```
