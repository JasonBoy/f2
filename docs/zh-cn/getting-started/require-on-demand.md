# 按需引用

F2 应用在移动端，所以对文件的大小非常重视，而默认情况下 F2 包含了完整的[几何标记类型](../tutorial/geometry.md)、[度量类型](../tutorial/scale.md)、[坐标系类型](../tutorial/coordinate.html)以及图表组件，但是用户有时候可能只需要其中几种，所以 F2 提供了按需引用的策略，由用户自定义引用需要的功能。

## 常用版本以及大小

> 版本大小可能会有稍微变动，请以最新版本为准。

| 版本 | 描述 | 大小（压缩之后） |
| -------- | -------- | -------- |
| `require('@antv/f2/lib/core')`     | 不包含任何图形，只有图形语法的核心代码 | 82K |
| `require('@antv/f2/lib/index-simple')` | 仅包含简单的折线图、柱状图（包含分组柱状图、层叠柱状图、瀑布图）、饼图（包含环图） | 93 k |
| `require('@antv/f2/lib/index-common')` | 包含常用的图表类型: 面积图(包含层叠面积图)、柱状图（包含分组柱状图、层叠柱状图、瀑布图）、折线图、点图（包含气泡图）、饼图（包含环图）、雷达图等；坐标系包含：直角坐标系、极坐标系支持；timeCat 类型度量；图表组件（tooltip、legend 以及 guide）| 139K |
| `require('@antv/f2')` | 完整版 | 155K |

## 如何按需引用

通过以下步骤进行按需引用，降低无用代码的引入：

1. 安装 f2 npm 包
2. 引入核心包 Core，**必须引入**
3. 引入需要的模块

第 2 步和第 3 步不需要注意顺序。

### 安装 f2 npm 包

```bash
$ npm install @antv/f2
```

### 引入核心包 Core

**必须引入**

```js
const Core = require('@antv/f2/lib/core');
```

该包包含了核心的图形语法处理逻辑，具体包含如下：

* Chart：图表入口类
* Goem：几何标记基类，**仅包含核心数据处理流程，不包含任何具体的几何标记（线、面、柱等）实现**
* Attr：图形属性，position、color、shape、size
* Scale：度量，**仅包含基础的 Linear、Cat 以及 Identity 这三种类型**
* Coord：坐标系，**仅包含直角坐标系**
* Axis：坐标轴，**仅包含直角坐标系的坐标轴**
* G：绘制引擎
* Util: 通用工具类

### 引入需要的模块

可动态加载的模块包含如下:

#### geom 类型

几何标记模块，用户绘制具体的图形，用法：

```js
require('@antv/f2/lib/geom/'); // 加载全部图形

require('@antv/f2/lib/geom/line'); // 只加载折线图
require('@antv/f2/lib/geom/area'); // 只加载面积图
require('@antv/f2/lib/geom/interval'); // 只加载柱状图
require('@antv/f2/lib/geom/path'); // 只加载路径图
require('@antv/f2/lib/geom/point'); // 只加载点图
require('@antv/f2/lib/geom/polygon'); // 只加载色块图
require('@antv/f2/lib/geom/schema'); // 只加载箱型图、股票图
```

#### 坐标系类型

```js
require('@antv/f2/lib/coord/polar'); // 极坐标

require('@antv/f2/lib/coord/cartesian'); // 直角坐标系（已经在 core 核心包中）
```

#### Axis 坐标轴类型

```js
require('@antv/f2/lib/component/axis/circle'); // 弧长坐标轴（用于极坐标）

require('@antv/f2/lib/component/axis/line'); // 直线坐标轴（已经在 core 核心包中）
```

#### adjust 数据调整类型

```js
require('@antv/f2/lib/geom/adjust/'); // 加载全部的 adjust 类型

require('@antv/f2/lib/geom/adjust/dodge'); // 只加载分组类型
require('@antv/f2/lib/geom/adjust/stack'); // 只加载层叠类型
```

#### scale 度量类型

```js
require('@antv/f2/lib/scale/time-cat'); // 加载 timeCat 类型的度量
```

#### 动画

**动画模块也作为 Chart 的插件，所以在加载该模块之后，还需要将模块注册至 Chart 上。**

> 以下两种动画按情况选择一种即可。

1. 仅包含入场的群组

```js
const GroupAnimation = require('@antv/f2/lib/animation/group');
Chart.plugins.register(GroupAnimation); // 这里进行全局注册，也可以给 chart 的实例注册
```

2. 精细的动画模块（包含入场、更新以及销毁动画）

```js
const Animation = require('@antv/f2/lib/animation/detail');
Chart.plugins.register(Animation); // 这里进行全局注册，也可以给 chart 的实例注册
```

#### Guide

插件，辅助元素模块，在使用该模块时，用户可以动态选择需要使用的辅助元素类型，然后再将对应的插件注册至 Chart 中。

```js
// 第一步：加载需要的 guide 组件，用户可以选择加载全部 guide 组件，也可以按需加载
require('@antv/f2/lib/component/guide'); // 加载全部的 guide 组件

require('@antv/f2/lib/component/guide/arc'); // 只加载 Guide.Arc 组件
require('@antv/f2/lib/component/guide/html'); // 只加载 Guide.Html 组件
require('@antv/f2/lib/component/guide/text'); // 只加载 Guide.Text 组件
require('@antv/f2/lib/component/guide/rect'); // 只加载 Guide.Rect 组件
require('@antv/f2/lib/component/guide/line'); // 只加载 Guide.Line 组件
require('@antv/f2/lib/component/guide/tag'); // 只加载 Guide.Tag 组件
require('@antv/f2/lib/component/guide/region-filter'); // 只加载 Guide.RegionFilter 组件

// 第二步：加载插件 Guide
const Guide = require('@antv/f2/lib/plugin/guide');

// 第三步：注册插件 Guide
Chart.plugins.register(Guide); // 这里进行全局注册，也可以给 chart 的实例注册
```

#### Tooltip

插件，提示信息模块。

```js
// 第一步：加载插件 Tooltip
const Tooltip = require('@antv/f2/lib/plugin/tooltip');
// 第二步：注册插件 Tooltip
Chart.plugins.register(Tooltip); // 这里进行全局注册，也可以给 chart 的实例注册
```

#### Legend

插件，图例。

```js
// 第一步：加载插件 Legend
const Legend = require('@antv/f2/lib/plugin/legend');
// 第二步：注册插件 Legend
Chart.plugins.register(Legend); // 这里进行全局注册，也可以给 chart 的实例注册
```

#### Interaction

交互行为引入。

```js
require('@antv/f2/lib/interaction/'); // 引入所有的交互行为

require('@antv/f2/lib/interaction/pie-select'); // 只引入 pie-select 饼图选中交互

require('@antv/f2/lib/interaction/interval-select');  // 只引入 interval-select 柱状图选中交互

require('@antv/f2/lib/interaction/pan'); // 只引入 pan 图表平移交互

require('@antv/f2/lib/interaction/pinch'); // 引入 pinch 图表缩放交互
```

## 按需引用示例

假如一个业务场景下仅需要绘制饼图（不带动画）：

```js
const F2 = require('@antv/f2/lib/core'); // 必须引入
require('@antv/f2/lib/geom/interval'); // 引入 interval 几何标记
require('@antv/f2/lib/coord/polar'); // 引入 极坐标
```

## 可视化工具

为了方便用户，我们提供了 UI 化的按需打包工具，帮助用户自由选择所需图表和组件进行打包下载，使用方法如下：

```bash
# 进入 f2 项目根目录，运行如下命令
$ npm run bundler
```

在出现的界面中进行需要模块的勾选，最后打包下载即可。

<img src="https://gw.alipayobjects.com/zos/rmsportal/RmUwBPLSWIbecmKEgoSw.png">

