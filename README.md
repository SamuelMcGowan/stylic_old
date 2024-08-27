A library for conveniently creating styled ANSI text.

# Features
- `no_std` support
- No dependencies
- Hyperlink support
- [`styled`] and [`hyperlink`] macros for convenient styling of formatted text
- `const` [`Style`] constructors allows defining styles as constants
- Correctly supports nested styled text (currently requires `std` feature)
- Enable or disable styling globally using the [`set_style_mode`] function

# Basic styling

Use the [`styled`] macro to create styled text. This supports format arguments and returns an instance of [`Styled`], which has methods for styling the text.

```rust
use antsy::{styled, Color};

println!("Making a word {}!", styled!("bold red").fg(Color::Red).bold());
```

Attributes that can be set using [`Styled`]'s methods are:

- [`bold`](Styled::bold)
- [`dim`](Styled::dim)
- [`italic`](Styled::italic)
- [`underlined`](Styled::underlined)
- [`blinking`](Styled::blinking)
- [`inverted`](Styled::inverted)
- [`hidden`](Styled::hidden)
- [`crossed`](Styled::crossed)

Colors can use the 8-color ANSI palette, 256-color ANSI palette or RGB colors.

```rust
use antsy::{Color, Styled};

// 8-color ANSI palette
println!("{}", Styled::new("Hello").fg(Color::Red));

// 256-color ANSI palette
for i in 0..255 {
    println!("{}", Styled::new(i).fg(Color::indexed(i)));
}

// RGB colors
println!("{}", Styled::new("Hello").fg(Color::rgb(255, 0, 0)));
```

# Hyperlinks

Use the [`hyperlink`] macro to create hyperlinks. Similar to [`styled`], this supports format arguments and returns an instance of [`Hyperlink`], which has the same methods for styling the text.

# Styles

Styles can also be created on their own, allowing them to be reused:

```rust
use antsy::{apply, Style};

const MY_STYLE = Style::new().bold().italic();

println!("{}", apply!(MY_STYLE =>"Hello"));
```

They have the same set of methods available as [`Styled`].

They can also be applied to hyperlinks:

```rust
use antsy::{apply_hyperlink, Style};

const MY_STYLE = Style::new().bold().italic();

println!("{}", apply_hyperlink!(MY_STYLE => "https://rust-lang.org"; "Rust Language"));
```

# Enabling or disabling styling globally

You can use the [`set_style_mode`] function to enable or disable styling globally. By default, it is auto-detected from the environment (in a non-std environment, it is enabled by default).

```rust
use antsy::{set_style_mode, StyleMode};

set_style_mode(StyleMode::disable());

// Styling will not be applied
println!("{}", styled!("Hello").fg(Color::Red));
```

# Nested styled text

Consider the following case, which prints some text in cyan, with the word "brown" in brown:

```rust
use antsy::{styled, Color};

println!(
    "{}",
    styled!(
        "the quick {} fox jumps over the lazy dog",
        styled!("brown").fg(Color::rgb(161, 123, 90))
    )
    .fg(Color::Cyan)
);
```

Without proper support, this would result in all styling being reset after the word "brown". However with the `std` feature enabled (as it is by default), this is handled correctly, and the remaining text is printed cyan.