# FAQ

## 1. What if the chart is blurry?

#### Description:

The text of the chart is blurry.

<img src="https://gw.alipayobjects.com/zos/rmsportal/grIULJSXMLcLzmPFNWjz.png" width="417px" />

#### Analyzation

Since most current mobile devices are high-definition screens, one pixel of the high-definition of the screen will corresponds to multiple pixels on browser whose resolution is 1*1. Taking iphone plus as an example, the three pixels of the browser will correspond to one pixel on the iphone plus. It is the reason for blur.

#### Solution

F2 provides two ways to solve this problem:

```js
// Global configuration, which will adfect all chart instances
F2.Global.pixelRatio = window.devicePixelRatio;

// Just for the specific chart
const chart1 = new F2.Chart({
  id: 'can2',
  pixelRatio: 2 // often set as window.devicePixelRatio
});
```

<img src="https://gw.alipayobjects.com/zos/rmsportal/eTYdXipPOYuBqMMRRIDt.png" width="400px">

#### More comments

The reason why F2 does not have a default setting for window.devicePixelRatio:

- F2 is embedded in some native applications in addition to Web-side H5 applications, this property cannot be used in native applications.
- Different situations have different HD solutions. Some may adopt the overall zoom solution, this property is not allowed for these scenarios.

## 2. How to display time?

#### Description

We have the following data:

```js
const data = [
  { time: '2011-01-01', value: 10 },
  { time: '2011-01-02', value: 20 },
  ...
  { time: '2011-01-20', value: 0 }
];
```

It will be displayed as follow:

<img src="https://gw.alipayobjects.com/zos/rmsportal/fZqMhzPvKmAluCbUNBSr.png" alt="时间格式" style="width:500px">

#### Analyzation

F2 does not support time scale, so it automatically recognizes the time string as a category data type and cannot automatically calculate the time axis.

#### Solution

F2 provides 2 solutions:

1. specify the display number:

   ```js
   chart.source(data, {
     time: {
       tickCount: 3
     }
   })
   ```

  you can also specify the display data directly:

   ```js
   chart.source(data, {
     time: {
       ticks: [ '2011-01-01', '2011-01-10', '2011-01-20' ]
     }
   })
   ```

   If you are drawing a line chart or area chart, set the `range: [0, 1]`, for the reason that the x-axis of the category data will leave blanks on both sides.

   ```js
   chart.source(data, {
     time: {
       range: [ 0, 1 ],
       ticks: [ '2011-01-01', '2011-01-10', '2011-01-20' ]
     }
   })
   ```

2. The solution above will perform very well if the data has been sorted, but if the data is unordered, the data field can be declared as `timeCat`:

   ```js
   chart.source(data, {
     time: {
       type: 'timeCat',
       tickCount: 3
     }
   })
   ```

<img src="https://gw.alipayobjects.com/zos/rmsportal/FFmrEHSufiuzKqojzzyD.png" alt="时间格式" style="width:500px">

// TODO 校验到这里

## How to interact with charts

#### Description

There are scenes that require the mouse display the relative information when the mouse is located in the chart.There is not any demos or api for interaction.

#### Analyzation

The reason that F2 does not encapsulate interactions is that F2 mainly provides the ability to customize and draw charts, and there are great differences in the way of interaction between various mobile devices. Users therefore need to monitor the events of the canvas themselves. However, F2 provides some interfaces to assist interacting with charts.

#### Solution

F2 supports three ways to interact with charts:

1. `getPosition(record)`: Get the position of the canvas according to the record.
2. `getRecord(point)`: Get the record information based on the pixel locaiton on the canvas.
3. `getSnapRecords(point)`: Get nearby data based on location on location.

The difference between `getRecord` and `getSnapRecords` is that the former converts the coordinates of the canvas to the corresponding data regardless of whether there is corresponding data in the user data, and the latter will find the data in the original user data and then return it.

The common steps to obtain information are as follows:

1. Get the coordinate of the clicked canvas

2. Get the nearby data using `getSnapRecords()`

   ```js
   function getPoint(canvas, clientX, clientY) {
     const bbox = canvas.getBoundingClientRect();
     return {
       x: clientX - bbox.left,
       y: clientY - bbox.top
     };
   }

   canvas.onclick=function(event){
     const point = getPoint(canvas, event.clientX, event.clientY);
     const x = parseInt(point.x);
     const y = parseInt(point.y);
     console.log("x:" + x + ";" + "y:" + y);
     // Get the corresponding data set based on the coordinate
     const data = chart.getSnapRecords(point);
     console.log(data);
   }
   ```

   User can perform any interaction after geting the data.

## How to the gradient color

#### Description

Gradient color is needed in area chart.

#### Solution

There is no shortcut for gradient color in F2, developers need to generate gradient color themselves, see [canvas gradient](http://www.w3school.com.cn/jsref/dom_obj_canvasgradient.asp) for more details.

F2 has multiple ways to set gradient color when declaring a chart.

```js
 // create a gradient object
  const canvas = document.getElementById('mountNode');
  const ctx = canvas.getContext('2d');
  const grd = ctx.createLinearGradient(0,0,500,0);
  grd.addColorStop(0,"#293c55");
  grd.addColorStop(1,"#f7f7f7");


  // set gradient color directly
  chart.area().position('x*y').color(grd);

  // set in style
  chart.area().position('x*y').style({
    fill: grd
  });

  // if there are multiple colors
  chart.area().position('x*y').color('type', function(type) {
    if (type === '1') {
      return grd;
    }
    return grd1;
  });
```

[Example](../demo/area/gradient.html)

##  Chaotic number on the axis

#### Description

Sometimes the numbers on the axis are not sorted, such as '2, 4, 1, 0, 10'.

<img src="https://gw.alipayobjects.com/zos/rmsportal/sGpgCogaOcoBUpKqcsWr.png" style="width:500px" />

#### Analyzation

In this case, the numeric data is recognized as a string, see below:

```js
const data = [
  { time: '2010-01-01', value: '10' },
  { time: '2010-01-02', value: '8' },
  { time: '2010-01-03', value: '11' },
  { time: '2010-01-04', value: '9' }
];
```

#### Solution

Convert the string to numeric data will solve the problem.

```js
const data = [
  { time: '2010-01-01', value: 10 },
  { time: '2010-01-02', value: 8 },
  { time: '2010-01-03', value: 11 },
  { time: '2010-01-04', value: 9 }
];
```

<img src="https://gw.alipayobjects.com/zos/rmsportal/IFtApeUkFWwnrdrodfve.png" style="width:500px" />

## How to display text

#### Description

On mobile devices, charts are often required to display text. However, F2 does not support `.label` method.

#### Analyzation

Displaying text on chart is very complicated, different charts are perform differently in different coordinates, therefore F2 does not support `.label` method like G2 for light-weighted size considerations. However, F2 provides a very powerful component Guide to draw text on charts.

#### Solution

The usage of Guide in F2, see [Guide](../api/chart.html#_guide).

[demos](/zh-cn/f2/3.x/demo/column/diy.html)

