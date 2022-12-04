## Docs

[![star](https://gitee.com/bbigsun/docs/badge/star.svg?theme=white)](https://gitee.com/bbigsun/docs/stargazers)
[![fork](https://gitee.com/bbigsun/docs/badge/fork.svg?theme=white)](https://gitee.com/bbigsun/docs/members)

> Docsify 一个神奇的文档网站生成器

> Docs 使用 Docsify 搭建的在线文档。


### 高级配置

#### 默认 `index.html` 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
    <meta name="description" content="Description">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0">
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/vue.css">
</head>
<body>
    <div id="app"></div>
    <script>
        window.$docsify = {
            name: '',
            repo: ''
        }
    </script>
    <!-- Docsify v4 -->
    <script src="//cdn.jsdelivr.net/npm/docsify@4"></script>
</body>
</html>
```

#### 网页标题

```html
<head>
    <title>Docs & BBigSun</title>
</head>
```


#### 网页图标

```html
<head>
    <link rel="icon" href="/_media/favicon.ico">
</head>
```

#### 文档主题

*官方主题*

```html
<head>
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/buble.css">
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/dark.css">
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/pure.css">
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/dolphin.css">
</head>
```

*其他主题*

```html
<head>
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify-darklight-theme@latest/dist/style.min.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-defaults.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-simple.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-simple-dark.css">
</head>
```

#### 主题颜色

`index.html`

```javascript
window.$docsify = {
    themeColor: '#8c3fc8'
}
```

#### 顶部导航栏

`index.html`

```javascript
window.$docsify = {
    loadNavbar: true
}
```

`_navbar.md`

```markdown
<!-- _navbar.md -->

* [En](/)
* [中文](/zh-cn/)
```

#### 侧边导航栏

`index.html`

```javascript
window.$docsify = {
    loadSidebar: true
}
```

`_sidebar.md`

```markdown
* [高级配置](#高级配置)

    * [默认 index.html](#默认-indexhtml)
    * [网页标题](#网页标题)
    * [网页图标](#网页图标)
    * [文档主题](#文档主题)
        * [官方主题]()
        * [其他主题]()
    * [主题颜色]()
    * [顶部导航栏]()
    * [侧边导航栏]()
    * [封面]()

* [插件](#插件)

    * [搜索]()
    * [一键复制]()
    * []()
    * []()
```

#### 封面

`index.html`

```javascript
window.$docsify = {
    coverpage: true
}
```

`_coverpage.md`

```markdown
<!-- _coverpage.md -->

![logo](_media/icon.svg)

# Docs <small>1.0.0</small>

> 纸上得来终觉浅，得知此事要躬行。

- 一日不读书，尘生其中
- 两日不读书，言语乏味
- 三日不读书，便觉面目可憎

[Gitee](https://gitee.com/bbigsun/docs.git)
[GitHub](https://github.com/docsifyjs/docsify/)
[Get Started](#Docs)

<!-- 背景图片 -->

![](_media/bg.png)

<!-- 背景色 -->

<!-- ![color](#F5B2B2) -->
```

### 插件

#### 搜索

`index.html`

```html
<body>
    <div id="app"></div>
    <script>
        window.$docsify = {
            name: '',
            repo: '',
            // 全局搜索
            search: {
                paths: 'auto',
                placeholder: '全局搜索',
                noData: '找不到结果',
                depth: 3
            }
        }
    </script>
    <!-- 搜索插件 -->
    <script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/search.min.js"></script>
<body>
```

#### 一键复制

`index.html`

```html
<body>
    <div id="app"></div>
    <script>
        window.$docsify = {
            name: '',
            repo: '',
            // 一键复制
            copyCode: {
                buttonText : '复制',
                errorText  : 'Error',
                successText: 'Success'
            }
        }
    </script>
    <!-- 一键复制插件 -->
    <script src="//cdn.jsdelivr.net/npm/docsify-copy-code"></script>
<body>
```

#### emoji

`index.html`

```html
<body>
    <!-- emoji插件 -->
    <script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/emoji.min.js"></script>
</body>
```

#### 代码高亮

`index.html`

```html
<body>
    <!-- 代码高亮 -->
    <script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-c.min.js"></script>
</body>
```

#### 字数统计

`index.html`

```html
<body>
    <div id="app"></div>
    <script>
        window.$docsify = {
            name: '',
            repo: '',
            // 字数统计
            count:{
                countable: true,
                position: 'top',
                margin: '10px',
                float: 'right',
                fontsize:'.9em',
                color:'rgb(90,90,90)',
                language:'chinese',
                localization: {
                words: "",
                minute: ""
                },
                isExpected: true
            }
        }
    </script>
    <!-- 字数统计 -->
    <script src="//unpkg.com/docsify-count/dist/countable.js"></script>
<body>
```

#### 标签卡

`index.html`

```html
<body>
    <div id="app"></div>
    <script>
        window.$docsify = {
            name: '',
            repo: '',
            // 标签卡
            tabs: {
                persist    : true,      // default
                sync       : true,      // default
                theme      : 'classic', // default
                tabComments: true,      // default
                tabHeadings: true       // default
            }
        }
    </script>
    <!-- 标签卡插件 -->
    <script src="https://cdn.jsdelivr.net/npm/docsify-tabs@1"></script>
<body>
```

*使用语法*

```markdown
<!-- tabs:start -->

#### **English**

Hello!

#### **French**

Bonjour!

#### **Italian**

Ciao!

<!-- tabs:end -->
```

#### LaTex 公式

`index.html`

```html
<head>
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/katex@latest/dist/katex.min.css"/>
</head>
<body>
    <!-- LaTeX插件 -->
    <script src="//cdn.jsdelivr.net/npm/docsify-katex@latest/dist/docsify-katex.js"></script>
</body>
```

#### 高亮引用

```html
<body>
    <!-- 高亮引用插件 -->
    <script src="https://unpkg.com/docsify-plugin-flexible-alerts"></script>
</body>
```

#### 阅读进度条

`index.html`

```html
<body>
    <div id="app"></div>
    <script>
        window.$docsify = {
            name: '',
            repo: '',
            // 阅读进度条
            progress: {
                position: "top",//默认上方
                color: "var(--theme-color,#42b983)",//进度条颜色
                height: "3px",//进度条高度
            }
        }
    </script>
    <!-- 阅读进度条插件 -->
    <script src="https://cdn.jsdelivr.net/npm/docsify-progress@latest/dist/progress.min.js"></script>
<body>
```

#### Mermaid

`index.html`

```html
<body>
    <!-- mermaid插件 -->
    <script src="//unpkg.com/mermaid/dist/mermaid.js"></script>
    <script src="//unpkg.com/docsify-mermaid@latest/dist/docsify-mermaid.js"></script>
    <script>mermaid.initialize({ startOnLoad: true });</script>
</body>
```


---

> - 参考文档：https://blog.pyrroleach.com/index.php/archives/138/
> - 官方文档：https://docsify.js.org/
