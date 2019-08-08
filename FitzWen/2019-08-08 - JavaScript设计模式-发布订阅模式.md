### JavaScript设计模式

#### 发布订阅模式

发布-订阅模式又叫做观察者模式，他定义了一种一对多的依赖关系，即当一个对象的状态发生改变的时候，所有依赖他的对象都会得到通知。

```
document.body.addEventListener('click',function () {
  alert(2333);
},false);
```

* 发布者
* 缓存列表（订阅了消息对象）
* 发布消息的时候遍历缓存列表，依次触发里面存放的订阅者的回调函数
* 另外，回调函数中还可以添加很多参数，订阅者可以接收这些参数，订阅者收到消息后可以进行各自的处理。

```
var event = {
  peopleList: [],
  listen: function (key,fn) {
    if (!this.peopleList[key]) { 
    //如果没有订阅过此类消息，创建一个缓存列表
    this.peopleList[key] = [];
    }
    this.peopleList[key].push(fn)
  },
  trigger: function () {
    let key = Array.prototype.shift.call(arguments);
    let fns = this.peopleList[key];
    if (!fns || fns.length == 0) {//没有订阅 则返回
      return false;
    }
    for(var i=0,fn;fn=fns[i++];){
      fn.apply(this,arguments);
    }
  },
  remove:function (key,fn) {
    var fns = this.clientList[key];
    if(!fns){
      return false;
    }  
    if(!fn) {
      fns && (fns.length=0)
    }else {
      for (let index = 0; index < fns.length; index++) {
        const _fn = fns[index];
        if(_fn === fn){
          fns.splice(index,1);
        }
      }
    }
  }
}

var installEvent  = function (obj) {
  for(var i in event){
    obj[i] = event[i];
  }
}

let yourMsg = {};
installEvent(yourMsg);
yourMsg.listen('marrgie',function (name) {
  console.log(`${name}想知道你结婚`);
})
yourMsg.listen('unemployment',function (name) {
  console.log(`${name}想知道你失业`);
})

yourMsg.trigger('marrgie','张三');
yourMsg.trigger('unemployment','李四');

```