# Padmark

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://github.com/lambdapad/padmark/blob/main/LICENSE)
[![CI](https://github.com/lambdapad/padmark/actions/workflows/elixir.yml/badge.svg)](https://github.com/lambdapad/padmark/actions/workflows/elixir.yml)
[![Hex.pm](https://img.shields.io/hexpm/v/padmark.svg)](https://hex.pm/packages/padmark)
[![Hex.pm](https://img.shields.io/hexpm/dw/padmark.svg)](https://hex.pm/packages/padmark)
[![Hex.pm](https://img.shields.io/hexpm/dt/padmark.svg)](https://hex.pm/packages/padmark)

Padmark is a pure-Elixir Markdown to HTML converter. It has no native dependencies, no NIFs and no external binaries, so it compiles and runs anywhere Elixir does, including inside a Mix escript.

It can be used as a library (`Padmark.as_html/2`) or as a command-line tool (`mix escript.build`).

## Rationale

Padmark is a fork of [Earmark](https://github.com/pragdave/earmark), created and maintained for years by Dave Thomas and Robert Dober. Earmark is the library [Lambdapad](https://github.com/altenwald/lambdapad) has used since its first release to turn Markdown into HTML, and it is a solid, well-tested piece of software. Thank you to both of you for it.

Hex now lists Earmark as retired, with this notice from its own maintainers:

> Earmark is no longer maintained. Migrate to a replacement, for example MDEx.

We looked at MDEx, the suggested replacement. It is a good library, but it renders Markdown through [comrak](https://github.com/kivikakk/comrak), a Rust library, wired in through a NIF. That is a fine trade-off for a web application, but not for Lambdapad: `lpad`, Lambdapad's CLI, ships as a Mix escript, a single self-contained file. Escripts cannot load a NIF, because a NIF needs its compiled `.so` file to sit in a real `priv/` directory on disk, and an escript has no such thing, only a zipped bundle of `.beam` files. A dependency on MDEx would mean `lpad` crashing on the first real Markdown file it tried to render.

So instead of switching to a library that cannot work for us, we forked Earmark under Lambdapad and picked up where its authors left off: fixing the security issue that got it retired in the first place (a stored XSS via unescaped HTML attribute values), a couple of other bugs found along the way, and adding tests where there were none. Padmark will keep being maintained here for as long as Lambdapad needs a pure-Elixir Markdown converter, which is to say, for as long as Lambdapad itself is maintained.

## Usage

```elixir
Padmark.as_html!("# Hello Padmark")
# "<h1>\nHello Padmark</h1>\n"
```

```elixir
{:ok, html, []} = Padmark.as_html("*emphasis* and **strong**")
html
# "<p>\n<em>emphasis</em> and <strong>strong</strong></p>\n"
```

See the moduledocs of `Padmark`, `Padmark.Options` and `Padmark.Transform` for the full set of options (footnotes, GFM tables, subscript/superscript, postprocessors, AST traversal, and more).

## Installation

Padmark can be installed from Hex or directly from Github. Add it as a git dependency in `mix.exs`:

```elixir
def deps do
  [
     {:padmark, "~> 1.4.51"}
     # or from Githun
    {:padmark, github: "lambdapad/padmark"} # or
  ]
end
```

## License

Padmark is licensed under the [Apache License 2.0](LICENSE), the same license Earmark was released under.
