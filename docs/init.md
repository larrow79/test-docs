----
# 基础应用（Basic）

## 创建应用（App）
```javascript
// 创建APP，默认将使用div3d的标签作为3d渲染区域
const app = new THING.App();
```

```javascript
// 场景url，可以是一个gltf文件、或一个资源包的路径（资源包见后面文档）
const url = "./scenes/uino.gltf";
// 创建app
const app = new THING.App({
  url,
  onComplete: (ev) => {
    console.log(ev.object);
  }
});
```

```javascript
// 创建app，回调
const app = new THING.App({
  url,
  onComplete: (ev) => {
    console.log(ev.object);
  },
  onProgress: (num) => {
    console.log(num);
  },
  onError: (ev) => {
    console.log(ev);
  }
});
```

