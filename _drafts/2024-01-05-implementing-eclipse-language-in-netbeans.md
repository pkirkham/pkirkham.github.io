---
layout: article
title: Implementing the ECLIPSE Language with NetBeans IDE
modified:
categories: pyrus
excerpt: Implementing ECLIPSE simulator language support in the NetBeans IDE.
tags: [pyrus_suite, netbeans, dynamic_modelling, simulation, software, OPM_Flow, programming, lexing, parsing, simulation_deck, ide]
image:
  feature: 
  teaser: teaser-implement-eclipse-in-netbeans-400x250.jpg
  thumb:
comments: true
---

NetBeans is a open-source modular platform. The consequence of this is that it is not necessary to write an entire application specific to editing of ECLIPSE files. What is instead needed is to write a plug-in that describes aspects of ECLIPSE files that can be implemented within an IDE that was designed to accommodate new and different languages.

## Implementing the ECLIPSE Language with NetBeans IDE

So where does one begin? Inevitably, as with any open source project, the quality of the documentation and support is somewhat hit and miss. Generally NetBeans is very good, but this is offset by the sheer quantity of documentation available and the continually evolving nature of the codebase itself. Finding out how to do something is an exercise in managing dead links, appreciating how code in older tutorials needs to be adapted to the latest version of NetBeans and investigating the source code itself to understand how features have been implemented. This section goes into some of the details as to how support for another language was enabled in the NetBeans IDE, and the challenges that were encountered and overcome along the way.

### Lexing and Parsing ... Say What?

The first step in writing support for a new language is to write a lexer and a parser for the language in question. For myself, I'm not actually sure if this is easier said than done, because to be honest at first I had no idea what a [lexer](https://en.wikipedia.org/wiki/Lexical_analysis) and a [parser](https://en.wikipedia.org/wiki/Parsing) even are. Turns out, that lexing involves analysing a string of characters, determining the purpose of each word (series of sequential characters between whitespace) and assigning that word to a previously defined token such as a number, string, reserved keyword, or any other arbitrarily defined meaning. Where lexing is concerning just with matching character sequences to tokens, parsing is concerning with the order of the tokens. Parsing involves applying knowledge of the language grammar to the token sequence. For example, in ECLIPSE language we know that a keyword such as "DX" will be followed by a sequence of numbers or repeated numbers, and is concluded with a "/" character at the very end. Parsing checks that this expected token sequence exists and flags an error if it encounters something different.

For better or worse the [tutorials for lexing](https://netbeans.apache.org/tutorial/main/tutorials/nbm-javacc-lexer/) and [parsing in NetBeans](https://netbeans.apache.org/tutorial/main/tutorials/nbm-javacc-parser/) are based on the use of [JavaCC](https://javacc.github.io/javacc//). This is an open-source approach that was originally provided to the Java community by Sun Microsystems (the original developer of the Java language) on 1996. On the plus side, it has been around for a long time and can be considered mature and proven. On the down side, many alternatives have since emerged for which the syntax and generated code could be considered more elegant, such as [ANTLR](https://cwiki.apache.org/confluence/display/NETBEANS/Using+ANTLR) or [Language Server Protocol (LSP)](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=263425900). However, [converting from JavaCC to ANTLR](https://tomassetti.me/converting-from-javacc-to-antlr/) just for the sake of using a more modern approach is not necessarily worth it. Unless there is a need to change, JavaCC should be adequate.

So, given that I was going to be diving in at the deep end already, it was decided to write the lexer and parser using JavaCC. This would at least allow the tutorials to be followed and understood. But wait... there's more. Because JavaCC is not actively maintained or developed. It is a legacy approach which rarely received updates. The great thing about open-source is that anyone is free to create a fork of JavaCC, and [forks have been developed including JavaCC 21 and CongoCC](https://parsers.org/javacc21/i-shall-tell-you-my-plans-but-then-i-shall-have-to-kill-you-mr-bond-van-bruggen/), although if you read through the discussions it is evident that people seem to have taken offense to a fork being created. In my case, migration to a different fork of JavaCC can be left to the future. If JavaCC works, then it will be easier to learn this established syntax.

One downside of sticking with JavaCC is that there does not appear to be a plugin for NetBeans that will do syntax highlighting etc. for the .jj files. This is somewhat ironic as I'm writing the grammar to enable syntax highlighting and other features for another language, but the tools themselves don't benefit from those features. Whilst it's a little painful editing plain text files, the silver lining is that it only has to be done once.

Before jumping into the how I have implemented the lexing and parsing for ECLIPSE language, there are a few good resources on JavaCC that really helped me which I should mention:

* [**Official JavaCC Grammar Documentation:**](https://github.com/javacc/javacc/blob/master/docs/documentation/grammar.md)  The official documentation that explains how a .jj grammar file should be structured.
* [**Getting Started with JavaCC:**](http://eriklievaart.com/web/javacc1.html) An excellent series of blog posts with practical examples on how to do most of the things that you could want to do when defining your grammar.
 * [**Lexical Analysis in JavaCC:**](http://eriklievaart.com/web/javacc2.html) This excellent blog series continues with a specific focus on lexing. This includes basic information on the correct syntax for defining your tokens, handling of MORE states and SPECIAL_TOKEN types, and some useful advice on how to create UNKNOWN token types.
 * [**Parsing in JavaCC:**](http://eriklievaart.com/web/javacc3.html) Finally we get to final stage of parsing. The post here was useful to understand how to add custom Java code to handle the parsing of tokens.
 * [**JavaCC FAQ:**](https://javacc.github.io/javacc/faq.html) The offical community FAQ contains useful information but contains more advanced usage guidelines. As such it isn't a great place to start.

#### Lexing the ECLIPSE Language

At the simplest level, ECLIPSE files are a series of keywords which are up to eight characters long, where each character is a capital letter, and the keyword is encountered on its own line. A keyword is easily defined as follows:

```
TOKEN :
{
    < WHITESPACE: " "|"\t"|"\f" >
    | < EOL: "\n"|"\r"|"\r\n" > 
    | < KEYWORD: (["A"-"Z"]){2,8} (<WHITESPACE>)* <EOL> >
}
```

Note that this convention means the keyword token will include any whitespace and the EOL character ('\n', '\r' or '\r\n' depending on the operating system). To get the keyword itself we should strip the whitespace and newline characters from the token. For example, using the tokens generated by JavaCC:

```java
Token t = getNextToken(); // includes whitespace and EOL char
String keyword = t.image.strip(); // keyword with whitespace and EOL char removed
```

The problem is that there are many other character sequences that would fall under this definition. These include variables in the `SUMMARY` section like `FOPR` for the field oil production rate. This isn't strictly a keyword, so we need a way to differentiate between these summary variables and actual keywords. In the Notepad++ syntax highlighter a brute force approach was taken to define all known keywords, and assign those to a given type. A similar approach was taken using JavaCC, but because there are hundreds of keywords, this led to [a known issue whereby the generated Java code has a method that is so large it won't compile](https://parsers.org/javacc21/code-too-large-problem-fixed/). Forks of JavaCC have addressed the issue, but rather than switch to a different lexer, the solution utilised was to implement the inverse. Rather than defining all the keywords, leaving other character sequences as summary variables, the approach is to define the summary variables instead. This generates a smaller method, and then any remaining matches would be keywords.

Defining the lexical rules to match summary variables is a little more daunting than just providing a list of known keywords. There are many different combinations of characters that are possible. Fortunately these follow a logical sequence of up to five characters that can be defined.

#### Parsing the ECLIPSE Language

Parsing the structure of an ECLIPSE file is relatively straightforward. There are up to eight defined sections, two of which are optional. Within each section, there are a set of keywords that can be utilised, and each keyword has a known sequence of tokens that must succeed it.
