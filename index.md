---
layout: default
description: "A Technology blogging site"
---

# Yet Another Tech. Blog (YAT.B)

_Do we really need another Blog about Tech.?_

## Recent posts

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

## Stuff I've built (some of it pretty old!)

- A [Font Matcher tool](https://paulbrimicombe.github.io/font-matcher/) inspired by [this tool](https://meowni.ca/font-style-matcher/) but providing a pure-CSS solution to flashes of unstyled content.
- [`typescript-docs-verifier`](https://www.npmjs.com/package/typescript-docs-verifier) — a tool to type check TypeScript code snippets in documentation files.
- [`verify-it`](https://www.npmjs.com/package/verify-it) a generator of random property inputs for tests.
- The [Rexplorer](https://paulbrimicombe.github.io/rexplorer/) which lets you explore the scores from the [Rex Factor PodCast](https://rexfactor.wordpress.com/) .
- Some [crossword solving tools](https://paulbrimicombe.github.io/crossword-tools/) to help you cheat at crosswords.
