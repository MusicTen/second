# second

## `@vue/cli` **3.x**使用vw实现移动端适配

第一步，先用vue-cli快速构建出一个项目，然后，安装postcss，postcss-pxtorem，postcss-loader，postcss-import，postcss-url 。。。

```
npm i postcss-aspect-ratio-mini postcss-px-to-viewport postcss-write-svg postcss-cssnext postcss-viewport-units cssnano --S
```

**注意:** 通过Vue-cli3.x构建的项目，package.json里只有如下配置，需删除

```js
"postcss": {
    "plugins": {
    	"autoprefixer": {}
    }
},
```

**第二步**，在项目根目录下添加.postcssrc.js文件，在里面写上

```js
module.exports = {
  plugins: {
    "postcss-import": {},
    "postcss-url": {},
    "postcss-aspect-ratio-mini": {},
    "postcss-write-svg": { utf8: false },
    "postcss-cssnext": {},
    "postcss-px-to-viewport": {
      viewportWidth: 750,
      unitPrecision: 3,
      viewportUnit: "vw",
      selectorBlackList: [".ignore", ".hairlines"],
      minPixelValue: 1,
      mediaQuery: false
    },
    "postcss-viewport-units": {}
  }
};
```

## [PostCSS](https://github.com/postcss/postcss/blob/master/README-cn.md)插件介绍

通过Vue-cli2.x构建的项目，在项目的根目录下有一个`.postcssrc.js`，默认情况下已经有了：

```
module.exports = {
    "plugins": {
        "postcss-import": {},
        "postcss-url": {},
        "autoprefixer": {}
    }
}
```

注：Vue-cli3.x已经没有`.postcssrc.js`文件，配置时需要自己新建

### [postcss-import](https://github.com/postcss/postcss-import)

`postcss-import` 目前使用的是默认配置。只在`.postcssrc.js`文件中引入了该插件。

`postcss-import`主要功有是解决`@import`引入路径问题。使用这个插件，可以让你很轻易的使用本地文件、`node_modules`或者`web_modules`的文件。这个插件配合`postcss-url`让你引入文件变得更轻松。

### [postcss-url](https://github.com/postcss/postcss-url)

`postcss-url`该插件主要用来处理文件，比如图片文件、字体文件等引用路径的处理。

在Vue项目中，`vue-loader`已具有类似的功能，只需要配置中将`vue-loader`配置进去。

### [autoprefixer](https://github.com/postcss/autoprefixer)

`autoprefixer`插件是用来自动处理浏览器前缀的一个插件。如果你配置了`postcss-cssnext`，其中就已具备了`autoprefixer`的功能。在配置的时候，未显示的配置相关参数的话，表示使用的是`Browserslist`指定的列表参数，你也可以像这样来指定`last 2 versions` 或者 `> 5%`。

如此一来，你在编码时不再需要考虑任何浏览器前缀的问题，可以专心撸码。这也是PostCSS最常用的一个插件之一。

### 其他插件

Vue-cli2.x默认配置了上述三个PostCSS插件，但我们要完成`vw`的布局兼容方案，或者说让我们能更专心的撸码，还需要配置下面的几个PostCSS插件：

- postcss-aspect-ratio-mini
- postcss-px-to-viewport
- postcss-write-svg
- postcss-cssnext
- cssnano
- postcss-viewport-units

```
npm i postcss-aspect-ratio-mini postcss-px-to-viewport postcss-write-svg postcss-cssnext postcss-viewport-units cssnano --S
```

安装成功之后，在项目根目录下的`package.json`文件中，可以看到新安装的依赖包：

```json
"dependencies": {
    "cssnano": "^3.10.0",
    "postcss-aspect-ratio-mini": "0.0.2",
    "postcss-cssnext": "^3.1.0",
    "postcss-px-to-viewport": "0.0.3",
    "postcss-viewport-units": "^0.1.3",
    "postcss-write-svg": "^3.0.1",
    "vue": "^2.5.2",
    "vue-router": "^3.0.1"
}
```

接下来在`.postcssrc.js`文件对新安装的PostCSS插件进行配置：

```javascript
module.exports = {
    "plugins": {
        "postcss-import": {},
        "postcss-url": {},
        "postcss-aspect-ratio-mini": {}, 
        "postcss-write-svg": {
            utf8: false
        },
        "postcss-cssnext": {},
        "postcss-px-to-viewport": {
            viewportWidth: 750,
            viewportHeight: 1334,
            unitPrecision: 3,
            viewportUnit: 'vw',
            selectorBlackList: ['.ignore', '.hairlines'],
            minPixelValue: 1,
            mediaQuery: false
        }, 
        "postcss-viewport-units":{},
        "cssnano": {
            preset: "advanced",
            autoprefixer: false,
            "postcss-zindex": false
        }
    }
}
```

**特别声明：**由于`cssnext`和`cssnano`都具有`autoprefixer`,事实上只需要一个，所以把默认的`autoprefixer`删除掉，然后把`cssnano`中的`autoprefixer`设置为`false`。对于其他的插件使用，稍后会简单的介绍。

### [postcss-cssnext](https://github.com/MoOx/postcss-cssnext)

`postcss-cssnext`其实就是[cssnext](https://cssnext.github.io/)。该插件可以让我们使用CSS未来的特性，其会对这些特性做相关的兼容性处理。

### [cssnano](https://github.com/cssnano/cssnano)

`cssnano`主要用来压缩和清理CSS代码。在Webpack中，`cssnano`和`css-loader`捆绑在一起，所以不需要自己加载它。不过你也可以使用`postcss-loader`显式的使用`cssnano`。

在`cssnano`的配置中，使用了`preset: "advanced"`，所以我们需要另外安装：

```
npm i cssnano-preset-advanced --save-dev
```

`cssnano`集成了一些其他的PostCSS插件，如果你想禁用`cssnano`中的某个插件的时候，可以像下面这样操作：

```json
"cssnano": {
    autoprefixer: false,
    "postcss-zindex": false
}
```

上面的代码把`autoprefixer`和`postcss-zindex`禁掉了。前者是有重复调用，后者是一个讨厌的东东。只要启用了这个插件，`z-index`的值就会重置为`1`。这是一个天坑，**千万记得将postcss-zindex设置为false**。

### [postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport)

`postcss-px-to-viewport`插件主要用来把`px`单位转换为`vw`、`vh`、`vmin`或者`vmax`这样的视窗单位，也是`vw`适配方案的核心插件之一。

在配置中需要配置相关的几个关键参数：

```javascript
"postcss-px-to-viewport": {
    viewportWidth: 750,      // 视窗的宽度，对应的是我们设计稿的宽度，一般是750
    viewportHeight: 1334,    // 视窗的高度，根据750设备的宽度来指定，一般指定1334，也可以不配置
    unitPrecision: 3,        // 指定`px`转换为视窗单位值的小数位数（很多时候无法整除）
    viewportUnit: 'vw',      // 指定需要转换成的视窗单位，建议使用vw
    selectorBlackList: ['.ignore', '.hairlines'],  // 指定不转换为视窗单位的类，可以自定义，可以无限添加,建议定义一至两个通用的类名
    minPixelValue: 1,       // 小于或等于`1px`不转换为视窗单位，你也可以设置为你想要的值
    mediaQuery: false       // 允许在媒体查询中转换`px`
}
```

目前出视觉设计稿，我们都是使用`750px`宽度的，那么`100vw = 750px`，即`1vw = 7.5px`。那么我们可以根据设计图上的`px`值直接转换成对应的`vw`值。在实际撸码过程，不需要进行任何的计算，直接在代码中写`px`，比如：

```css
.test {
    border: .5px solid black;
    border-bottom-width: 4px;
    font-size: 14px;
    line-height: 20px;
    position: relative;
}
[w-188-246] {
    width: 188px;
}
```

编译出来的CSS：

```css
.test {
    border: .5px solid #000;
    border-bottom-width: .533vw;
    font-size: 1.867vw;
    line-height: 2.667vw;
    position: relative;
}
[w-188-246] {
    width: 25.067vw;
}
```

在不想要把`px`转换为`vw`的时候，首先在对应的元素（`html`）中添加配置中指定的类名`.ignore`或`.hairlines`(`.hairlines`一般用于设置`border-width:0.5px`的元素中)：

```html
<div class="box ignore"></div>
```

写CSS的时候：

```css
.ignore {
    margin: 10px;
    background-color: red;
}
.box {
    width: 180px;
    height: 300px;
}
.hairlines {
    border-bottom: 0.5px solid red;
}
```

编译出来的CSS:

```css
.box {
    width: 24vw;
    height: 40vw;
}
.ignore {
    margin: 10px; /*.box元素中带有.ignore类名，在这个类名写的`px`不会被转换*/
    background-color: red;
}
.hairlines {
    border-bottom: 0.5px solid red;
}
```

上面解决了`px`到`vw`的转换计算。那么在哪些地方可以使用`vw`来适配我们的页面。根据相关的测试：

- 容器适配，可以使用`vw` 
- 文本的适配，可以使用`vw` 
- 大于`1px`的边框、圆角、阴影都可以使用`vw` 
- 内距和外距，可以使用`vw` 

### [postcss-aspect-ratio-mini](https://github.com/yisibl/postcss-aspect-ratio-mini)

`postcss-aspect-ratio-mini`主要用来处理元素容器宽高比。在实际使用的时候，具有一个默认的结构

```html
<div aspectratio>
    <div aspectratio-content></div>
</div>
```

在实际使用的时候，你可以把自定义属性`aspectratio`和`aspectratio-content`换成相应的类名，比如：

```html
<div class="aspectratio">
    <div class="aspectratio-content"></div>
</div>
```

我个人比较喜欢用自定义属性，它和类名所起的作用是同等的。结构定义之后，需要在你的样式文件中添加一个统一的宽度比默认属性：

```css
[aspectratio] {
    position: relative;
}
[aspectratio]::before {
    content: '';
    display: block;
    width: 1px;
    margin-left: -1px;
    height: 0;
}

[aspectratio-content] {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    width: 100%;
    height: 100%;
}
```

如果我们想要做一个`188:246`（`188`是容器宽度，`246`是容器高度）这样的比例容器，只需要这样使用：

```css
[w-188-246] {
    aspect-ratio: '188:246';
}
```

**有一点需要特别注意：aspect-ratio属性不能和其他属性写在一起，否则编译出来的属性只会留下aspect-ratio的值，比如：**

```html
<div aspectratio w-188-246 class="color"></div>
```

编译前的CSS如下：

```css
[w-188-246] {
    width: 188px;
    background-color: red;
    aspect-ratio: '188:246';
}
```

编译之后：

```css
[w-188-246]:before {
    padding-top: 130.85106382978725%;
}
```

主要是因为在插件中做了相应的处理，不在每次调用`aspect-ratio`时，生成前面指定的默认样式代码，这样代码没那么冗余。所以在使用的时候，需要把`width`和`background-color`分开来写：

```css
[w-188-246] {
    width: 188px;
    background-color: red;
}
[w-188-246] {
    aspect-ratio: '188:246';
}
```

这个时候，编译出来的CSS就正常了：

```css
[w-188-246] {
    width: 25.067vw;
    background-color: red;
}
[w-188-246]:before {
    padding-top: 130.85106382978725%;
}
```

目前采用PostCSS插件只是一个过渡阶段，在将来我们可以直接在CSS中使用`aspect-ratio`属性来实现长宽比。

### [postcss-write-svg](https://github.com/jonathantneal/postcss-write-svg)

`postcss-write-svg`插件主要用来处理移动端`1px`的解决方案。该插件主要使用的是`border-image`和`background`来做`1px`的相关处理。比如：

```css
@svg 1px-border {
   height: 2px;
    @rect {
        fill: var(--color, black);
        width: 100%;
        height: 50%;
    }
}
.example {
    border: 1px solid transparent;
    border-image: svg(1px-border param(--color #00b1ff)) 2 2 stretch;
}
```

编译出来的CSS:

```css
.example {
    border: 1px solid transparent;
    border-image: url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg' height='2px'%3E%3Crect fill='%2300b1ff' width='100%25' height='50%25'/%3E%3C/svg%3E") 2 2 stretch;
}
```

上面演示的是使用`border-image`方式，除此之外还可以使用`background-image`来实现。比如：

```css
@svg square {
    @rect {
        fill: var(--color, black);
        width: 100%;
        height: 100%;
    }
}

#example {
    background: white svg(square param(--color #00b1ff));
}
```

编译出来就是：

```css
#example {
    background: white url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Crect fill='%2300b1ff' width='100%25' height='100%25'/%3E%3C/svg%3E");
}
```

解决`1px`的方案除了这个插件之外，还有其他的方法。可以阅读前期整理的《[再谈Retina下`1px`的解决方案](https://www.w3cplus.com/css/fix-1px-for-retina.html)》一文。

**特别声明：**由于有一些低端机对`border-image`支持度不够友好，个人建议你使用`background-image`的这个方案。

### [CSS Modules](https://github.com/css-modules/css-modules)

Vue中的`vue-loader`已经集成了CSS Modules的功能，个人建议在项目中开始使用CSS Modules。特别是在Vue和React的项目中，CSS Modules具有很强的优势和灵活性。建议看看CSS In JS相关的资料。在Vue中，使用CSS Modules的相关文档可以阅读Vue官方提供的文档《[CSS Modules](https://vue-loader.vuejs.org/zh/guide/css-modules.html)》。

### [postcss-viewport-units](https://github.com/springuper/postcss-viewport-units)

`postcss-viewport-units`插件主要是给CSS的属性添加`content`的属性，配合`viewport-units-buggyfill`库给`vw`、`vh`、`vmin`和`vmax`做适配的操作。

这是实现`vw`布局必不可少的一个插件，因为少了这个插件，这将是一件痛苦的事情。后面你就清楚。

到此为止，有关于所需要的PostCSS已配置完。并且简单的介绍了各个插件的作用，至于详细的文档和使用，可以参阅对应插件的官方文档。

vw最终的解决方案，就是使用`viewport`的polyfill：[Viewport Units Buggyfill](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Frodneyrehm%2Fviewport-units-buggyfill)。使用`viewport-units-buggyfill`主要分以下几步走：

#### 引入JavaScript文件

`viewport-units-buggyfill`主要有两个JavaScript文件：`viewport-units-buggyfill.js`和`viewport-units-buggyfill.hacks.js`。你只需要在你的HTML文件中引入这两个文件。比如在Vue项目中的`index.html`引入它们：

```javascript
<script src="//g.alicdn.com/fdilab/lib3rd/viewport-units-buggyfill/0.6.2/??viewport-units-buggyfill.hacks.min.js,viewport-units-buggyfill.min.js"></script>
```

你也可以使用其他的在线CDN地址，也可将这两个文件合并压缩成一个`.js`文件。这主要看你自己的兴趣了。

第二步，在HTML文件中调用`viewport-units-buggyfill`，比如：

```javascript
<script>
    window.onload = function () {
        window.viewportUnitsBuggyfill.init({
            hacks: window.viewportUnitsBuggyfillHacks
        });
    }
</script>
```

为了你Demo的时候能获取对应机型相关的参数，我在示例中添加了一段额外的代码，估计会让你有点烦：

```javascript
<script>
    window.onload = function () {
        window.viewportUnitsBuggyfill.init({
        hacks: window.viewportUnitsBuggyfillHacks
        });

        var winDPI = window.devicePixelRatio;
        var uAgent = window.navigator.userAgent;
        var screenHeight = window.screen.height;
        var screenWidth = window.screen.width;
        var winWidth = window.innerWidth;
        var winHeight = window.innerHeight;

        alert(
            "Windows DPI:" + winDPI +
            ";\ruAgent:" + uAgent +
            ";\rScreen Width:" + screenWidth +
            ";\rScreen Height:" + screenHeight +
            ";\rWindow Width:" + winWidth +
            ";\rWindow Height:" + winHeight
        )
    }
</script>
```

具体的使用。在你的CSS中，只要使用到了`viewport`的单位（`vw`、`vh`、`vmin`或`vmax`）地方，需要在样式中添加`content`：

```css
.my-viewport-units-using-thingie {
    width: 50vmin;
    height: 50vmax;
    top: calc(50vh - 100px);
    left: calc(50vw - 100px);
    /* hack to engage viewport-units-buggyfill */
    content: 'viewport-units-buggyfill; width: 50vmin; height: 50vmax; top: calc(50vh - 100px); left: calc(50vw - 100px);';
}
```

这可能会令你感到恶心，而且我们不可能每次写`vw`都去人肉的计算。特别是在我们的这个场景中，咱们使用了`postcss-px-to-viewport`这个插件来转换`vw`，更无法让我们人肉的去添加`content`内容。

**这个时候就需要前面提到的postcss-viewport-units插件**。这个插件将让你无需关注`content`的内容，插件会自动帮你处理。比如插件处理后的代码：

`Viewport Units Buggyfill`还提供了其他的功能。详细的这里不阐述了。但是`content`也会引起一定的副作用。比如`img`和伪元素`::before`(`:before`)或`::after`（`:after`）。在`img`中`content`会引起部分浏览器下，图片不会显示。这个时候需要全局添加：

```css
img {
    content: normal !important;
}
```

而对于`::after`之类的，就算是里面使用了`vw`单位，[Viewport Units Buggyfill](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Frodneyrehm%2Fviewport-units-buggyfill)对其并不会起作用。比如：

```css
// 编译前
.after {
    content: 'after content';
    display: block;
    width: 100px;
    height: 20px;
    background: green;
}
// 编译后
.after[data-v-469af010] {
    content: "after content";
    display: block;
    width: 13.333vw;
    height: 2.667vw;
    background: green;
}
```

这个时候我们需要通过添加额外的标签来替代伪元素（这个情景我没有测试到，后面自己亲测一下）。