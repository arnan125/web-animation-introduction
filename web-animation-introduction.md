# Html简单动画的几种实现方式

本文介绍了html页面几种简单动画的实现方式，涉及css transform/transition动画，svg动画，[FLIP](https://aerotwist.com/blog/flip-your-animations/)动画.

内容比较琐碎，主要着眼于这些动画实现的原理和一些小技巧、小tips。

多年不写东西，文字功底下降得厉害。如有不明白的地方，请参阅demo代码。

> Talk is cheap. Show me the code.

## css animation/transition 动画

css3 引入的`transition`和`animation`属性给网页动画提供了一种新的解决方案，通过控制dom元素变动属性按照特定的时间函数变化，从而呈现动画效果。

### 有过渡效果的属性：

| 属性名称 | 类型 |
| -------- | ---- |
| transform | all |
| background-color | color |
| background-image | only gradients |
| background-position | percentage, length |
| border-bottom-color | color |
| border-bottom-width | length |
| border-color | color |
| border-left-color | color |
| border-left-width | length |
| border-right-color | color |
| border-right-width | length |
| border-spacing | length |
| border-top-color | color |
| border-top-width | length |
| border-width | length |
| bottom | length, percentage |
| color | color |
| crop | rectangle |
| font-size | length, percentage |
| font-weight | number |
| grid-* | various |
| height | length, percentage |
| left | length, percentage |
| letter-spacing | length |
| line-height | number, length, percentage |
| margin-bottom | length |
| margin-left | length |
| margin-right | length |
| margin-top | length |
| max-height | length, percentage |
| max-width | length, percentage |
| min-height | length, percentage |
| min-width | length, percentage |
| opacity | number |
| outline-color | color |
| outline-offset | integer |
| outline-width | length |
| padding-bottom | length |
| padding-left | length |
| padding-right | length |
| padding-top | length |
| right | length, percentage |
| text-indent | length, percentage |
| text-shadow | shadow |
| top | length, percentage |
| vertical-align | keywords, length, percentage |
| visibility | visibility |
| width | length, percentage |
| word-spacing | length, percentage |
| z-index | integer |
| zoom | number |

> 这里并未全部列出具有过渡效果的属性，比如svg元素属性`stroke-dasharray`等，通常凡是表示位置、大小尺寸的属性皆可以用作transition或者animation作用的对象。

### 语法

- animation 

  ``` css
    
    animation: name duration timing-function delay iteration-count direction fill-mode [, name duration timing-function delay iteration-count direction fill-mode] * ;  // 可指定多组值

  ```
  name 预定义关键帧的名称。关键帧由@keyframes定义，见后文。

  timing-function 可为预定义的值如`linear`、`ease`等，三次贝塞尔函数，阶跃函数steps。
  
  timing-function 作用周期是两个关键帧（keyframe）之间（非一次完整动画过程）。
  
  delay 可为负值，对于-n秒，表示动画跳过前n秒直接从既定时序的第n秒开始执行动画，否则即为延迟开始执行动画。
  
  duration 一次完整动画过程所需时间（不重复）。
  
  fill-mode 为结束动画后元素的位置（大小）状态，默认取值`normal`表示恢复原位置（大小）。当取其他值的时候，还依赖于属性`animation-direction`和`iteration-count`。常用的取值之一`forwards`将会使只执行一次的正序（normal）动画保持最终状态。其它取值不再一一列举，具体细节请查看[animation-fill-mode](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-fill-mode).

- transition

  ``` css
    
    transition: property duration timing-function delay [, property duration timing-function delay] * ; // 可指定多组值
    
  ```

  timing-function 取预定义的值如`linear`、`ease`等，三次贝塞尔函数，阶跃函数steps。
  
  一般通过修改属性值(添加class)或者由伪类(:hover)来触发transition动画。

- 时间函数——阶跃函数`steps(num, flag)`
  
  `num`是两次属性变动之间发生的阶跃次数，`flag`表示阶跃的变动点在时隙的开始还是结束，默认为`end`，即保持当前状态到时隙结束才发生变动（例如，当`num`为1，flag取`end`时，动画将保持初始状态直到时隙结束，如果取`start`，状态则在时隙一开始就发生了变化）
  
  steps常用来作为雪碧图（sprite）动画的时间函数，如后文示例。

- 事件
  
  transitionend：在transition动画结束后触发

  animationstart：在animation动画开始时触发

  animationend：在animation动画结束后触发

  animationiteration：在执行完一次animation动画周期时触发（`animation-iteration-count`为1的时不触发）

- 关键帧`@keyframes`

  `@keyframes`定义了使用animation定义动画时其中的关键帧，关键帧之间的动画将按照时间函数和持续时间补全，所以应注意，时间函数是作用于两个关键帧(keyframe)之间的。
  
  ``` css
  
    @keyframes keyfram-name {
      0% { ... }
      ...
      100% { ... }
    }

    // or

    @keyframes keyfram-name {
      from { ... }
      to { ... }
    }

  ```

  当一共只有两个关键帧时可使用`form`和`to`关键字，但应注意`form`和`to`在部分低版本安卓手机上不被支持

- tips
  
  `trasition`和`animation`都是复合属性，当拆开书写全部或者部分属性时，可以为该属性指定多组属性值，用逗号隔开。见后文demo

### demos
  
  一个常规animation动画
  [](codepen://arnan125/WGgRvz?height=500)

  多属性多配置动画
  [](codepen://arnan125/pEGoXa?height=600)
  
  雪碧图animation动画（内含transition实现）
  [](codepen://arnan125/dpqNLR)


## svg动画

svg（Scalable Vector Graphics，可缩放矢量图形），是一种用来描述二维矢量图形的XML标记语言，与其他的W3C标准，比如CSS, DOM和SMIL等能够协同工作。关于可讲的东西很多，这里不做深入探讨，如需了解细节，请移步[MDN-svg](https://developer.mozilla.org/zh-CN/docs/Web/SVG)。

上文已经介绍了通过animation/transition来实现一些简单的动画（过渡）效果，虽然这种方法已经可以实现大多数简单动画，但是考虑这样一些需求——绘制某元素沿着一段轨迹运动的动画，或者绘制一段轨迹本身的生成过程（笔迹，路径），上文提到的方法显然是不好实现的。所幸。svg为我们提供了一系列animation元素：

- `<set>` 变动一个元素的数字属性，无过渡效果
- `<animate>` 变动一个元素的数字属性，有过渡效果
- `<animateColor>` 变动颜色属性
- `<animateTransform>` 变动变形属性（translation或rotation）
- `<animateMotion>` 物件运动方向与路径方向同步

其中前三个都可以用css animation/transition 替代实现，唯独第四个沿路径运动是css animation/transition无法直接实现的。因此这里重点介绍`<animateMotion>`。

`<animateMotion>`元素使一个元素的位置动起来，并顺着路径同步旋转。定义这个路径是与在(`<path>`)[https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/path]元素中定义路径的方法相同。

``` svg

  <svg xmlns="http://www.w3.org/2000/svg" width="300px" height="100px">
    <rect x="0" y="0" width="300" height="100" stroke="black" stroke-width="1" />
    <circle cx="0" cy="50" r="15" fill="blue" stroke="black" stroke-width="1">
      <animateMotion path="M 0 0 H 300 Z" dur="3s" repeatCount="indefinite" rotate='auto'/>
    </circle>
  </svg>

```

其中`<rect>`元素作为背景存在，静止不动。`<circle>`为运动元素，它的子元素是一个动画元素`<animateMotion>`，`<animateMotion>`将会使`<circle>`动起来。
我们详细看一下各个属性。

- path 运动路径，坐标原点为运动元素的初始定位点，而非画布的坐标原点（svg容器左上角）。路径指令字符串中的英文字母为指令，后面跟一个或者两个值做为坐标。`M`即move，即移动到该坐标；`H` 即horizontal，后跟一个数值，表示水平移动到某坐标；`V` 即vertical，后跟一个数值，表示垂直移动到某坐标；`L` ，即line，后跟两个数值，用逗号隔开，表示到某坐标的连线；`Z` 表示闭合路径，将会连接起始坐标。更多的指令请参阅[MDN-paths](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial/Paths)

- repeatCount 指定重复次数，`indefinite`表示无限次

- rotate 指定`rotate`属性为`auto`将会使运动元素始终沿着运动路径的切线方向运动，否则元素只是沿着路径平移。

这样，我们实现了沿路径运动动画的需求

> 注意，运动路径是相对运动元素的，即path的坐标原点为运动元素的初始定位点。

那么如何实现路径本身生成过程的动画这里介绍一个svg`<path>`元素的属性——`stroke-dasharray`。

`stroke-dasharray`定义了将路径（path）边框描为虚线一种描边方式。是一组用逗号分割的数字组成的数列。每一组数字，第一个用来表示实线，第二个用来表示空白。`stroke-dasharray="5,10"`，表示路径将以5px实线和10px空白交替组成。

通过改变`stroke-dasharray`的取值，不断增加实线的长度，缩短空白的长度，路径生成过程的动画就可以实现了。这里不得不赞叹css的强大，大部分css特性同样是可以作用于svg元素的。文章一开头就提过`stroke-dasharray`也是具有过渡效果的属性，因此我们可以给`stroke-dasharray`添加 animation/transition 动画。

``` svg

  <svg viewBox="0,0,200,150" xmlns="http://www.w3.org/2000/svg" version="1.1">
    <defs>
      <style type="text/css"><![CDATA[
         #motion-path {
            animation: motion-path 1s linear infinite alternate;
         }
         @keyframes motion-path  {
            0% {
              stroke-dasharray: 0, 210;
            }
            100% {
              // stroke-dasharray: 210, 0;
              stroke-dasharray: 210, 210;
            }
         }
      ]]></style>
    </defs>
    <path d="M 20 25 L 180 25 V 75" stroke="red"
      stroke-linecap="round" stroke-width="1" stroke-dasharray="0,180" fill="none" id="motion-path"/>
</svg>


```

[](codepen://arnan125/zKZvqp?height=200)

一个综合了沿路径运动动画，路径生成动画，以及普通animation动画的demo

[](codepen://arnan125/RGqYRV?height=600)


## FLIP动画

[FLIP](https://aerotwist.com/blog/flip-your-animations/)动画，FLIP即First（起始），Last（终止），Invert（反转），Play（播放）。
FLIP


一些应用:

[](codepen://arnan125/bwxgyO?height=600)

- [vue-animated-list](https://github.com/vuejs/vue-animated-list)

## canvas动画

canvas动画的实现原理更类似于生活中的电影放映原理，通过绘制每一帧画面，从而将连续的动画展现给用户。这种方式可以绘制更加复杂的动画，但是其实现原理却很简单，主要关注于具体绘制操作，以及如何保证绘制的效率，这里不做讨论。

