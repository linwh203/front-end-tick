## 一、文字的換行
### word-wrap
  強迫文字斷行，預設是 normal 不會斷行，但這是針對過長的英文文字，如果是整段句子還是會斷行的，我們今天要讓英文字斷行，可以直接在 CSS 寫上word-wrap:break-word;
  
### white-space
  整段文章不换行，超出显示省略号
```
overflow:hidden;
white-space:nowrap;
text-overflow:ellipsis;
```
## 二、linear-gradient
### 线条渐变

```
linear-gradient([<angle> | to <side-or-corner>]? , <color-stop-list>)
```
第一个参数是渐变的角度，他可以接受一个表示角度的值（可用的单位deg、rad、grad或turn）或者是表示方向的关键词（top、right、bottom、left、left top、top right、bottom right或者left bottom）。第二个参数是接受一系列颜色节点（终止点的颜色）

color参数中，<color> [<percentage> | <length>]
最简单的情况下只有两个颜色，颜色1将被放置在渐变线0%位置（渐变线开始位置），颜色2将被放置在100%位置处（渐变线的结束点）。如果有三个颜色，那么颜色1在渐变线的0%，颜色2在渐变线的50%，颜色3在渐变线的100%。在上面的这个示例中，有五个颜色，那么它们的位置分别在0%、25%、50%、75%和100%。它们将沿着渐变线平均分布渐变颜色。

### 边框渐变
```
  .box{
     width: 100px;
     height: 100px;
     border:10px solid #ddd;
     border-image: -webkit-linear-gradient(#ddd,#000) 30 30;
     border-image: -moz-linear-gradient(#ddd,#000) 30 30;
     border-image: linear-gradient(#ddd,#000) 30 30;
}
```







