# 地图相关知识整理(Layer、Marker)

### 前言
在web工程中使用地图，往往需要在地图图层中添加覆盖物完成某些需求。结合比较规范的mapbox地图阅读文档后，可以比较直观的得到一些信息，那就是开发者在地图上添加的覆盖物大致可分为Layer和Marker两种类型。

==下面会着重分析mapbox中的覆盖物概念，个人觉得它对geojson的应用比较合理，会用国内MineMap的示例==

[mapbox api](https://docs.mapbox.com/mapbox-gl-js/api/)

[minemap 示例中心](http://minedata.cn/minemapapi/demo/index.html#base_map)

## Layer
Layer的字面意思为图层，在地图中添加layer,可以理解为在地图上添加图层。通过layer图层与地图本身的瓦片图层相叠加，已达到添加覆盖物的目的。

#### 特点
- 通过绘制生成图层
- 图层附着在原始地图图层之上
- 对前端性能消耗较少
- 支持海量数据的展示

#### mapbox举例
以mapbox举例Layer的图层属性一般分类为以下几种：

 
 * fill 面
 * line 线
 * symbol 点
 * circle 圆
 * extrusion 建筑物
 * sprite 动态粒子
 
 前三种最为常用，extrusion和sprite需要特殊属性支持。

##### [举例绘制一条线](http://minedata.cn/minemapapi/demo/index.html#geojson_line)
```
国内mapbox暂时建议直接使用，可以参考一下minemap示例，它基于mapbox二次开发。
```

```
// 1、添加线的geojosn
map.addSource("lineSource", {
    "type": "geojson",
    "data": {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "LineString",
            "coordinates": [
                [116.46+ 0.52419*0.01, 39.92+ 0.71778*0.01],
                [116.46+ 0.11176*0.01, 39.92+ 0.11075*0.01],
            ]
        }
    }
});

// 2、添加layer属性
map.addLayer({
    "id": "lines",
    "type": "line",
    "source": "lineSource",
    "layout": {
        "line-join": "round",
        "line-cap": "round"
    },
    "paint": {
        "line-color": "#ff0000", 
        "line-width": 6
    }
});
```
![image](./images/minemap%E7%BB%98%E5%88%B6%E7%BA%BF%E6%9D%A1.png)


 
#### 百度地图举例
[百度地图关于layer的描述，文档中表明了一些类。](http://lbsyun.baidu.com/cms/jsapi/reference/jsapi_reference.html#a3b16)

==实际上你仔细观察后会发现，百度地图添加覆盖物的大部分 API，都是通过使用添加 DOM 的方式实现，所以严格意义上来说下面大部分算不上是真正意义上的 layer==

- Overlay 覆盖物的抽象基类，所有覆盖物均继承基类的方法。此类不可实例化。
- TileLayer   此类表示一个地图图层，您可以向地图中添加自定义图层。
- Icon 此类表示标注覆盖物所使用的图标。
- Label 此类表示地图上的文本标注。
- polyline 使用浏览器的矢量制图工具（如果可用）在地图上绘制折线的地图叠加层。
- IconSequence 此类用于设置polyline上的符号显示。
- Polygon 此类表示一个多边形覆盖物。
- Circle 此类表示地图上的圆覆盖物。




[举例百度地图绘制线条和区域（实际上此处是以 添加 DOM 的方式绘制而成）](http://lbsyun.baidu.com/jsdemo.htm#c2_9)


```
var polyline = new BMap.Polyline([
	new BMap.Point(116.399, 39.910),
	new BMap.Point(116.405, 39.920),
	new BMap.Point(116.423493, 39.907445)
], {strokeColor:"blue", strokeWeight:2, strokeOpacity:0.5});   //创建折线
map.addOverlay(polyline);   //增加折线

var polygon = new BMap.Polygon([
	new BMap.Point(116.387112,39.920977),
	new BMap.Point(116.385243,39.913063),
	new BMap.Point(116.394226,39.917988),
	new BMap.Point(116.401772,39.921364),
	new BMap.Point(116.41248,39.927893)
], {strokeColor:"blue", strokeWeight:2, strokeOpacity:0.5});  //创建多边形
map.addOverlay(polygon);   //增加多边形
```

## Marker
在 web 地图的应用中,一般需要在地图上通过添加 DOM 元素，来达到自定义覆盖物的需求，这种通过地图 API 添加在地图上的覆盖物，我们可以将它称为 Marker

#### 特点
- 通过 API 生成自定义的 DOM 元素
- 对前端性能消耗有一定影响
- 与浏览器 DOM 元素一致

#### mapbox举例

##### [举例添加一个自定义覆盖物](http://minedata.cn/minemapapi/demo/index.html#marker_popup)
```
/**
     * marker的方法如下
     * #addTo(map)                            添加到地图
     * #remove()                              从地图上删除
     * #getLngLat                             获取经纬度对象
     * #setLngLat([lng,lat] 或 LngLat对象)    设置经纬度对象
     * #setPopup(popup)                       设置信息窗体
     * #getPopup                              获取信息窗体
     * #togglePopup                           切换信息窗体状态
     */
    var popup = new minemap.Popup({offset: [0, -30]})
            .setText('这里是一个执勤人员');
    var el = document.createElement('div');
    el.id = 'marker';

    var marker = new minemap.Marker(el, {offset: [-25, -25]})
            .setLngLat([116.46,39.92])
            .setPopup(popup)
            .addTo(map);

```

#### 百度地图举例
[百度地图关于marker的描述，文档中表明了一些类。](http://lbsyun.baidu.com/cms/jsapi/reference/jsapi_reference.html#a3b2)

##### [举例添加一个自定义覆盖物](http://lbsyun.baidu.com/jsdemo.htm#c1_16)

```
var pt = new BMap.Point(116.417, 39.909);
var myIcon = new BMap.Icon("http://lbsyun.baidu.com/jsdemo/img/fox.gif", new BMap.Size(300,157));
var marker2 = new BMap.Marker(pt,{icon:myIcon});  // 创建标注
map.addOverlay(marker2);     // 将标注添加到地图中
```

此部分文章的内容，建议自己在查看官方文档的时候结合浏览器去审查元素，这样就能更清晰的看到，是否是 layer 或 marker。

#### 小结
1、为什么要对 layer 和 marker 进行区分？

这里主要是为了区分清楚layer 和 marker 的概念，在地图添加覆盖物的时候，哪些是需要通过自定义DOM元素去生成，那些是需要通过调用特定API去生成。

2、layer 在百度地图中似乎涵盖了 marker

3、mapbox 总体对 layer 和 marker 的定义和实现都比较的直观，而且与 geojson 结合紧密，并且也易于理解。有机会可以尝试一下。

等待大佬完善和补充。