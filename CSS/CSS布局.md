## position+float布局

  1. 浮动元素脱离文档流, 周围元素对齐环绕;
  2. 浮动元素生成一个块级框,
  3. 行内元素与浮动元素重叠, border/background/content都会显示在浮动元素之上;
  4. 块级元素与浮动元素重叠, border/background都会显示在浮动元素之下, 内容会显示在浮动元素之上;
  5. 块级元素如果没有设置高度, 其高度是由子元素撑起来的. 在子元素设置浮动之后, 子元素会脱离标准文档流, 父元素没有内容可以撑开其高度, 这样父元素就不存在高度, 即高度塌陷;

### flex

- flex-grow定义flex元素项的拉伸属性，适应性的拉伸到相应的高度或者宽度。
- flex-shrink定义flex元素项的收缩规则，flex 元素仅在默认宽度之和大于容器的时候才会发生收缩
- flex-basis指定了 flex 元素在主轴方向上的初始大小。如果不使用 box-sizing 来改变盒模型的话，那么这个属性就决定了 flex 元素的内容盒（content-box）的宽或者高（取决于主轴的方向）的尺寸大小
- flex-wrap指定 flex 元素单行显示还是多行显示 。如果允许换行，这个属性允许你控制行的堆叠方向。
- justify-content 属性定义了浏览器如何分配顺着父容器主轴的弹性元素之间及其周围的空间。
- align-content 属性定义了当作为一个弹性盒子容器的属性时，浏览器如何在容器的侧轴围绕弹性盒子项目分配空间。

    - justify-content和align-items所用的属性space-evenly/space-around都不能在iOS8中兼容
        - iOS8中识别-webkit-box-align和-webkit-box-pack两个属性. 值设置start/end/center/justify 将容器两边内容使用padding留白留出来, 再使用around.
        - iOS9以上识别space-around/space-evenly属性, 不用前缀.
        - 水平布局时-webkit-space-around值可以被识别布局. 竖直布局时不可以.
        - 其它属性待测试.
    - iOS8中div的宽度, 不会自动由内容撑开.

### grid 布局

1. 网格轨道 grid-template-columns, grid-template-rows,
2. 轨道单位 fr
3. repeat() 标记
4. 显式和隐式网格 grid-auto-columns, grid-auto-rows,
5. 轨道大小和 minmax()
6. 网格线
7. 跨轨道放置网格线 grid-column-start, grid-column-end, grid-row-start, grid-row-end,
8. 网格单元/网格区域
9. 网格间距 grid-gap, grid-column-gap, grid-row-gap,
10. 嵌套网格
