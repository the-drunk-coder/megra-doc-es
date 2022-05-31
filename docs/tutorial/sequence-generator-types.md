# Sequence Generator Types

If the sync context is a sink for events, then we need a source. That's what 
the sequence generators are for.
We've already seen one type of generator, the cycle generator. There are several more.
Each sequence generator is fundamentally a *Markov chain* that behaves in a certain way,
depending on how it is created. A sequence generator can behave **non-deterministically**, but
doesn't necessarily have to.

**TIP:**
You can visualize the Markov chains, as they can easily be represented as a graph.
For that end, you need graphviz (https://graphviz.org/) installed! (more later as we go)

Let's look at some generators now. For a complete list, see the 'Sequence Generator' section
in the function reference below. As already mentioned, each generator needs an identifier.
This is so that we can adress them for visualization, and keep their state over the 
various executions.

## The Nucleus Generator

.. or 'nuc', for short ...

```lisp
(sx 'ctx #t ;; <-- a continous beep
  (nuc 'core :dur 400 ;; <-- this keyword argument controls the time interval. 
  ;; It can be used with all sequence generators!
    (sine 440)
    ;;(sine 885) ;; <-- you can pass one or many events, uncomment to try
    ))

(clear)
```

This is the most simple generator, it just repeats the events it is given over and over at a steady time interval. Each generator has a bunch of keyword arguments, as you can see above.

## The Cycle Generator

We've already seen this above in the introduction of contexts. The cyc generator creates, you've guessed it, cycles, even though there's more to it, as we will see.

```lisp
;; Here's a basic cycle:
(sx 'trololo #t ;; <-- there's no need to name every context 'context' ... you don't use 'password' for all your passwords, do you ?
  (cyc 'bells "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5"))
```

```lisp
;; If you want the generator to be faster, you can modify the time interval:
(sx 'trololo # ;; much faster
  (cyc 'bells :dur 100 "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5"))

(sx 'trololo # ;; way slower !
  (cyc 'bells :dur 400 "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5")) 
```

The 'cyc' function takes a string as an argument that describes a cycle. If you already know TidalCycles, the concept is borrowed from that. It is a little sequencing language of its own. 

So far everything has been very deterministic, so what's all the stochastic talk in the readme about ?
Well, take a look at this:

```lisp
(sx 'trololo #t 
  (cyc 'bells :rep 70 :max-rep 4 "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5")) 
```

Doesn't sound as predictable, does it ? So what do the keyword arguments do ? The 'rep' keyword defines
the chance of an event to be repeated (70% chance in this case). The 'max-rep' keyword specifies the 
maximum number of repetitions.  

For the sake of visualization, let's reduce the number of repetitions:

```lisp
(sx 'trololo #t 
  (cyc 'bells :rep 70 :max-rep 2 "risset:'a4 ~ risset:'a6 ~ risset:'a4 ~ risset:'c5 risset:'e5")) 
```

Now, let's see what this markov chain looks like as a graph:

```lisp
(export-dot "trololo" :live 'trololo 'bells)
```

You'll now find a file called "trololo_trololo_bells.dot" in the folder you started Mégra from.
Run `$ neato -Tsvg trololo_trololo_bells.dot -o trololo_trololo_bells.svg` to render an SVG file that you
can run in your browser. You should see the markov chain represented as a graph, where you can see all
the repetitions, etc. Try different settings and see what they look like !

Because of its capacity to create repetetive sequences, the cyc generator is perfect for creating 
beats and so on. 
For a full description, see the entry in the function reference !

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
