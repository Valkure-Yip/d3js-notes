# Basics

## selections

**chaining**:
The return value of most selection methods is the selection itself. This means that selection methods such as `.style`, `.attr` and `.on` can be **chained**.
### select and modify

`d3.selectAll(), d3.select()`

| Name        | Behaviour                    | Example                                                                   |
| ----------- | ---------------------------- | ------------------------------------------------------------------------- |
| `.style`    | Update the style             | `d3.selectAll('circle').style('fill', 'red')`                             |
| `.attr`     | Update an attribute          | `d3.selectAll('rect').attr('width', 10)`                                  |
| `.classed`  | Add/remove a class attribute | `d3.select('.item').classed('selected', true)`                            |
| `.property` | Update an element's property | `d3.selectAll('.checkbox').property('checked', false)`                    |
| `.text`     | Update the text content      | `d3.select('div.title').text('My new book')`                              |
| `.html`     | Change the html content      | `d3.select('.legend').html('<div class="block"></div><div>0 - 10</div>')` |

can also pass a callback function:
param:
d: joined data
i: index of the element
```js
d3.selectAll('rect')
.attr('x', function(d, i) {
if (i==1) return 100;
	return i * 40;
});
```

### event handling
| Event name   | Description                                              |
| ------------ | -------------------------------------------------------- |
| `click`      | Element has been clicked                                 |
| `mouseenter` | Mouse pointer has moved onto the element                 |
| `mouseover`  | Mouse pointer has moved onto the element or its children |
| `mouseleave` | Mouse pointer has moved off the element                  |
| `mouseout`   | Mouse pointer has moved off the element or its children  |
| `mousemove`  | Mouse pointer has moved over the element                 |
cb param:
e: DOM event obj
d: joined data

In the event callback function the `this` variable is **bound to the DOM element that triggered the event.**
```js
d3.selectAll('circle')
    .on('click', function(e, d) {
        d3.select(this)
            .style('fill', 'orange');
    });

```

### insert & remove elements

append
If the elements already have children, the new element will become the **last** child.
```js
d3.select('g.item')
	.append('text')
	.text("A")
```

insert
a second argument which specifies (as a CSS selector) which element to insert the new element **before**
```js
d3.selectAll('g.item')
	.insert('text', 'circle')
	.text("A")
	.attr('y', 50)
	.attr('x', 30);
```

remove
```js
d3.selectAll('circle')
	.remove();
```

### .each()
The `.each` method lets you call a function for **each element of a selection**.
```js
d3.selectAll('circle')  
	.each(function(d, i) {    
		var odd = i % 2 === 1;    
		
		d3.select(this)      
		.style('fill', odd ? 'orange' : '#ddd')      
		.attr('r', odd ? 40 : 20);  
});
```

![](assets/Pasted%20image%2020240923180945.png)
### .call()
The `.call` method allows a function to be called into which the **selection itself** is passed as the first argument
```js
function colorAll(selection) {  
	selection    
		.style('fill', 'orange');
}

d3.selectAll('circle')  
	.call(colorAll);
```
![](assets/Pasted%20image%2020240923182004.png)
### .filter()
```js
d3.selectAll('circle')  
	.filter(function(d, i) {    
		return i % 2 === 0;  
	})  
	.style('fill', 'orange');
```
![](assets/Pasted%20image%2020240923182039.png)




## join data

array of data --- selection of html or svg
- HTML (or SVG) elements are added or removed such that **each array element has a corresponding HTML (or SVG) element**
- each HTML/SVG element may be **positioned, sized and styled** according to the value of its corresponding array element

```js
d3.select(container)
	.selectAll(element-type)
	.data(array)
	.join(element-type);
```
where:
- `container` is a CSS selector string that specifies a **single element** that'll contain the joined HTML/SVG elements
- `element-type` is a string describing the **type of element** you’re joining (e.g. ‘div' or 'circle')
- `array` is the name of **the array** you’re joining

Typically four methods are used in a data join:
- `.select` defines the element that'll act as a container (or parent) to the joined HTML/SVG elements
- `.selectAll` defines the type of element that'll be joined to each array element
- `.data` defines the array that's being joined
- `.join` performs the join. This is where HTML or SVG elements are added and removed

e.g.
```js
var myData = [40, 10, 20, 60, 30];

d3.select('.chart')
  .selectAll('circle')
  .data(myData)
  .join('circle')
```

edit attr according to joined value
```js
var myData = [40, 10, 20, 60, 30];

d3.select('.chart')
  .selectAll('circle')
  .data(myData)
  .join('circle')
  .attr('cx', function(d, i) {
    return i * 100;
  })
  .attr('cy', 50)
  .attr('r', function(d) {
    return 0.5 * d;
  })
  .style('fill', function(d) {
    return d > 30 ? 'orange' : '#eee';
  });
```

e.g. Each circle is sized according to the city's population.
```js
var cities = [
  { name: 'London', population: 8674000},
  { name: 'New York', population: 8406000},
  { name: 'Sydney', population: 4293000},
  { name: 'Paris', population: 2244000},
  { name: 'Beijing', population: 11510000}
];

d3.select('.chart')
  .selectAll('circle')
  .data(cities)
  .join('circle')
  .attr('cx', function(d, i) {
    return i * 100;
  })
  .attr('cy', 50)
  .attr('r', function(d) {
    let scaleFactor = 0.000004;
    return scaleFactor * d.population;
  })
  .style('fill', '#aaa');

```
![](assets/Pasted%20image%2020240919110321.png)

D3 is **not responsive**, need to join again if data change: put join ops in a `update` function, call every time data changes

### key functions

if the order of array elements changes (due to sorting, inserting or removing elements) the array elements can get joined **to different DOM elements**.

passing a **key function** into the `.data` method

```js
d3.select('#content')
	.selectAll('div')
	.data(data, function(d) {
		return d;
	})
```

## enter, exit & update

## scale functons
https://d3js.org/d3-scale
transform **data values into visual values** such as positions and colours.
a functions that:
- take an input (usually a number, date or category) and
- return a value (such as a coordinate, a colour, a length or a radius)

e.g. `myScale` accepts input between 0 and 10 (the **domain**) and maps it to output between 0 and 600 (the **range**).
```js
// scale function
var myScale = d3.scaleLinear()
	.domain([0, 10])
	.range([0, 600]);

// use scale function for offset
d3.select('svg .inner')
	.selectAll('circle')
	.data(data)
	.join('circle')
	.attr('r', 3)
	.attr('cx', function(d) {
		return myScale(d); // use scale fn
	});
```

### scale type

12 different scale types (scaleLinear, scalePow, scaleQuantise, scaleOrdinal etc.)  
3 groups:
- [scales with continuous input and continuous output](https://www.d3indepth.com/scales/#scales-with-continuous-input-and-continuous-output)
- [scales with continuous input and discrete output](https://www.d3indepth.com/scales/#scales-with-continuous-input-and-discrete-output)
- [scales with discrete input and discrete output](https://www.d3indepth.com/scales/#scales-with-discrete-input-and-discrete-output)

### continuous input & output
#### scaleLinear
linear function interpolation, value to position/lengths (e.g. bar charts, line charts)
```js
let linearScale = d3.scaleLinear()  
	.domain([0, 10])  
	.range([0, 600]);
	
linearScale(0);   // returns 0
linearScale(5);   // returns 300
linearScale(10);  // returns 600
```

colors:
```js
let linearScale = d3.scaleLinear()  
	.domain([0, 10])  
	.range(['yellow', 'red']);
	
linearScale(0);   // returns "rgb(255, 255, 0)"
linearScale(5);   // returns "rgb(255, 128, 0)"
linearScale(10);  // returns "rgb(255, 0, 0)"
```

#### scaleSqrt
for sizing circles by area (rather than radius).
https://observablehq.com/@d3/continuous-scales#scale_sqrt
```js
let sqrtScale = d3.scaleSqrt()  
	.domain([0, 100])  
	.range([0, 30]);

sqrtScale(0);   // returns 0
sqrtScale(50);  // returns 21.21...
sqrtScale(100); // returns 30
```

#### scalePow
 interpolates using a power function $y=mx^k+c$.
 `scaleSqrt` is `d3.scalePow().exponent(1/2)`.

#### scaleLog
$y=m*log(x)+b$

#### scaleTime
```js
timeScale = d3.scaleTime()  
	.domain([new Date(2016, 0, 1), new Date(2017, 0, 1)])  
	.range([0, 700]);
	
timeScale(new Date(2016, 0, 1));   // returns 0
timeScale(new Date(2016, 6, 1));   // returns 348.00...
timeScale(new Date(2017, 0, 1));   // returns 700
```

#### scaleSequential
mapping continuous values to an output range determined by a preset (or custom) **interpolator**.

### clamping

By default `scaleLinear`, `scalePow`, `scaleSqrt`, `scaleLog`, `scaleTime` and `scaleSequential` **extrapolate** when the **input value goes beyond the domain.**

For example:

```js
let linearScale = d3.scaleLinear()  .domain([0, 10])  .range([0, 100]);
linearScale(20);  // returns 200linearScale(-10); // returns -100
```

You can **clamp** the scale function so that the input value stays within the domain using `.clamp`:

```js
linearScale.clamp(true);linearScale(20);  // returns 100linearScale(-10); // returns 0
```

You can switch off clamping using `.clamp(false)`.

### nice()
output首尾值round

### multiple segment
domain & range 多个段
```js
let linearScale = d3.scaleLinear()  
	.domain([-10, 0, 10])  
	.range(['red', '#ddd', 'blue']);
	
linearScale(-10);  // returns "rgb(255, 0, 0)"
linearScale(0);    // returns "rgb(221, 221, 221)"
linearScale(5);    // returns "rgb(111, 111, 238)"
```

### inversion
given output，get input
e.g. when you want to convert a user's click along an axis into a domain value
```js
let linearScale = d3.scaleLinear()  
	.domain([0, 10])  
	.range([0, 100]);
	
linearScale.invert(50);   // returns 5
linearScale.invert(100);  // returns 10
```

### continuous input & discrete output

#### scaleQunatize
```js
let quantizeScale = d3.scaleQuantize()  
	.domain([0, 100])  
	.range(['lightblue', 'orange', 'lightgreen', 'pink']);
	
quantizeScale(10);  // returns 'lightblue'
quantizeScale(30);  // returns 'orange'
quantizeScale(90);  // returns 'pink'
```

![](assets/Pasted%20image%2020240920183017.png)

#### scaleQuantile
The domain is defined by **an array of numbers**
```js
let myData = [0, 5, 7, 10, 20, 30, 35, 40, 60, 62, 65, 70, 80, 90, 100];

let quantileScale = d3.scaleQuantile()  
	.domain(myData)  
	.range(['lightblue', 'orange', 'lightgreen']);
	
quantileScale(0);   // returns 'lightblue'
quantileScale(20);  // returns 'lightblue'
quantileScale(30);  // returns 'orange'
quantileScale(65);  // returns 'lightgreen'
```

![](assets/Pasted%20image%2020240920183027.png)
#### scaleThreshold
https://www.d3indepth.com/scales/#scalethreshold

```js
let thresholdScale = d3.scaleThreshold()  
	.domain([0, 50, 100])  
	.range(['#ccc', 'lightblue', 'orange', '#ccc']);
	
thresholdScale(-10);  // returns '#ccc'
thresholdScale(20);   // returns 'lightblue'
thresholdScale(70);   // returns 'orange'
thresholdScale(110);  // returns '#ccc'
```

![](assets/Pasted%20image%2020240920183124.png)

### discrete input & output

#### scaleOrdinal

#### scaleBand

#### scalePoint

## shapes

The shapes in the above examples are made up of **SVG `path` elements.** Each of them has a **`d` attribute (path data)** which defines the shape of the path.

|                                                            |                                                                                      |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| [line](https://www.d3indepth.com/shapes/#line-generator)   | Generates path data for a multi-segment line (typically for line charts)             |
| [area](https://www.d3indepth.com/shapes/#area-generator)   | Generates path data for an area (typically for stacked line charts and streamgraphs) |
| [stack](https://www.d3indepth.com/shapes/#stack-generator) | Generates stack data from multi-series data                                          |
| [arc](https://www.d3indepth.com/shapes/#arc-generator)     | Generates path data for an arc (typically for pie charts)                            |
| [pie](https://www.d3indepth.com/shapes/#pie-generator)     | Generates pie angle data from array of data                                          |
| [symbol](https://www.d3indepth.com/shapes/#symbols)        | Generates path data for symbols such as plus, star, diamond                          |
### line generator

```js
var lineGenerator = d3.line();

var points = [];
for(var i = 0; i < 200; i++)
	points.push([ i * 3, Math.random() * 100 ]);

// generate line chart
var pathData = lineGenerator(points);

d3.select('path')
	.attr('d', pathData);
```

**.x, .y methods**: specify how the line generator interprets each array element using the methods `.x` and `.y`.

```js
var xScale = d3.scaleLinear().domain([0, 6]).range([0, 600]);
var yScale = d3.scaleLinear().domain([0, 80]).range([150, 0]);

// use .x, .y to handle x y seperately
var lineGenerator = d3.line()
	.x(function(d, i) {
		return xScale(i);
	})
	.y(function(d) {
		return yScale(d.value);
	});

var data = [
	{value: 10}, 
	{value: 50}, 
	{value: 30}, 
	{value: 40}, 
	{value: 20}, 
	{value: 70},
	{value: 50}
];

var line = lineGenerator(data);

// Create a path element and set its d attribute
d3.select('g')
	.append('path')
	.attr('d', line);
```

**.defined()** configure the behaviour when there's **missing data**

```js
// leaves a gap where data is missing
var lineGenerator = d3.line()
	.defined(function(d) {
		return d !== null;
	});
```

**.curve()**
平滑插值
```js
var lineGenerator = d3.line()  
	.curve(d3.curveCardinal);
```

curve types: 
- pass through the points: (`curveLinear`, `curveCardinal`, `curveCatmullRom`, `curveMonotone`, `curveNatural` and `curveStep`) 
- don't pass the points: (`curveBasis` and `curveBundle`).

### render to canvas

By default the shape generators output SVG path data. However they can be configured to draw to a canvas element using the `.context()` function:
```js
var context = d3.select('canvas').node().getContext('2d');

lineGenerator.context(context);

context.strokeStyle = '#999';
context.beginPath();
lineGenerator(points);
context.stroke();
```

### Radial line

极坐标系line generator
```js
var radialLineGenerator = d3.radialLine();

// [angle, radius]
var points = [
	[0, 80],
	[Math.PI * 0.25, 80],
	[Math.PI * 0.5, 30],
	[Math.PI * 0.75, 80],
	[Math.PI, 80],
	[Math.PI * 1.25, 80],
	[Math.PI * 1.5, 80],
	[Math.PI * 1.75, 80],
	[Math.PI * 2, 80]
];

var radialLine = radialLineGenerator(points);

d3.select('g')
	.append('path')
	.attr('d', radialLine);
```

![](assets/Pasted%20image%2020240921191045.png)

can also use `.angle()` & `.radius()`

```js
var points = [
  { a: 0, r: 80 },
  { a: Math.PI * 0.25, r: 80 },
  { a: Math.PI * 0.5, r: 30 },
  { a: Math.PI * 0.75, r: 80 },
  ...
];

radialLineGenerator
  .angle(function (d) {
    return d.a;
  })
  .radius(function (d) {
    return d.r;
  });

var pathData = radialLineGenerator(points);
```

### area generator

generates the area **between `y=0` and a multi-segment line** defined by an array of points:

```js
var areaGenerator = d3.area();

var points = [
  [0, 80],
  [100, 100],
  [200, 30],
  [300, 50],
  [400, 40],
  [500, 80],
];

var pathData = areaGenerator(points);
```

configure the baseline using the `.y0` method:
```js
areaGenerator.y0(150);
```

You can also pass accessor functions into the `.y0` and `.y1` methods:
```js
var yScale = d3.scaleLinear().domain([0, 100]).range([200, 0]);

var points = [
	{x: 0, low: 30, high: 80},
	{x: 100, low: 80, high: 100},
	{x: 200, low: 20, high: 30},
	{x: 300, low: 20, high: 50},
	{x: 400, low: 10, high: 40},
	{x: 500, low: 50, high: 80}
];

var areaGenerator = d3.area()
	.x(function(d) {
		return d.x;
	})
	.y0(function(d) {
		return yScale(d.low);
	})
	.y1(function(d) {
		return yScale(d.high);
	});

var area = areaGenerator(points);

// Create a path element and set its d attribute
d3.select('g')
	.append('path')
	.attr('d', area);
  
```

![](assets/Pasted%20image%2020240921193543.png)

### radial area

极坐标area
```js
var radialAreaGenerator = d3.radialArea()
	.angle(function(d) {
		return d.angle;
	})
	.innerRadius(function(d) {
		return d.r0;
	})
	.outerRadius(function(d) {
		return d.r1;
	});

var points = [
	{angle: 0, r0: 20, r1: 80},
	{angle: Math.PI * 0.25, r0: 20, r1: 40},
	{angle: Math.PI * 0.5, r0: 20, r1: 80},
	{angle: Math.PI * 0.75, r0: 20, r1: 40},
	{angle: Math.PI, r0: 20, r1: 80},
	{angle: Math.PI * 1.25, r0: 20, r1: 40},
	{angle: Math.PI * 1.5, r0: 20, r1: 80},
	{angle: Math.PI * 1.75, r0: 20, r1: 40},
	{angle: Math.PI * 2, r0: 20, r1: 80}
];

var pathData = radialAreaGenerator(points);

// Create a path element and set its d attribute
d3.select('g')
	.append('path')
	.attr('d', pathData);
```

![](assets/Pasted%20image%2020240921193837.png)

### stack generator

 Each array contains **lower and upper values** for each data point. The lower and upper values are computed so that each series is stacked on top of the previous series.
```js
var colors = ['#FBB65B', '#513551', '#de3163'];

var data = [
  {day: 'Mon', apricots: 120, blueberries: 180, cherries: 100},
  {day: 'Tue', apricots: 60, blueberries: 185, cherries: 105},
  {day: 'Wed', apricots: 100, blueberries: 215, cherries: 110},
  {day: 'Thu', apricots: 80, blueberries: 230, cherries: 105},
  {day: 'Fri', apricots: 120, blueberries: 240, cherries: 105}
];

// stack generator
var stack = d3.stack()
  .keys(['apricots', 'blueberries', 'cherries']);

var stackedSeries = stack(data);

// stackedSeries = [
//   [ [0, 120],   [0, 60],   [0, 100],    [0, 80],    [0, 120] ],   // Apricots
//   [ [120, 300], [60, 245], [100, 315],  [80, 310],  [120, 360] ], // Blueberries
//   [ [300, 400], [245, 350], [315, 425], [310, 415], [360, 465] ]  // Cherries
// ]

// stacked bar charts
// Create a g element for each series
var g = d3.select('g')
	.selectAll('g.series')
	.data(stackedSeries)
	.enter()
	.append('g')
	.classed('series', true)
	.style('fill', function(d, i) {
		return colors[i];
	});

// For each series create a rect element for each day
g.selectAll('rect')
	.data(function(d) {
		return d;
	})
	.join('rect')
	.attr('width', function(d) {
		return d[1] - d[0];
	})
	.attr('x', function(d) {
		return d[0];
	})
	.attr('y', function(d, i) {
		return i * 20;
	})
	.attr('height', 19);
  
```

![](assets/Pasted%20image%2020240921202703.png)

stacked line charts:
```js

var yScale = d3.scaleLinear().domain([0, 600]).range([200, 0]);

var areaGenerator = d3.area()
	.x(function(d, i) {
		return i * 100;
	})
	.y0(function(d) {
		return yScale(d[0]);
	})
	.y1(function(d) {
		return yScale(d[1]);
	});

var colors = ['#FBB65B', '#513551', '#de3163'];

var data = [
	{day: 'Mon', apricots: 120, blueberries: 180, cherries: 100},
	{day: 'Tue', apricots: 60, blueberries: 185, cherries: 105},
	{day: 'Wed', apricots: 100, blueberries: 215, cherries: 110},
	{day: 'Thu', apricots: 80, blueberries: 230, cherries: 105},
	{day: 'Fri', apricots: 120, blueberries: 240, cherries: 105}
];

var stack = d3.stack()
	.keys(['apricots', 'blueberries', 'cherries']);

var stackedSeries = stack(data);

d3.select('g')
	.selectAll('path')
	.data(stackedSeries)
	.join('path')
	.style('fill', function(d, i) {
		return colors[i];
	})
	.attr('d', areaGenerator) // passed a shape generator instead of SVG
```

**stack.order()**

|                      |                                                        |
| -------------------- | ------------------------------------------------------ |
| stackOrderNone       | (Default) Series in same order as specified in .keys() |
| stackOrderAscending  | Smallest series at the bottom                          |
| stackOrderDescending | Largest series at the bottom                           |
| stackOrderInsideOut  | Largest series in the middle                           |
| stackOrderReverse    | Reverse of stackOrderNone                              |
**stack.offset()**
  
|   |   |
|---|---|
|stackOffsetNone|(Default) No offset|
|stackOffsetExpand|Sum of series is normalised (to a value of 1)|
|stackOffsetSilhouette|Center of stacks is at y=0|
|stackOffsetWiggle|Wiggle of layers is minimised (typically used for streamgraphs)|

### arc generator

```js
var pieGenerator = d3.pie()
	.value(function(d) {return d.quantity;})
	.sort(function(a, b) {
		return a.name.localeCompare(b.name);
	});

var fruits = [
	{name: 'Apples', quantity: 20},
	{name: 'Bananas', quantity: 40},
	{name: 'Cherries', quantity: 50},
	{name: 'Damsons', quantity: 10},
	{name: 'Elderberries', quantity: 30},
];

// Create an arc generator with configuration
var arcGenerator = d3.arc()
	.innerRadius(20)
	.outerRadius(100);

var arcData = pieGenerator(fruits);

// Create a path element and set its d attribute
d3.select('g')
	.selectAll('path')
	.data(arcData)
	.join('path')
	.attr('d', arcGenerator);


// Labels
d3.select('g')
	.selectAll('text')
	.data(arcData)
	.join('text')
	.each(function(d) {
		var centroid = arcGenerator.centroid(d);
		d3.select(this)
			.attr('x', centroid[0])
			.attr('y', centroid[1])
			.attr('dy', '0.33em')
			.text(d.data.name);
	});

```

![](assets/Pasted%20image%2020240922202741.png)

## Axes

You need two things to create a D3 axis:
- an SVG element to contain the axis (usually a `g` element)
- a D3 [scale function](https://www.d3indepth.com/scales)

To create an axis:
- make an axis generator function using `d3.axisBottom`, `d3.axisTop`, `d3.axisLeft` or `d3.axisRight` (and pass in your scale function)
- select the container element and pass the axis generator into `.call`

```html
<svg width="600" height="100">  
	<g transform="translate(20, 50)"></g>
</svg>
```

```js
let scale = d3.scaleLinear().domain([0, 100]).range([0, 500]);

let axis = d3.axisBottom(scale);

d3.select('svg g')
	.call(axis);
```

### orientation

`d3.axisBottom`, `d3.axisTop`, `d3.axisLeft` and `d3.axisRight`

### scale types

`scaleLinear`, `scaleSqrt`, `scaleLog`, `scaleTime`, `scaleBand` and `scalePoint`.

