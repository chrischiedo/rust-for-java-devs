# Packages and Modules

Packages are used in Java to organize types, as well as for controlling the
scope of types and methods in projects.

The equivalent of a package in Rust is a [module][rust-module]. For both Java and Rust, visibility of items can be restricted using access modifiers and visibility modifiers respectively. In Rust, the default visibility is _private_ (with only few exceptions). The equivalent of Java's `public` is `pub` in Rust, and `package-private` corresponds to 
`pub(crate)`. For more fine-grained access control, refer to the [visibility modifiers] reference.

[rust-module]: https://doc.rust-lang.org/reference/items/modules.html
[visibility modifiers]: https://doc.rust-lang.org/reference/visibility-and-privacy.html
