# mtfPicViewer MTF影像查看器
  
给网站和H5应用增加 像微信朋友圈一样 的看图功能，可看大图，双击双指缩放，滑动切换图片，点击返回。
## 灵感
`IMWeb` 微信朋友圈项目
## 演示
- PC
  - 鼠标及键盘控制：  
https://mantoufan.github.io/mtfPicViewer/  
  - 鼠标模拟触屏：（Shift + 鼠标左键拖动模拟双指缩放）  
https://mantoufan.github.io/mtfPicViewer/?touch=on
- 移动版  
   请用手机浏览器打开的网址即可
## 使用
`全屏` 点击 图片 / 视频  
`切换` 滑动 / 鼠标拖拽 / ←→键 / 小圆点 / 切换按钮  
`缩放` 双指 / 双击  
`还原` 双击 / R键  
`拖动` 单指 / 鼠标拖拽  
`返回` 单击 / ESC键
## 安装
1. 本插件依赖jQuery，请先引入jQuery
    ```
    <script type='text/javascript' src="https://cdn.jsdelivr.net/combine/npm/jquery@1.12.4"></script>
    ```
2. 再引入本插件
- CDN
    ```
    <script type='text/javascript' src="https://cdn.dm126.com/all/m/mtf/mtfPicViewer/dist/1.0.0/mtfPicViewer.js"></script>
    ```
- 下载到本地
https://github.com/mantoufan/mtfPicViewer/releases
## 配置
### 选项
```
$(areaSelector).mtfpicviewer({
    selector: 'img',
    attrSelector: 'src',
    parentSelector: 'div',
    className: 'pic-viewer',
    debug: false,
    onChange: function(curIndex, preIndex) {},
    onOpen: function(curIndex) {},
    onClose: function(curIndex) {}
});
```
- `areaSelector` {jQuery选择器} 区域选择器
    - 后面的所有设置都会应用在这个区域内的图片 / 视频上
    - 同一页面，不同区域，设置相同，可在选择器一次选中这些区域，英文半角逗号`,`分隔也可
    - 同一页面，不同区域，设置不同，可声明多个jQuery选择器.mtfpicviewer(options)
- `selector` {jQuery选择器} 元素选择器
    - 元素可以是图片（未来包括视频），也可以是包含图片地址属性的元素（懒加载场景）
- `attrSelector` {属性名} 属性选择器
    - 元素上的哪个属性，包含真实图片地址。
- `parentSelector` {jQuery选择器} [父级元素选择器] 可选
    - 需要对区域内的多张图片分组时，可用该属性标识 每组图片的父级容器。
    - 图片切换只在同组图片间进行。
    - 该属性为空时，整个区域的图片为一组。
- `className` {样式名} [查看器样式名] 可选
    - 多个样式名可用 英文半角空格 分隔。
    - 常用属性名：
        - max-width 查看器最大宽度，默认为全屏，设置后，宽度不能超过最大宽度，位置水平居中
        - background-color 查看器背景颜色，默认是黑色，可以设置其他颜色
- `debug` {布尔值} [调试模式] 可选
    - 设置为true后，放缩操作时，左上角，会显示被操作影像的坐标。
- `onChange` {函数} [切换时调用] 可选  
    - 当用户切换 影像 时，调用。
    - 按先后顺序，传入两个参数
        - curIndex 切换后影像在`组`内的索引值，`组`内，按从上到下，从左往右顺序，从0开始，下同
        - preIndex 切换前影像在`组`内的索引值
- `onOpen` {函数} [查看器打开时调用] 可选 
    - 当 影像 被点击放大，即 查看器打开时，调用
    - 传入参数
        - curIndex 被放大影像在`组`内的索引值
- `onClose` {函数} [查看器关闭时调用] 可选
    - 点击返回，即 查看器关闭时，调用
    - 传入参数
        - curIndex 关闭前正在浏览的影像在`组`内的索引值
### 图示
层级关系：areaSelector > parentSelector > selector
![](https://i.loli.net/2020/09/14/eaiZSQAsBDHxGpN.jpg)
### 方法
- $.fn.mtfpicviewer.open({currentUrl, [urls], [onOpen]}) 打开查看器
    - `currentUrl` {String} 当前地址
    - `urls` {Array} 所有地址数组，可选
    - `onOpen` {Function} 当查看器被打开时调用，可选
 - $.fn.mtfpicviewer.close({[selector], [onClose]}) 关闭查看器
    - `selector` {String} 元素的选择器，关闭时，查看器逐渐缩小到这个元素，可选，为空时，查看器渐渐隐藏
    - `onClose` {Function} 当查看器被关闭时调用，可选
- $.fn.mtfpicviewer.change({index, [onChange]}) 切换
    - `index` {Integer} 要跳转第几个影像。`组`内从0开始，小于0，跳转到第一张，大于小组最多的影像数量，跳转到最后一张
    - `onChange` {Function} 切换时调用，可选
## 第三方应用
- rgbaster.js  
https://github.com/briangonzalez/rgbaster.js  
一款优秀的识别图片主题色的JS插件  
通过rgbaster.js，在点击放大图片`onOpen`和图片切换`onChange`时，更新背景色
    ```
    /**
    * 使用图片的主要颜色值设置背景色
    * @param {Integer} curIndex 当前图片的索引值
    */
    function setBgWithDominantColor(curIndex) {
        var img = $('.mtf-pic-list').children().eq(curIndex).children('img')[0];
        if (img.src.indexOf('file://') === -1) {
            RGBaster.colors(img, {
                paletteSize: 1,
                success: function(payload) {
                    $('.mtf-pic-viewer').css('backgroundColor', payload.palette[0]);
                }
            });
        }
    }
    $('.main').mtfpicviewer({
        selector: 'img',
        attrSelector: 'src',
        parentSelector: 'div',
        className: 'pic-viewer',
        debug: false,
        onChange: function(curIndex, preIndex) {
            setBgWithDominantColor(curIndex);
        },
        onOpen: function(curIndex) {
            setBgWithDominantColor(curIndex);
        }
    });
    ```
完整代码您可以参考我们的演示DEMO源码。
- shopXO 图片查看器 插件

![shopXO 图片查看器 插件介绍](https://i.loli.net/2020/09/14/LOPMylJafqYAr1v.jpg)
## 待办事项
- 支持视频