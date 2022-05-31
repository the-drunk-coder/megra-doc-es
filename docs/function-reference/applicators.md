# Applicators

## `apple` - Probability-Based

Modify a generator with a certain probability

### Example

```lisp
(sx 'ba #t 
    (apple :p 10 (skip 2) :p 9 (rewind 2) ;; skip with probability of 10%, rewind with chance of 9%
        (cyc 'ba "bd ~ hats ~ sn ~ hats ~")))
```

---

## `every` - Count-Based Generator Manipulators

Every so-and-so steps, do something with the generator or event stream.

### Examples

```lisp
(sx 'simple #t
    (cmp 
	    (every :n 20 (skip 2) :n 33 (rewind 2)) ;; <- every 20 steps, skip 2, every 33, rewind 2
        (cyc 'beat "bd ~ hats ~ sn ~ hats ~")))
```

## `exh` - Event Stream Manipulator

Exhibit event type, that is, mute all other events, with a certain probability.

### Parameters

* probablility (int) - exhibit probablility
* filter (filter function) - event type filter

### Syntax
```lisp
(exh <probability> <filter>)
```

### Example
```lisp
(sx 'simple #t 
  (cmp 
      (exh 30 'hats)
      (exh 30 'bd)
      (nuc 'beat (bd) (sn) (hats)))) 
```

## `inh` - Event Stream Manipulator

Inhibit event type, that is, mute event of that type, with a certain probability.

### Parameters

* probablility (int) - inhibit probablility
* filter (filter function) - event type filter

### Syntax

```lisp
(inh <probability> <filter>)
```

### Example

```lisp
(sx 'simple #t
  (cmp (inh 30 'hats)
       (inh 30 'bd)
       (inh 30 'sn)
       (nuc 'beat (bd) (sn) (hats))))
```

---

## `pear` - Probability-Based

Appl-ys and Pears (don't ask me why it's named like this, I like good pears and found it funny).

### Example

```lisp
(sx 'ba #t
    (cmp
        (pear (freq-mul 1.5)) ;; <- always multiply frequency by 1.5
		(pear :p 10 (freq-mul 0.5) :p 20 (freq-mul 2.0)) ;; <- with a probablility of 10%, multiply with 0.5, and 20% multiply by two
		(pear :p 20 (rev 0.2)) ;; <- with a probability of 20%, apply reverb
		(pear :for 'sqr :p 20 (freq-mul 1.7)) ;; <- only for sqr events, apply a multiplicator with a chance of 20%
	    (cyc 'ta "saw:150 ~ sqr:100 ~ saw:150 ~ sqr:100")))
```

