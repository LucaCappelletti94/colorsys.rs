# colorsys

[![Crates.io](https://img.shields.io/crates/v/colorsys.svg)](https://crates.io/crates/colorsys/)

A module for color conversion and mutation written in Rust. 
For now works with next color models:
 - RGB(a)( as hexadecimal too)
 - HSL(a)
 - CMYK(a)
 - ANSI256 codes

[Documentation](https://docs.rs/colorsys)



## What It Can Do

#### getters & setters
```rust
use colorsys::{Rgb, Hsl, ColorAlpha};

let rgb = Rgb::from((57.3, 12.7, 53.0));
let r = rgb.red();
// 57.3

let mut hsl = Hsl::default();
// Hsl { h: 0, s: 0, l: 0, a: 1 }
hsl.set_saturation(13.98);
hsl.set_saturation(305.71);
hsl.set_alpha(0.75);
// Hsl { h: 305.71, s: 13.98, l: 0, a: 0.75 }
```

#### conversion
See `From/FromStr/Into` traits implementation in docs for more info
```rust
use colorsys::{Rgb, Hsl};

let rbga_tuple = (57.3, 12.7, 53.0, 0.33);
let rgba = Rgb::from(&rbga_tuple);
let hsla: Hsl = rgba.as_ref().into();
// ~Hsl { h: 305.78, s: 63.71, l: 13.73, a: 0.33 }

let rgb_arr: [u8; 3] = Rgb::from(&hsla).into();
// ~[57, 13, 53]

let hsla_tuple: (f64,f64,f64,f64) = Hsl::from( Rgb::from(rgb_tuple) ).into();
// ~Hsl { h: 305.78, s: 63.71, l: 13.73, a: 1 }

let hex: String = rgba.to_hex_string();
// #390d35

//
// From/Into
//
let rgb1 = Rgb::from_hex_str("37ea4c").unwrap();
  
let rgb2 = Rgb::from(
Into::<[f32; 4]>::into(Rgb::from(
  Into::<[u8; 3]>::into(
    Rgb::from(
      Into::<(i32,i32,i32)>::into(
        Rgb::from(
          Into::<[i64; 3]>::into(&rgb1)
        )
      )
    )
  )
))
);

assert_eq!(rgb1, rgb2);

//
// Ratio
//
use colorsys::{RgbRatio, ApproxEq};
let blue = Rgb::from([34, 111, 235]);

let ratio: [f32; 4] = blue.as_ratio().into();
// ~[0.133, 0.435, 0.922, 1.0]

let converted: Rgb = RgbRatio::from(&ratio).into();
assert!(blue.approx_eq_clarify(&converted, 0.0001));
```


#### modification
See `ColorTransform/Add*/Sub*..` traits in docs for more
```rust
use colorsys::{Hsl, Rgb, ColorTransform,ColorAlpha, SaturationInSpace};

let mut rgb: Rgb = (245.0,152.0,53.0).into();

rgb.lighten(20.1);
// ~Rgb { r: 249.83, g: 201.80, b: 150.67 }

rgb.opacify(-0.7);
rgb.saturate( SaturationInSpace::Hsl(-35.7) );
// ~Rgb { r: 230.29, g: 201.19, b: 170.21, a: 0.3 }

rgb.grayscale_simple();
// ~Rgb { r: 200.255, g: 200.255, b: 200.255, a: 0.3 }


let mut hsl = Hsl::from(&rgb);
hsl.opacify(1.0);
// ~Hsl { h: 0.0, s: 0.0, l: 78.53 }

hsl.adjust_hue(231.99);
hsl.saturate(SaturationInSpace::Hsl(55.7));

let mut rgb2: Rgb = hsl.as_ref().into();
// ~Rgb { r: 169.76, g: 177.9, b: 230.75}

rgb2.invert();
// ~Rgb { r: 85.24, g: 77.09, b: 24.25 }

let rgb3 = rgb - rgb2;
// ~Rgb { r: 115.01, g: 123.16, b: 176.0 }

let hsl2 = hsl + rgb3.into();
// ~Hsl { h: 0.0, s: 83.55, l: 100.0 }

```

#### parsing from string & css string representation
```rust
use colorsys::{Hsl, Rgb};
use std::str::FromStr;

let s = "rgb(177, 255, 176)";

let rgb: Rgb = s.parse().unwrap();

rgb.to_css_string();
// String: "rgb(177,255,176)"

rgb.to_hex_string();
// #b1ffb0

Hsl::from_str("hsl(168, 52%, 42%)").unwrap().to_css_string();
// String: hsl(168,52%,42%)

```

## `no_std`
Crate has a Cargo feature named `"std"` that is enabled by default.
In order to use `colorsys` in a `no_std` context this feature needs to be disabled.
Modify your dependency to opt out of enabled-by-default features.
```toml
[dependencies]
colorsys = { version = "*", default-features = false }
```

## Color unit ranges
All color units is f64. Here are their ranges:
 - red: 0.0 - 255.0
 - green: 0.0 - 255.0
 - blue: 0.0 - 255.0
 - hue: 0.0 - 360.0
 - saturation: 0.0 - 100.0
 - lightness: 0.0 - 100.0
 - all in cmyk are: 0.0 - 100.0  
 - alpha: 0.0 - 1.0
 - ansi256 code is `u8`


If you specify a value that does not fit within these ranges, they are replaced with a minimum or maximum value.

##### Enjoy using!

### License

This module is [MIT licensed](./LICENSE).


