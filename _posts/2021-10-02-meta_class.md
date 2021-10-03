---
layout: post
title: python metaclass
category: 技术
tags: python metaclass
keywords: metaclass
description: python metaclass
---
* contents
{:toc}

python metaclass 记录


## KEYPOINTS

- PYTHON中一切用户定义的类均是type类的实例
- 用户定义类的过程，实际是调用type的__call__函数的过程
- type的__call__函数分别会调用__new__和__init__函数
- __new__函数是用于生成实例，__init__函数是用于初始化实例
- metaclass是type的子类，可以通过重载__call__/__new__/__init__函数从而影响类的构造过程
- 使用metaclass可以减少重复代码量，对同质工作抽象统一，与decorator类似，可以理解为在构建这个类时，先要统一执行metaclass里边定义的行为

## SAMPLES

### metaclass demo

``` python
class Meta(type):
    def __init__(self, name, base, dic):
        super().__init__(name, base, dic)
        print('Meta Init')
        print(self.__name__)
        print(self.__dict__)
        print(dic)
        print(self.yaml_tag)

    def __new__(cls, *args, **kwargs):
        print('Meta New')
        print(cls.__name__)
        print(cls.__dict__)
        obj = type.__new__(cls, *args, **kwargs)
        print(obj)
        return obj

    def __call__(cls, *args, **kwargs):
        print('Meta Call')
        obj = cls.__new__(cls)
        print(obj)
        cls.__init__(cls, *args, **kwargs)
        return obj


class Foo(metaclass=Meta):
    yaml_tag = 'FOOOOO'

    def __init__(self, name):
        print(f'Foo Init {name}')
        self.name = name

    def __new__(cls, *args, **kwargs):
        print('Foo New')
        return object.__new__(cls)

    def __call__(self):
        print(f'call {self.name}')

# Foo = Meta('Foo', (), {'yaml_tag': 'FOOOO'})
# print(Foo.yaml_tag)
foo = Foo('a')
foo()
```

可以看到，实际上使用`class Foo`的方式和调用`metaclass`的`__call__`函数的行为完全一致

### 使用metaclass实现单例模式

``` python
class Singleton(type):
    def __init__(self, name, base, dic):
        super().__init__(name, base, dic)
        self._instance = None
        print(self.su)

    def __call__(self, *args, **kwargs):
        # cls.__init__(cls, *args, **kwargs)
        if self._instance is None:
            self._instance = super().__call__(*args, **kwargs)
        return self._instance


class Foo(metaclass=Singleton):
    su = 'aa'

    def __init__(self, a, b, c):
        print(self._instance)
        self.a = a
        self.b = b
        self.c = c

    def __str__(self):
        print(self._instance.su)
        print(dir(self))
        return f'{self.a}\t{self.b}\t{self.c}'


class Foo2(metaclass=Singleton):
    su = 'bb'

    def __init__(self, a, b, c):
        print(self._instance)
        self.a = a
        self.b = b
        self.c = c

    def __str__(self):
        print(self._instance.su)
        print(dir(self))
        return f'{self.a}\t{self.b}\t{self.c}'


foo1 = Foo(1, 1, 3)
print(foo1)
foo2 = Foo(1, 1, 2)
print(foo2)

foo1 = Foo2(1, 1, 3)
print(foo1)
foo2 = Foo2(1, 1, 2)
print(foo2)

print(foo1.su)
```

### 使用metaclass实现工厂模式

``` python
class Factory(object):
    products = {}
    storage = {}
    total_incom = 0
    __factory_name__ = 'COM'
    @classmethod
    def register_product(cls, product, product_cls):
        cls.products[product] = product_cls
        print('{} as {} register {} as {}'.format(cls.__factory_name__, cls.__name__, product, product_cls.__name__))

    @classmethod
    def sell(cls, prod_name):
        assert prod_name in cls.products, f'{prod_name} is not available'
        cls.products[prod_name].sell()

    @classmethod
    def produce(cls, prod_name):
        assert prod_name in cls.products, f'{prod_name} is not available'
        cls.products[prod_name].produce()

    @classmethod
    def desc(cls):
        print('storage info')
        for k, v in cls.storage.items():
            print(f'product {k} has {v}')
        print(f'total incom until now ${cls.total_incom}')


class RegProduct(type):
    def __new__(meta, name, bases, class_dict):
        cls = type.__new__(meta, name, bases, class_dict)
        # 要求产品类有这个产品的名称，且告知哪个工厂生产
        if hasattr(cls, 'product_name') and hasattr(cls, 'factory'):
            if cls.factory and issubclass(cls.factory, Factory):
                print(f'register {cls.__name__}:{cls.factory.__name__}')
                cls.factory.register_product(cls.product_name, cls)
        return cls


class Product(object, metaclass=RegProduct):
    factory: Factory = None
    product_name: str = None

    def __init__(self) -> None:
        super().__init__()

        assert self.factory is not None
        assert self.product_name is not None

    @classmethod
    def sell(cls):
        if cls.factory.storage.get(cls.product_name, 0) < 1:
            print(f'{cls.product_name} has no storage')
            return
        cls.factory.storage[cls.product_name] -= 1
        cls.factory.total_incom += cls.price
        print(f'{cls.product_name} sell at price ${cls.price}')

    @classmethod
    def produce(cls):
        print(f'{cls.product_name} produing')
        if cls.product_name not in cls.factory.storage:
            cls.factory.storage[cls.product_name] = 0
        cls.factory.storage[cls.product_name] += 1


class AppleInc(Factory):
    __factory_name__ = 'AppleInc'


class Ipad(Product):
    product_name = 'ipad'
    factory = AppleInc
    price = 1000


class IPhone(Product):
    product_name = 'iphone'
    factory = AppleInc
    price = 5000


def Test():
    order = {'ipad': 10, 'iphone': 20}
    prod = {'ipad': 20, 'iphone': 25}
    for k, v in prod.items():
        for _ in range(v):
            AppleInc.produce(k)
    for k, v in order.items():
        for _ in range(v):
            AppleInc.sell(k)
    AppleInc.desc()


Test()
```

可以方便的注册新产品，新工厂

### 使用__new__和__init__区别

``` python
class A(object):
    k = 0
    def __init__(self, b):
        print('init here')
        self.b = b

    def __new__(cls, k):
        print('new here')
        cls.k = k
        return object.__new__(cls)  # 没有这句不会生成实例

    def __call__(self, b):
        print('call here')
        self.b = b


b = A(10)
print(A.k, b.b, b.k)
```
