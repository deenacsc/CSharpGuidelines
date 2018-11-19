---
title: Documentation Guidelines
permalink: /documentation-guidelines/
classes: wide
sidebar:
  nav: "sidebar"
---

### <a name="av2305"></a> Document all `public`, `protected` and `internal` types and members (AV2305) ![](/assets/images/2.png) ![](/assets/images/A.png)

Documenting your code allows Visual Studio to pop-up the documentation when your class is used somewhere else. Furthermore, by properly documenting your classes, tools can generate professionally looking class documentation.

### <a name="av2316"></a> Only write comments to explain complex algorithms or decisions (AV2316) ![](/assets/images/1.png)

Try to focus comments on the *why* and *what* of a code block and not the *how*. Avoid explaining the statements in words, but instead help the reader understand why you chose a certain solution or algorithm and what you are trying to achieve. If applicable, also mention that you chose an alternative solution because you ran into a problem with the obvious solution.

### <a name="av2351"></a> Use InheritDoc (CRD2300) ![](/assets/images/1.png) ![](/assets/images/R.png)
Add `<inheritdoc/>` tag to XML comments in C# source code to inherit XML comments from base classes, interfaces, and similar methods. This eliminates unwanted copying and pasting of duplicate XML comments and automatically keeps XML comments sychronized.


