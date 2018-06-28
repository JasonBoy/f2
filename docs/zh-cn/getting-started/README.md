# 快速开始

首先我们需要在页面创建一个 `<canvas></canvas>` 标签。

```html
<canvas id="myChart"></canvas>
```

之后我们需要将 F2 引入：

```html
<!-- online -->
<script src="https://gw.alipayobjects.com/os/antv/assets/f2/3.1.1/f2.js"></script>
```

以上两部就绪之后就可以开始使用 F2 绘制图表了:

```javascript
const data = [
  { year: '1951 年', sales: 38 },
  { year: '1952 年', sales: 52 },
  { year: '1956 年', sales: 61 },
  { year: '1957 年', sales: 145 },
  { year: '1958 年', sales: 48 },
  { year: '1959 年', sales: 38 },
  { year: '1960 年', sales: 38 },
  { year: '1962 年', sales: 38 },
];
const chart = new F2.Chart({
  id: 'myChart',
  width: 375,
  height: 265,
  pixelRatio: window.devicePixelRatio
});

chart.source(data);
chart.interval().position('year*sales');
chart.render();
```

<img src="https://gw.alipayobjects.com/zos/rmsportal/vNBNIGvCiIwqLwaYjWUy.png" width="375">

使用 F2 创建图表就是这么容易，从现在起开始探索 F2 更多的功能吧！~

在 AntV 官网可查看更多 F2 的图表 [demos](https://antv.alipay.com/zh-cn/f2/3.x/demo/index.html)。
