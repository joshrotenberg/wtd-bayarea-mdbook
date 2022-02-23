# How to mdbook

## Initialize a new book

```sh
$ mdbook init --title="my new book" --ignore=git my-new-book
2022-02-22 16:32:41 [INFO] (mdbook::book::init): Creating a new book with stub content

All done, no errors...

$ tree my-new-book 
my-new-book
├── book
├── book.toml
└── src
    ├── SUMMARY.md
    └── chapter_1.md

2 directories, 3 files
```

## Add some basic content and structure 

The `SUMMARY.md` explicitly determines the structure of the output. Let's add an introduction, an appendix and another chapter with a sub section. A `README.md`
in a directory will become an `index.html` in the generated output:

```markdown
# Summary

[Introduction](./intro.md)
- [Chapter 1](./chapter_1.md)
- [Chapter 2](./chapter_2.md)
    - [Notes](./chapter_2/README.md)
[Appendix](./appendix.md)
```

## Config the config

The first section is created for us by the `init` command. In this example, we've also added some options for the
search functionality. Plugins such as preprocessors and alternative backends can also be configured here.

```toml
[book]
authors = ["josh rotenberg"]
language = "en"
multilingual = false
src = "src"
title = "my new book"

[rust]
edition = "2021"

[output.html.search]
limit-results = 20
use-boolean-and = true
boost-title = 2
boost-hierarchy = 2
boost-paragraph = 1
expand = true
heading-split-level = 2
```

## Integrate Continuously

Deploying an mdbook to GitHub Pages is easy:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-20.04
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:
      - uses: actions/checkout@v2

      - name: Setup mdBook
        uses: peaceiris/actions-mdbook@v1
        with:
           mdbook-version: 'latest'

      - run: mdbook build

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: ${{ github.ref == 'refs/heads/main' }}
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./book
```
