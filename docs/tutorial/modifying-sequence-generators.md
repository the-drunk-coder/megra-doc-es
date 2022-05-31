# Modifying Sequence Generators

One of Mégra's interesting features is that you can modify the generators themselves, or their execution speed and order, again, both over discrete time (steps) and probabilistically.

For a full list of modifiers that can be applied, see the "Generator Modifiers" section in the function reference.

## apple - probability-based

```lisp
(sx 'ba #t 
  (apple :p 20 (haste 2 0.5) ;; <-- with a probability of 20%, speed up for 2 steps, cut the duration in half
    (cyc 'go "sqr:120 sqr:180 ~ sqr:80")))
```

This only modifies the execution speed. You can also modify execution order.
In the following example, we skip ahead 2 steps with a chance of 9%, or rewind 2 steps with a chance of 10%

```lisp
(sx 'ba #t 
  (apple :p 10 (rewind 2) :p 9 (skip 2) 
    (cyc 'go "sqr:120 sqr:180 ~ sqr:80")))
```

Finally, you can modify the generator itself by adding new information based on its history:

```
(sx 'ba #t
  (apple :p 10 (grow 0.5 :method 'flower) 
    (cyc 'ta "sqr:'c2 sqr:'a3 sqr:'e3 sqr:'f4"))) ;; <-- you can use note names !
```

The 'grow' function takes a sound event from the generator, adds a certain amount of variation (the first argument, wheren 0.0 means no variation and 1.0 means a lot of variation).

Let it run for a while and then check what it looks like:

```
(export-dot "grown" :live 'ba 'ta)
```

## every - step-based

The step-based version actually fills both roles:

```lisp
(sx 'ba #t 
  (every :n 12 (haste 2 0.5) ;; <-- every 12 steps, speed up for 2 steps, cut the duration in half
    (cyc 'go "sqr:120 sqr:180 ~ sqr:80")))
```
