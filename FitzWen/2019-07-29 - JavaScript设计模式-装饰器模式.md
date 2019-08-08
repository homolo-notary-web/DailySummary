### JavaScript设计模式

#### 装饰器模式

##### 装饰器是一种函数，写成``` @ + 函数名 ```。它可以放在类和类方法的定义前面,增加或修改类的功能。

##### 装饰器只能用于类和类的方法，不能用于函数，因为存在函数提升，但是类是不不会提升的。

```
@decorator
class A {}

// 等同于

class A {}
A = decorator(A) || A;
```
###### 也就是说，**装饰器是一个对类进行处理的函数。装饰器函数的第一个参数，就是所要装饰的目标类。**

```
/**
 * 装饰器函数
 * @param {Object} target 被装饰器的类的原型
 * @param {string} name 被装饰的类、属性、方法的名字
 * @param {Object} descriptor 被装饰的类、属性、方法的descriptor
 */
function Decorator(target, name, descriptor) {
  // 以此可以获取实例化的时候此属性的默认值
  let v = descriptor.initializer && descriptor.initializer.call(this);

  // 返回一个新的描述对象作为被修饰对象的descriptor，或者直接修改 descriptor 也可以
  return {
    enumerable: true,
    configurable: true,
    get() {
      return v;
    },
    set(c) {
      v = c;
    },
  };
}
```