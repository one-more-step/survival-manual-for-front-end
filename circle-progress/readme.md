# 环形进度条画法

<a name="SR5my"></a>
### 实现效果
**![image.png](https://cdn.nlark.com/yuque/0/2019/png/116146/1562429291547-cfa3b909-96f5-41cc-b471-c95f04fa7ddf.png#align=left&display=inline&height=276&name=image.png&originHeight=1104&originWidth=1234&size=99776&status=done&width=309)<br />**图1 **实现效果
<a name="Wyhr0"></a>
### 实现步骤

下面我们分步实现

<a name="w1Pub"></a>
#### 拆解问题

  1. 实现一个扇形
    1. 把一个圆分成100份儿
    1. 用1个 `div` 代表一份儿
      1. div的宽高
      1. 位置怎么定
    3. 给 `div` 一定的旋转角度
      1. 多少角度
      1. 角度怎么变
  2. 在扇形中间覆盖一个小圆，从而形成环

<a name="tdAbZ"></a>
#### 静态实现
直接贴初始代码吧<br />

```html
<div class="out-circle">
  <div class="inner-circle">4</div>
  <div class="item item1"></div>
  <div class="item item2"></div>
  <div class="item item3"></div>
  <div class="item item4"></div>
</div>
```

```css
.out-circle {
  position: relative;
  width: 500px;
  height: 500px;
  border-radius: 50%;
  border: 1px solid red;
  overflow: hidden;
 }
.inner-circle {
  z-index: 10;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 480px;
  height: 480px;
  text-align: center;
  line-height: 480px;
  font-size: 30px;
  border-radius: 50%;
  border: 1px solid red;
  background-color: #fff;
}

.item {
  position: absolute;
  left: 50%;
  height: 250.1234208092861px;
  width: 15.705379539064147px;
  transform-origin: 50% 100%;
  background-color: blue;
}

.item1 {
  transform: translateX(-50%) rotate(0deg);
}

.item2 {
  transform: translateX(-50%) rotate(3.6deg);
}

.item3 {
  transform: translateX(-50%) rotate(7.2deg);
}

.item4 {
  transform: translateX(-50%) rotate(10.8deg);
}
```


                                                ![image.png](https://cdn.nlark.com/yuque/0/2019/png/116146/1562429017046-091ac4cb-a6d3-40da-a748-893cf1efc765.png#align=left&display=inline&height=259&name=image.png&originHeight=1036&originWidth=1040&size=89816&status=done&width=260)
**             图2** 静态效果图

**要点：**

  - 蓝条的位置和旋转角度
    1. `transfrom-origin` 属性
    1. `transform` 的rotate和translate需同时设置，不然后面的会把前面的覆盖
  - 蓝条的宽高

![image.png](https://cdn.nlark.com/yuque/0/2019/png/116146/1562421170550-5a3b02d8-30d6-4a15-8f50-92ec2ab15e6a.png#align=left&display=inline&height=285&name=image.png&originHeight=569&originWidth=1237&size=92051&status=done&width=618.5)<br />**图3** 计算规则
<a name="RRDq6"></a>
#### 动态实现
把 `.progress` 内的所有元素清空，用js生成进度条相关的**dom，**从而达到解偶的目的。甚至可以将css也写到js中，这样使用者只需要引入js即可。<br />代码如下：

```javascript
class Progress {
  constructor({ dom, ratio }) {
    this.dom = dom || document.body;
    this.ratio = ratio || 0;
    this.create();
  }

  create() {
    this.outCircleDom = document.createElement('div');
    this.outCircleDom.classList.add('out-circle');
    this.innerCircleDom = document.createElement('div');
    this.innerCircleDom.classList.add('inner-circle');
    this.outCircleDom.appendChild(this.innerCircleDom);
    this.dom.appendChild(this.outCircleDom);
    this.render(this.ratio)
  }

  onChange(ratio) {
    this.render(ratio)
  }

  renderRatio(ratio) {
    if (ratio > 1 || ratio < 0) {
      throw Error('ratio may be great than 0 and less than 1')
    }
    this.innerCircleDom.textContent = parseInt(100 * ratio) + '%';
  }

  renderItems(ratio) {
    this.itemCount = parseInt(100 * ratio);
    var domFrag = document.createDocumentFragment();
    for (let i = 0; i < this.itemCount; i++) {
      var item = document.createElement('div');
      item.classList.add('item')
      item.style.cssText = `transform: translateX(-50%) rotate(${3.6 * i}deg);`
      domFrag.appendChild(item)
    }
    this.outCircleDom.appendChild(domFrag)
  }

  render(ratio) {
    this.renderItems(ratio)
    this.renderRatio(ratio)
  }
}
```

<br />到此，我们就可以以如下的方式调用我们的组件了：

```javascript
// 实例化我们的进度条
let progress = new Progress({
  dom: document.querySelector('.progress'),
  ratio: 0
});

// 模拟：假设r是某事件的进度：
let r = 0
document.body.onclick = e => {
  progress.onChange(r+=0.01)
}
```

<br />便可实现如下效果：

[![Jietu20190707-002213-HD.mp4 (474.84KB)](https://cdn.nlark.com/yuque/0/2019/jpeg/116146/1562430180103-cd4cbb0d-230a-472b-8cf1-7bf6e9bdb997.jpeg?x-oss-process=image/resize,h_450)](https://www.yuque.com/ea14bs/cqff1r/zlgkzr?_lake_card=%7B%22status%22%3A%22done%22%2C%22name%22%3A%22Jietu20190707-002213-HD.mp4%22%2C%22size%22%3A486235%2C%22percent%22%3A0%2C%22id%22%3A%22fT41E%22%2C%22videoId%22%3A%2272a0b0295e4a4294bf6a570fea85cf88%22%2C%22coverUrl%22%3A%22https%3A%2F%2Fcdn.nlark.com%2Fyuque%2F0%2F2019%2Fjpeg%2F116146%2F1562430180103-cd4cbb0d-230a-472b-8cf1-7bf6e9bdb997.jpeg%22%2C%22aliyunVideoSrc%22%3Anull%2C%22taobaoVideoId%22%3A%22230540234124%22%2C%22uploaderId%22%3A116146%2C%22authKey%22%3A%22YXBwX2tleT04MDAwMDAwMTImYXV0aF9pbmZvPXsidGltZXN0YW1wRW5jcnlwdGVkIjoiYjczODVjMDJlNWY2NmI2MjM4ZTgyMzAxMDMxYmRlZGEifSZkdXJhdGlvbj0mdGltZXN0YW1wPTE1NjI0MzA3Mjk%3D%22%2C%22docUrl%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fea14bs%2Fcqff1r%2Fzlgkzr%22%2C%22card%22%3A%22video%22%7D#fT41E)

<a name="hn00L"></a>
### 总结

虽然完成了基础功能，但还有很多东西需要改进。这篇文章只提供思路，生产环境不建议使用。<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

