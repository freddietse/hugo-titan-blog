---
title: "Hugo: The world’s fastest framework for building websites"
date: "2020-08-17"

---

### 目录（Table of Contents）

Hugo 能够自动解析 Markdown 的内容，然后创建一个目录。

```html
<div>
  {{ .TableOfContents }}
</div>
```

### 语法高亮（Syntax Highlighting）

Hugo 内置了默认的语法高亮功能（基于 [Chroma](https://github.com/alecthomas/chroma) ）。我们可以在 `config.toml` 中进行配置，例如 `style` 选项用于设置高亮主题，[Chroma Style Gallery](https://xyproto.github.io/splash/docs/all.html) 中还有其他主题可以选择。

```toml
[markup]
  [markup.highlight]
    codeFences = true
    guessSyntax = false
    hl_Lines = ""
    lineNoStart = 1
    lineNos = false
    lineNumbersInTable = true
    noClasses = true
    style = "monokai"
    tabWidth = 4
```

### 上一篇文章 / 下一篇文章

`.PrevInSection` 和 `.NextInSection` 属于 Page Variables 。

```html
<nav>
    {{ with .PrevInSection }}
    <a href="{{.Permalink}}">{{.Title}}</a>
    {{ end }}
    {{ with .NextInSection }}
    <a href="{{.Permalink}}">{{.Title}}</a>
    {{ end }}
</nav>
```

