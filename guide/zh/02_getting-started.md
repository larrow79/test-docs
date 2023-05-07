# 入门
<!-- getting-started -->

## 初始化
使用`ThingJS`引擎，首先要创建一个`THING.App`实例，`App`是引擎中的主要入口，一般只需要创建一个，提供了资源加载、对象管理、对象查询、事件注册等等各种功能。

```javascript
// 创建APP
new THING.App();
```

`ThingJS`默认使用页面上的`div3d`的标签作为3d渲染区域：
```html
<canvas id="div3d" width="800" height="500" />
```
如果需要手动指定标签，可以使用`container`参数：
```javascript
// 创建APP，如果需要可以手动指定div
const app = new THING.App({
    container: document.getElementById('div3d')，
});
```

## Hello World
在初始化`App`时，可以传入一个`url`参数，`url`可以是一个 <a href="https://www.khronos.org/gltf/">gltf</a> 格式的文件，或一个<a href="">资源包</a> 的路径，这样就可以加载一个场景：
```javascript
// 创建APP，场景url
const app = new THING.App({
    url: "./scenes/uino.gltf"
    onComplete: function(ev) {
        // 加载完成后回调
        console.log(ev);
    }
});
```

在初始化`App`后，创建一个立方体盒子，并让这个`Box`旋转：
```javascript
// 创建一个宽、高、深为1、2、3的盒子
let box = new THING.Box(1, 2, 3);

// 场景更新事件
app.on('update', function(deltaTime) {
    if (box != null)
        box.rotateY(0.1);
});
```

示例：
<playground src="sample_box.js"></playground>

关于`App`的更多功能请参考API手册。


