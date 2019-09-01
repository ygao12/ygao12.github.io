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

cpp factory mode design, code at repo [github](https://github.com/ygao12/experiments/tree/master/cpp/factory_demo)



## product.h
``` cpp
#pragma once
#include <iostream>

class Product {
 public:
  Product() {}
  virtual ~Product() {}

 public:
  virtual void Say() = 0;
};
```

## factory.h
``` cpp
#include <iostream>
#include <unordered_map>
#include "product.h"

typedef std::unordered_map<std::string, Product*> ProductMap;

class Factory {
 public:
  Factory() {}
  ~Factory() { Destroy(); }
  static inline void AddIns(const std::string &product_name, Product *product) {
    if (product_map_.find(product_name) != product_map_.end()) {
      std::cout << product_name << " already in factory, delete this new one" << std::endl;
      if (product != NULL) {
        delete product;
        product = NULL;
      }
    }
    product_map_.insert(std::make_pair(product_name, product));
  }
  static inline Product *GetIns(const std::string &product_name) {
    if (product_map_.find(product_name) != product_map_.end()) {
      return product_map_[product_name];
    } else {
      return NULL;
    }
  }
  static inline void Destroy() {
    for (auto itd : product_map_) {
      if (itd.second != NULL) {
        delete itd.second;
        itd.second = NULL;
      }
    }
    ProductMap().swap(product_map_);
  }
  static inline void ShowAllIns() {
    std::cout << "----------" << std::endl;
    for (auto itd : product_map_) {
      std::cout << "I have " << std::endl;
      std::cout << itd.first << std::endl;
    }
    if (product_map_.empty()) {
      std::cout << "I cannot product anything!" << std::endl;
    }
    std::cout << "----------" << std::endl;
  }
 private:
  static ProductMap product_map_;
};

template <class T>
struct VirtualTag {};

class RegisterProduct {
 public:
  RegisterProduct(std::string product_name, Product *product) {
    std::cout << "reg here without template " << product_name << std::endl;
    Factory::AddIns(product_name, product);
  }
  template <class T>
  RegisterProduct(VirtualTag<T>, std::string product_name) {
    std::cout << "reg here with template " << product_name << std::endl;
    Factory::AddIns(
        product_name,
        []() { return static_cast<Product *>(new T()); }()
        );
  }
};

#define REGISTER_PRODUCT_ADV(CLS_NAME, PTR) \
    RegisterProduct _##CLS_NAME(#CLS_NAME, PTR);

#define REGISTER_PRODUCT(CLS_NAME) \
    RegisterProduct _##CLS_NAME(VirtualTag<CLS_NAME>(), #CLS_NAME);
```
we need a `RegisterProduct` class to add product into factory, because we cannot call function in MARCRO directly,
in class `RegisterProduct`, we have two construction method, one with template, one with product pointer.
Notice, we cannot call construction function directly, so we need a virtual struct named VirtualTag to help specifying
the template T; Look carefully the MARCRO `REGISTER_PRODUCT`;

## product_impl.h
``` cpp
#pragma once
#include "product.h"
#include "factory.h"

class ProductA : public Product {
 public:
  void Say() {
    std::cout << "This is ProductA speaking" << std::endl;
  }
};

class ProductB : public Product {
 public:
  void Say() {
    std::cout << "This is ProductB speaking" << std::endl;
  }
};

class ProductC : public Product {
 public:
  void Say() {
    std::cout << "This is ProductC speaking" << std::endl;
  }
};

inline Product *RegA() {
  Product *product = new ProductA();
  return product;
}

REGISTER_PRODUCT_ADV(ProductA, RegA());
REGISTER_PRODUCT(ProductB);
REGISTER_PRODUCT(ProductC);
```
