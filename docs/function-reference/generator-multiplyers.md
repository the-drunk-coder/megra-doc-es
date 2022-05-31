# Generator Multiplyers

## `ls` - Create Generator List

If you want to modifiy multiple generators, you can collect them in a list.

### Example

```lisp
(sx 'ba #t
  (pear (rev 0.1) 
    (ls ;; thanks to ls, you can apply the reverb to everything at once 
      (cyc 'drum "bd ~ sn ~")
      (cyc 'bass "saw:'a1 ~ ~ ~"))))
```

---

## `xdup` - Multiply Generators with Modifiers

If you want to juxtapose (obvious reference here) a generator with a modified copy of itself,
without re-writing the whole generator. 

### Example
```lisp
(sx 'more #t
    (xdup
     (cmp ;; this is the copy with modifiers ...
      (pear (freq-mul 2.0) (rev 0.1))
      (every :n 20 (haste 2 0.5)))     
     ;; this is the "original" 
     (cyc 'one "tri:'f3 tri:'a3 tri:'c4 tri:'e4 ~ ~ tri:'f3 tri:'a3 tri:'c4 tri:'e4 ~")))
```

---

## `xspread` - Multiply Generators with Modifiers and spread over Channels.

If you want to juxtapose (obvious reference here) a generator with a modified copy of itself,
without re-writing the whole generator. As opposed to `xdup`, this one spreads the copies over
the available loudspeakers/channels, or spatial spectrum (once binaural stereo or ambisonics are available).

### Example
```lisp
(sx 'more #t
    (xspread
     (cmp ;; this is the copy with modifiers ...
          (pear (freq-mul 2.0) (rev 0.1))
          (every :n 20 (haste 2 0.5)))
     (cmp ;; this is another copy with modifiers ...
          (pear (freq-mul 4.02) (rev 0.1))
          (every :n 20 (haste 3 0.5)))     
     ;; this is the "original" 
     (cyc 'one "tri:'f3 tri:'a3 tri:'c4 tri:'e4 ~ ~ tri:'f3 tri:'a3 tri:'c4 tri:'e4 ~")))
```
