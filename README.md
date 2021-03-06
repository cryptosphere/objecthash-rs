# ObjectHash for Rust [![Latest Version][crate-image]][crate-link] [![Build Status][build-image]][build-link] [![Apache 2 licensed][license-image]][license-link]

[crate-image]: https://img.shields.io/crates/v/objecthash.svg
[crate-link]: https://crates.io/crates/objecthash
[build-image]: https://travis-ci.org/cryptosphere/objecthash-rs.svg?branch=master
[build-link]: https://travis-ci.org/cryptosphere/objecthash-rs
[license-image]: https://img.shields.io/badge/license-Apache2-blue.svg
[license-link]: https://github.com/cryptosphere/objecthash-rs/blob/master/LICENSE

A content hash algorithm which works across multiple encodings (JSON, Protobufs, etc).

This crate provides a Rust implementation of an algorithm originally created by Ben Laurie:

https://github.com/benlaurie/objecthash

### Is it any good?

[Yes.](http://news.ycombinator.com/item?id=3067434)

### Is it "Production Ready™"?

![DANGER: EXPERIMENTAL](https://raw.github.com/cryptosphere/cryptosphere/master/images/experimental.png)

**No!** ObjectHash is an *experimental* algorithm, and is subject to change. Please do not depend on it yet.

Additionally, this is a project of a cryptographic nature and has not received any expert review.

Use at your own risk.

## Installation

You will need to select a supported cryptography library to use as ObjectHash's backend. The following backend libraries
are supported:

* [ring]: A safe, fast, small Rust crypto library based on BoringSSL's cryptography primitives

[ring]: https://github.com/briansmith/ring

Please make sure to add a crypto backend crate or the `objecthash` crate will not work!

## Usage

ObjectHashes can be used to compute a content hash of a deeply nested structure. The intended use is to first
deserialize data into a nested structure, then perform an ObjectHash digest of its contents. This way, the same
content hash to be computed regardless of how the data is serialized, which allows the data to be transcoded between
formats without having to recompute the content hash.

This crate defines a trait called ObjectHash:

```rust
pub trait ObjectHash {
    fn objecthash<H: ObjectHasher>(&self, hasher: &mut H);
}
```

There are built-in implementations of the `ObjectHash` trait for the
following types:

* `Vec<T: ObjectHash>`
* `HashMap<K: ObjectHash, V: ObjectHash>`
* `str`
* `String`
* **Integers:**
  * `i8`
  * `i16`
  * `i32`
  * `i64`
  * `u8`
  * `u16`
  * `u32`
  * `u64`
  * `isize`
  * `usize`

To calculate the ObjectHash digest of some data, call the following:

```rust
let digest: Vec<u8> = objecthash::digest(42);
```

This will compute a digest (using the SHA-256 algorithm) of the given value, provided the type of the value given
implements the ObjectHash trait.


## Macros

The `objecthash_struct!` macro is designed to simplify implementing the ObjectHash trait on structs, producing
a dict-type hash across their keys and values:

```rust
impl ObjectHash for MyStruct {
    #[inline]
    fn objecthash<H: ObjectHasher>(&self, hasher: &mut H) {
        objecthash_struct!(
            hasher,
            "foo" => self.foo,
            "bar" => self.bar,
            "baz" => self.baz
        )
    }
}
```

## TODO

* More types
* More test vectors
* Redaction support

## Contributing

* Fork this repository on Github
* Make your changes and send a pull request
* If your changes look good, we'll merge them

## Copyright

Copyright (c) 2016-2017 Tony Arcieri. Distributed under the Apache 2.0 License.
See LICENSE file for further details.
