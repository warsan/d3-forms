# d3-формы

Визуализации обычно состоят из отдельных графических меток, таких как [символы](#symbols), [дуги](#arcs), [линии](#lines) и [области](#areas). Хотя прямоугольники гистограммы могут быть достаточно просты для непосредственного создания с использованием [SVG](http://www.w3.org/TR/SVG/paths.html#PathData) или [Canvas](http://www.w3.org/TR/2dcontext/#canvaspathmethods), другие формы являются сложными, например, закругленные кольцевые сектора и центростремительные сплайны Катмулла-Рома. Этот модуль предоставляет различные генераторы формы для вашего удобства.

Как и в других аспектах D3, эти формы управляются данными: каждый генератор форм предоставляет средства доступа, которые управляют тем, как входные данные отображаются в визуальном представлении. Например, вы можете определить генератор строк для временного ряда, [масштабируя](https://github.com/d3/d3-scale) поля ваших данных, чтобы они соответствовали диаграмме:  

```js
var line = d3.line()
    .x(function(d) { return x(d.date); })
    .y(function(d) { return y(d.value); });
```
Этот генератор строк может затем использоваться для вычисления `d` атрибута элемента пути SVG:  

```js
path.datum(data).attr("d", line);
```
Или вы можете использовать его для рендеринга в 2D-контекст Canvas:  

```js
line.context(context)(data);
```
Для получения дополнительной информации прочитайте [Введение в d3-формы](https://medium.com/@mbostock/introducing-d3-shape-73f8367e6d12).

## Установка

Если вы используете НПМ, то `npm install d3-shape`. В противном случае загрузите [последнюю версию](https://github.com/d3/d3-shape/releases/latest). Вы также можете загрузить напрямую из [d3js.org](https://d3js.org), либо как отдельную библиотеку, либо как часть [D3 4.0](https://github.com/d3/d3). Поддерживаются AMD, CommonJS и vanilla. В ванили `d3` экспортируется глобально:

```html
<script src="https://d3js.org/d3-path.v1.min.js"></script>
<script src="https://d3js.org/d3-shape.v1.min.js"></script>
<script>

var line = d3.line();

</script>
```

[Попробуйте d3-shape в вашем браузере.](https://tonicdev.com/npm/d3-shape)  

## Справочник по API

* [Дуги](#arcs)
* [Сектора](#pies)
* [Линии](#lines)
* [Области](#areas)
* [Кривые](#curves)
* [Custom Curves](#custom-curves)
* [Связи](#links)
* [Знаки](#symbols)
* [Custom Symbol Types](#custom-symbol-types)
* [Кучи](#stacks)

### Дуги

[<img alt="Pie Chart" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/pie.png" width="295" height="295">](http://bl.ocks.org/mbostock/8878e7fd82034f1d63cf)[<img alt="Donut Chart" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/donut.png" width="295" height="295">](http://bl.ocks.org/mbostock/2394b23da1994fc202e1)

Генератор дуги создает [круговой](https://en.wikipedia.org/wiki/Circular_sector) или [кольцевой](https://en.wikipedia.org/wiki/Annulus_\(mathematics\)) сектор, как на круговой или кольцевой диаграмме. Если разница между [начальным](#arc_startAngle) и [конечным](#arc_endAngle) углами (*угловой промежуток*) больше, чем [τ](https://en.wikipedia.org/wiki/Turn_\(geometry\)#Tau_proposal), генератор дуги создаст полный круг или затрубное пространство. Если оно меньше τ, дуги могут иметь [закруглённые углы](#arc_cornerRadius) и [угловое заполнение](#arc_padAngle). Дуги всегда центрированы на ⟨0,0⟩; трансформируйте (см: [SVG](http://www.w3.org/TR/SVG/coords.html#TransformAttribute), [Canvas](http://www.w3.org/TR/2dcontext/#transformations)), чтобы переместить дугу в другое положение.

Смотрите также [круговой генератор](#pies), вычисляющий необходимые углы к массивам данных в виде круговой или кольцевой диаграммы; эти углы могут быть переданы на генератор дуги.

<a name="arc" href="#arc">#</a> d3.<b>arc</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js "Source")

Создаёт новый генератор дуги с настройками по умолчанию.

<a name="_arc" href="#_arc">#</a> <i>arc</i>(<i>arguments…</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L89 "Source")

Создает дугу для заданных *аргументов* . Эти *аргументы* являются произвольными; они просто распространяются на функции доступа генератора дуги вместе с `this` объектом. Например, с настройками по умолчанию ожидается объект с радиусами и углами:

```js
var arc = d3.arc();

arc({
  innerRadius: 0,
  outerRadius: 100,
  startAngle: 0,
  endAngle: Math.PI / 2
}); // "M0,-100A100,100,0,0,1,100,0L0,0Z"
```

Если радиусы и углы определены как константы, вы можете создать дугу без каких-либо аргументов:

```js
var arc = d3.arc()
    .innerRadius(0)
    .outerRadius(100)
    .startAngle(0)
    .endAngle(Math.PI / 2);

arc(); // "M0,-100A100,100,0,0,1,100,0L0,0Z"
```

Если генератор дуги имеет [контекст](#arc_context), то дуга отображается в этом контексте как последовательность вызовов [метода пути](http://www.w3.org/TR/2dcontext/#canvaspathmethods), и эта функция возвращает void.  
В противном случае возвращается строка [данных пути](http://www.w3.org/TR/SVG/paths.html#PathData).

<a name="arc_centroid" href="#arc_centroid">#</a> <i>arc</i>.<b>centroid</b>(<i>arguments…</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L224 "Source")

Вычисляет среднюю точку [*x*, *y*] центральной линии дуги, которая будет [сгенерирована](#_arc) данными *аргументами*. Эти *аргументы* являются произвольными; они просто распространяются на функции доступа генератора дуги вместе с `this` объектом. Чтобы соответствовать сгенерированной дуге, методы доступа должны быть детерминированными, *то есть* возвращать одно и то же значение при одинаковых аргументах. Средняя точка определяется как ([startAngle](#arc_startAngle) + [endAngle](#arc_endAngle)) / 2 и ( [innerRadius](#arc_innerRadius) + [outerRadius](#arc_outerRadius)) / 2. Например:

[<img alt="Circular Sector Centroids" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/centroid-circular-sector.png" width="250" height="250">](http://bl.ocks.org/mbostock/9b5a2fd1ce1a146f27e4)[<img alt="Annular Sector Centroids" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/centroid-annular-sector.png" width="250" height="250">](http://bl.ocks.org/mbostock/c274877f647361f3df7d)

Обратите внимание, что это **не геометрический центр** дуги, который может находиться за пределами дуги; этот метод просто удобен для размещения меток.

<a name="arc_innerRadius" href="#arc_innerRadius">#</a> <i>arc</i>.<b>innerRadius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L230 "Source")

Если указан *радиус*, то устанавливает внутренний радиус для указанной функции или числа и возвращает этот генератор дуги.  
Если *радиус* не указан, возвращает текущий метод доступа к внутреннему радиусу, который по умолчанию:

```js
function innerRadius(d) {
  return d.innerRadius;
}
```

Указание внутреннего радиуса как функции полезно для построения столбчатой диаграммы с накоплением, часто в сочетании со [шкалой sqrt](https://github.com/d3/d3-scale#sqrt). Чаще всего постоянный внутренний радиус используется для кольцевой или круговой диаграммы. Если внешний радиус меньше внутреннего радиуса, внутренний и внешний радиусы меняются местами. Отрицательное значение рассматривается как ноль.

<a name="arc_outerRadius" href="#arc_outerRadius">#</a> <i>arc</i>.<b>outerRadius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L234 "Source")

Если указан *радиус*, устанавливает внешний радиус для указанной функции или числа и возвращает этот генератор дуги.  
Если *радиус* не указан, возвращает текущий метод доступа к внешнему радиусу, который по умолчанию:

```js
function outerRadius(d) {
  return d.outerRadius;
}
```

Указание внешнего радиуса в качестве функции полезно для построения диаграмм Кокса или Полярной гистограммы, часто в сочетании со [шкалой sqrt](https://github.com/d3/d3-scale#sqrt). Чаще всего постоянный внешний радиус используется для круговой или кольцевой диаграммы. Если внешний радиус меньше внутреннего радиуса, внутренний и внешний радиусы меняются местами. Отрицательное значение рассматривается как ноль.

<a name="arc_cornerRadius" href="#arc_cornerRadius">#</a> <i>arc</i>.<b>cornerRadius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L238 "Source")

Если указан *радиус* , устанавливает угловой радиус для указанной функции или числа и возвращает этот генератор дуги. Если *радиус* не указан, возвращает текущий метод доступа к радиусу угла, который по умолчанию:

```js
function cornerRadius() {
  return 0;
}
```

Если радиус угла больше нуля, углы дуги округляются с использованием окружностей данного радиуса. Для круглого сектора два внешних угла закруглены; для кольцевого сектора все четыре угла закруглены. Угловые круги показаны на этой диаграмме:

[<img alt="Rounded Circular Sectors" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/rounded-circular-sector.png" width="250" height="250">](http://bl.ocks.org/mbostock/e5e3680f3079cf5c3437)[<img alt="Rounded Annular Sectors" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/rounded-annular-sector.png" width="250" height="250">](http://bl.ocks.org/mbostock/f41f50e06a6c04828b6e)

Радиус угла не может быть больше ([outerRadius](#arc_outerRadius) - [innerRadius](#arc_innerRadius)) / 2. Кроме того, для дуг, угловой диапазон которых меньше π, радиус угла может быть уменьшен при пересечении двух смежных закругленных углов. Это чаще всего происходит с внутренними углами. Смотрите [анимацию углов дуги](http://bl.ocks.org/mbostock/b7671cb38efdfa5da3af) для иллюстрации.

<a name="arc_startAngle" href="#arc_startAngle">#</a> <i>arc</i>.<b>startAngle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L246 "Source")

Если указан *угол*, задает начальный угол для указанной функции или числа и возвращает этот генератор дуги. Если *угол* не указан, возвращает текущий начальный угол доступа, который по умолчанию:

```js
function startAngle(d) {
  return d.startAngle;
}
```

*Угол* задается в радианах, с 0 на - *у* (12 часов) и положительных углов , идущих по часовой стрелке. Если |endAngle - startAngle| ≥ τ, создается полный круг или кольцо, а не сектор.

<a name="arc_endAngle" href="#arc_endAngle">#</a> <i>arc</i>.<b>endAngle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L250 "Source")

Если указан *угол*, устанавливает конечный угол для указанной функции или числа и возвращает этот генератор дуги. Если *угол* не указан, возвращает текущий метод доступа к конечному углу, который по умолчанию:

```js
function endAngle(d) {
  return d.endAngle;
}
```

*Угол* задается в радианах, с 0 на - *у* (12 часов) и положительных углов , идущих по часовой стрелке. Если |endAngle - startAngle| ≥ τ, создается полный круг или кольцо, а не сектор.

<a name="arc_padAngle" href="#arc_padAngle">#</a> <i>arc</i>.<b>padAngle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L254 "Source")

Если указан *угол*, устанавливает угол пэда для указанной функции или числа и возвращает этот генератор дуги. Если *угол* не указан, возвращает текущий метод доступа к углу площадки, который по умолчанию:

```js
function padAngle() {
  return d && d.padAngle;
}
```

Угол площадки преобразуется в фиксированное линейное расстояние, разделяющее соседние дуги, определяемое как [padRadius](#arc_padRadius) * padAngle. Это расстояние вычитается одинаково из [начала](#arc_startAngle) и [конца](#arc_endAngle) дуги. Если дуга образует полный круг или кольцо, как в случае |endAngle - startAngle| ≥ τ, угол площадки игнорируется.

Если [внутренний радиус](#arc_innerRadius) или угловой промежуток мал по отношению к углу площадки, может быть невозможно поддерживать параллельные края между соседними дугами. В этом случае внутренний край дуги может свалиться в точку, похожую на круговой сектор. По этой причине заполнение обычно применяется только к кольцевым секторам ( *то есть*, когда innerRadius положителен), как показано на этой диаграмме:

[<img alt="Padded Circular Sectors" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/padded-circular-sector.png" width="250" height="250">](http://bl.ocks.org/mbostock/f37b07b92633781a46f7)[<img alt="Padded Annular Sectors" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/padded-annular-sector.png" width="250" height="250">](http://bl.ocks.org/mbostock/99f0a6533f7c949cf8b8)

Рекомендуемый минимальный внутренний радиус при использовании заполнения - outerRadius \* padAngle / sin(θ), где θ - угловой диапазон наименьшей дуги перед заполнением. Например, если внешний радиус составляет 200 пикселей, а угол площадки равен 0,02 радиана, разумное значение θ составляет 0,04 радиана, а разумный внутренний радиус равен 100 пикселям. Смотрите [анимацию заполнения дуги](http://bl.ocks.org/mbostock/053fcc2295a445afab07) для иллюстрации.

Часто угол заполнения не задается непосредственно на генераторе дуги, а вместо этого вычисляется [круговым генератором](#pies), чтобы гарантировать, что площадь дополненных дуг пропорциональна их значению; смотрите [*пирог*.padAngle](#pie_padAngle). См. [Анимацию заполнения пирога](http://bl.ocks.org/mbostock/3e961b4c97a1b543fff2) для иллюстрации. Если вы применяете постоянный угол заполнения непосредственно к генератору дуги, он имеет тенденцию непропорционально вычитаться из меньших дуг, создавая искажение.

<a name="arc_padRadius" href="#arc_padRadius">#</a> <i>arc</i>.<b>padRadius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L242 "Source")

Если указан *радиус* , устанавливает радиус пэда для указанной функции или числа и возвращает этот генератор дуги. Если *radius* не указан, возвращает текущий метод доступа к радиусу пэда, который по умолчанию равен нулю, указывая, что радиус пэда должен автоматически вычисляться как sqrt([innerRadius](#arc_innerRadius) * innerRadius + [outerRadius](#arc_outerRadius) * outerRadius). Радиус площадки определяет фиксированное линейное расстояние, разделяющее соседние дуги, определяемое как padRadius * [padAngle](#arc_padAngle).

<a name="arc_context" href="#arc_context">#</a> <i>arc</i>.<b>context</b>([<i>context</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/arc.js#L258 "Source")

Если указан *контекст*, устанавливает контекст и возвращает этот генератор дуги. Если *контекст* не указан, возвращает текущий контекст, который по умолчанию равен нулю. Если контекст не нулевой, то [сгенерированная дуга](#_arc) визуализируется в этот контекст как последовательность вызовов [метода пути](http://www.w3.org/TR/2dcontext/#canvaspathmethods). В противном случае возвращается строка [данных пути](http://www.w3.org/TR/SVG/paths.html#PathData), представляющая созданную дугу.

### Сектора

Круговой генератор не создает форму непосредственно, а вместо этого вычисляет необходимые углы для представления табличного набора данных в виде круговой или кольцевой диаграммы; эти углы могут быть переданы на [генератор дуги](#arcs).

<a name="pie" href="#pie">#</a> d3.<b>pie</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/pie.js "Source")

Создает новый круговой генератор с настройками по умолчанию.

<a name="_pie" href="#_pie">#</a> <i>pie</i>(<i>data</i>[, <i>arguments…</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/pie.js#L14 "Source")

Создает круговую диаграмму для данного массива *данных*, возвращая массив объектов, представляющих углы дуги каждого элемента. Любые дополнительные *аргументы* являются произвольными; они просто распространяются на функции доступа к генератору кругового генератора вместе с `this` объектом. Длина возвращаемого массива совпадает с *данными*, и каждый элемент *i* в возвращенном массиве соответствует элементу *i* во входных данных. Каждый объект в возвращаемом массиве имеет следующие свойства:

* `data` - исходные данные; соответствующий элемент во входном массиве данных.
* `value` - числовое [значение](#pie_value) дуги.
* `index` - [отсортированный по нулю индекс](#pie_sort) дуги.
* `startAngle` - [начальный угол](#pie_startAngle) дуги.
* `endAngle` - [конечный угол](#pie_endAngle) дуги.
* `padAngle` - [угол площадки](#pie_padAngle) дуги.

Это представление предназначено для работы со стандартными аксессорами [startAngle](#arc_startAngle), [endAngle](#arc_endAngle) и [padAngle](#arc_padAngle) генератора дуги. Угловые единицы являются произвольными, но если вы планируете использовать круговой генератор в сочетании с [генератором дуги](#arcs), вы должны указать углы в радианах, с 0 at -*y* (12 часов) и положительными углами, идущими по часовой стрелке.

Учитывая небольшой набор данных чисел, вот как вычислить углы дуги, чтобы представить эти данные в виде круговой диаграммы:

```js
var data = [1, 1, 2, 3, 5, 8, 13, 21];
var arcs = d3.pie()(data);
```

Первая пара скобок, `pie()`, [строит](#pie) круговой генератор по умолчанию. Второй, `pie()(data)`, [вызывает](#_pie) этот генератор на набор данных, возвращая массив объектов:

```json
[
  {"data":  1, "value":  1, "index": 6, "startAngle": 6.050474740247008, "endAngle": 6.166830023713296, "padAngle": 0},
  {"data":  1, "value":  1, "index": 7, "startAngle": 6.166830023713296, "endAngle": 6.283185307179584, "padAngle": 0},
  {"data":  2, "value":  2, "index": 5, "startAngle": 5.817764173314431, "endAngle": 6.050474740247008, "padAngle": 0},
  {"data":  3, "value":  3, "index": 4, "startAngle": 5.468698322915565, "endAngle": 5.817764173314431, "padAngle": 0},
  {"data":  5, "value":  5, "index": 3, "startAngle": 4.886921905584122, "endAngle": 5.468698322915565, "padAngle": 0},
  {"data":  8, "value":  8, "index": 2, "startAngle": 3.956079637853813, "endAngle": 4.886921905584122, "padAngle": 0},
  {"data": 13, "value": 13, "index": 1, "startAngle": 2.443460952792061, "endAngle": 3.956079637853813, "padAngle": 0},
  {"data": 21, "value": 21, "index": 0, "startAngle": 0.000000000000000, "endAngle": 2.443460952792061, "padAngle": 0}
]
```

Note that the returned array is in the same order as the data, even though this pie chart is [sorted](#pie_sortValues) by descending value, starting with the arc for the last datum (value 21) at 12 o’clock.

<a name="pie_value" href="#pie_value">#</a> <i>pie</i>.<b>value</b>([<i>value</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/pie.js#L54 "Source")

If *value* is specified, sets the value accessor to the specified function or number and returns this pie generator. If *value* is not specified, returns the current value accessor, which defaults to:

```js
function value(d) {
  return d;
}
```

When a pie is [generated](#_pie), the value accessor will be invoked for each element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. The default value accessor assumes that the input data are numbers, or that they are coercible to numbers using [valueOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf). If your data are not simply numbers, then you should specify an accessor that returns the corresponding numeric value for a given datum. For example:

```js
var data = [
  {"number":  4, "name": "Locke"},
  {"number":  8, "name": "Reyes"},
  {"number": 15, "name": "Ford"},
  {"number": 16, "name": "Jarrah"},
  {"number": 23, "name": "Shephard"},
  {"number": 42, "name": "Kwon"}
];

var arcs = d3.pie()
    .value(function(d) { return d.number; })
    (data);
```

This is similar to [mapping](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) your data to values before invoking the pie generator:

```js
var arcs = d3.pie()(data.map(function(d) { return d.number; }));
```

The benefit of an accessor is that the input data remains associated with the returned objects, thereby making it easier to access other fields of the data, for example to set the color or to add text labels.

<a name="pie_sort" href="#pie_sort">#</a> <i>pie</i>.<b>sort</b>([<i>compare</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/pie.js#L62 "Source")

If *compare* is specified, sets the data comparator to the specified function and returns this pie generator. If *compare* is not specified, returns the current data comparator, which defaults to null. If both the data comparator and the value comparator are null, then arcs are positioned in the original input order. Otherwise, the data is sorted according to the data comparator, and the resulting order is used. Setting the data comparator implicitly sets the [value comparator](#pie_sortValues) to null.

The *compare* function takes two arguments *a* and *b*, each elements from the input data array. If the arc for *a* should be before the arc for *b*, then the comparator must return a number less than zero; if the arc for *a* should be after the arc for *b*, then the comparator must return a number greater than zero; returning zero means that the relative order of *a* and *b* is unspecified. For example, to sort arcs by their associated name:

```js
pie.sort(function(a, b) { return a.name.localeCompare(b.name); });
```

Sorting does not affect the order of the [generated arc array](#_pie) which is always in the same order as the input data array; it merely affects the computed angles of each arc. The first arc starts at the [start angle](#pie_startAngle) and the last arc ends at the [end angle](#pie_endAngle).

<a name="pie_sortValues" href="#pie_sortValues">#</a> <i>pie</i>.<b>sortValues</b>([<i>compare</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/pie.js#L58 "Source")

If *compare* is specified, sets the value comparator to the specified function and returns this pie generator. If *compare* is not specified, returns the current value comparator, which defaults to descending value. The default value comparator is implemented as:

```js
function compare(a, b) {
  return b - a;
}
```

If both the data comparator and the value comparator are null, then arcs are positioned in the original input order. Otherwise, the data is sorted according to the data comparator, and the resulting order is used. Setting the value comparator implicitly sets the [data comparator](#pie_sort) to null.

The value comparator is similar to the [data comparator](#pie_sort), except the two arguments *a* and *b* are values derived from the input data array using the [value accessor](#pie_value), not the data elements. If the arc for *a* should be before the arc for *b*, then the comparator must return a number less than zero; if the arc for *a* should be after the arc for *b*, then the comparator must return a number greater than zero; returning zero means that the relative order of *a* and *b* is unspecified. For example, to sort arcs by ascending value:

```js
pie.sortValues(function(a, b) { return a - b; });
```

Sorting does not affect the order of the [generated arc array](#_pie) which is always in the same order as the input data array; it merely affects the computed angles of each arc. The first arc starts at the [start angle](#pie_startAngle) and the last arc ends at the [end angle](#pie_endAngle).

<a name="pie_startAngle" href="#pie_startAngle">#</a> <i>pie</i>.<b>startAngle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/pie.js#L66 "Source")

If *angle* is specified, sets the overall start angle of the pie to the specified function or number and returns this pie generator. If *angle* is not specified, returns the current start angle accessor, which defaults to:

```js
function startAngle() {
  return 0;
}
```

The start angle here means the *overall* start angle of the pie, *i.e.*, the start angle of the first arc. The start angle accessor is invoked once, being passed the same arguments and `this` context as the [pie generator](#_pie). The units of *angle* are arbitrary, but if you plan to use the pie generator in conjunction with an [arc generator](#arcs), you should specify an angle in radians, with 0 at -*y* (12 o’clock) and positive angles proceeding clockwise.

<a name="pie_endAngle" href="#pie_endAngle">#</a> <i>pie</i>.<b>endAngle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/pie.js#L70 "Source")

If *angle* is specified, sets the overall end angle of the pie to the specified function or number and returns this pie generator. If *angle* is not specified, returns the current end angle accessor, which defaults to:

```js
function endAngle() {
  return 2 * Math.PI;
}
```

The end angle here means the *overall* end angle of the pie, *i.e.*, the end angle of the last arc. The end angle accessor is invoked once, being passed the same arguments and `this` context as the [pie generator](#_pie). The units of *angle* are arbitrary, but if you plan to use the pie generator in conjunction with an [arc generator](#arcs), you should specify an angle in radians, with 0 at -*y* (12 o’clock) and positive angles proceeding clockwise.

The value of the end angle is constrained to [startAngle](#pie_startAngle) ± τ, such that |endAngle - startAngle| ≤ τ.

<a name="pie_padAngle" href="#pie_padAngle">#</a> <i>pie</i>.<b>padAngle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/pie.js#L74 "Source")

If *angle* is specified, sets the pad angle to the specified function or number and returns this pie generator. If *angle* is not specified, returns the current pad angle accessor, which defaults to:

```js
function padAngle() {
  return 0;
}
```

The pad angle here means the angular separation between each adjacent arc. The total amount of padding reserved is the specified *angle* times the number of elements in the input data array, and at most |endAngle - startAngle|; the remaining space is then divided proportionally by [value](#pie_value) such that the relative area of each arc is preserved. See the [pie padding animation](http://bl.ocks.org/mbostock/3e961b4c97a1b543fff2) for illustration. The pad angle accessor is invoked once, being passed the same arguments and `this` context as the [pie generator](#_pie). The units of *angle* are arbitrary, but if you plan to use the pie generator in conjunction with an [arc generator](#arcs), you should specify an angle in radians.

### Lines

[<img width="295" height="154" alt="Line Chart" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/line.png">](http://bl.ocks.org/mbostock/1550e57e12e73b86ad9e)

The line generator produces a [spline](https://en.wikipedia.org/wiki/Spline_\(mathematics\)) or [polyline](https://en.wikipedia.org/wiki/Polygonal_chain), as in a line chart. Lines also appear in many other visualization types, such as the links in [hierarchical edge bundling](http://bl.ocks.org/mbostock/7607999).

<a name="line" href="#line">#</a> d3.<b>line</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/line.js "Source")

Constructs a new line generator with the default settings.

<a name="_line" href="#_line">#</a> <i>line</i>(<i>data</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/line.js#L14 "Source")

Generates a line for the given array of *data*. Depending on this line generator’s associated [curve](#line_curve), the given input *data* may need to be sorted by *x*-value before being passed to the line generator. If the line generator has a [context](#line_context), then the line is rendered to this context as a sequence of [path method](http://www.w3.org/TR/2dcontext/#canvaspathmethods) calls and this function returns void. Otherwise, a [path data](http://www.w3.org/TR/SVG/paths.html#PathData) string is returned.

<a name="line_x" href="#line_x">#</a> <i>line</i>.<b>x</b>([<i>x</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/line.js#L34 "Source")

If *x* is specified, sets the x accessor to the specified function or number and returns this line generator. If *x* is not specified, returns the current x accessor, which defaults to:

```js
function x(d) {
  return d[0];
}
```

When a line is [generated](#_line), the x accessor will be invoked for each [defined](#line_defined) element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. The default x accessor assumes that the input data are two-element arrays of numbers. If your data are in a different format, or if you wish to transform the data before rendering, then you should specify a custom accessor. For example, if `x` is a [time scale](https://github.com/d3/d3-scale#time-scales) and `y` is a [linear scale](https://github.com/d3/d3-scale#linear-scales):

```js
var data = [
  {date: new Date(2007, 3, 24), value: 93.24},
  {date: new Date(2007, 3, 25), value: 95.35},
  {date: new Date(2007, 3, 26), value: 98.84},
  {date: new Date(2007, 3, 27), value: 99.92},
  {date: new Date(2007, 3, 30), value: 99.80},
  {date: new Date(2007, 4,  1), value: 99.47},
  …
];

var line = d3.line()
    .x(function(d) { return x(d.date); })
    .y(function(d) { return y(d.value); });
```

<a name="line_y" href="#line_y">#</a> <i>line</i>.<b>y</b>([<i>y</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/line.js#L38 "Source")

If *y* is specified, sets the y accessor to the specified function or number and returns this line generator. If *y* is not specified, returns the current y accessor, which defaults to:

```js
function y(d) {
  return d[1];
}
```

When a line is [generated](#_line), the y accessor will be invoked for each [defined](#line_defined) element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. The default y accessor assumes that the input data are two-element arrays of numbers. See [*line*.x](#line_x) for more information.

<a name="line_defined" href="#line_defined">#</a> <i>line</i>.<b>defined</b>([<i>defined</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/line.js#L42 "Source")

If *defined* is specified, sets the defined accessor to the specified function or boolean and returns this line generator. If *defined* is not specified, returns the current defined accessor, which defaults to:

```js
function defined() {
  return true;
}
```

The default accessor thus assumes that the input data is always defined. When a line is [generated](#_line), the defined accessor will be invoked for each element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. If the given element is defined (*i.e.*, if the defined accessor returns a truthy value for this element), the [x](#line_x) and [y](#line_y) accessors will subsequently be evaluated and the point will be added to the current line segment. Otherwise, the element will be skipped, the current line segment will be ended, and a new line segment will be generated for the next defined point. As a result, the generated line may have several discrete segments. For example:

[<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/line-defined.png" width="480" height="250" alt="Line with Missing Data">](http://bl.ocks.org/mbostock/0533f44f2cfabecc5e3a)

Note that if a line segment consists of only a single point, it may appear invisible unless rendered with rounded or square [line caps](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/stroke-linecap). In addition, some curves such as [curveCardinalOpen](#curveCardinalOpen) only render a visible segment if it contains multiple points.

<a name="line_curve" href="#line_curve">#</a> <i>line</i>.<b>curve</b>([<i>curve</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/line.js#L46 "Source")

If *curve* is specified, sets the [curve factory](#curves) and returns this line generator. If *curve* is not specified, returns the current curve factory, which defaults to [curveLinear](#curveLinear).

<a name="line_context" href="#line_context">#</a> <i>line</i>.<b>context</b>([<i>context</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/line.js#L50 "Source")

If *context* is specified, sets the context and returns this line generator. If *context* is not specified, returns the current context, which defaults to null. If the context is not null, then the [generated line](#_line) is rendered to this context as a sequence of [path method](http://www.w3.org/TR/2dcontext/#canvaspathmethods) calls. Otherwise, a [path data](http://www.w3.org/TR/SVG/paths.html#PathData) string representing the generated line is returned.

<a name="lineRadial" href="#lineRadial">#</a> d3.<b>lineRadial</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/lineRadial.js "Source")

<img alt="Radial Line" width="250" height="250" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/line-radial.png">

Constructs a new radial line generator with the default settings. A radial line generator is equivalent to the standard Cartesian [line generator](#line), except the [x](#line_x) and [y](#line_y) accessors are replaced with [angle](#lineRadial_angle) and [radius](#lineRadial_radius) accessors. Radial lines are always positioned relative to ⟨0,0⟩; use a transform (see: [SVG](http://www.w3.org/TR/SVG/coords.html#TransformAttribute), [Canvas](http://www.w3.org/TR/2dcontext/#transformations)) to change the origin.

<a name="_lineRadial" href="#_lineRadial">#</a> <i>lineRadial</i>(<i>data</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/lineRadial.js#L4 "Source")

Equivalent to [*line*](#_line).

<a name="lineRadial_angle" href="#lineRadial_angle">#</a> <i>lineRadial</i>.<b>angle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/lineRadial.js#L7 "Source")

Equivalent to [*line*.x](#line_x), except the accessor returns the angle in radians, with 0 at -*y* (12 o’clock).

<a name="lineRadial_radius" href="#lineRadial_radius">#</a> <i>lineRadial</i>.<b>radius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/lineRadial.js#L8 "Source")

Equivalent to [*line*.y](#line_y), except the accessor returns the radius: the distance from the origin ⟨0,0⟩.

<a name="lineRadial_defined" href="#lineRadial_defined">#</a> <i>lineRadial</i>.<b>defined</b>([<i>defined</i>])

Equivalent to [*line*.defined](#line_defined).

<a name="lineRadial_curve" href="#lineRadial_curve">#</a> <i>lineRadial</i>.<b>curve</b>([<i>curve</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/lineRadial.js#L10 "Source")

Equivalent to [*line*.curve](#line_curve). Note that [curveMonotoneX](#curveMonotoneX) or [curveMonotoneY](#curveMonotoneY) are not recommended for radial lines because they assume that the data is monotonic in *x* or *y*, which is typically untrue of radial lines.

<a name="lineRadial_context" href="#lineRadial_context">#</a> <i>lineRadial</i>.<b>context</b>([<i>context</i>])

Equivalent to [*line*.context](#line_context).

### Areas

[<img alt="Area Chart" width="295" height="154" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/area.png">](http://bl.ocks.org/mbostock/3883195)[<img alt="Stacked Area Chart" width="295" height="154" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/area-stacked.png">](http://bl.ocks.org/mbostock/3885211)[<img alt="Difference Chart" width="295" height="154" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/area-difference.png">](http://bl.ocks.org/mbostock/3894205)

The area generator produces an area, as in an area chart. An area is defined by two bounding [lines](#lines), either splines or polylines. Typically, the two lines share the same [*x*-values](#area_x) ([x0](#area_x0) = [x1](#area_x1)), differing only in *y*-value ([y0](#area_y0) and [y1](#area_y1)); most commonly, y0 is defined as a constant representing [zero](http://www.vox.com/2015/11/19/9758062/y-axis-zero-chart). The first line (the <i>topline</i>) is defined by x1 and y1 and is rendered first; the second line (the <i>baseline</i>) is defined by x0 and y0 and is rendered second, with the points in reverse order. With a [curveLinear](#curveLinear) [curve](#area_curve), this produces a clockwise polygon.

<a name="area" href="#area">#</a> d3.<b>area</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/area.js "Source")

Constructs a new area generator with the default settings.

<a name="_area" href="#_area">#</a> <i>area</i>(<i>data</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L17 "Source")

Generates an area for the given array of *data*. Depending on this area generator’s associated [curve](#area_curve), the given input *data* may need to be sorted by *x*-value before being passed to the area generator. If the area generator has a [context](#line_context), then the area is rendered to this context as a sequence of [path method](http://www.w3.org/TR/2dcontext/#canvaspathmethods) calls and this function returns void. Otherwise, a [path data](http://www.w3.org/TR/SVG/paths.html#PathData) string is returned.

<a name="area_x" href="#area_x">#</a> <i>area</i>.<b>x</b>([<i>x</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L59 "Source")

If *x* is specified, sets [x0](#area_x0) to *x* and [x1](#area_x1) to null and returns this area generator. If *x* is not specified, returns the current x0 accessor.

<a name="area_x0" href="#area_x0">#</a> <i>area</i>.<b>x0</b>([<i>x</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L63 "Source")

If *x* is specified, sets the x0 accessor to the specified function or number and returns this area generator. If *x* is not specified, returns the current x0 accessor, which defaults to:

```js
function x(d) {
  return d[0];
}
```

When an area is [generated](#_area), the x0 accessor will be invoked for each [defined](#area_defined) element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. The default x0 accessor assumes that the input data are two-element arrays of numbers. If your data are in a different format, or if you wish to transform the data before rendering, then you should specify a custom accessor. For example, if `x` is a [time scale](https://github.com/d3/d3-scale#time-scales) and `y` is a [linear scale](https://github.com/d3/d3-scale#linear-scales):

```js
var data = [
  {date: new Date(2007, 3, 24), value: 93.24},
  {date: new Date(2007, 3, 25), value: 95.35},
  {date: new Date(2007, 3, 26), value: 98.84},
  {date: new Date(2007, 3, 27), value: 99.92},
  {date: new Date(2007, 3, 30), value: 99.80},
  {date: new Date(2007, 4,  1), value: 99.47},
  …
];

var area = d3.area()
    .x(function(d) { return x(d.date); })
    .y1(function(d) { return y(d.value); })
    .y0(y(0));
```

<a name="area_x1" href="#area_x1">#</a> <i>area</i>.<b>x1</b>([<i>x</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L67 "Source")

If *x* is specified, sets the x1 accessor to the specified function or number and returns this area generator. If *x* is not specified, returns the current x1 accessor, which defaults to null, indicating that the previously-computed [x0](#area_x0) value should be reused for the x1 value.

When an area is [generated](#_area), the x1 accessor will be invoked for each [defined](#area_defined) element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. See [*area*.x0](#area_x0) for more information.

<a name="area_y" href="#area_y">#</a> <i>area</i>.<b>y</b>([<i>y</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L71 "Source")

If *y* is specified, sets [y0](#area_y0) to *y* and [y1](#area_y1) to null and returns this area generator. If *y* is not specified, returns the current y0 accessor.

<a name="area_y0" href="#area_y0">#</a> <i>area</i>.<b>y0</b>([<i>y</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L75 "Source")

If *y* is specified, sets the y0 accessor to the specified function or number and returns this area generator. If *y* is not specified, returns the current y0 accessor, which defaults to:

```js
function y() {
  return 0;
}
```

When an area is [generated](#_area), the y0 accessor will be invoked for each [defined](#area_defined) element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. See [*area*.x0](#area_x0) for more information.

<a name="area_y1" href="#area_y1">#</a> <i>area</i>.<b>y1</b>([<i>y</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L79 "Source")

If *y* is specified, sets the y1 accessor to the specified function or number and returns this area generator. If *y* is not specified, returns the current y1 accessor, which defaults to:

```js
function y(d) {
  return d[1];
}
```

A null accessor is also allowed, indicating that the previously-computed [y0](#area_y0) value should be reused for the y1 value. When an area is [generated](#_area), the y1 accessor will be invoked for each [defined](#area_defined) element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. See [*area*.x0](#area_x0) for more information.

<a name="area_defined" href="#area_defined">#</a> <i>area</i>.<b>defined</b>([<i>defined</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L96 "Source")

If *defined* is specified, sets the defined accessor to the specified function or boolean and returns this area generator. If *defined* is not specified, returns the current defined accessor, which defaults to:

```js
function defined() {
  return true;
}
```

The default accessor thus assumes that the input data is always defined. When an area is [generated](#_area), the defined accessor will be invoked for each element in the input data array, being passed the element `d`, the index `i`, and the array `data` as three arguments. If the given element is defined (*i.e.*, if the defined accessor returns a truthy value for this element), the [x0](#area_x0), [x1](#area_x1), [y0](#area_y0) and [y1](#area_y1) accessors will subsequently be evaluated and the point will be added to the current area segment. Otherwise, the element will be skipped, the current area segment will be ended, and a new area segment will be generated for the next defined point. As a result, the generated area may have several discrete segments. For example:

[<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/area-defined.png" width="480" height="250" alt="Area with Missing Data">](http://bl.ocks.org/mbostock/3035090)

Note that if an area segment consists of only a single point, it may appear invisible unless rendered with rounded or square [line caps](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/stroke-linecap). In addition, some curves such as [curveCardinalOpen](#curveCardinalOpen) only render a visible segment if it contains multiple points.

<a name="area_curve" href="#area_curve">#</a> <i>area</i>.<b>curve</b>([<i>curve</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L100 "Source")

If *curve* is specified, sets the [curve factory](#curves) and returns this area generator. If *curve* is not specified, returns the current curve factory, which defaults to [curveLinear](#curveLinear).

<a name="area_context" href="#area_context">#</a> <i>area</i>.<b>context</b>([<i>context</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L104 "Source")

If *context* is specified, sets the context and returns this area generator. If *context* is not specified, returns the current context, which defaults to null. If the context is not null, then the [generated area](#_area) is rendered to this context as a sequence of [path method](http://www.w3.org/TR/2dcontext/#canvaspathmethods) calls. Otherwise, a [path data](http://www.w3.org/TR/SVG/paths.html#PathData) string representing the generated area is returned.

<a name="area_lineX0" href="#area_lineX0">#</a> <i>area</i>.<b>lineX0</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L83 "Source")
<br><a name="area_lineY0" href="#area_lineY0">#</a> <i>area</i>.<b>lineY0</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L84 "Source")

Returns a new [line generator](#lines) that has this area generator’s current [defined accessor](#area_defined), [curve](#area_curve) and [context](#area_context). The line’s [*x*-accessor](#line_x) is this area’s [*x0*-accessor](#area_x0), and the line’s [*y*-accessor](#line_y) is this area’s [*y0*-accessor](#area_y0).

<a name="area_lineX1" href="#area_lineX1">#</a> <i>area</i>.<b>lineX1</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L92 "Source")

Returns a new [line generator](#lines) that has this area generator’s current [defined accessor](#area_defined), [curve](#area_curve) and [context](#area_context). The line’s [*x*-accessor](#line_x) is this area’s [*x1*-accessor](#area_x1), and the line’s [*y*-accessor](#line_y) is this area’s [*y0*-accessor](#area_y0).

<a name="area_lineY1" href="#area_lineY1">#</a> <i>area</i>.<b>lineY1</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/area.js#L88 "Source")

Returns a new [line generator](#lines) that has this area generator’s current [defined accessor](#area_defined), [curve](#area_curve) and [context](#area_context). The line’s [*x*-accessor](#line_x) is this area’s [*x0*-accessor](#area_x0), and the line’s [*y*-accessor](#line_y) is this area’s [*y1*-accessor](#area_y1).

<a name="areaRadial" href="#areaRadial">#</a> d3.<b>areaRadial</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js "Source")

<img alt="Radial Area" width="250" height="250" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/area-radial.png">

Constructs a new radial area generator with the default settings. A radial area generator is equivalent to the standard Cartesian [area generator](#area), except the [x](#area_x) and [y](#area_y) accessors are replaced with [angle](#areaRadial_angle) and [radius](#areaRadial_radius) accessors. Radial areas are always positioned relative to ⟨0,0⟩; use a transform (see: [SVG](http://www.w3.org/TR/SVG/coords.html#TransformAttribute), [Canvas](http://www.w3.org/TR/2dcontext/#transformations)) to change the origin.

<a name="_areaRadial" href="#_areaRadial">#</a> <i>areaRadial</i>(<i>data</i>)

Equivalent to [*area*](#_area).

<a name="areaRadial_angle" href="#areaRadial_angle">#</a> <i>areaRadial</i>.<b>angle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L13 "Source")

Equivalent to [*area*.x](#area_x), except the accessor returns the angle in radians, with 0 at -*y* (12 o’clock).

<a name="areaRadial_startAngle" href="#areaRadial_startAngle">#</a> <i>areaRadial</i>.<b>startAngle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L14 "Source")

Equivalent to [*area*.x0](#area_x0), except the accessor returns the angle in radians, with 0 at -*y* (12 o’clock). Note: typically [angle](#areaRadial_angle) is used instead of setting separate start and end angles.

<a name="areaRadial_endAngle" href="#areaRadial_endAngle">#</a> <i>areaRadial</i>.<b>endAngle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L15 "Source")

Equivalent to [*area*.x1](#area_x1), except the accessor returns the angle in radians, with 0 at -*y* (12 o’clock). Note: typically [angle](#areaRadial_angle) is used instead of setting separate start and end angles.

<a name="areaRadial_radius" href="#areaRadial_radius">#</a> <i>areaRadial</i>.<b>radius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L16 "Source")

Equivalent to [*area*.y](#area_y), except the accessor returns the radius: the distance from the origin ⟨0,0⟩.

<a name="areaRadial_innerRadius" href="#areaRadial_innerRadius">#</a> <i>areaRadial</i>.<b>innerRadius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L17 "Source")

Equivalent to [*area*.y0](#area_y0), except the accessor returns the radius: the distance from the origin ⟨0,0⟩.

<a name="areaRadial_outerRadius" href="#areaRadial_outerRadius">#</a> <i>areaRadial</i>.<b>outerRadius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L18 "Source")

Equivalent to [*area*.y1](#area_y1), except the accessor returns the radius: the distance from the origin ⟨0,0⟩.

<a name="areaRadial_defined" href="#areaRadial_defined">#</a> <i>areaRadial</i>.<b>defined</b>([<i>defined</i>])

Equivalent to [*area*.defined](#area_defined).

<a name="areaRadial_curve" href="#areaRadial_curve">#</a> <i>areaRadial</i>.<b>curve</b>([<i>curve</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L24 "Source")

Equivalent to [*area*.curve](#area_curve). Note that [curveMonotoneX](#curveMonotoneX) or [curveMonotoneY](#curveMonotoneY) are not recommended for radial areas because they assume that the data is monotonic in *x* or *y*, which is typically untrue of radial areas.

<a name="areaRadial_context" href="#areaRadial_context">#</a> <i>areaRadial</i>.<b>context</b>([<i>context</i>])

Equivalent to [*line*.context](#line_context).

<a name="areaRadial_lineStartAngle" href="#areaRadial_lineStartAngle">#</a> <i>areaRadial</i>.<b>lineStartAngle</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L19 "Source")
<br><a name="areaRadial_lineInnerRadius" href="#areaRadial_lineInnerRadius">#</a> <i>areaRadial</i>.<b>lineInnerRadius</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L21 "Source")

Returns a new [radial line generator](#lineRadial) that has this radial area generator’s current [defined accessor](#areaRadial_defined), [curve](#areaRadial_curve) and [context](#areaRadial_context). The line’s [angle accessor](#lineRadial_angle) is this area’s [start angle accessor](#areaRadial_startAngle), and the line’s [radius accessor](#lineRadial_radius) is this area’s [inner radius accessor](#areaRadial_innerRadius).

<a name="areaRadial_lineEndAngle" href="#areaRadial_lineEndAngle">#</a> <i>areaRadial</i>.<b>lineEndAngle</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L20 "Source")

Returns a new [radial line generator](#lineRadial) that has this radial area generator’s current [defined accessor](#areaRadial_defined), [curve](#areaRadial_curve) and [context](#areaRadial_context). The line’s [angle accessor](#lineRadial_angle) is this area’s [end angle accessor](#areaRadial_endAngle), and the line’s [radius accessor](#lineRadial_radius) is this area’s [inner radius accessor](#areaRadial_innerRadius).

<a name="areaRadial_lineOuterRadius" href="#areaRadial_lineOuterRadius">#</a> <i>areaRadial</i>.<b>lineOuterRadius</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/areaRadial.js#L22 "Source")

Returns a new [radial line generator](#lineRadial) that has this radial area generator’s current [defined accessor](#areaRadial_defined), [curve](#areaRadial_curve) and [context](#areaRadial_context). The line’s [angle accessor](#lineRadial_angle) is this area’s [start angle accessor](#areaRadial_startAngle), and the line’s [radius accessor](#lineRadial_radius) is this area’s [outer radius accessor](#areaRadial_outerRadius).

### Curves

While [lines](#lines) are defined as a sequence of two-dimensional [*x*, *y*] points, and [areas](#areas) are similarly defined by a topline and a baseline, there remains the task of transforming this discrete representation into a continuous shape: *i.e.*, how to interpolate between the points. A variety of curves are provided for this purpose.

Curves are typically not constructed or used directly, instead being passed to [*line*.curve](#line_curve) and [*area*.curve](#area_curve). For example:

```js
var line = d3.line()
    .x(function(d) { return x(d.date); })
    .y(function(d) { return y(d.value); })
    .curve(d3.curveCatmullRom.alpha(0.5));
```

<a name="curveBasis" href="#curveBasis">#</a> d3.<b>curveBasis</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/basis.js#L12 "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/basis.png" width="888" height="240" alt="basis">

Produces a cubic [basis spline](https://en.wikipedia.org/wiki/B-spline) using the specified control points. The first and last points are triplicated such that the spline starts at the first point and ends at the last point, and is tangent to the line between the first and second points, and to the line between the penultimate and last points.

<a name="curveBasisClosed" href="#curveBasisClosed">#</a> d3.<b>curveBasisClosed</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/basisClosed.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/basisClosed.png" width="888" height="240" alt="basisClosed">

Produces a closed cubic [basis spline](https://en.wikipedia.org/wiki/B-spline) using the specified control points. When a line segment ends, the first three control points are repeated, producing a closed loop with C2 continuity.

<a name="curveBasisOpen" href="#curveBasisOpen">#</a> d3.<b>curveBasisOpen</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/basisOpen.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/basisOpen.png" width="888" height="240" alt="basisOpen">

Produces a cubic [basis spline](https://en.wikipedia.org/wiki/B-spline) using the specified control points. Unlike [basis](#basis), the first and last points are not repeated, and thus the curve typically does not intersect these points.

<a name="curveBundle" href="#curveBundle">#</a> d3.<b>curveBundle</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/bundle.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/bundle.png" width="888" height="240" alt="bundle">

Produces a straightened cubic [basis spline](https://en.wikipedia.org/wiki/B-spline) using the specified control points, with the spline straightened according to the curve’s [*beta*](#curveBundle_beta), which defaults to 0.85. This curve is typically used in [hierarchical edge bundling](http://bl.ocks.org/mbostock/7607999) to disambiguate connections, as proposed by [Danny Holten](https://www.win.tue.nl/vis1/home/dholten/) in [Hierarchical Edge Bundles: Visualization of Adjacency Relations in Hierarchical Data](https://www.win.tue.nl/vis1/home/dholten/papers/bundles_infovis.pdf). This curve does not implement [*curve*.areaStart](#curve_areaStart) and [*curve*.areaEnd](#curve_areaEnd); it is intended to work with [d3.line](#lines), not [d3.area](#areas).

<a name="curveBundle_beta" href="#curveBundle_beta">#</a> <i>bundle</i>.<b>beta</b>(<i>beta</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/bundle.js#L51 "Source")

Returns a bundle curve with the specified *beta* in the range [0, 1], representing the bundle strength. If *beta* equals zero, a straight line between the first and last point is produced; if *beta* equals one, a standard [basis](#basis) spline is produced. For example:

```js
var line = d3.line().curve(d3.curveBundle.beta(0.5));
```

<a name="curveCardinal" href="#curveCardinal">#</a> d3.<b>curveCardinal</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/cardinal.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/cardinal.png" width="888" height="240" alt="cardinal">

Produces a cubic [cardinal spline](https://en.wikipedia.org/wiki/Cubic_Hermite_spline#Cardinal_spline) using the specified control points, with one-sided differences used for the first and last piece. The default [tension](#curveCardinal_tension) is 0.

<a name="curveCardinalClosed" href="#curveCardinalClosed">#</a> d3.<b>curveCardinalClosed</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/cardinalClosed.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/cardinalClosed.png" width="888" height="240" alt="cardinalClosed">

Produces a closed cubic [cardinal spline](https://en.wikipedia.org/wiki/Cubic_Hermite_spline#Cardinal_spline) using the specified control points. When a line segment ends, the first three control points are repeated, producing a closed loop. The default [tension](#curveCardinal_tension) is 0.

<a name="curveCardinalOpen" href="#curveCardinalOpen">#</a> d3.<b>curveCardinalOpen</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/cardinalOpen.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/cardinalOpen.png" width="888" height="240" alt="cardinalOpen">

Produces a cubic [cardinal spline](https://en.wikipedia.org/wiki/Cubic_Hermite_spline#Cardinal_spline) using the specified control points. Unlike [curveCardinal](#curveCardinal), one-sided differences are not used for the first and last piece, and thus the curve starts at the second point and ends at the penultimate point. The default [tension](#curveCardinal_tension) is 0.

<a name="curveCardinal_tension" href="#curveCardinal_tension">#</a> <i>cardinal</i>.<b>tension</b>(<i>tension</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/cardinalOpen.js#L44 "Source")

Returns a cardinal curve with the specified *tension* in the range [0, 1]. The *tension* determines the length of the tangents: a *tension* of one yields all zero tangents, equivalent to [curveLinear](#curveLinear); a *tension* of zero produces a uniform [Catmull–Rom](#curveCatmullRom) spline. For example:

```js
var line = d3.line().curve(d3.curveCardinal.tension(0.5));
```

<a name="curveCatmullRom" href="#curveCatmullRom">#</a> d3.<b>curveCatmullRom</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/catmullRom.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/catmullRom.png" width="888" height="240" alt="catmullRom">

Produces a cubic Catmull–Rom spline using the specified control points and the parameter [*alpha*](#curveCatmullRom_alpha), which defaults to 0.5, as proposed by Yuksel et al. in [On the Parameterization of Catmull–Rom Curves](http://www.cemyuksel.com/research/catmullrom_param/), with one-sided differences used for the first and last piece.

<a name="curveCatmullRomClosed" href="#curveCatmullRomClosed">#</a> d3.<b>curveCatmullRomClosed</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/catmullRomClosed.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/catmullRomClosed.png" width="888" height="330" alt="catmullRomClosed">

Produces a closed cubic Catmull–Rom spline using the specified control points and the parameter [*alpha*](#curveCatmullRom_alpha), which defaults to 0.5, as proposed by Yuksel et al. When a line segment ends, the first three control points are repeated, producing a closed loop.

<a name="curveCatmullRomOpen" href="#curveCatmullRomOpen">#</a> d3.<b>curveCatmullRomOpen</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/catmullRomOpen.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/catmullRomOpen.png" width="888" height="240" alt="catmullRomOpen">

Produces a cubic Catmull–Rom spline using the specified control points and the parameter [*alpha*](#curveCatmullRom_alpha), which defaults to 0.5, as proposed by Yuksel et al. Unlike [curveCatmullRom](#curveCatmullRom), one-sided differences are not used for the first and last piece, and thus the curve starts at the second point and ends at the penultimate point.

<a name="curveCatmullRom_alpha" href="#curveCatmullRom_alpha">#</a> <i>catmullRom</i>.<b>alpha</b>(<i>alpha</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/catmullRom.js#L83 "Source")

Returns a cubic Catmull–Rom curve with the specified *alpha* in the range [0, 1]. If *alpha* is zero, produces a uniform spline, equivalent to [curveCardinal](#curveCardinal) with a tension of zero; if *alpha* is one, produces a chordal spline; if *alpha* is 0.5, produces a [centripetal spline](https://en.wikipedia.org/wiki/Centripetal_Catmull–Rom_spline). Centripetal splines are recommended to avoid self-intersections and overshoot. For example:

```js
var line = d3.line().curve(d3.curveCatmullRom.alpha(0.5));
```

<a name="curveLinear" href="#curveLinear">#</a> d3.<b>curveLinear</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/linear.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/linear.png" width="888" height="240" alt="linear">

Produces a polyline through the specified points.

<a name="curveLinearClosed" href="#curveLinearClosed">#</a> d3.<b>curveLinearClosed</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/linearClosed.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/linearClosed.png" width="888" height="240" alt="linearClosed">

Produces a closed polyline through the specified points by repeating the first point when the line segment ends.

<a name="curveMonotoneX" href="#curveMonotoneX">#</a> d3.<b>curveMonotoneX</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/monotone.js#L98 "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/monotoneX.png" width="888" height="240" alt="monotoneX">

Produces a cubic spline that [preserves monotonicity](https://en.wikipedia.org/wiki/Monotone_cubic_interpolation) in *y*, assuming monotonicity in *x*, as proposed by Steffen in [A simple method for monotonic interpolation in one dimension](http://adsabs.harvard.edu/full/1990A%26A...239..443S): “a smooth curve with continuous first-order derivatives that passes through any given set of data points without spurious oscillations. Local extrema can occur only at grid points where they are given by the data, but not in between two adjacent grid points.”

<a name="curveMonotoneY" href="#curveMonotoneY">#</a> d3.<b>curveMonotoneY</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/monotone.js#L102 "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/monotoneY.png" width="888" height="240" alt="monotoneY">

Produces a cubic spline that [preserves monotonicity](https://en.wikipedia.org/wiki/Monotone_cubic_interpolation) in *x*, assuming monotonicity in *y*, as proposed by Steffen in [A simple method for monotonic interpolation in one dimension](http://adsabs.harvard.edu/full/1990A%26A...239..443S): “a smooth curve with continuous first-order derivatives that passes through any given set of data points without spurious oscillations. Local extrema can occur only at grid points where they are given by the data, but not in between two adjacent grid points.”

<a name="curveNatural" href="#curveNatural">#</a> d3.<b>curveNatural</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/natural.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/natural.png" width="888" height="240" alt="natural">

Produces a [natural](https://en.wikipedia.org/wiki/Spline_interpolation) [cubic spline](http://mathworld.wolfram.com/CubicSpline.html) with the second derivative of the spline set to zero at the endpoints.

<a name="curveStep" href="#curveStep">#</a> d3.<b>curveStep</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/step.js "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/step.png" width="888" height="240" alt="step">

Produces a piecewise constant function (a [step function](https://en.wikipedia.org/wiki/Step_function)) consisting of alternating horizontal and vertical lines. The *y*-value changes at the midpoint of each pair of adjacent *x*-values.

<a name="curveStepAfter" href="#curveStepAfter">#</a> d3.<b>curveStepAfter</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/step.js#L51 "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/stepAfter.png" width="888" height="240" alt="stepAfter">

Produces a piecewise constant function (a [step function](https://en.wikipedia.org/wiki/Step_function)) consisting of alternating horizontal and vertical lines. The *y*-value changes after the *x*-value.

<a name="curveStepBefore" href="#curveStepBefore">#</a> d3.<b>curveStepBefore</b>(<i>context</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/step.js#L47 "Source")

<img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/stepBefore.png" width="888" height="240" alt="stepBefore">

Produces a piecewise constant function (a [step function](https://en.wikipedia.org/wiki/Step_function)) consisting of alternating horizontal and vertical lines. The *y*-value changes before the *x*-value.

### Custom Curves

Curves are typically not used directly, instead being passed to [*line*.curve](#line_curve) and [*area*.curve](#area_curve). However, you can define your own curve implementation should none of the built-in curves satisfy your needs using the following interface. You can also use this low-level interface with a built-in curve type as an alternative to the line and area generators.

<a name="curve_areaStart" href="#curve_areaStart">#</a> <i>curve</i>.<b>areaStart</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/curve/step.js#L7 "Source")

Indicates the start of a new area segment. Each area segment consists of exactly two [line segments](#curve_lineStart): the topline, followed by the baseline, with the baseline points in reverse order.

<a name="curve_areaEnd" href="#curve_areaEnd">#</a> <i>curve</i>.<b>areaEnd</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/curve/step.js#L10 "Source")

Indicates the end of the current area segment.

<a name="curve_lineStart" href="#curve_lineStart">#</a> <i>curve</i>.<b>lineStart</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/curve/step.js#L13 "Source")

Indicates the start of a new line segment. Zero or more [points](#curve_point) will follow.

<a name="curve_lineEnd" href="#curve_lineEnd">#</a> <i>curve</i>.<b>lineEnd</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/curve/step.js#L17 "Source")

Indicates the end of the current line segment.

<a name="curve_point" href="#curve_point">#</a> <i>curve</i>.<b>point</b>(<i>x</i>, <i>y</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/curve/step.js#L22 "Source")

Indicates a new point in the current line segment with the given *x*- and *y*-values.

### Links

[<img alt="Tidy Tree" src="https://raw.githubusercontent.com/d3/d3-hierarchy/master/img/tree.png">](http://bl.ocks.org/mbostock/9d0899acb5d3b8d839d9d613a9e1fe04)

The **link** shape generates a smooth cubic Bézier curve from a source point to a target point. The tangents of the curve at the start and end are either [vertical](#linkVertical), [horizontal](#linkHorizontal) or [radial](#linkRadial).

<a name="linkVertical" href="#linkVertical">#</a> d3.<b>linkVertical</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L74 "Source")

Returns a new [link generator](#_link) with vertical tangents. For example, to visualize [links](https://github.com/d3/d3-hierarchy/blob/master/README.md#node_links) in a [tree diagram](https://github.com/d3/d3-hierarchy/blob/master/README.md#tree) rooted on the top edge of the display, you might say:

```js
var link = d3.linkVertical()
    .x(function(d) { return d.x; })
    .y(function(d) { return d.y; });
```

<a name="linkHorizontal" href="#linkHorizontal">#</a> d3.<b>linkHorizontal</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L70 "Source")

Returns a new [link generator](#_link) with horizontal tangents. For example, to visualize [links](https://github.com/d3/d3-hierarchy/blob/master/README.md#node_links) in a [tree diagram](https://github.com/d3/d3-hierarchy/blob/master/README.md#tree) rooted on the left edge of the display, you might say:

```js
var link = d3.linkHorizontal()
    .x(function(d) { return d.y; })
    .y(function(d) { return d.x; });
```

<a href="#_link" name="_link">#</a> <i>link</i>(<i>arguments…</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L21 "Source")

Generates a link for the given *arguments*. The *arguments* are arbitrary; they are simply propagated to the link generator’s accessor functions along with the `this` object. For example, with the default settings, an object expected:

```js
link({
  source: [100, 100],
  target: [300, 300]
});
```

<a name="link_source" href="#link_source">#</a> <i>link</i>.<b>source</b>([<i>source</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L28 "Source")

If *source* is specified, sets the source accessor to the specified function and returns this link generator. If *source* is not specified, returns the current source accessor, which defaults to:

```js
function source(d) {
  return d.source;
}
```

<a name="link_target" href="#link_target">#</a> <i>link</i>.<b>target</b>([<i>target</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L32 "Source")

If *target* is specified, sets the target accessor to the specified function and returns this link generator. If *target* is not specified, returns the current target accessor, which defaults to:

```js
function target(d) {
  return d.target;
}
```

<a name="link_x" href="#link_x">#</a> <i>link</i>.<b>x</b>([<i>x</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L36 "Source")

If *x* is specified, sets the *x*-accessor to the specified function or number and returns this link generator. If *x* is not specified, returns the current *x*-accessor, which defaults to:

```js
function x(d) {
  return d[0];
}
```

<a name="link_y" href="#link_y">#</a> <i>link</i>.<b>y</b>([<i>y</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L40 "Source")

If *y* is specified, sets the *y*-accessor to the specified function or number and returns this link generator. If *y* is not specified, returns the current *y*-accessor, which defaults to:

```js
function y(d) {
  return d[1];
}
```

<a name="link_context" href="#link_context">#</a> <i>link</i>.<b>context</b>([<i>context</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L44 "Source")

If *context* is specified, sets the context and returns this link generator. If *context* is not specified, returns the current context, which defaults to null. If the context is not null, then the [generated link](#_link) is rendered to this context as a sequence of [path method](http://www.w3.org/TR/2dcontext/#canvaspathmethods) calls. Otherwise, a [path data](http://www.w3.org/TR/SVG/paths.html#PathData) string representing the generated link is returned. See also [d3-path](https://github.com/d3/d3-path).

<a name="linkRadial" href="#linkRadial">#</a> d3.<b>linkRadial</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L78 "Source")

Returns a new [link generator](#_link) with radial tangents. For example, to visualize [links](https://github.com/d3/d3-hierarchy/blob/master/README.md#node_links) in a [tree diagram](https://github.com/d3/d3-hierarchy/blob/master/README.md#tree) rooted in the center of the display, you might say:

```js
var link = d3.linkRadial()
    .angle(function(d) { return d.x; })
    .radius(function(d) { return d.y; });
```

<a name="linkRadial_angle" href="#linkRadial_angle">#</a> <i>linkRadial</i>.<b>angle</b>([<i>angle</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L80 "Source")

Equivalent to [*link*.x](#link_x), except the accessor returns the angle in radians, with 0 at -*y* (12 o’clock).

<a name="linkRadial_radius" href="#linkRadial_radius">#</a> <i>linkRadial</i>.<b>radius</b>([<i>radius</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/link/index.js#L81 "Source")

Equivalent to [*link*.y](#link_y), except the accessor returns the radius: the distance from the origin ⟨0,0⟩.

### Symbols

<a href="#symbolCircle"><img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/circle.png" width="100" height="100"></a><a href="#symbolCross"><img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/cross.png" width="100" height="100"></a><a href="#symbolDiamond"><img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/diamond.png" width="100" height="100"></a><a href="#symbolSquare"><img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/square.png" width="100" height="100"></a><a href="#symbolStar"><img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/star.png" width="100" height="100"></a><a href="#symbolTriangle"><img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/triangle.png" width="100" height="100"><a href="#symbolWye"><img src="https://raw.githubusercontent.com/d3/d3-shape/master/img/wye.png" width="100" height="100"></a>

Symbols provide a categorical shape encoding as is commonly used in scatterplots. Symbols are always centered at ⟨0,0⟩; use a transform (see: [SVG](http://www.w3.org/TR/SVG/coords.html#TransformAttribute), [Canvas](http://www.w3.org/TR/2dcontext/#transformations)) to move the symbol to a different position.

<a name="symbol" href="#symbol">#</a> d3.<b>symbol</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/symbol.js "Source")

Constructs a new symbol generator with the default settings.

<a name="_symbol" href="#_symbol">#</a> <i>symbol</i>(<i>arguments</i>…) [<>](https://github.com/d3/d3-shape/blob/master/src/symbol.js#L11 "Source")

Generates a symbol for the given *arguments*. The *arguments* are arbitrary; they are simply propagated to the symbol generator’s accessor functions along with the `this` object. For example, with the default settings, no arguments are needed to produce a circle with area 64 square pixels. If the symbol generator has a [context](#symbol_context), then the symbol is rendered to this context as a sequence of [path method](http://www.w3.org/TR/2dcontext/#canvaspathmethods) calls and this function returns void. Otherwise, a [path data](http://www.w3.org/TR/SVG/paths.html#PathData) string is returned.

<a name="symbol_type" href="#symbol_type">#</a> <i>symbol</i>.<b>type</b>([<i>type</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/symbol.js#L33 "Source")

If *type* is specified, sets the symbol type to the specified function or symbol type and returns this line generator. If *type* is a function, the symbol generator’s arguments and *this* are passed through. (See [*selection*.attr](https://github.com/d3/d3-selection/blob/master/README.md#selection_attr) if you are using d3-selection.) If *type* is not specified, returns the current symbol type accessor, which defaults to:

```js
function type() {
  return circle;
}
```

See [symbols](#symbols) for the set of built-in symbol types. To implement a custom symbol type, pass an object that implements [*symbolType*.draw](#symbolType_draw).

<a name="symbol_size" href="#symbol_size">#</a> <i>symbol</i>.<b>size</b>([<i>size</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/symbol.js#L37 "Source")

If *size* is specified, sets the size to the specified function or number and returns this symbol generator. If *size* is a function, the symbol generator’s arguments and *this* are passed through. (See [*selection*.attr](https://github.com/d3/d3-selection/blob/master/README.md#selection_attr) if you are using d3-selection.) If *size* is not specified, returns the current size accessor, which defaults to:

```js
function size() {
  return 64;
}
```

Specifying the size as a function is useful for constructing a scatterplot with a size encoding. If you wish to scale the symbol to fit a given bounding box, rather than by area, try [SVG’s getBBox](http://bl.ocks.org/mbostock/3dd515e692504c92ab65).

<a name="symbol_context" href="#symbol_context">#</a> <i>symbol</i>.<b>context</b>([<i>context</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/symbol.js#L41 "Source")

If *context* is specified, sets the context and returns this symbol generator. If *context* is not specified, returns the current context, which defaults to null. If the context is not null, then the [generated symbol](#_symbol) is rendered to this context as a sequence of [path method](http://www.w3.org/TR/2dcontext/#canvaspathmethods) calls. Otherwise, a [path data](http://www.w3.org/TR/SVG/paths.html#PathData) string representing the generated symbol is returned.

<a name="symbols" href="#symbols">#</a> d3.<b>symbols</b>

An array containing the set of all built-in symbol types: [circle](#symbolCircle), [cross](#symbolCross), [diamond](#symbolDiamond), [square](#symbolSquare), [star](#symbolStar), [triangle](#symbolTriangle), and [wye](#symbolWye). Useful for constructing the range of an [ordinal scale](https://github.com/d3/d3-scale#ordinal-scales) should you wish to use a shape encoding for categorical data.

<a name="symbolCircle" href="#symbolCircle">#</a> d3.<b>symbolCircle</b> [<>](https://github.com/d3/d3-shape/blob/master/src/symbol/circle.js "Source")

The circle symbol type.

<a name="symbolCross" href="#symbolCross">#</a> d3.<b>symbolCross</b> [<>](https://github.com/d3/d3-shape/blob/master/src/symbol/cross.js "Source")

The Greek cross symbol type, with arms of equal length.

<a name="symbolDiamond" href="#symbolDiamond">#</a> d3.<b>symbolDiamond</b> [<>](https://github.com/d3/d3-shape/blob/master/src/symbol/diamond.js "Source")

The rhombus symbol type.

<a name="symbolSquare" href="#symbolSquare">#</a> d3.<b>symbolSquare</b> [<>](https://github.com/d3/d3-shape/blob/master/src/symbol/square.js "Source")

The square symbol type.

<a name="symbolStar" href="#symbolStar">#</a> d3.<b>symbolStar</b> [<>](https://github.com/d3/d3-shape/blob/master/src/symbol/star.js "Source")

The pentagonal star (pentagram) symbol type.

<a name="symbolTriangle" href="#symbolTriangle">#</a> d3.<b>symbolTriangle</b> [<>](https://github.com/d3/d3-shape/blob/master/src/symbol/triangle.js "Source")

The up-pointing triangle symbol type.

<a name="symbolWye" href="#symbolWye">#</a> d3.<b>symbolWye</b> [<>](https://github.com/d3/d3-shape/blob/master/src/symbol/wye.js "Source")

The Y-shape symbol type.

<a name="pointRadial" href="#pointRadial">#</a> d3.<b>pointRadial</b>(<i>angle</i>, <i>radius</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/pointRadial.js "Source")

Returns the point [<i>x</i>, <i>y</i>] for the given *angle* in radians, with 0 at -*y* (12 o’clock) and positive angles proceeding clockwise, and the given *radius*.

### Custom Symbol Types

Symbol types are typically not used directly, instead being passed to [*symbol*.type](#symbol_type). However, you can define your own symbol type implementation should none of the built-in types satisfy your needs using the following interface. You can also use this low-level interface with a built-in symbol type as an alternative to the symbol generator.

<a name="symbolType_draw" href="#symbolType_draw">#</a> <i>symbolType</i>.<b>draw</b>(<i>context</i>, <i>size</i>)

Renders this symbol type to the specified *context* with the specified *size* in square pixels. The *context* implements the [CanvasPathMethods](http://www.w3.org/TR/2dcontext/#canvaspathmethods) interface. (Note that this is a subset of the CanvasRenderingContext2D interface!)

### Stacks

[<img alt="Stacked Bar Chart" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/stacked-bar.png" width="295" height="154">](http://bl.ocks.org/mbostock/3886208)[<img alt="Streamgraph" src="https://raw.githubusercontent.com/d3/d3-shape/master/img/stacked-stream.png" width="295" height="154">](http://bl.ocks.org/mbostock/4060954)

Some shape types can be stacked, placing one shape adjacent to another. For example, a bar chart of monthly sales might be broken down into a multi-series bar chart by product category, stacking bars vertically. This is equivalent to subdividing a bar chart by an ordinal dimension (such as product category) and applying a color encoding.

Stacked charts can show overall value and per-category value simultaneously; however, it is typically harder to compare across categories, as only the bottom layer of the stack is aligned. So, chose the [stack order](#stack_order) carefully, and consider a [streamgraph](#stackOffsetWiggle). (See also [grouped charts](http://bl.ocks.org/mbostock/3887051).)

Like the [pie generator](#pies), the stack generator does not produce a shape directly. Instead it computes positions which you can then pass to an [area generator](#areas) or use directly, say to position bars.

<a name="stack" href="#stack">#</a> d3.<b>stack</b>() [<>](https://github.com/d3/d3-shape/blob/master/src/stack.js "Source")

Constructs a new stack generator with the default settings.

<a name="_stack" href="#_stack">#</a> <i>stack</i>(<i>data</i>[, <i>arguments…</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/stack.js#L16 "Source")

Generates a stack for the given array of *data*, returning an array representing each series. Any additional *arguments* are arbitrary; they are simply propagated to accessors along with the `this` object.

The series are determined by the [keys accessor](#stack_keys); each series *i* in the returned array corresponds to the *i*th key. Each series is an array of points, where each point *j* corresponds to the *j*th element in the input *data*. Lastly, each point is represented as an array [*y0*, *y1*] where *y0* is the lower value (baseline) and *y1* is the upper value (topline); the difference between *y0* and *y1* corresponds to the computed [value](#stack_value) for this point. The key for each series is available as *series*.key, and the [index](#stack_order) as *series*.index. The input data element for each point is available as *point*.data.

For example, consider the following table representing monthly sales of fruits:

Month   | Apples | Bananas | Cherries | Dates
--------|--------|---------|----------|-------
 1/2015 |   3840 |    1920 |      960 |   400
 2/2015 |   1600 |    1440 |      960 |   400
 3/2015 |    640 |     960 |      640 |   400
 4/2015 |    320 |     480 |      640 |   400

This might be represented in JavaScript as an array of objects:

```js
var data = [
  {month: new Date(2015, 0, 1), apples: 3840, bananas: 1920, cherries: 960, dates: 400},
  {month: new Date(2015, 1, 1), apples: 1600, bananas: 1440, cherries: 960, dates: 400},
  {month: new Date(2015, 2, 1), apples:  640, bananas:  960, cherries: 640, dates: 400},
  {month: new Date(2015, 3, 1), apples:  320, bananas:  480, cherries: 640, dates: 400}
];
```

To produce a stack for this data:

```js
var stack = d3.stack()
    .keys(["apples", "bananas", "cherries", "dates"])
    .order(d3.stackOrderNone)
    .offset(d3.stackOffsetNone);

var series = stack(data);
```

The resulting array has one element per *series*. Each series has one point per month, and each point has a lower and upper value defining the baseline and topline:

```js
[
  [[   0, 3840], [   0, 1600], [   0,  640], [   0,  320]], // apples
  [[3840, 5760], [1600, 3040], [ 640, 1600], [ 320,  800]], // bananas
  [[5760, 6720], [3040, 4000], [1600, 2240], [ 800, 1440]], // cherries
  [[6720, 7120], [4000, 4400], [2240, 2640], [1440, 1840]], // dates
]
```

Each series in then typically passed to an [area generator](#areas) to render an area chart, or used to construct rectangles for a bar chart.

<a name="stack_keys" href="#stack_keys">#</a> <i>stack</i>.<b>keys</b>([<i>keys</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/stack.js#L40 "Source")

If *keys* is specified, sets the keys accessor to the specified function or array and returns this stack generator. If *keys* is not specified, returns the current keys accessor, which defaults to the empty array. A series (layer) is [generated](#_stack) for each key. Keys are typically strings, but they may be arbitrary values. The series’ key is passed to the [value accessor](#stack_value), along with each data point, to compute the point’s value.

<a name="stack_value" href="#stack_value">#</a> <i>stack</i>.<b>value</b>([<i>value</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/stack.js#L44 "Source")

If *value* is specified, sets the value accessor to the specified function or number and returns this stack generator. If *value* is not specified, returns the current value accessor, which defaults to:

```js
function value(d, key) {
  return d[key];
}
```

Thus, by default the stack generator assumes that the input data is an array of objects, with each object exposing named properties with numeric values; see [*stack*](#_stack) for an example.

<a name="stack_order" href="#stack_order">#</a> <i>stack</i>.<b>order</b>([<i>order</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/stack.js#L48 "Source")

If *order* is specified, sets the order accessor to the specified function or array and returns this stack generator. If *order* is not specified, returns the current order acccesor, which defaults to [stackOrderNone](#stackOrderNone); this uses the order given by the [key accessor](#stack_key). See [stack orders](#stack-orders) for the built-in orders.

If *order* is a function, it is passed the generated series array and must return an array of numeric indexes representing the stack order. For example, the default order is defined as:

```js
function orderNone(series) {
  var n = series.length, o = new Array(n);
  while (--n >= 0) o[n] = n;
  return o;
}
```

The stack order is computed prior to the [offset](#stack_offset); thus, the lower value for all points is zero at the time the order is computed. The index attribute for each series is also not set until after the order is computed.

<a name="stack_offset" href="#stack_offset">#</a> <i>stack</i>.<b>offset</b>([<i>offset</i>]) [<>](https://github.com/d3/d3-shape/blob/master/src/stack.js#L52 "Source")

If *offset* is specified, sets the offset accessor to the specified function or array and returns this stack generator. If *offset* is not specified, returns the current offset acccesor, which defaults to [stackOffsetNone](#stackOffsetNone); this uses a zero baseline. See [stack offsets](#stack-offsets) for the built-in offsets.

If *offset* is a function, it is passed the generated series array and the order index array. The offset function is then responsible for updating the lower and upper values in the series array to layout the stack. For example, the default offset is defined as:

```js
function offsetNone(series, order) {
  if (!((n = series.length) > 1)) return;
  for (var i = 1, s0, s1 = series[order[0]], n, m = s1.length; i < n; ++i) {
    s0 = s1, s1 = series[order[i]];
    for (var j = 0; j < m; ++j) {
      s1[j][1] += s1[j][0] = s0[j][1];
    }
  }
}
```

### Stack Orders

Stack orders are typically not used directly, but are instead passed to [*stack*.order](#stack_order).

<a name="stackOrderAppearance" href="#stackOrderAppearance">#</a> d3.<b>stackOrderAppearance</b>(<i>series</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/order/appearance.js "Source")

Returns a series order such that the earliest series (according to the maximum value) is at the bottom.

<a name="stackOrderAscending" href="#stackOrderAscending">#</a> d3.<b>stackOrderAscending</b>(<i>series</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/order/ascending.js "Source")

Returns a series order such that the smallest series (according to the sum of values) is at the bottom.

<a name="stackOrderDescending" href="#stackOrderDescending">#</a> d3.<b>stackOrderDescending</b>(<i>series</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/order/descending.js "Source")

Returns a series order such that the largest series (according to the sum of values) is at the bottom.

<a name="stackOrderInsideOut" href="#stackOrderInsideOut">#</a> d3.<b>stackOrderInsideOut</b>(<i>series</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/order/insideOut.js "Source")

Returns a series order such that the earliest series (according to the maximum value) are on the inside and the later series are on the outside. This order is recommended for streamgraphs in conjunction with the [wiggle offset](#stackOffsetWiggle). See [Stacked Graphs—Geometry & Aesthetics](http://leebyron.com/streamgraph/) by Byron & Wattenberg for more information.

<a name="stackOrderNone" href="#stackOrderNone">#</a> d3.<b>stackOrderNone</b>(<i>series</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/order/none.js "Source")

Returns the given series order [0, 1, … *n* - 1] where *n* is the number of elements in *series*. Thus, the stack order is given by the [key accessor](#stack_keys).

<a name="stackOrderReverse" href="#stackOrderReverse">#</a> d3.<b>stackOrderReverse</b>(<i>series</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/order/reverse.js "Source")

Returns the reverse of the given series order [*n* - 1, *n* - 2, … 0] where *n* is the number of elements in *series*. Thus, the stack order is given by the reverse of the [key accessor](#stack_keys).

### Stack Offsets

Stack offsets are typically not used directly, but are instead passed to [*stack*.offset](#stack_offset).

<a name="stackOffsetExpand" href="#stackOffsetExpand">#</a> d3.<b>stackOffsetExpand</b>(<i>series</i>, <i>order</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/offset/expand.js "Source")

Applies a zero baseline and normalizes the values for each point such that the topline is always one.

<a name="stackOffsetDiverging" href="#stackOffsetDiverging">#</a> d3.<b>stackOffsetDiverging</b>(<i>series</i>, <i>order</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/offset/diverging.js "Source")

Positive values are stacked above zero, while negative values are [stacked below zero](https://bl.ocks.org/mbostock/b5935342c6d21928111928401e2c8608).

<a name="stackOffsetNone" href="#stackOffsetNone">#</a> d3.<b>stackOffsetNone</b>(<i>series</i>, <i>order</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/offset/none.js "Source")

Applies a zero baseline.

<a name="stackOffsetSilhouette" href="#stackOffsetSilhouette">#</a> d3.<b>stackOffsetSilhouette</b>(<i>series</i>, <i>order</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/offset/silhouette.js "Source")

Shifts the baseline down such that the center of the streamgraph is always at zero.

<a name="stackOffsetWiggle" href="#stackOffsetWiggle">#</a> d3.<b>stackOffsetWiggle</b>(<i>series</i>, <i>order</i>) [<>](https://github.com/d3/d3-shape/blob/master/src/offset/wiggle.js "Source")

Shifts the baseline so as to minimize the weighted wiggle of layers. This offset is recommended for streamgraphs in conjunction with the [inside-out order](#stackOrderInsideOut). See [Stacked Graphs—Geometry & Aesthetics](http://leebyron.com/streamgraph/) by Bryon & Wattenberg for more information.
