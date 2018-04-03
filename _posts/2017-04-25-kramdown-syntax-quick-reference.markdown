---
layout: single
title:  "Kramdown Syntax Quick Reference"
date:   2017-04-25 11:25:25 +0200
categories: jekyll
tags: kramdown markdown
comments: true
header:
  teaser: /assets/images/common/markdown-logo.png
---

The Kramdown syntax is a superset of the Markdown syntax and it is designed so that is can be converted to HTML. This is the sample page where basics of markdown syntax are demonstrated. It is a bunch of nonsense text just to show some markdown language capabilities. It is quite easy to write markdown once you get used to it.

## Content

[Page linking](#page-linking)

[Headings](#headings)

[Paragraphs and line breaks](#paragraphs-and-line-breaks)

[Italic and bold](#italic-and-bold)

[List](#lists)

[Code snippets](#code-snippets)

[Images](#images)

[Links](#links)

[Escaping special characters](#escaping-special-characters)

[Summary](#summary)

## Page linking

It is possible to enable 'jumping' to content just by referencing to the heading name.

Example:

```
[Paragraphs and line breaks](#paragraphs-and-line-breaks)
```

Result:

[Paragraphs and line breaks](#paragraphs-and-line-breaks)

## Headings

There are four levels of headers available in markdown. This is achieved by surrounding text with *hash* characters (*space* is needed between hash characters and the text). This is an example of markdown code for headings:

```
First level header
==================

Second level header
-------------------

### Third level header ###

#### Fourth level header ####
```

And that will produce:

First level header
==================

Second level header
-------------------

### Third level header ###

#### Fourth level header ####

This actually used in this post in order for content to look more organized.

#### Paragraphs and line breaks ####
To have separated paragraphs all you have to do is to insert an empty line between two paragraphs as shown in next two nonsense paragraphs:

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce finibus nisi ante, quis mollis tellus scelerisque non. Duis ullamcorper ultrices ipsum non eleifend. Morbi ut varius orci. Sed risus est, volutpat et fringilla sed, fringilla in tellus. Ut sit amet nunc consectetur, mollis dui non, commodo leo. Fusce vehicula mi eu lorem posuere maximus. Aenean ante est, consectetur nec massa eget, consequat facilisis ipsum. Quisque in tortor sem. Etiam aliquam vitae sapien sit amet egestas!

Donec sed dapibus sem. Fusce aliquet vulputate lorem et dignissim. Nullam eu convallis ante. Fusce eu dui lacus. Aliquam rhoncus dolor quis risus blandit posuere. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce arcu ex, consectetur id tincidunt quis, elementum quis est. Maecenas placerat congue nisi lobortis euismod. Suspendisse porta sed ipsum eu gravida. Aliquam tempor ultricies nulla a pretium. In ultrices lobortis purus, eget aliquet odio laoreet nec. Aliquam aliquet risus quis tincidunt semper. Ut facilisis, orci non tempus lobortis, diam tellus finibus nisl, vel vehicula purus massa nec ante. 

## Italic and bold

Making text italic and bold is also possible in markdown with help of *star* and *underscore* characters. Let's write an example sentence:
```
Ut iaculis id **purus** at *lacinia*. Proin nibh mi, **egestas** sit amet lacus a, *placerat pretium arcu*.
```
That will produce:
Ut iaculis id **purus** at *lacinia*. Proin nibh mi, **egestas** sit amet lacus a, *placerat pretium arcu*.

## Lists
Markdown includes support for ordered and unordered lists:
```
Animals list (unordered):
* Carnivores
    * Tiger
    * Shark
    * Croc
* Omnivores
    * Elephant
    * Gazelle

To-do list (ordered):
1. Learn markdown
2. Write blog 
3. ?
4. Profit
```
That will produce:

Animals list (unordered):
* Carnivores
    * Tiger
    * Shark
    * Croc
* Omnivores
    * Elephant
    * Gazelle

To-do list (ordered):

1. Learn markdown
2. Write blog 
3. ?
4. Profit

## Code snippets

Code snippets are written using back-tick character:

`int i = 0;`

For multiline snippets, three back-ticks are used:

\`\`\`  
for(int i = 0; i < 10; i++)  
{  
    Console.WriteLine("Viva la markdown!");  
}  
\`\`\`  
That will produce:
```
for(int i = 0; i < 10; i++)
{
    Console.WriteLine("Viva la markdown!");
}
```
For syntax highlighting, just add language name after opening three back ticks (eg. \`\`\`javascript)
## Images

To insert image into blog post following syntax is used:

```
![minion](https://octodex.github.com/images/minion.png "Minion")
```
That will produce:
![minion](https://octodex.github.com/images/minion.png "Minion")

## Links

Also, you can reference any content using the similar syntax:

```
[Google](http://www.google.com)
```
That will produce:

[Google](http://www.google.com)

## Escaping special characters

You can escape special character using backslash in front of special character:

Example code:

```
\`int i = 0;\` 
```

That will produce:

\`int i = 0;\` 

## Summary 

This quick reference shows only the basic syntax with usage example for the Kramdown syntax. For more information I recommend visiting offical [page](https://kramdown.gettalong.org/).  
  
Thanks for reading and feel free to check out other articles on the Code Freeze!