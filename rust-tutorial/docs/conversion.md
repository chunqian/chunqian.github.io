# 类型转换

Rust 使用 [trait][traits] 解决类型之间的转换问题。最一般的转换会用到 [`From`] 
 和 [`into`] 两个 trait。不过，即便常见的情况也可能会用到特别的 trait，尤其是
从 `String` 转换到别的类型，以及把别的类型转换到 `String` 时。

[traits]: rust-tutorial/docs/trait.md
[`From`]: https://doc.rust-lang.org/std/convert/trait.From.html
[`Into`]: https://doc.rust-lang.org/std/convert/trait.Into.html
