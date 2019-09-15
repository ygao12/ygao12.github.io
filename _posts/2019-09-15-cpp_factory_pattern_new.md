---
layout: post
title: c++工厂模式
category: 技术
tags: cpp coding
keywords: factory-pattern
description: cpp factory pattern design
---
* contents
{:toc}

cpp factory mode design with creators, code at repo [github](https://github.com/ygao12/experiments/tree/master/cpp/factory_demo_with_creator)


Use c++11 feature `functional` to generate a factory with product creators, is more flexible than previous one. See more detail in the code.
This is very similar with the realization of `layer / solver` from [BVLC/caffe](https://github.com/BVLC/caffe).

Notice `REG_*` macros should be call in `.cc` files, and `.o` should be linked to final executable file;
