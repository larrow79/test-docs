# 对象基础
<!-- object -->
在`ThingJS`中，对象`Object`是引擎提供的基本控制单元。对象具有唯一标识的，能被单独创建、销毁、查询，具有属性、方法、事件，能与其他对象建立关系。

对象的基类是`BaseObject`，可以通过**继承**或**组合**对齐进行扩展。如：三维对象类`Object3D`提供了空间变换和渲染功能；物体类`Entity`提供了模型或预制件的功能；三维空间类`Space3D`提供了空间计算功能，你可以继承这些类型，来进行扩展。你也可以通过`Compnent`，给对象增加组件，进行扩展。

在`ThingJS`，真实世界中的“物”在虚拟场景中有所对应的对象就是**孪生体**，孪生体在技术是线上，就是就是`BaseObject`的子类。一般认为对象的`id`和真实世界中的孪生体的`id`一致时，就认为这个对象是一个孪生体。


## 创建物体

**物体**或叫实体`Entity`，是一个具有可见外观的三维对象，比如：设备、车辆等。

创建`Entity`，构造方法中可以直接传入一个`url`来进行初始化，`url`可以是一个gltf文件，或一个资源包的路径，所指定的资源可以是一个模型资源，也可以预制件资源。

同时可以设置位置、旋转等基本参数，可以通过`onComplete`回调，或者`obj.waitForComplete`方法来等待加载完成

```javascript
// 资源url，可以是一个gltf文件，或一个资源包的路径
const url = "./models/car.gltf";

// 创建物体，根据给定URL的资源物体，路径为模型或预制件
let obj = new THING.Entity({
    url,
    position: [2,0,0],
    angle: 45,
    onComplete: function() {
        // 加载资源完成的回调
        console.log("object created");
    }
});
```

`await`、then的方法等待`Entity`加载完成：
```javascript
// 创建物体，await等待完成
let obj = new THING.Entity({url});
await obj.waitForComplete();
console.log(obj.name);

// 或者使用then
obj.waitForComplete().then(function() {
    console.log(obj.name);
});

```

示例：
<playground src="sample_box.js"></playground>

## 创建空间

**三维空间** `Space3D`是具有一定体积的三维对象，可进行空间计算，不具有可见外观，但可渲染其边界。空间对象可以作为一个可选项，实体对象可以包含空间对象，空间对象也包含实体对象。

```javascript
// 创建立方体空间
let space = new THING.Space3D({
    position: [0, 0, 0],
    size: [10, 20, 30]
});
```

```javascript
// 空间组合
space.add(space01);
space.add(space02);
space.add(space03);

// 子空间就在children中
space.children;
```

```javascript
// 空间包含计算，返回bool，默认传递孩子
space.contains(obj, cascade = true);

// 空间相交计算，返回bool
space.intersects(obj, cascade = true);

// 空间相离计算，返回bool
space.disjoint(obj, cascade = true);
```

```javascript
// 空间可视化
space.showBounding(true);
```

## 克隆和销毁

可通过`clone`方法，来复制一个对象，复制一个对象会连同对象的子对象、对象本身的组件、属性等一起复制：
```javascript
// 克隆 对象
let cloneObj = obj.clone();
```

可以直接调用对象的销毁，或通过`App`查询对象并进行销毁。销毁一个对象，会包括他身上的组件、子对象等一起销毁。同时，对象所使用的相关资源，也会通过引用计数的方式被销毁（没有用到的资源会被自动销毁）。

```javascript
// 销毁
obj.destroy();
obj = null;

// 先查询到对象，再销毁
app.query('car01').destroy();
```

