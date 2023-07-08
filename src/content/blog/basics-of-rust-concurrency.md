---
author: cosin
pubDatetime: 2023-07-08T14:42:16+08:00 
title: rust atomics and locks char1
postSlug: basics-of-rust-concurrency
featured: true
draft: true
tags:
  - note
description:
  学习rust异步和多线程的笔记
---

## Threads in rust
```rust
use std::thread;
fn main() {
    let t1 = thread::spawn(f);
    let t2 = thread::spawn(f);

    println!("Hello from the main thread.");

    t1.join().unwrap();
    t2.join().unwrap();
}
```
ownership of numbers is transferred to the newly spawned thread, since we used a move closure.

```rust
let numbers = vec![1, 2, 3];

thread::spawn(move || {
    for n in &numbers {
        println!("{n}");
    }
}).join().unwrap();
```
## statics
