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

Before jumping into how I have implemented the lexing and parsing for ECLIPSE language, there are a few good resources on JavaCC that really helped me which I should mention:

* [**Official JavaCC Grammar Documentation:**](https://github.com/javacc/javacc/blob/master/docs/documentation/grammar.md)  The official documentation that explains how a .jj grammar file should be structured.
* [**Getting Started with JavaCC:**](http://eriklievaart.com/web/javacc1.html) An excellent series of blog posts with practical examples on how to do most of the things that you could want to do when defining your grammar.
 * [**Lexical Analysis in JavaCC:**](http://eriklievaart.com/web/javacc2.html) This excellent blog series continues with a specific focus on lexing. This includes basic information on the correct syntax for defining your tokens, handling of MORE states and SPECIAL_TOKEN types, and some useful advice on how to create UNKNOWN token types.
 * [**Parsing in JavaCC:**](http://eriklievaart.com/web/javacc3.html) Finally we get to final stage of parsing. The post here was useful to understand how to add custom Java code to handle the parsing of tokens.
 * [**JavaCC FAQ:**](https://javacc.github.io/javacc/faq.html) The offical community FAQ contains useful information but contains more advanced usage guidelines. As such it isn't a great place to start.

#### Lexing the ECLIPSE Language

The lexical grammar for the Eclipse language was written in a `*.jj` file which is the extension used for JavaCC syntax files. The "jj" of the extension stands for 'Java Jack' which is a play on words for 'Java Yacc', where [Yacc or '**Y**et **A**nother **C**ompiler **C**ompiler'](https://en.wikipedia.org/wiki/Yacc) is one of the older parsing programs. `*.jj` files are compiled using the JavaCC compiler, which is a command line tool. To integrate this into a build process in NetBeans, it is necessary to add the compilation targets into the ANT build script for the NetBeans module containing the files. This was strangely hard to discover how to do this correctly, and I ended up just hard coding the paths to the `*.jj` files (one for the lexing rules and one for the parsing rules) into the ANT build script. On the other hand, it works, and it's not like I'm planning on writing lots of lexers and parsers in JavaCC which would provide an incentive for writing a more general script...

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="au.com.newwavegeo.eclipse.filetype" default="netbeans" basedir=".">
    <description>Builds, tests, and runs the project au.com.newwavegeo.eclipse.filetype.</description>
    <import file="nbproject/build-impl.xml"/>    

    <!-- 
        Compile our JavaCC files. JJ files and executable are hard coded. Of course this means that the script isn't
        portable but quite honestly, I'm the only one compiling this at the moment so does it really matter?
    -->
    <target name="build" depends="build-javacc,netbeans,test-build"/>
    <target name="build-javacc" depends="init">
        <echo message="Compiling JavaCC files"/>
        <javacc target="${src.dir}/au/com/newwavegeo/eclipse/jcclexer/eclipse.jj" javacchome="C:/Tools/javacc-7.0.13/target" />
        <javacc target="${src.dir}/au/com/newwavegeo/eclipse/jccparser/eclipse.jj" javacchome="C:/Tools/javacc-7.0.13/target" />
    </target>    
</project>
```

Once included in the ANT build script, writing and updating the JavaCC grammar is suddenly much less painful. Before I wrote this I was opening a terminal window and using the command line. It works, but with lots of incremental additions, changes, fixes etc. during the writing of the grammar, it quickly becomes very tedious and error-prone.

What follows are some of the more interesting grammar notes I encountered when writing the lexical grammar for the Eclipse language.

##### Keywords

At the simplest level, ECLIPSE files are a series of keywords which are up to eight characters long, where each character is a capital letter, and the keyword is encountered on <u>its own line</u>. Note the emphasis here, that the keyword appears by itself on a line. There are no trailing or preceding characters and words. In practice, the keyword might have trailing spaces or tabs (whitespace), which means we must allow for that. Unfortunately, because there is no special syntax to indicate the start of a line in JavaCC (unlike with a regular expression where '^' indicates the start of a line, we instead detect the end of a line, and define a keyword as including the end of line character. Using this logic, a keyword can be defined as follows:

```
TOKEN :
{
    < WHITESPACE: " "|"\t"|"\f" >
    | < EOL: "\n"|"\r"|"\r\n" > 
    | < KEYWORD: (["A"-"Z"]){2,8} (<WHITESPACE>)* <EOL> >
}
```

The lexical grammar above defines three tokens. Our token definitions appear within the `TOKEN : { }` block, and each token itself is within a `< TOKENID: ~token_definition~ >` block that. The first `WHITESPACE` is a token which consists of a space character, or a tab character or a form feed character (is this ever used?). The second `EOL` is a token to define the end of the line, which consists of either a newline character, a carriage return character, or a carriage return and a newline. The [reason for the differences between newline characters on different operating systems](https://en.wikipedia.org/wiki/Newline) is very much a geek history lesson, but for the purposes of our lexer all that matters is that we detect and allow for any possible variant. The final third `KEYWORD` comprises between two to eight capital letter characters followed by zero or more whitespace characters, and terminated with an end of line character.

Note that this convention means the keyword token will include any whitespace and the EOL character ('\n', '\r' or '\r\n' depending on the operating system). When using JavaCC, the token image, or `String` representation of the token, will contain text representing the EOL token e.g., instead of "KEYWORD" the string returned is "KEYWORD\n". This is merely a minor annoyance and it means that in our program, to get the keyword itself we should strip the whitespace and newline characters from the token. For example, using the tokens generated by JavaCC:

```java
Token t = getNextToken(); // includes whitespace and EOL char
String keyword = t.image.strip(); // keyword with whitespace and EOL char removed
```

The problem is that there are many other character sequences that would fall under this definition. These include variables in the `SUMMARY` section like `FOPR` for the field oil production rate. This isn't strictly a keyword, so we need a way to differentiate between these summary variables and actual keywords. In the Notepad++ syntax highlighter a brute force approach was taken to define all known keywords, and assign those to a given type. A similar approach was taken using JavaCC, but because there are hundreds of keywords, this led to [a known issue whereby the generated Java code has a method that is so large it won't compile](https://parsers.org/javacc21/code-too-large-problem-fixed/). Forks of JavaCC have addressed the issue, but rather than switch to a different lexer, the solution utilised was to implement the inverse. Rather than defining all the keywords, leaving other character sequences as summary variables, the approach is to define the summary variables and a small subset of special keywords instead. This generates a smaller method, and then any remaining matches would be keywords.

##### Comments

Comments should, in theory, be quite simple to define. A comment is all text up to the end of line after a "&minus;&minus;" sequence of characters. In addition all text after the last '/' character on a line is treated as a character. However, in the wild, what happens is that sometimes it is useful to use text containing a '/' character after the terminating slash on a line. This is illegal because it means that the terminating slash is no longer the last slash on a line, but is in fact the penultimate slash. 

As an example, consider the line `ASSIGN FULPGYLD 0.065775 / LPG Sep Gas Yield (stb/Mscf)`. This assigns the value 0.065775 to the UDQ variable `FULPGYLD`, and the comment after the terminating slash simply notes that this is in units of stock tank barrels per thousand standard cubic feet. It looks innocent enough, except that the text "stb/Mscf" contains a slash character, so the actual terminating slash is not recognised as the terminating slash. One possible workaround would be to use a more sophisticated method of determining whether the slash should be a terminating slash or not based on the preceding tokens. However, the line does not execute in *OPM Flow* and causes an error. The accepted workaround (as per advice from David Baxendale and included in the manual) is to use a "&minus;&minus;" after the terminating slash, and thus the "stb/Mscf" will be part of a comment and the slash character in that block of text is ignored. In other words, to restate the line as `ASSIGN FULPGYLD 0.065775 / -- LPG Sep Gas Yield (stb/Mscf)`. This format is acceptable for *OPM Flow*.

What that means is that this real world practice must be considered when defining the lexical rules for comments.

```
TOKEN :
{
    < SINGLE_LINE_COMMENT: "--" (~["\n","\r"])* <EOL> >
    | < TERMINATING_SLASH: <SLASH> (~["/","\n","\r"])* (<EOL>|<SINGLE_LINE_COMMENT>) >
    | < SLASH: "/" >
}
```

Here our `SINGLE_LINE_COMMENT` starts with the "&minus;&minus;" character sequence and is followed by zero or more characters that are not a carriage return or newline character, and is concluded by the end of line token. This encompasses all characters between "&minus;&minus;" and the end of a line inclusive.

The `TERMINATING_SLASH` starts with the `SLASH` token (which is simply the '/' character) and is then followed by zero or more characters that are not a slash, carriage return or newline, and is concluded by either an end of line token or a single line comment token. Note that the JavaCC lexer is greedy, so the single line comment token is, in this case, consumed by the terminating slash token.

For our lexer this means that there are precisely three, and only three, ways that a line can end. These are:

  1. the `EOL` token, which could be the end of a `KEYWORD`. OK, so that might be two ways, but I'm counting it as one.
  2. the `SINGLE_LINE_COMMENT` token, which includes an `EOL` token at its end.
  3. the `TERMINATING_SLASH` token, which includes either an `EOL` or a `SINGLE_LINE_COMMENT` at its end.

##### Summary Variables

Defining the lexical rules to match summary variables is a little more daunting than just providing a list of known keywords. There are many different combinations of characters that are possible. Fortunately these follow a logical sequence of up to five characters that can be defined.

#### Parsing the ECLIPSE Language

Parsing the structure of an ECLIPSE file is relatively straightforward. There are up to eight defined sections, two of which are optional. Within each section, there are a set of keywords that can be utilised, and each keyword has a known sequence of tokens that must succeed it.
