# Html简单动画的几种实现方式

本文介绍了html页面几种简单动画的实现方式，涉及css transform动画，css transition动画，svg动画，[FLIP](https://aerotwist.com/blog/flip-your-animations/)动画.

## css animation 动画

通过css3animation

- timming function 

- step sprit

- animation-fill-mode

## css transition动画

有过渡效果的属性：

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



## svg动画

- dash-array

- animation-path

## FLIP动画

[FLIP](https://aerotwist.com/blog/flip-your-animations/)动画，FLIP即First（起始），Last（终止），Invert（反转），Play（播放）。
FLIP


一些应用:

- [hanno](https://arnan125.github.io/hanno/?n=10)

- [vue-animated-list](https://github.com/vuejs/vue-animated-list)

## canvas动画

canvas动画的实现原理更类似于生活中的电影放映原理，通过绘制每一帧画面，从而将连续的动画展现给用户。这种方式可以绘制更加复杂的动画，但是其实现原理却很简单，主要关注于具体绘制操作，以及如何保证绘制的效率，这里不做讨论。