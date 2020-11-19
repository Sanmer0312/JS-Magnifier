# 放大镜

## 原理

同一张图片放在两个不同的盒子里，小图盒子里放尺寸较小的图，大图盒子加`overflow:hidden`属性，大图使用相对定位`position: relative`，大图偏移量对应小图盒子里的放大镜的移动距离，距离的算法看下面 **JS** 部分。[在线预览](https://sanmer0312.github.io/JS-Magnifier/)  [国内预览](http://sanmer0312.gitee.io/js-magnifier)

### HTML

`small-img`表示小图盒子，`small-img`宽度固定，`small-img`里的图片`width:100%`

`small-img-copy`是一个宽高均为 **100%** 的 **绝对定位** 盒子，与`magnifier`（放大镜）同级

`small-img-copy`的`z-index`大于`magnifier`的`z-index`

这样设置结构是因为在`small-img`触发`mousemove`事件时，鼠标移动到`magnifier`上会出现错误的结果，而给`small-img-copy`添加移动事件，鼠标就不会移动到`magnifier`上（这是因为`small-img-copy`的`z-index`层级大于`magnifier`）

`big-img`表示大图盒子，大图盒子宽高为放大镜宽高的1倍，大图宽高也是小图的1倍

```html
<div class="small-img">
    <img src="images/浑元形意太极拳掌门人.jpg" alt="">
    <div class="small-img-copy"></div>
    <div class="magnifier"></div>
</div>
<div class="big-img">
    <img src="images/浑元形意太极拳掌门人.jpg" alt="">
</div>
```

### CSS

```css
<style>
    * {
        margin: 0;
        padding: 0;
    }
    body {
        padding: 20px;
        display: flex;
    }
    .small-img {
        width: 268px;
        margin-right: 20px;
        position: relative;
    }
    .small-img img {
        width: 100%;
    }
    .small-img-copy {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        cursor: move;
        z-index: 99;
    }
    .magnifier {
        position: absolute;
        top: 0px;
        left: 0px;
        width: 100px;
        height: 100px;
        background-color: rgba(0, 0, 0, .2);
        z-index: 98;
        display: none;
    }
    .big-img {
        width: 200px;
        height: 200px;
        border: 1px solid #eee;
        overflow: hidden;
        position: relative;
    }
    .big-img img {
        width: 536px;
        position: absolute;
        top: 0px;
        left: 0px;
    }
</style>
```

### JS

```javascript
<script>
    //放大镜
    let magnifier = document.getElementsByClassName("magnifier")[0],
        //小图
        smallImgCopy = document.getElementsByClassName("small-img-copy")[0],
        //大图
        bigImg = document.getElementsByClassName("big-img")[0].firstElementChild;
    //小图鼠标移入显示放大镜
    smallImgCopy.onmouseover = function () {
        magnifier.style.display = "block";
    }
    //小图鼠标移动事件，即放大镜移动
    smallImgCopy.onmousemove = function (e) {
        //当移动到小于50（放大镜宽度的一半）时
        //放大镜和大图移动到盒子的左边缘
        if (e.offsetX <= 50) {
            magnifier.style.left = 0 + "px";
            bigImg.style.left = 0 + "px";
        }
        //当移动到大于218（小图宽度减放大镜宽度的一半）时
        //放大镜和大图移动到盒子的右边缘
        else if (e.offsetX >= 218) {
            magnifier.style.left = 168 + "px";
            bigImg.style.left = -336 + "px";
        }
        //如果以上两个条件都不成立
        //放大镜移动的距离等于鼠标位置减50（放大镜宽度的一半）
        //大图移动的距离等于 负（小图偏移量/小图最大偏移量*大图最大偏移量）+“px”
        else {
            magnifier.style.left = e.offsetX - 50 + "px";
            bigImg.style.left = -((e.offsetX - 50) / 168) * 336 + "px";
        }
        //Y轴移动方式同理
        if (e.offsetY <= 50) {
            magnifier.style.top = 0 + "px";
            bigImg.style.top = 0 + "px";
        } else if (e.offsetY >= 352) {
            magnifier.style.top = 302 + "px";
            bigImg.style.top = -602 + "px";
        } else {
            magnifier.style.top = e.offsetY - 50 + "px";
            bigImg.style.top = -((e.offsetY - 50) / 302) * 602 + "px";
        }
    }
    //小图鼠标移出隐藏放大镜
    smallImgCopy.onmouseout = function () {
        magnifier.style.display = "none";
    }
</script>
```
