# ArcGIS API for JavaScript 

## 一、基础使用

### 1、安装

 [点击查看文档](https://developers.arcgis.com/javascript/latest/es-modules/)

```bash
npm install @arcgis/core
yarn install @arcgis/core
pnpm install @arcgis/core
```

### 2、引入

CSS： [点击查看文档](https://developers.arcgis.com/javascript/latest/styling/#custom-css-using-sass)

```js
import '@arcgis/core/assets/esri/themes/dark/main.css'
```

JS：

```js
import Map from '@arcgis/core/Map'
import MapView from '@arcgis/core/views/MapView'
```

### 3、创建

[点击查看文档](https://developers.arcgis.com/javascript/latest/sample-code/intro-mapview/#4-create-a-2d-view)

[点击查看Map文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-Map.html)    [点击查看MapView文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html)

```vue
<script setup lang="ts">
import Map from '@arcgis/core/Map'
import MapView from '@arcgis/core/views/MapView'
import { onMounted } from 'vue'

onMounted(() => {
  const map = new Map({
    basemap: 'topo-vector'
  })

  const view = new MapView({
    container: 'map',
    map,
    center: [116.6, 35.5],
    zoom: 8,
  })
})
</script>

<template>
  <div id="map" />
</template>

<style>
html,
body,
#app,
#map {
  height: 100%;
  width: 100%;
  margin: 0;
  padding: 0;
}
</style>

```

### 4、地图UI

[点击查看Zoom文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-Zoom.html)

[点击查看ScaleBar文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-ScaleBar.html)

```js
import Zoom from '@arcgis/core/widgets/Zoom'
import ScaleBar from '@arcgis/core/widgets/ScaleBar'

const zoom = new Zoom({ view })
view.ui.add(zoom, { position: 'bottom-right' })

const scaleBar = new ScaleBar({ view, unit: 'metric' })
view.ui.add(scaleBar, { position: 'bottom-right' })

```



### 5、使用自定义瓦片地图

[点击查看TileLayer文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-TileLayer.html)

[点击查看Basemap文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-Basemap.html)

```js
import TileLayer from '@arcgis/core/layers/TileLayer'
import BaseMap from '@arcgis/core/Basemap'

const mapBaseLayer = new TileLayer({
  url: 'http://222.173.156.235:6080/arcgis/rest/services/JINING/GEO_JN_IMAGES/MapServer',
})
const mapBaseMap = new BaseMap({ baseLayers: [mapBaseLayer] })
const map = new Map({ basemap: mapBaseMap })
```

### 6、添加地图图片图层

[点击查看MapImageLayer文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-MapImageLayer.html)

[点击查看SimpleRender文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-renderers-SimpleRenderer.html#properties-summary)

[点击查看SimpleFillSymbol文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-symbols-SimpleFillSymbol.html)

  ```js
  import MapImageLayer from '@arcgis/core/layers/MapImageLayer'
  const imageLayer = new MapImageLayer({
    url: 'http://222.173.156.235:6080/arcgis/rest/services/JNORCL/GEO_JN_XZQH/MapServer',
    visible: true,
    sublayers: [
      {
        id: 2,
        title: '县',
        renderer: new SimpleRenderer({
          symbol: new SimpleFillSymbol({
            color: [0, 0, 0, 0],
            outline: {
              color: '#05e4c6',
              width: 2.5,
            },
          }),
        }),
        labelsVisible: true,
        visible: true,
      },
    ],
  })
  map.add(imageLayer)
  ```

### 6、添加图形覆盖物

引入 [Graphic](https://developers.arcgis.com/javascript/latest/api-reference/esri-Graphic.html) 构建工具

```js
import Graphic from '@arcgis/core/Graphic'
```

引入构建图形的两个重要元素geometry和symbols（这里以 [Point](https://developers.arcgis.com/javascript/latest/api-reference/esri-geometry-Point.html) 和 [SimpleMarkerSymbol](https://developers.arcgis.com/javascript/latest/api-reference/esri-symbols-SimpleMarkerSymbol.html) 为例）

```js
import Point from '@arcgis/core/geometry/Point'
import SimpleMarkerSymbol from '@arcgis/core/symbols/SimpleMarkerSymbol'
// 或
import { Point } from '@arcgis/core/geometry'
import { SimpleMarkerSymbol } from '@arcgis/core/symbols'
```

使用

```js
const marker = new Graphic({
  geometry: new Point({
    longitude: 120,
    latitude: 36,
  }),
  symbol: new SimpleMarkerSymbol({
    color: [226, 119, 40],
    outline: {
      color: [255, 255, 255],
      width: 2,
    },
  }),
})
```

添加

```js
// (method) __esri.Collection<__esri.Graphic>.add(item: Graphic, index?: number | undefined): void
view.graphics.add(marker)
```

删除

```js
// (method) __esri.Collection<__esri.Graphic>.remove(item: Graphic): void
view.graphics.remove(marker)
// 删除所有
view.graphics.removeAll()
```

### 8、MapView点击事件与命中测试

[点击查看click文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#events-summary)

[点击查看hitTest文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#hitTest)

```js
view.on('click', (event) => {
  console.log(event.mapPoint)
  view.hitTest(event).then((response) => {
    console.log(response.results)
  })
})
```

### 9、Popup弹出窗

[方式一：](https://developers.arcgis.com/javascript/latest/api-reference/esri-Graphic.html#popupTemplate)

```js
const pictureMarker = new Graphic({
  geometry: new Point({
    longitude: 120,
    latitude: 36,
  }),
  symbol: new PictureMarkerSymbol({
    url: img,
    width: '64px',
    height: '64px',
  }),
  popupTemplate: {
    title: 'Vue',
    content: 'Vue is a progressive framework for building user interfaces.',
  },
})
```

[方式二：](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#popup)

```js
view.popup.id = 'person'
view.popup.alignment = 'bottom-right'
const popupTemplate = {
  title: '执法人员详情',
  location: new Point({ longitude: info.longitude, latitude: info.latitude }),
  content: `<div class="flex-wrap flex-start peopleInfoWindow">
              <div class="item">姓名：${account}</div>
              <div class="item" ${sexName ? '' : 'style="display: none"'}>性别：${sexName}</div>
              <div class="item">手机号：${phone}</div>
              <div class="item">经度：${info.longitude}</div>
              <div class="item">纬度：${info.latitude}</div>
              <div class="item">所属区县：${postName}</div>
            </div>`
}
view.popup.open(popupTemplate)
```

### 10、视角移动缩放

[点击查看文档](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-MapView.html#goTo)

```js
view.goTo({ target: Point, zoom: range })

view.goTo({ center: [lng, lat], zoom: range })
```

## 二、进阶使用

### 1、draw绘制

引入[Draw](https://developers.arcgis.com/javascript/latest/api-reference/esri-views-draw-Draw.html)

```js
import Draw from '@arcgis/core/views/draw/Draw'
```

使用

```js
const draw = new Draw({ view: MapView })
```

绘制

```js
type Type = 'point' | 'polyline' | 'polygon' | 'rectangle' | 'circle'
const action = draw.value!.create(Type)
MapView.focus()
action.value.on(
  ['vertex-add', 'vertex-remove', 'cursor-update', 'redo', 'undo', 'draw-complete'],
  (event) => {
    // dosomething
  }
)
```

### 2、sketch绘制

引入[SketchViewModel](https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-Sketch-SketchViewModel.html#properties-summary)

```JS
import SketchViewModel from '@arcgis/core/widgets/Sketch/SketchViewModel'
```

使用

```js
const sketch = new SketchViewModel({
  view: MapView,
  layer: GraphicLayer,
})
```

绘制

```js
type Type = 'point' | 'polyline' | 'polygon' | 'rectangle' | 'circle'
sketch.create(Type)
```

### 3、测量、测面、判断相交等

引入[geometryEngine](https://developers.arcgis.com/javascript/latest/api-reference/esri-geometry-geometryEngine.html)

```js
import * as geometryEngine from '@arcgis/core/geometry/geometryEngine'
```

使用

```js
geometryEngine.geodesicLength(geometry, unit)
geometryEngine.geodesicArea(geometry, unit)
geometryEngine.intersect(geometry1, geometry2)
```

### 4、Editor进行layer编辑

引入[Editor](https://developers.arcgis.com/javascript/latest/api-reference/esri-widgets-Editor.html)

```js
import Editor from '@arcgis/core/widgets/Editor'
```

使用

```js
const editor = new Editor({ view: MapView })
```

### 5、使用queryFeatures查询

使用[queryFeatures](https://developers.arcgis.com/javascript/latest/api-reference/esri-layers-FeatureLayer.html#queryFeatures)方法

相关[Query](https://developers.arcgis.com/javascript/latest/api-reference/esri-rest-support-Query.html)

```js
FeatureLayer.queryFeatures().then((results) => {
  console.log(results.features)
  // dosomething
})
```

```js
// 查询符合字段的features
const layer = new FeatureLayer({ url: Url })

const query = new Query()
query.where = 'STATE_NAME = \'济宁\''
query.outSpatialReference = { wkid: 4326 }
query.returnGeometry = true
query.outFields = ['*']

layer.queryFeatures(query).then((results) => {
  console.log(results.features)
  // dosomething
})
```

```js
// 传递一个geometry返回与之相交的features
const queryParams = layer.createQuery()
queryParams.geometry = geometry
queryParams.spatialRelationship = 'intersects'
layer.queryFeatures(queryParams).then((results) => {
  console.log(results.features)
  // dosomething
})
```



