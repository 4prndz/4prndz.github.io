---
title: CheatSheet Posts
date: 2023-04-21 04:34:00 +/-0300
categories: [Blog, Post]
tags: [markdown, meta] # TAG names should always be lowercase
---

**Post Name**: YYYY-MM-DD-TITLE.md

---

## Meta

```
title: TITLE
date: YYYY-MM-DD HH:MM:SS +/-0300
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [TAG] # TAG names should always be lowercase
```

---

## Categorias

```
categories: [(), ()] - {2 Categorias}
tags: [bee] - {0..N Tags}
```

---

## Author

```
<author_id>:
name: <full name>
twitter: <twitter_of_author>
url: <homepage_of_author>
```

---

## HUD

```
toc: false
comments: false
math: true
mermaid: true
```

---

## Images

```
![img-description](/path/to/image)
_Image Caption_
```

### Atributos

```
![img-description](/path/to/image) + {}

{: w="" h="" } => Width & Height
{: .position} => .normal, .left, .right
{: .ThemeMode} => .light, .dark
{: .shadow}

```

### CDN

**\_config.yml**

```
img_cdn: https://cdn.com

![The flower](/path/to/flower.png) => https://cdn.com/path/to/flower.png

```

### Image Path

**\_config.yml**

```
img_path: /img/path

d![The flower](flower.png) => /img/path/flower.png

```

### Preview Image

```
image:
path: /path/to/image
alt: image alternative text
```

### LQIP

```
image:
  lqip: /path/to/lqip-file # or base64 URI

  ![Image description](/path/to/image){: lqip="/path/to/lqip-file" }

```

---

## Pinned Posts

```
pin: true

```

---

## Prompts

```
{: .prompt-{type} }

- tip
- info
- warning
- danger
```

## Syntax's

````
`inline code part`

`/path/to/a/file.extend`{: .filepath}

\```
This is a plaintext code snippet.
\```
````

### Language Programming Highlight

````
```{language}
key: value
\```
````

#### Line Number

````
\```shell
echo 'No more line numbers!'
\```
{: .nolineno }
````

#### File Name

````
\```shell
# content
\```
{: file="path/to/file" }
````

#### Liquid Codes

````

{% raw %}
\```liquid
{% if product.title contains 'Pack' %}
  This product's title contains the word Pack.
{% endif %}
\```
{% endraw %}
````

---

## Videos

```
{
  % include embed/{Platform}.html id='{ID}' %
}

Video URL	| Platform	| ID
{link}/ID	| youtube   | H-B46URT4mg
{link}/ID	| twitch    | 1634779211

```
