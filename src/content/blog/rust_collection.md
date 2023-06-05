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

## Box<dyn Error>
在 Rust 中，`Box<dyn Error>`是一个动态多态的错误类型，通常用于表示可能的错误情况。该类型表示一个指向堆上分配的、实现了`Error` trait的任意类型的指针，这种方式可以使得代码更加灵活和可扩展。

`Box<dyn Error>`通常用于在函数或方法返回可能失败的结果时，作为错误类型的返回值类型。例如，在下面的代码中，`Result`枚举的`Err`分支返回了一个`Box<dyn Error>`类型的错误：

```rust
use std::error::Error;
use std::fs::File;

fn read_file(filename: &str) -> Result<String, Box<dyn Error>> {
    let mut file = File::open(filename)?;
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}
```

在上面的代码中，`read_file`函数尝试打开指定的文件并将其内容读取到一个字符串中。如果出现错误，函数将返回一个`Result`枚举的`Err`分支，其中的错误类型是一个`Box<dyn Error>`，这意味着可以返回任何实现了`Error` trait的错误类型。

使用`Box<dyn Error>`的好处是，它可以将具体的错误类型抽象出来，从而允许用户在错误处理时更加灵活。例如，用户可以使用`match`表达式来匹配特定的错误类型，或者使用`err.downcast_ref::<MyError>()`方法来从`Box<dyn Error>`类型中提取出特定类型的错误。

https://github.com/rust-lang/rust/issues/70887

## matches!
```rust
// dst type is InnerDrain
matches!(dst, InnerDrain::NotProvided)
```

## fs::canonicalize
```rust
// 获取标准换路径
use std::fs;
use std::path::PathBuf;

fn main() {
    let path = PathBuf::from("/usr/local/../bin");
    let canonical_path = fs::canonicalize(&path).unwrap();
    println!("Canonical path: {:?}", canonical_path);
}
```

## unreachable!
```rust
// 为了编译通过的不可以到达的宏
match &src {
    InnerSource::Stdin => Source::Stdin,
    InnerSource::File(src) => Source::File(src.clone()),
    InnerSource::Dir(_) => unreachable!(),
}
```

## std::ffi::OsStr
在 Rust 标准库中，`std::ffi::OsStr` 是一个用于表示操作系统本地字符串的类型。它是一个不可变的字符串类型，通常用于在 Rust 代码和操作系统 API 之间进行交互。`OsStr`的名称代表"操作系统字符串"（Operating System String）。

`OsStr`的主要特点是它可以在不同的操作系统之间进行透明地转换。在不同操作系统中，字符串可能具有不同的编码和表示方式，例如在 Windows 中使用 UTF-16 编码，在 Unix 系统中使用 UTF-8 编码。`OsStr`可以自动识别并处理这些差异，从而使得 Rust 代码更加可移植和跨平台。

`OsStr`通常与 `std::ffi::OsString` 和 `std::os::unix::ffi::OsStrExt` 等其他相关类型和 trait 一起使用，以便进行操作系统字符串的创建、转换和处理。例如，可以使用 `OsStr` 和 `OsString` 类型来表示文件路径和命令行参数等系统相关的字符串数据。

## vec!
```rust
// Create a [Vec] from a given element and size:
let v = vec![1; 3];
assert_eq!(v, [1, 1, 1]);
```

## trait Into
```rust
struct Person {
    name: String,
    age: u32,
}

impl Into<String> for Person {
    fn into(self) -> String {
        format!("{} ({})", self.name, self.age)
    }
}

fn main() {
    let person = Person {
        name: "Alice".to_string(),
        age: 30,
    };
    let name_and_age: String = person.into();
    println!("{}", name_and_age);
}
```

## zip
将两个vec合并压缩为一个vec的工具函数
```rust
let a1 = [1, 2, 3];
let a2 = [4, 5, 6];
// [(1,4),(2，5),(3,6)]
let mut iter = a1.iter().zip(a2.iter());

assert_eq!(iter.next(), Some((&1, &4)));
assert_eq!(iter.next(), Some((&2, &5)));
assert_eq!(iter.next(), Some((&3, &6)));
assert_eq!(iter.next(), None);
```
