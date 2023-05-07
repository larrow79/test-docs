# 场景
<!-- scene -->

`ThingJS`的场景是具有一定关系的对象所组成的集合。场景具有一个根对象`app.root`，所有对象都在这个根对象下，组成一个树状结构。可以通过对象的`add`、`remove`方法，给这个场景树增加或移除对象。

场景一般由场景搭建工具 或 三维建模工具 生成，通过资源包`bundle`或`gltf`文件加载到引擎中。

## 加载场景

可以通过`app.load()`的方法来加载场景：
```javascript
// 资源包路径
const url = "./scenes/uino";

// await 的方式，等待加载完成
let result = await app.load(url);
console.log( result.object );

// 或 then 的方式，等待加载完成后回调
app.load(url).then((ev) => {
    console.log(ev.object); // ev.object是根节点
})；
```
上面例子中，传入的`url`是一个资源包的路径，在资源包内指定了资源包的类型为`scene`类型。

但如果传入的`url`是一个文件的路径，如：`.gltf`或`.json`后缀，则需要用`type`参数去指定加载的类型为`scene`：
```javascript
// 文件路径
const url1 = "./scenes/uino.gltf";
const url2 = "./scenes/simple.json";

// 需要设置type为scene
await app.load(url1, {type: "scene"});
await app.load(url2, {type: "scene"});
```

---
在`app.load()`方法中，除了`url`必选参数外，还可以设置更多的参数，如：指定加载到哪个根对象`parent`、加载时是否忽略某些内容`ignore`等等。

```javascript
app.load(url, {
    // 加载到这个对象下
    parent: parentObj,  // ？参数名是这个
    // 忽略效果
    ignore: "theme",
});
```

## 加载事件
除了在`then`方法外，也可以在参数`onComplete`中进行指定加载完成的回调。

加载进度`onProgress`、加载错误`onError`的回调方法等，也可以在参数中进行指定。

```javascript
app.load(url, {
    // 场景加载完成回调
    onComplete: (ev) => {
        console.log(ev.object);
    },
    // 场景加载进度回调
    onProgress: (num) => {
        console.log(num);
    },
    // 场景加载错误回调
    onError: (ev) => {
        console.log(ev);
    }	
});
```

在代码的其他地方，也可以通过注册`app`的`load`事件，来响应场景加载完成的事件：
```javascript
// 场景加载完成事件
app.on('load', function(ev) {
    console.log(ev);
});

// 只监听符合筛选条件的对象加载事件
app.on('load', '.Campus', function(ev) {
    console.log(ev);
});
```


