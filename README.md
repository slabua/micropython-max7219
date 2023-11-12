# MicroPython Max7219 8x8 LED Matrix and 7 Segment Displays*
![PyPI](https://img.shields.io/pypi/v/micropython-max7219)
![PyPI - Implementation](https://img.shields.io/pypi/implementation/micropython-max7219)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/micropython-max7219)
![PyPI - License](https://img.shields.io/pypi/l/micropython-max7219)
![PyPI - Downloads](https://img.shields.io/pypi/dm/micropython-max7219)

A MicroPython library for the MAX7219 8x8 LED matrix driver using the SPI interface. Supporting custom fonts and cascading matrices.

## Features
- Cascading matrices together
- Custom font support
- Extends the [FrameBuffer](http://docs.micropython.org/en/latest/pyboard/library/framebuf.html) class
- Removes the need to specify offset for text method
- Extendable


## Examples
### Raspberry Pi Pico

| Pico      | max7219 |
| :-------- | :------ |
| 40 (VBUS) | VCC 5V  |
| 38 (GND)  | GND     |
| 5 (GP3)   | DIN     |
| 7 (GP5)   | CS      |
| 4 (GP2)   | CLK     |

```python
from machine import Pin, SPI
from max7219 import Matrix8x8

spi = SPI(0, baudrate=10000000, polarity=1, phase=0, sck=Pin(2), mosi=Pin(3))
ss = Pin(5, Pin.OUT)

display = Matrix8x8(spi, ss, 4)

# change brightness 1-15
display.brightness(5)

# clear display
display.zero()
display.show()

# show text using FrameBuffer's font
display.text("CODE")
display.show()
```


## Docs
- For any change to the `FrameBuffer` to appear, call the `.show()` method
- You may have to add delays when calling methods, these are documented in the datasheet
- Tested on 1.19.1

### Display Text

```python
display = Matrix8x8(...)

display.zero()
display.text("HI!")
display.show()
```

### Custom Fonts
Custom fonts (glyphs) can be provided, each glyph must be 8x8. Missing characters will use default characters from `FrameBuffer`.

```python
GLYPHS = {
    "X": [
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 1, 0, 0, 0, 0, 1, 0],
        [0, 0, 1, 0, 0, 1, 0, 0],
        [0, 0, 0, 1, 1, 0, 0, 0],
        [0, 0, 0, 1, 1, 0, 0, 0],
        [0, 0, 1, 0, 0, 1, 0, 0],
        [0, 1, 0, 0, 0, 0, 1, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
    ],
}

display = Matrix8x8(...)
display.text_from_glyph("X", GLYPHS)
```

### Format Options
Setting the different format options for the display.

```python
#Test Different Format Options

from framebuf import MONO_HLSB, MONO_HMSB, MONO_VLSB

display = max7219.Matrix8x8(spi, ss, 1, MONO_VLSB)
display.fill(0)
display.text("P")
display.show()
time.sleep(0.5)
display = max7219.Matrix8x8(spi, ss, 1, MONO_HMSB)
display.fill(0)
display.text("P")
display.show()
time.sleep(0.5)
display = max7219.Matrix8x8(spi, ss, 1, MONO_HLSB)
display.fill(0)
display.text("P")
display.show()
```

### Shutdown / Wake
Shutting down and waking up the display is supported. This should allow a device to consume just 150μA when it's not needed. When the display is woken from shutdown the previous display should appear.

```python
# shutdown display
display.shutdown()

# wake from shutdown
display.wake()
```

### Test Mode
Test mode will enable all pixels, shutdown mode has no effect when testing mode is enabled.

```python
# enable test mode
display.test()
# disable test mode
display.test(False)
```

### Extending
#### Scrolling Text
Although not built-in you could add scrolling text like shown in the following example:

```python
from utime import sleep_ms

from max7219 import Matrix8x8

class Matrix8x8Ext(Matrix8x8):
    def scroll_text(self, s, ms_delay=100):
        s_width = len(s) * 8
        n_pixels = self.num * 8
        while True:
            for x in range(n_pixels, -s_width, -1):
                self.zero()
                self.text(s, x)
                self.show()
                sleep_ms(ms_delay)
        return s_width
```


## Attribution*
- Original code by [@mcauser](https://github.com/mcauser/micropython-max7219)
- [Data-Sheet](https://www.analog.com/media/en/technical-documentation/data-sheets/max7219-max7221.pdf)

- 7 segments code by [@JennaSys](https://github.com/JennaSys/micropython-max7219)
  -  Please refer to JennaSys repository for the README and examples.


## License
Licensed under the [MIT License](http://opensource.org/licenses/MIT), found in `LICENSE.txt`.
