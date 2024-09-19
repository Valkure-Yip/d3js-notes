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

