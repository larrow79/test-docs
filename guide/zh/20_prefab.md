# 预制件
<!-- prefab -->

预制件是一个预先制定好的资源，是具有一定属性、行为、效果的对象模板，目的是用于生成对象（实例化对象），下面是使用预制件的方式：

```javascript
const url = "./models/car.json";

// 创建预制件实例
const obj = new THING.Entity({url});
await obj.waitForComplete();

// 调用预制件提供的方法
obj.doSomeMethod();
```

可通过CLI创建一个预制件包，详细内容请参考预制件部分文档 ？


