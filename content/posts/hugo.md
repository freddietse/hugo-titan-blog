---
title: "Hugo 完全指南"
date: "2020-08-17"
toc: true
---

Hugo 是用 Go 语言编写的静态网站生成器，超快的编译速度、易于使用和可配置性是它最大的优势。

<!--more-->

### 常用功能

#### 菜单高亮

`config.toml`

```toml
[menu]
  [[menu.nav]]
    name = "Home"
    url = "/"
    weight = 1
  [[menu.nav]]
    name = "Blog"
    url = "/posts/"
    weight = 2
```

`navbar.html`

```html
<nav>
  {{ $current := . }} {{ range .Site.Menus.nav }} {{ $active := or
  ($current.IsMenuCurrent "nav" .) ($current.HasMenuCurrent "nav" .) }} {{
  $active = or $active (eq .Name $current.Title) }} {{ $active = or $active (and
  (eq .Name "Blog") (eq $current.Section "posts")) }} {{ $active = or $active
  (and (eq .Name "Tags") (eq $current.Section "tags")) }}
  <a href="{{ .URL }}" class="{{ if $active }}active{{ end }}">{{ .Name }}</a>
  {{ end }}
</nav>
```

#### 分页（Pagination）

```html
{{ template "_internal/pagination.html" . }} {{ range .Paginator.Pages }} {{
.Title }} {{ end }}
```

#### 目录（Table of Contents）

Hugo 能够自动解析 Markdown 的内容，创建一个由标题组成目录。一般我们还会添加一个判断，只有当 Markdown 文档 Front Matter 的 `toc` 设置为 `true` 才显示目录。

```html
{{ if (.Params.toc) }}
<div>{{.TableOfContents}}</div>
{{ end }}
```

我们可以在 `config.toml` 中对目录进行配置，仅适用于 Goldmark 渲染器：

```toml
[markup]
  [markup.tableOfContents]
    endLevel = 3
    ordered = false
    startLevel = 2
```

#### 语法高亮（Syntax Highlighting）

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

#### 上一篇文章 / 下一篇文章

`.PrevInSection` 和 `.NextInSection` 属于 Page Variables 。

```html
<nav>
  {{ with .PrevInSection }}
  <a href="{{.Permalink}}">{{.Title}}</a>
  {{ end }} {{ with .NextInSection }}
  <a href="{{.Permalink}}">{{.Title}}</a>
  {{ end }}
</nav>
```

#### SCSS + PostCSS + tailwindcss

第 1 步：安装 `extended` 版本的 Hugo，比如我的版本是 `hugo_extended_0.74.3_Windows-64bit` 。

第 2 步：全局安装 `postcss-cli` ：

```bash
$ npm install -g postcss-cli
```

第 3 步：在 `yourtheme/` 目录中安装 tailwindcss：

```bash
$ npm install tailwindcss
```

第 4 步：在 `yourtheme/` 目录中创建 `postcss.config.js` 文件，然后引入 tailwindcss：

```javascript
module.exports = {
  plugins: [require("tailwindcss")],
};
```

第 5 步：在 `yourtheme/assets/scss/` 目录中添加 `main.scss` 文件，然后引入 tailwindcss：

```scss
@tailwind base;
@tailwind components;
@tailwind utilities;
```

第 6 步：在 `yourtheme/layouts/partials/head.html` 中添加引用：

```html
<head>
  {{ $styles := resources.Get "scss/main.scss" | resources.ToCSS |
  resources.PostCSS (dict "config" "postcss.config.js") | minify | fingerprint
  }}
  <link
    rel="stylesheet"
    href="{{ $styles.Permalink }}"
    integrity="{{ $styles.Data.Integrity }}"
    media="screen"
  />
</head>
```

#### 在 `a` 标签中添加 `target=_blank`

在 `yourtheme/layouts/_default/_markup/render-link.html` 中添加如下代码：

```html
<a href="{{ .Destination | safeURL }}" {{ with .Title}} title="{{ . }}"
    {{ end }}{{ if strings.HasPrefix .Destination "http" }} target="_blank" rel="noopener"
    {{ end }}>{{ .Text | safeHTML }}</a>
```

#### CODEPEN

在 `yourtheme/layouts/shortcodes/codepen.html` 中添加如下代码：

```html
{{/* DEFAULTS */}}
{{ $user    := "your_username" }}
{{ $height  := 500 }}
{{ $tab     := "result" }}{{/* html|css|js|result */}}
{{ $theme   := 8862 }}{{/* create on codepen.io */}}


<script
    data-slug-hash="{{ .Get "id" }}"
    data-user="{{ or (.Get "user") $user }}"
    data-height="{{ or (.Get "height") $height }}"
    data-default-tab="{{ or (.Get "tab") $tab }}"
    data-theme-id="{{ or (.Get "theme") $theme }}"
    class='codepen'
    async
    src="//codepen.io/assets/embed/ei.js"
></script>
```

#### [拓展 hugo 的 markdown 流程图 mermaid](https://kentxxq.com/contents/%E6%8B%93%E5%B1%95hugo%E7%9A%84markdown_%E6%B5%81%E7%A8%8B%E5%9B%BEmermaid/)

```html
<!--head部分添加-->
<script src="https://cdn.bootcss.com/mermaid/8.0.0-rc.8/mermaid.min.js"></script>
```

在 `shortcodes` 目录下新建 `mermaid.html` 文件

```html
<!--mermaid.html-->
<div class="mermaid" align="{{ if .Get "align" }}
                                {{ .Get "align" }}
                            {{ else }}
                                center
                            {{ end }}">
    {{ safeHTML .Inner }}
</div>
```
