---
layout: default
title: Projects
permalink: /projects/
navigation_weight: 2
---

## Projects

The following are a couple of projects I have released as open-source.

### Slang

[Github link](https://github.com/damonswayn/slang)

Slang (short for Simple LANGuage) is an interpreted language built in Rust with the goal being to build a toy language that could actually be somewhat usable for novelty projects.

The syntax is C-like and should be very familiar to anyone who has written JavaScript.

**Features**
- basic arithmatic with proper precendence handling
- variable definitions
- control statements (if, while and for-loops)
- function definitions
- object literal definitions with `this` support
- class definitions (basically templates for creation of object literals)
- higher-order functions (partial function application)
- built-in monad support
- type-casting
- namespaces and namespace export/import
- small but useful standard library

### Built-in pub/sub via tags

The weird idea I experimented with when it comes to Slang is the language supports publishing and subscribing to messages as a language feature. Tags are defined by the `(:TagName)` syntax and messages are published to tags via the `-> :TagName` syntax.

```
(:Square)
function square(values) {           // one-arg subscriber gets an array
    let n = values[0];
    let s = n * n;
    print(s);
    return s;
}

(:Print)
function output(values) {           // also one-arg -> array
    print(values[0]);
}

let a = 5;
let b = 7;

a + b -> :Square -> :Print          // prints 144 twice in the sample test
```

## SLED

[Github link](https://github.com/damonswayn/sled)

SLED (short for Simple Language EDitor) is a simple TUI text-editor designed specifically for editing Slang programs. it is written in golang.

**Features**
- syntax highlighting for Slang
- shortcut for executing the currently focused Slang file and printing the output (if Slang is in your `$PATH`)
- multiple buffers allowing for multiple file editing in one session
- cursor movement shortcuts for moving to beginning/ending of word/line.

## Agentconfig

[Github link](https://github.com/damonswayn/agentconfig)

Agentconfig is a CLI tool built to try and handle the sprawl of various coding agent tools and their various config directories and naming conventions by allowing you to have a single source of truth folder that is sync'ed across all config directories.

In typical fashion AI-driven development fashion, this is a tool built by AI to solve an AI problem.