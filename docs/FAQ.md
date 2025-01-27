# FAQ

## General

### Will Tetra be written in pure Rust eventually?

Probably not - SDL2 is a stable and well-tested foundation for building games, and it runs on basically every platform under the sun, so I'm hesitant to replace it. That's likely to remain the only non-Rust dependency, however.

If you're looking for a similar engine that *is* working towards being pure Rust, [GGEZ](https://github.com/ggez/ggez) and [Quicksilver](https://github.com/ryanisaacg/quicksilver) are excellent options.

### Why is it called Tetra?

I'm terrible at naming projects, and [this](https://www.youtube.com/watch?v=g3xg28yaZ5E) happened to be playing when I was typing `cargo new`. I wish there was a better origin story than that :D

### Do I have to install SDL manually?

It's possible to have your project automatically compile SDL2 from source as part of the build process. To do so, specify your dependency on Tetra like this:

```toml
[dependencies.tetra]
version = "0.2"
features = ["sdl2_bundled"]
```

This is more convienent, but does however require you to have various build tools installed on your machine (e.g. a C compiler, CMake, etc). In particular, this can be a pain on Windows - hence why it's not the default!

### Can I static link SDL?

If you want to avoid your users having to install SDL2 themselves (or you having to distribute it as a DLL), you can specify for it to be statically linked:

```toml
[dependencies.tetra]
version = "0.2"
features = ["sdl2_static_link"]
```

This comes with some trade-offs, however - make sure you read [this document](https://hg.libsdl.org/SDL/file/default/docs/README-dynapi.md) in the SDL2 repository so that you understand what you're doing!

## Compatibility

### Why am I getting a black screen?

First, check the debug output on the console to see which OpenGL version your drivers are using - Tetra primarily aims to support 3.2 and above, and explicitly will **not** work with anything lower than 3.0.

If your OpenGL version is higher than 3.0 and you're still getting a black screen, that may indicate a bug - I currently only have access to a Windows machine with a reasonably modern graphics card, so it's not outside the realms of possibility that something that works for me might be broken for others! Please submit an issue, and I'll try to fix it and release a patch version.

## Performance

### Why is my game running slow?

Cargo builds projects in debug mode by default. This can lead to your game running slowly, as the compiler does not fully optimize your code.

Optimizations can be enabled by passing `--release` when building/running your project, but this increases build times quite significantly and removes debug info from the binary, meaning you cannot easily debug or profile your code.

To work around this, add one of the following snippets to your `Cargo.toml`:

```toml
# To enable optimizations in debug mode for Tetra only (requires Rust 1.41):
[profile.dev.package.tetra]
opt-level = 3

# To enable optimizations in debug mode for all dependencies (requires Rust 1.41):
[profile.dev.package."*"]
opt-level = 3

# To enable optimizations in debug mode for the entire project (works with all Rust versions):
[profile.dev]
opt-level = 3
```

Choosing one of the first two options is preferred, as they will not slow down rebuilds of your game's code.

You should also make sure to build with `--release` when distributing your game, so that the final binary is as fast as possible.

#### Benchmarks

The impact of compiler optimizations can be observed by running the `bunnymark` example both with and without the `--release` flag. This example adds 100 new sprites to the screen every time the user clicks, until rendering conistently drops below 60fps.

These were the results when I ran it against Tetra 0.2.9 on my local machine:

| Configuration | Bunnies Rendered |
| --- | --- |
| Debug | 3200 |
| Release | 230000 |

For reference, my system specs are:

* CPU: AMD Ryzen 5 1600 3.2GHz
* GPU: NVidia GeForce GTX 1050 Ti
* RAM: 8GB DDR4


