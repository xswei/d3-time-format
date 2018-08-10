# d3-time-format

这个模块提供了来自 `C` 标准库 [strptime](http://pubs.opengroup.org/onlinepubs/009695399/functions/strptime.html) 和 [strftime](http://pubs.opengroup.org/onlinepubs/007908799/xsh/strftime.html) 的 `JavaScript` 实现, 并且可以用来对多种区域形式的 [dates](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) 进行解析或格式化。格式化日期，首先根据指定的说明符(由 `%` 表示的用来描述格式的*directives*)创建一个 [formatter](#locale_format)；然后将日期传递给 `formatter` 返回一个字符串。例如将当前日期转换为人类友好的字符串:

```js
var formatTime = d3.timeFormat("%B %d, %Y");
formatTime(new Date); // "June 30, 2015"
```

同样的，将字符串解析为日期类型，需要创建一个 [parser](#locale_parse):

```js
var parseTime = d3.timeParse("%B %d, %Y");
parseTime("June 30, 2015"); // Tue Jun 30 2015 00:00:00 GMT-0700 (PDT)
```

你也可以实现更复杂的条件时间格式。例如使用 [time intervals](https://github.com/d3/d3-time) 实现的 [multi-scale time format](http://bl.ocks.org/mbostock/4149176):

```js
var formatMillisecond = d3.timeFormat(".%L"),
    formatSecond = d3.timeFormat(":%S"),
    formatMinute = d3.timeFormat("%I:%M"),
    formatHour = d3.timeFormat("%I %p"),
    formatDay = d3.timeFormat("%a %d"),
    formatWeek = d3.timeFormat("%b %d"),
    formatMonth = d3.timeFormat("%B"),
    formatYear = d3.timeFormat("%Y");

function multiFormat(date) {
  return (d3.timeSecond(date) < date ? formatMillisecond
      : d3.timeMinute(date) < date ? formatSecond
      : d3.timeHour(date) < date ? formatMinute
      : d3.timeDay(date) < date ? formatHour
      : d3.timeMonth(date) < date ? (d3.timeWeek(date) < date ? formatDay : formatWeek)
      : d3.timeYear(date) < date ? formatMonth
      : formatYear)(date);
}
```

这个模块可以被 [time scales](https://github.com/d3/d3-scale/blob/master/README.md#time-scales) 用来生成人类友好的刻度.

## Installing

`NPM` 安装: `npm install d3-time-format`. 此外还可以下载 [latest release](https://github.com/d3/d3-time-format/releases/latest). 可以直接从 [d3js.org](https://d3js.org) 以 [standalone library](https://d3js.org/d3-time-format.v2.min.js) 或作为 [D3 4.0](https://github.com/d3/d3) 的一部分直接载入. 支持 `AMD`, `CommonJS` 以及基本的标签引入形式，如果使用标签引入则会暴露全局 `d3` 变量:

```html
<script src="https://d3js.org/d3-time.v1.min.js"></script>
<script src="https://d3js.org/d3-time-format.v2.min.js"></script>
<script>

var format = d3.timeFormat("%x");

</script>
```

区域文件托管在 [unpkg](https://unpkg.com/)，可以直接使用 [d3.json](https://github.com/d3/d3-request/blob/master/README.md#json) 加载. 例如，将默认的区域配置设置为 `Russian`：

```js
d3.json("https://unpkg.com/d3-time-format@2/locale/ru-RU.json", function(error, locale) {
  if (error) throw error;

  d3.timeFormatDefaultLocale(locale);

  var format = d3.timeFormat("%c");

  console.log(format(new Date)); // понедельник,  5 декабря 2016 г. 10:31:59
});
```

[在浏览器中测试 `d3-time-format`.](https://tonicdev.com/npm/d3-time-format)

## API Reference

<a name="timeFormat" href="#timeFormat">#</a> d3.<b>timeFormat</b>(<i>specifier</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/defaultLocale.js#L4 "Source")

[default locale(默认区域设置)](#timeFormatDefaultLocale) 下 [*locale*.format](#locale_format) 的别名。

<a name="timeParse" href="#timeParse">#</a> d3.<b>timeParse</b>(<i>specifier</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/defaultLocale.js#L5 "Source")

[default locale(默认区域设置)](#timeFormatDefaultLocale) 下 [*locale*.parse](#locale_parse) 的别名。

<a name="utcFormat" href="#utcFormat">#</a> d3.<b>utcFormat</b>(<i>specifier</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/defaultLocale.js#L6 "Source")

[default locale(默认区域设置)](#timeFormatDefaultLocale) 下 [*locale*.utcFormat](#locale_utcFormat) 的别名。

<a name="utcParse" href="#utcParse">#</a> d3.<b>utcParse</b>(<i>specifier</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/defaultLocale.js#L7 "Source")

[default locale(默认区域设置)](#timeFormatDefaultLocale) 下 [*locale*.utcParse](#locale_utcParse) 的别名。

<a name="isoFormat" href="#isoFormat">#</a> d3.<b>isoFormat</b> [<>](https://github.com/d3/d3-time-format/blob/master/src/isoFormat.js "Source")

符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 的 `UTC` 日期格式化器。这个方法使用 [Date.toISOString](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Date/toISOString) 来格式化日期。

<a name="isoParse" href="#isoParse">#</a> d3.<b>isoParse</b> [<>](https://github.com/d3/d3-time-format/blob/master/src/isoParse.js "Source")

符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 的 `UTC` 日期解析器。这个方法使用 [Date constructor](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Date) 来解析字符串。如果字符串严格依据 `ISO 8601` 标准，则可以构建一个 [UTC parser function](#utcParse):

```js
var strictIsoParse = d3.utcParse("%Y-%m-%dT%H:%M:%S.%LZ");
```

<a name="locale_format" href="#locale_format">#</a> <i>locale</i>.<b>format</b>(<i>specifier</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/locale.js#L293 "Source")

根据指定的 *specifier* 返回一个新的日期格式化工具。指定的 `specifier` 必须包含以下指令:

* `%a` - 简写的周?.*
* `%A` - 周?的全称.*
* `%b` - 简写的月.*
* `%B` - 月的全称.*
* `%c` - 区域日期和时间, 比如 `%x, %X`.*
* `%d` - 使用 `0` 填补位数的天 [01,31].
* `%e` - 使用空格填补位数的天 [ 1,31]; 等价于 `%_d`.
* `%f` - microseconds as a decimal number [000000, 999999].
* `%H` - `24` 小时制小时 [00,23].
* `%I` - `12` 小时制小时 [01,12].
* `%j` - 一年中的第几天 [001,366].
* `%m` - 月份 [01,12].
* `%M` - 分钟 [00,59].
* `%L` - 毫秒 [000, 999].
* `%p` - `AM` 或 `PM`.*
* `%Q` - `UNIX` 毫秒.
* `%s` - `UNIX` 秒.
* `%S` - 秒 [00,61].
* `%u` - 一周中的第几天 [1, 7] (`ISO 8601`)
* `%U` - 一年中的第几周 [00,53].
* `%V` - 一年中的第几周 [01,53] (`ISO 8601`)
* `%w` - 从周日开始算起，是所在周的第几天 [0,6].
* `%W` - 一年中的第几周，从周一开始算新的一周 [00,53].
* `%x` - 区域日期格式, 比如 `%m/%d/%Y`.不同地方格式不一样*
* `%X` - 区域时间格式, 比如 `%H:%M:%S`.不同地方格式不一样*
* `%y` - 所在世纪的第几年( `16` ) [00,99].
* `%Y` - 年的全写( `2016` ).
* `%Z` - 返回当前时区，比如 `-0700`, `-07:00`, `-07`, or `Z`.
* `%%` - 百分比 (`%`).

带有星号 (\*) 的指令可能会受 [locale definition](#localeFormat) 的影响。

对于 `%U` 来说，在第一个星期日之前的新年里的所有日子都被认为是在第 `0` 周. 对于 `%W` ，在第一个星期一之前的新年里的所有日子都被认为是在第 `0` 周。周的数量可以使用 [*interval*.count](https://github.com/xswei/d3-time/blob/master/README.md#interval_count) 来计算。例如 `2015-52` 和 `2016-00` 代表 `Monday, December 28, 2015` 而 `2015-53` 和 `2016-01` 代表 `Monday, January 4, 2016`. 这与 [ISO week date](https://en.wikipedia.org/wiki/ISO_week_date) 说明符 `%V` 不同，它是一个更复杂的定义。

对于 `%V`, [strftime man page](http://man7.org/linux/man-pages/man3/strftime.3.html) 里说道:

For `%V`, per the [strftime man page](http://man7.org/linux/man-pages/man3/strftime.3.html):

> 在这个系统中，周起始于 `Monday`，第一周从 `01` 开始而最后一周为 `52` 或 `53`。`Week 1` 是第 `1` 周，其中 `4` 天或更多天处于新的一年中(或者同理，`week 01` 是包含 `Thursday` 的第一周；或者包含一月四号).

`%` 符号后面可能会跟着一个填充字符:

* `0` - `0` 填充
* `_` - 空格填充
* `-` - 禁止填充

If no padding modifier is specified, the default is `0` for all directives except `%e`, which defaults to `_`. (In some implementations of strftime and strptime, a directive may include an optional field width or precision; this feature is not yet implemented.)

The returned function formats a specified *[date](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Date)*, returning the corresponding string.

```js
var formatMonth = d3.timeFormat("%B"),
    formatDay = d3.timeFormat("%A"),
    date = new Date(2014, 4, 1); // Thu May 01 2014 00:00:00 GMT-0700 (PDT)

formatMonth(date); // "May"
formatDay(date); // "Thursday"
```

<a name="locale_parse" href="#locale_parse">#</a> <i>locale</i>.<b>parse</b>(<i>specifier</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/locale.js#L298 "Source")

Returns a new parser for the given string *specifier*. The specifier string may contain the same directives as [*locale*.format](#locale_format). The `%d` and `%e` directives are considered equivalent for parsing.

The returned function parses a specified *string*, returning the corresponding [date](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Date) or null if the string could not be parsed according to this format’s specifier. Parsing is strict: if the specified <i>string</i> does not exactly match the associated specifier, this method returns null. For example, if the associated specifier is `%Y-%m-%dT%H:%M:%SZ`, then the string `"2011-07-01T19:15:28Z"` will be parsed as expected, but `"2011-07-01T19:15:28"`, `"2011-07-01 19:15:28"` and `"2011-07-01"` will return null. (Note that the literal `Z` here is different from the time zone offset directive `%Z`.) If a more flexible parser is desired, try multiple formats sequentially until one returns non-null.

<a name="locale_utcFormat" href="#locale_utcFormat">#</a> <i>locale</i>.<b>utcFormat</b>(<i>specifier</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/locale.js#L303 "Source")

Equivalent to [*locale*.format](#locale_format), except all directives are interpreted as [Coordinated Universal Time (UTC)](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) rather than local time.

<a name="locale_utcParse" href="#locale_utcParse">#</a> <i>locale</i>.<b>utcParse</b>(<i>specifier</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/locale.js#L308 "Source")

Equivalent to [*locale*.parse](#locale_parse), except all directives are interpreted as [Coordinated Universal Time (UTC)](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) rather than local time.

### Locales

<a name="timeFormatLocale" href="#timeFormatLocale">#</a> d3.<b>timeFormatLocale</b>(<i>definition</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/locale.js "Source")

Returns a *locale* object for the specified *definition* with [*locale*.format](#locale_format), [*locale*.parse](#locale_parse), [*locale*.utcFormat](#locale_utcFormat), [*locale*.utcParse](#locale_utcParse) methods. The *definition* must include the following properties:

* `dateTime` - the date and time (`%c`) format specifier (<i>e.g.</i>, `"%a %b %e %X %Y"`).
* `date` - the date (`%x`) format specifier (<i>e.g.</i>, `"%m/%d/%Y"`).
* `time` - the time (`%X`) format specifier (<i>e.g.</i>, `"%H:%M:%S"`).
* `periods` - the A.M. and P.M. equivalents (<i>e.g.</i>, `["AM", "PM"]`).
* `days` - the full names of the weekdays, starting with Sunday.
* `shortDays` - the abbreviated names of the weekdays, starting with Sunday.
* `months` - the full names of the months (starting with January).
* `shortMonths` - the abbreviated names of the months (starting with January).

For an example, see [Localized Time Axis II](https://bl.ocks.org/mbostock/805115ebaa574e771db1875a6d828949).

<a name="timeFormatDefaultLocale" href="#timeFormatDefaultLocale">#</a> d3.<b>timeFormatDefaultLocale</b>(<i>definition</i>) [<>](https://github.com/d3/d3-time-format/blob/master/src/defaultLocale.js "Source")

Equivalent to [d3.timeFormatLocale](#timeFormatLocale), except it also redefines [d3.timeFormat](#timeFormat), [d3.timeParse](#timeParse), [d3.utcFormat](#utcFormat) and [d3.utcParse](#utcParse) to the new locale’s [*locale*.format](#locale_format), [*locale*.parse](#locale_parse), [*locale*.utcFormat](#locale_utcFormat) and [*locale*.utcParse](#locale_utcParse). If you do not set a default locale, it defaults to [U.S. English](https://github.com/d3/d3-time-format/blob/master/locale/en-US.json).

For an example, see [Localized Time Axis](https://bl.ocks.org/mbostock/6f1cc065d4d172bcaf322e399aa8d62f).
