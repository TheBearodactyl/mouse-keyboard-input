Easy to use `uinput` wrapper for Rust.

**Much faster (lower latency) than any other library for input emulation on Linux.**

Allows you to send keyboard and mouse events by creating a virtual device in Linux.

`uinput` is a basic Linux library, so this works on any distro and on `X11` or `Wayland`.

High-resolution events are sent for the mouse wheel, allowing smoother scrolling and better precision.

Lib is safe by design, resources are released automatically when `VirtualDevice`'s destructor is called. Dependencies are up-to-date in contrast to other `uinput` libs for Rust.


## Installation
To use it without `sudo` add the current user to input group (replace `user` with your username):
```
sudo usermod -a -G input user
sudo reboot
```

### Libraries required

On Ubuntu and Debian:
```
sudo apt install libudev-dev libevdev-dev libhidapi-dev
```

Add to `Cargo.toml`
```
mouse-keyboard-input = "0.9.1"
```
To use the latest development version:
```
mouse-keyboard-input = { git = "https://github.com/positiveway/mouse-keyboard-input", branch = "main" }
```

#### armv7 build on linux
Build script can be found in [/scripts/build_armv7.sh](https://github.com/positiveway/mouse-keyboard-input/blob/main/scripts/build_armv7.sh)
```
cd ./scripts
sudo chmod +x ./build_armv7.sh #make script executable
./build_armv7.sh
```
For this to work `arm-linux-gnueabi-gcc` needs to be installed

For Ubuntu:
```
sudo apt install gcc-arm-linux-gnueabi
```
Reboot is required after installing the package

For more details read [official discussion](https://github.com/rust-lang/cargo/issues/11212)

## How to use
### Functions
```
click(button_or_key) - click mouse button or type a key
press(button_or_key)
release(button_or_key)

smooth_move_mouse(x, y) - gradually move mouse from the current position on screen by (x, y) pixels. this method is preferred

move_mouse(x, y) - move mouse instantly
move_mouse_x(value) - move mouse instantly
move_mouse_y(value)- move mouse instantly

smooth_scroll(x, y) - gradually scroll. this method is preferred

scroll_x(value) - instantly scroll horizontally
scroll_y(value) - instantly scroll vertically
```
### List of buttons
#### Mouse
```
BTN_LEFT - left mouse button
BTN_RIGHT - right mouse button
BTN_MIDDLE - middle mouse button
```

#### Keyboard
Key codes can be found in [/src/key_codes.rs](https://github.com/positiveway/mouse-keyboard-input/blob/main/src/key_codes.rs)

Example:
```
KEY_A
KEY_LEFTSHIFT
KEY_LEFTMETA (Meta means Windows button on Linux)
```

### Code examples
#### Mouse
```
use mouse_keyboard_input::VirtualDevice;
use mouse_keyboard_input::key_codes::*;
use std::thread;
use std::time::Duration;

fn main() {
    let mut device = VirtualDevice::default().unwrap();

    for _ in 1..3 {
        thread::sleep(Duration::from_secs(1));

        // gradually scroll down by 100
        device.smooth_scroll(0, -100).unwrap();
        // gradually move cursor 250 pixels up and 250 pixels to the right from the current position
        device.smooth_move_mouse(250, 250).unwrap();
        //click the left mouse button
        device.click(BTN_RIGHT).unwrap();
    }

    for _ in 1..2 {
        thread::sleep(Duration::from_secs(1));

        // scroll down by 100
        device.scroll_y(-100).unwrap();
        // instantly move cursor 250 pixels up and 250 pixels to the right from the current position
        device.move_mouse(250, 250).unwrap();
        //click the left mouse button
        device.click(BTN_RIGHT).unwrap();
    }
}
```
#### Keyboard
```
use mouse_keyboard_input::VirtualDevice;
use mouse_keyboard_input::key_codes::*;
use std::thread;
use std::time::Duration;

fn main() {
    let mut device = VirtualDevice::default().unwrap();

    thread::sleep(Duration::from_secs(2));

    // type hello
    for key in [KEY_H, KEY_E, KEY_L, KEY_L, KEY_O] {
        device.click(key).unwrap();
    }
}
```

### Sending events from multiple threads is also supported. See [/examples/channels.rs](https://github.com/positiveway/mouse-keyboard-input/blob/main/examples/channels.rs)

## Contributors
Based on [github.com/meh/rust-uinput](https://github.com/meh/rust-uinput)