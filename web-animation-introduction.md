# Html简单动画的几种实现方式

本文介绍了html页面几种简单动画的实现方式，涉及css transform/transition动画, svg动画，[FLIP](https://aerotwist.com/blog/flip-your-animations/)动画.

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

> 这里并未全部列出具有过渡效果的属性，比如svg元素属性`dash-array`等，通常凡表位置、大小尺寸的属性皆可以用作transition或者animation作用的对象。

### 语法和api

- animation 

  ``` css
    animation: name duration timing-function delay iteration-count direction fill-mode;
  ```
  
  timing-function可以取预定义的值如`linear`、`ease`等，三次贝塞尔函数，阶跃函数steps。
  
  timing-function作用周期是两次属性变化之间（非一次完整动画过程）。
  
  delay可以为负值，对于-n秒，表示动画跳过前n秒直接从既定时序的第n秒开始执行动画，否则即为延迟开始执行动画。
  
  duration指的是一次完整动画过程所需时间（不重复）。
  
  fill-mode指的是结束动画后元素的位置（大小）状态，默认取值`normal`表示恢复原位置（大小）。当取其他值的时候，还依赖于属性`animation-direction`和`iteration-count`。常用的取值之一`forwards`,将会使只执行一次的正序（normal）动画保持最终状态。其它取值不再一一列举，具体细节请查看[animation-fill-mode](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-fill-mode).

- transition

  ``` css
    transition: property duration timing-function delay;
  ```
  
  timing-function可以取预定义的值如`linear`、`ease`等，三次贝塞尔函数，阶跃函数steps。
  
  一般通过修改属性值或者由伪类来触发transition动画。

- 时间函数——阶跃函数`steps(num, flag)`
  
  `num`是两次属性变动之间发生的阶跃次数，`flag`表示阶跃的变动点在时隙的开始还是结束，默认为`end`，即保持当前状态到时隙结束才发生变动（例如，当`num`为1，flag取`end`时，动画将保持初始状态直到时隙结束，如果取`start`，状态则在时隙一开始就发生了变化）
  
  steps常用来作为雪碧图（sprite）动画的时间函数，如后文示例

- 事件
  
  transitionend：在transition动画结束后触发

  animationstart：在animation动画开始时触发

  animationend：在animation动画结束后触发

  animationiteration：在执行完一次animation动画周期时触发（`animation-iteration-count`为1的时不触发）
  
### demo
  
  一个常规animation动画
  [](codepen://arnan125/WGgRvz?height=500)

  雪碧图animation动画（内含transition实现）
  [](codepen://arnan125/dpqNLR)


## svg动画

- dash-array

- animation-path

[](codepen://arnan125/RGqYRV?height=600)

## FLIP动画

[FLIP](https://aerotwist.com/blog/flip-your-animations/)动画，FLIP即First（起始），Last（终止），Invert（反转），Play（播放）。
FLIP


一些应用:

[](codepen://arnan125/bwxgyO?height=600)

- [vue-animated-list](https://github.com/vuejs/vue-animated-list)

## canvas动画

canvas动画的实现原理更类似于生活中的电影放映原理，通过绘制每一帧画面，从而将连续的动画展现给用户。这种方式可以绘制更加复杂的动画，但是其实现原理却很简单，主要关注于具体绘制操作，以及如何保证绘制的效率，这里不做讨论。