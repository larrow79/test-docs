# App
<!-- app -->

`App`是`ThingJS`引擎中的主要入口，一般只需要创建一个实例。`App`提供了对象管理、对象查询、事件注册、加载场景和各种资源的方法。

## 创建App

在`App`的构造函数中，可以传入参数，如：背景、场景`url`，如果需要，还可以设置加载完成、加载进度或错误的回调方法：
```javascript
// 创建app，回调
const app = new THING.App({
    url: "./scenes/uino.gltf",
    background: 'gray', // 背景颜色 或 天空盒
    onComplete: init // 加载完成回调
    // onProgress: (num) => {}, 加载进度回调
    // onError: (ev) => {} 加载错误回调
});

function init(ev) {
    console.log("app begin...");
}
```

## 加载场景



## 更新事件
如果需要一些全局的更新操作，可以注册`App`的`update`事件：
```javascript
let box = new THING.Box(1, 2, 3);

// 场景更新事件
app.on('update', function(deltaTime) {
    if (box != null)
        box.rotateY(0.1);
});
```

## 更多方法
`App`还提供了切换层级`level`、对象查询`query`、更多参数的注册`on`等，详细见后续教程。

