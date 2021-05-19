---
title: 'Using Bookdown: Multiple authors'
date: '2021-05-17'
slug: using-bookdown-multiple-authors
categories:
  - R
tags:
  - bookdown
  - LaTeX
subtitle: 'A quest to add multiple authors in Bookdown'
summary: 'You are using Bookdown to write a manuscript. How do you add multiple authors that are affiliated to the same institution?'
authors: [Ariel]
lastmod: '2021-05-17T22:40:37-05:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
draft: true
---

# "Why don't you try RMarkdown?" and moving to Bookdown
A while ago I started to work on a manuscript and in a meeting with one of the co-authors he said "Why don't you try RMarkdown to write this project?". Until that moment, I had only used Word to write scientific documents, and a quick look on the Internet confirmed my initial suspicion that using RMarkdown was a complete change from using Word. Nonetheless, I also had a feeling that learning to do things in RMarkdown _had_ to be beneficial, so I decided that it was worth to give it a shot. If I didn't like how things were going after a week, I would go back to Word. 

Fast forward a couple days later and I realize that I need to cross-reference an equation in the manuscript. A Google search indicates that to do this, I should probably use [Bookdown](https://bookdown.org/yihui/rmarkdown/). A quick glimpse on the Bookdown manual also indicates that Bookdown offers more capabilities than RMarkdown to reference equation and figures. As I work on the manuscript I find that using Bookdown is way more easy that doing things in Word. Equations? Easy, and unlike the equations made in Word, here they look gorgeous, textbook-like. Citations? A small learning curve, but you know what you are doing and you control the amount of information you have for each reference. The list of benefits goes on and on.

However, the day comes when I have to add the author information on the manuscript (which hencefort I will call `Manuscript.Rmd`). There are three authors, two of which (including myself) are from the same academic institution. I try the option of listing the authors in the YAML header like this (title and author information in this case are fake):

```{}
---
title: '**A longitudinal analysis of Twitter trends in the year 2020**'
subtitle: _Political and sociological considerations_
author:
- Benicio del Ox^[Department of Sociology, University of the Insula]
- Jake A. Motaungh^[Department of Sociology, University of the Insula]
- Frederick P. Montego^[Department of Psychology, University of Mars]
---
```

The main problem here is that when rendered, the header looks like this:

![header-1](Header_1.JPG)

While the footnote looks like this:
![footnote-1](footnote_1.JPG)


Its clear that every author is being assigned a "different" institution, I think I was naive enough to think that Bookdown will "detect" that two of the authors were in the same institution, but of course, that is not the case. 

I couldn't find a satisfactory answer to this problem in the Bookdown Cookbook, but by looking through the internet I find that there is a `$\LaTeX$` package called `authblk` that allows for better control on the author information. This search leads me to [this](https://www.r-bloggers.com/2020/12/author-with-affiliation-in-bookdown-html-and-pdf/) post on Rbloggers with a proposed solution. However, the syntax that the post offers does not work for me, and after multiple attempts, I am starting to think that I would probably have to submit a question about this in some specialized forum (which would involve creating a `reprex`, creating an account, registering, and the list goes on...).


A couple more hours of reading and I discover (yes, I know this is pretty well known for some people, but it felt like a great discover to me, nonetheless) that more control over the creation of the document can be achieved by using a `.sty` file. In short, the `.sty` file can contain some `\(\LaTeX\)` code that allows to customize the final document (longer explanation [here](http://dw.tug.org/pracjourn/2005-3/asknelly/nelly-sty-&-cls.pdf)). This is really convenient as it allows to do other things such as adding line numbers to the document.

To make the story short, I ended up doing this:

- Creating a `.sty` file called "preamble" that is the same directory where `Manuscript.Rmd` 
is.
- In `preamble.sty` I wrote the following:

```{}
\usepackage{lineno}
\usepackage{authblk}

%command for the package lineno to put line numbers in the manuscript
\linenumbers

%authors
\author[1]{Benicio del Ox}
\author[2]{Jake A. Motaungh}
\author[1]{Frederick P. Montego}
\affil[1]{\footnotesize Department of Sociology, University of the Insula}
\affil[2]{\footnotesize Department of Psychology, University of Mars}

```

Then, in the YAML header of `Manuscript.Rmd` I added two lines to indicate that I will call `preamble.sty` when the PDF is compiled:

```{}
---
title: '**A longitudinal analysis of Twitter trends in the year 2020**'
subtitle: _Political and sociological considerations_
output:  
  bookdown::pdf_document2:
    keep_tex: yes
    includes:
      in_header: preamble.sty
---

```



