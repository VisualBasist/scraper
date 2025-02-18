# scraper

[![crates.io package](https://repology.org/badge/version-for-repo/crates_io/rust:scraper.svg)](https://repology.org/project/rust:scraper/versions)
![test](https://github.com/causal-agent/scraper/actions/workflows/test.yml/badge.svg)
[![Join the chat at https://gitter.im/scraper-rs/community](https://badges.gitter.im/scraper-rs/community.svg)](https://gitter.im/scraper-rs/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

HTML parsing and querying with CSS selectors.

`scraper` is on [Crates.io][crate] and [GitHub][github].

[crate]: https://crates.io/crates/scraper
[github]: https://github.com/programble/scraper

Scraper provides an interface to Servo's `html5ever` and `selectors` crates, for browser-grade
parsing and querying.

## Examples

### Parsing a document

```rust
use scraper::Html;

let html = r#"
    <!DOCTYPE html>
    <meta charset="utf-8">
    <title>Hello, world!</title>
    <h1 class="foo">Hello, <i>world!</i></h1>
"#;

let document = Html::parse_document(html);
```

### Parsing a fragment

```rust
use scraper::Html;
let fragment = Html::parse_fragment("<h1>Hello, <i>world!</i></h1>");
```

### Parsing a selector

```rust
use scraper::Selector;
let selector = Selector::parse("h1.foo").unwrap();
```

### Selecting elements

```rust
use scraper::{Html, Selector};

let html = r#"
    <ul>
        <li>Foo</li>
        <li>Bar</li>
        <li>Baz</li>
    </ul>
"#;

let fragment = Html::parse_fragment(html);
let selector = Selector::parse("li").unwrap();

for element in fragment.select(&selector) {
    assert_eq!("li", element.value().name());
}
```

### Selecting descendent elements

```rust
use scraper::{Html, Selector};

let html = r#"
    <ul>
        <li>Foo</li>
        <li>Bar</li>
        <li>Baz</li>
    </ul>
"#;

let fragment = Html::parse_fragment(html);
let ul_selector = Selector::parse("ul").unwrap();
let li_selector = Selector::parse("li").unwrap();

let ul = fragment.select(&ul_selector).next().unwrap();
for element in ul.select(&li_selector) {
    assert_eq!("li", element.value().name());
}
```

### Accessing element attributes

```rust
use scraper::{Html, Selector};

let fragment = Html::parse_fragment(r#"<input name="foo" value="bar">"#);
let selector = Selector::parse(r#"input[name="foo"]"#).unwrap();

let input = fragment.select(&selector).next().unwrap();
assert_eq!(Some("bar"), input.value().attr("value"));
```

### Serializing HTML and inner HTML

```rust
use scraper::{Html, Selector};

let fragment = Html::parse_fragment("<h1>Hello, <i>world!</i></h1>");
let selector = Selector::parse("h1").unwrap();

let h1 = fragment.select(&selector).next().unwrap();

assert_eq!("<h1>Hello, <i>world!</i></h1>", h1.html());
assert_eq!("Hello, <i>world!</i>", h1.inner_html());
```

### Accessing descendent text

```rust
use scraper::{Html, Selector};

let fragment = Html::parse_fragment("<h1>Hello, <i>world!</i></h1>");
let selector = Selector::parse("h1").unwrap();

let h1 = fragment.select(&selector).next().unwrap();
let text = h1.text().collect::<Vec<_>>();

assert_eq!(vec!["Hello, ", "world!"], text);
```

## Contributing

Please feel free to open pull requests. If you're planning on implementing
something big (i.e. not fixing a typo, a small bug fix, minor refactor, etc)
then please open an issue first.

## Stargazers over time

[![Stargazers over time](https://starchart.cc/causal-agent/scraper.svg)](https://starchart.cc/causal-agent/scraper)

License: ISC
