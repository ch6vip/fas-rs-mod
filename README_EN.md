# **fas-rs-mod**

[![English][readme-cn-badge]][readme-cn-url]
[![Stars][stars-badge]][stars-url]

[readme-cn-badge]: https://img.shields.io/badge/README-简体中文-blue.svg?style=for-the-badge&logo=readme
[readme-cn-url]: README.md
[stars-badge]: https://img.shields.io/github/stars/shadow3aaa/fas-rs?style=for-the-badge&logo=github
[stars-url]: https://github.com/shadow3aaa/fas-rs

## **Introduction**

> If the picture seen by the naked eye can be directly reflected in the scheduling, that is to say, the scheduler is placed from the perspective of the viewer to determine the performance, can perfect performance control and maximized experience be achieved? `FAS (Frame Aware Scheduling)` is this scheduling concept, trying to control performance by monitoring screen rendering to minimize overhead while ensuring rendering time.

- ### **What is `fas-rs`?**

  - `fas-rs` is an implementation of `FAS (Frame Aware Scheduling)` running in user mode. Compared with `MI FEAS` in kernel mode, it has the same core idea but has the advantages of almost universal compatibility and flexibility on any device.
 
- ### **What is `fas-rs-mod`?**
  - `fas-rs-mod` is a modified `fas-rs`, by patches `scene` 's tuner configurations，let `fas-rs` work with `scene` seamlessly.

## **Extension System**

- In order to maximize the flexibility of user mode, `fas-rs` has its own extension system. For development instructions, please see our [extension template repository](https://github.com/shadow3aaa/fas-rs-extension-module-template)

## **Customization (configuration)**

- ### **Configuration path: `/sdcard/Android/fas-rs/games.toml`**

- ### **Parameter (`config`) description:**

  - **keep_std**

    - Type: `bool`
    - `true`: Always keep the standard configuration profile when merging configurations, retain the local configuration application list, and other places are the same as false \*
    - `false`: see [default behavior of config merge](#config merge)

- ### **Game list (`game_list`) description:**

  - **`"package"` = `target_fps`**

    - `package`: string, application package name
    - `target_fps`: an array (such as `[30, 60, 120, 144]`) or a single integer, indicating the target frame rate that the game will render to, `fas-rs` will dynamically match it at runtime

- ### **`powersave` / `balance` / `performance` / `fast` / `pedestal` Description:**

  - **mode:**
    - `fas-rs-mod` relies on [`scene`](http://vtools.omarea.com). By patches scene's tuner configuration, let `fas-rs` work with `scene` seamlessly.
    - If you have some understanding of programming on Linux, you can switch to the corresponding mode by writing any one of the 5 modes to the `/dev/fas_rs/mode` node, and at the same time, reading it can also know the current `fas-rs` mode
  - **Parameter Description:**
    - margin(ms): Allowed frame drop margin. The smaller the value, the higher the frame rate, the larger the value, the more power is saved (0 <= margin < 1000)

### **`games.toml` configuration standard example:**

```toml
[config]
keep_std = true

[game_list]
"example.game" = [30, 60, 90, 120]

[powersave]
margin = 6

[balance]
margin = 4

[performance]
margin = 2

[fast]
margin = 0

[pedestal]
margin = 1
```

## **Configuration merge**

- ### `fas-rs` has a built-in configuration merging system to solve the problem of future configuration function changes. It behaves as follows

  - Delete configurations that do not exist in the local configuration and standard configuration
  - Insert the configuration where the local configuration is missing and the standard configuration exists
  - Retain configurations that exist in both standard and local configurations

- ### Notice

  - Implemented using automatic serialization and deserialization, unable to save non-serialization necessary information such as comments
  - The automatic merged configuration during installation will not be applied immediately, otherwise it may affect the operation of the current version. Instead, the local one will be replaced with the new merged configuration during the next restart.

- ### Manual merge

  - The module will be automatically called once every time it is installed.
  - Manual example

    ```bash
    fas-rs merge /path/to/std/profile
    ```

## **Compile**

```bash
# Ubuntu(NDK is required)
apt install gcc-multilib git-lfs clang python3

# ruff(python lints & format)
pip install ruff

# Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup target add aarch64-linux-android armv7-linux-androideabi x86_64-linux-android i686-linux-android

# Cargo-ndk
cargo install cargo-ndk

# Clone
git clone https://github.com/shadow3aaa/fas-rs
cd fas-rs

# Compile
python3 ./make.py build --release
```
