# Parts and Control Events

The things we've covered so far help you in a live situation, but Mégra also 
has some features that help in writing scores.

First of all, you can define parts:

```
(defpart 'part-a
  (cyc 'da "risset:'a6 ~ ~ risset:'c5")
  (cyc 'ba "bd ~ sn ~"))

(defpart 'part-b
  (cyc 'da "saw:'a2 ~ saw:'e2 ~")
  (cyc 'ba "bd ~ sn ~"))

(defpart 'part-c
  (xspread
    (pear (freq-mul 1.5) (apple :p 5 (haste 2 0.5)))
    (cyc 'da "saw:'a4 ~ saw:'e4 saw:'f5")))
```

You can play them with a regular context: 

```
(sx 'ba #t
  'part-b) ;; switch between parts by changing the part name

(sx 'ba #t
  'part-b ;; combine multiple parts
  'part-c) 

(clear) ;; <-- clear also clears the parts, so you have to add them again !
```

You can use control events to create scores. Control events can call
any Mégra function from a generator:

```
(sx 'conductor #t
  (infer 'score
    :events 
    'a (ctrl (sx 'ba #t 'part-a)) ;; <-- instead of starting the contexts by hand, use a control event here !
    'b (ctrl (sx 'ba #t 'part-a 'part-c))
    'c (ctrl (sx 'ba #t 'part-b 'part-c))
    :rules
    (rule 'a 'b 50 6400)
    (rule 'a 'c 50 6400)
    (rule 'c 'a 100 6400)
    (rule 'b 'c 100 6400)
    ))
```

While the conductor is running, you can also modify the parts.
You'll hear the result when the conductor arrives at that part
the next time.
