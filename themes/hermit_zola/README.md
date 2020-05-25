[![Build Status](https://travis-ci.org/VersBinarii/hermit_zola.svg?branch=master)](https://travis-ci.org/VersBinarii/hermit_zola)

# Hermit 

> this is a port of the [Hermit theme](https://github.com/Track3/hermit) for [Zola](https://www.getzola.org/)

Hermit is a  minimal & fast Zola theme for bloggers.

![screenshot](hermit_zola.png)

[View demo](https://versbinarii.gitlab.io/blog/)

## Installation

First download the theme to your `themes` directory:

```bash
$ cd themes
$ git clone https://github.com/VersBinarii/hermit_zola
```
and then enable it in your `config.toml`:

```toml
theme = "hermit_zola"
```

## Configuration

```toml
[extra]
home_subtitle = "Some profound and catchy statement"

footer_copyright = ' &#183; <a href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank" rel="noopener">CC BY-NC 4.0</a>'

hermit_menu = [
    { link = "/posts", name = "Posts" },
    { link = "/about", name = "About" }
]

hermit_social = [
    { name = "twitter", link = "https://twitter.com" },
    { name = "github", link = "https://github.com" },
    { name = "email", link = "mailto:author@domain.com" }
]

[extra.author]
name = "The Author"
email = "author@domain.com"

[extra.google_analytics]
enable = false
id = "UA-4XXXXXXX-X"
```

### Table of content
Table of content can be enabled by adding 
```
[extra]
toc=true
```
to the page front matter. Icon will then appear above the page title that will
allow to toggle the ToC.

## License

[MIT](LICENSE)

Thanks to [Track3](https://github.com/Track3) for creating the original!
