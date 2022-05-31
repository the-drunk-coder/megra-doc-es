# Global Commands

## `clear` - Clear Session

Stops and deletes all present generators.

### Examples

```lisp
(sx 'some #t
  (cyc 'bear "bd ~ hats ~ sn ~ hats ~"))

(sx 'more #t :sync 'some
  (cyc 'bass "saw:100 ~"))

(clear) ;; clear everything
```
---

## `connect-visualizer`

---

## `ctrl`

Executes any function, can be used to conduct execution of generators.

### Parameters

* function

### Syntax

```lisp
(ctrl <function>)
```

### Example

```lisp
;; define some parts
(defpart 'bass 	
		(nuc 'bass (saw 100)))
	
(defpart 'mid 
	(nuc 'midrange (saw 1000)))
	
(defpart 'treble 
	(nuc 'treble (saw 5000)))

;; Define a score, here as a learned one, even 
;; though any other generator might be used.
(sx 'ga #t 
	(learn 'ta 
		:events
		'a (ctrl (sx 'ba #t 'bass))
		'b (ctrl (sx 'ba #t 'mid))
		'c (ctrl (sx 'ba #t 'treble))
		:sample "ababaabbababcabaababaacabcabac"
		:dur 2400))
```


---

## `defpart`

Define parts, which are basically lists of generators that you can name.
Parts are not updated in the running sync contexts, that is, when you
modify the part, you have to re-evaluate the sync context as well.

### Syntax
`(defpart <part-id> <generator-list>)`

### Example

```lisp
(defpart 'drum-and-bass 
  (cyc 'drums "bd ~ sn ~")
  (nuc 'bass (saw 100)))
	
(defpart 'hats-and-mid 
  (cyc 'hats "hats hats ~ hats")
  (nuc 'midrange (saw 1000)))
	
(defpart 'treble-and-cym
  (cyc 'cym "cym ~ cym cym")
  (nuc 'treble (saw 2000)))

;; Define a score, here as a learned one, even 
;; though any other generator might be used.
(sx 'ga #t 
  (learn 'ta 
    :events 
    'a (ctrl (sx 'ba #t 'drum-and-bass))
    'b (ctrl (sx 'ba #t 'hats-and-mid))
    'c (ctrl (sx 'ba #t 'treble-and-cym))
    :sample "ababaabbababcabaababaacabcabac"
    :dur 2400))
```

---

## `export-dot` - Export to DOT File

Export a generator (or at least its underlying structure) to a DOT file that can be 
rendered with GraphViz.

### Syntax
`(export-dot <filename> <generator> or <keyword> and <tag list>)`

### Example
```
;; if a generator is provided, it will be exported as a DOT file directly
(export-dot "dotdotdot.dot"
	(cyc 'bu "saw ~ saw ~ saw ~"))
	
;; if the keyword "live" and a tag list are provided, all running generators matching the tag list will be exported
(sx 'ba #t 
  (cyc 'bu "bd ~ sn ~"))
  
(export-dot "babu" :live 'ba 'bu)

;; if a part is defined ... 
(defpart 'ga
	(cyc 'du "cym cym cym cym")
	(cyc 'ba "bd ~ sn ~"))
	
;; you can export it using the "part" keyword
(export-dot "partpart" :part 'ga)

```
---

## `rec` - Record Session

Allows you to record your session directly from Megra.
The file will end up in the recordings folder that lies
next to the sketchbook folder.

Only one recording can be running at a time.

As a current limitation, the number of inputs can't be bigger 
than the number of outputs.

Also, even if there's only one input, the input recording file will have 
the same amount of channels as the output file.

Recording format is 32-bit float WAV.

### Example

```lisp

;; record with prefix, record input
(rec "pref" :input #t)

;; use default prefix, don't record input
(rec)

;; stop recording
(stop-rec)
```


---

## `step-part` - Evaluate Parts Step by Step

Define a part and evaluate it step by step. This exists mostly for debugging purposes.

### Example

```lisp
(defpart 'ba ;; <-- define some part
  (cyc 'hu "hats cym hats cym cym hats hats cym")
  (cyc 'du "bd ~ sn ~ bd bd sn ~"))

(step-part 'ba) ;; <-- step through ...
```

---

## `sx` - Event Sink

Short for `SyncconteXt`.

### Example

```lisp
(sx 'simple #t
  (nuc 'beat (bd) :dur 400))

(sx 'simple2 #t :sync 'simple :shift 200
  (nuc 'beat2 (sn) :dur 400))
  
;; you can solo and mute by tag ...
  
(sx 'solo #t :solo 'bd ;; <-- solo all events tagged 'bd'
  (nuc 'snare (sn) :dur 400)
  (nuc 'hats (hats) :dur 400)
  (nuc 'bass (bd) :dur 400))

(sx 'solo #t :block 'bd ;; <-- block all events tagged 'bd'
  (nuc 'snare (sn) :dur 400)
  (nuc 'hats (hats) :dur 400)
  (nuc 'bass (bd) :dur 400))
```
## `stop-rec`

Stop recording, see above ...

### Syntax

```
(stop-rec)
```


