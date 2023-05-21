---
author: cosin
pubDatetime: 2023-05-19T10:00:23+08:00 
title: rust杂项记录
postSlug: ""
featured: false
draft: true
tags:
  - color-schemes
  - docs
ogImage: ""
description:
  记录学习rust细碎记录
---

### 移除未用到的依赖
```shell
cargo install cargo-udeps
cargo +nightly udeps
```

### 初次初始化(避免每次都需要初始化)crate
```rust
use std::{sync::Mutex, collections::HashMap};
use once_cell::sync::Lazy;

static GLOBAL_DATA: Lazy<Mutex<HashMap<i32, String>>> = Lazy::new(|| {
    let mut m = HashMap::new();
    m.insert(13, "Spica".to_string());
    m.insert(74, "Hoyten".to_string());
    Mutex::new(m)
});

fn main() {
    println!("{:?}", GLOBAL_DATA.lock().unwrap());
}
```

### secrecy 避免密码输出
```shell
cargo add secrey
```
```rust
use secrecy::Secret;

#[derive(serde::Deserialize)]
pub struct DatabaseSettings {
  pub password: Secret<String>,
}

// 需要暴露密码则使用expose_secret
format!("{}", database_settings.password.expose_secret());
```

### rust 格式化字符串语法
https://doc.rust-lang.org/1.53.0/std/fmt/#%E8%AF%AD%E6%B3%95
