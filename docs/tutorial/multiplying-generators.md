# Layering (or Multiplying) Generators

The idea behind multipliers is to provide a shorthand for layers.

Imagine the following ... you want to create a slightly modified version
of the same cycle and spred them over the stereo spectrum. (If you already know 
TidalCycles, jux rev comes to mind here)

```
(sx 'ba #t 
  (pear (rate-mul 0.8) (pos -1) 
    (every :n 32 (haste 2 0.5) 
      (cyc 'fa "bd hats sn hats")))
  (pear (pos 1) (cyc 'fa2 "bd hats sn hats")))
```

## Multiply and Spread

The 'xspread' multiplyer provides a convenient shorthand for that!
here you only need to type the original generator and the modifiers 
you want to apply:

```
(sx 'ba #t
  (xspread
    (pear (rate-mul 0.8) (every :n 32 (haste 2 0.5)))
    (cyc 'fa "bd hats sn hats")))
```

Every modifier (or chain of modifiers) adds another layer:

```
(sx 'ba #t
  (xspread
    (pear (rate-mul 1.5) (sus 50) :p 30 (rev 0.3) (apple :p 10 (rewind 3)))
    (pear (rate-mul 0.8) (every :n 32 (haste 4 0.5)))
    (cyc 'fa "bd hats sn hats")))
```

If you're working with more than 2 channels, the sound will be spread over the available channels (i.e. in an 8-channel setup).

## Just Multiply

If you don't want to spread them over the stereo spectrum, 
the 'xdup' multiplyer skips that:

```
(sx 'ba #t
  (xdup
    (pear (rate-mul 1.5) (sus 50) :p 30 (rev 0.3) (apple :p 10 (rewind 3)))
    (pear (rate-mul 0.8) (every :n 32 (haste 4 0.5)))
    (cyc 'fa "bd hats sn hats")))
```

You can apply modifiers to the multiplied result:

```
(sx 'ba #t
  (pear (lvl 0.4) ;; <-- modify level of multiplied generators
    (xspread
      (pear (rate-mul 0.8) (every :n 32 (haste 2 0.5)))
      (cyc 'fa "bd hats sn hats"))))
```

## Generator List

In fact, the 'ls' function puts the subsequent generators in a list so you can 
modify them as a whole:

```
(sx 'context #t ;; <-- set this to #f to mute this context !
  (pear 
    :for 'sn :p 20 (rev 0.3) 
    :for 'percussion-generator :p 30 (del 0.5)
    (ls ;; <-- collect generators in list
      (cyc 'percussion-generator "~ ~ ~ risset:'a5 ~ ~ risset:'c6 ~")
      (cyc 'bassline-generator "saw:'a1 ~ saw:'ds2 saw:'e2 ~ ~ saw:'c3 ~")
      (cyc 'beat-generator "bd ~ hats ~ sn ~ hats ~")))) 

(clear)
```
