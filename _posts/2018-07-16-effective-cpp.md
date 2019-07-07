---
layout: post
title: Effective C++笔记
category: 技术
tags: cpp coding
keywords: effective-cpp
description: effective-cpp笔记
---
* contents
{:toc}

# Prefix

这是effective c++学习笔记，参考自网上翻译：

[Effective c++](https://legacy.gitbook.com/book/wizardforcel/effective-cpp/details)



# Introduction
## Terminology
1. declaration & definition
   - declaration: tells compilers about the name and type of something, but omits certain details
     ``` c++
     extern int x;                       //  object declaration
     std::size_t numDigits(int number);  //  function declaration
     class Widget;                       //  calss declaration
     template<typename T>                //  template declaration
     class GraphNode;
     ```
   - definition: provides compilers with the details a declaration omits. \
     For an object, the definition is where compilers set aside memory for the object. \
     For a function or a function template, the definition provides the code body. \
     For a class or a class template, the definition lists the members of the class or template.
     ``` c++
     int x;                               //  object definition
     std::size_t numDigits(int number) {  //  function definition
       std::size_t digit = 10;
       return digit * digit;
     }
     class Widget {                       //  class definition
      public:
       Widget();
       ~Widget();
     };
     template<typename T>                 //  template definition
     class GraphNode {
      public:
       GraphNode();
       ~GraphNode();
     }
     ```

