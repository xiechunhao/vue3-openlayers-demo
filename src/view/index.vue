<template>
  <!--地图-->
  <div class="map" id="map"></div>
  <!--弹窗内容-->
  <section ref="popup" id="popupDiv" class="popup">
    <div>OpenLayers makes it easy to put a dynamic map in any web page. It can display map tiles, vector data and
      markers loaded from any source. OpenLayers has been developed to further the use of geographic information of all
      kinds. It is completely free, Open Source JavaScript, released under the 2-clause BSD License (also known as the
      FreeBSD).</div>
      <br>
    <div>这是一个基于Vue3开发的Openlayer开发的Demo,实现一个marker标注,鼠标悬浮上去展示弹窗信息</div>
  </section>
</template>
<script lang="ts" setup>
import { onMounted, reactive, ref } from "vue";
import { Feature, Map, Overlay, View } from "ol";
import "ol/ol.css";
import TileLayer from "ol/layer/Tile";
import BingMaps from 'ol/source/BingMaps.js';
import OSM from "ol/source/OSM";
import XYZ from "ol/source/XYZ";
import { fromLonLat } from "ol/proj";
import { Point } from "ol/geom";
import { Icon, Style } from "ol/style";
import VectorSource from "ol/source/Vector";
import VectorLayer from "ol/layer/Vector";
import { useRouter } from "vue-router";
const $router = useRouter();
let popup = ref(null) as any;
const state = reactive({
  map: null as any,
  popupParams: {} as any, //弹窗参数
  overlay: null as any, //弹窗overlay
});
/**
* 模块名:初始化地图
*/
const getOpenLayersMap = () => {
  let target = "map"; //跟页面元素的 id 绑定来进行渲染
  let tileLayer = new TileLayer({
    // source: new XYZ({
    //   url: 'https://{a-c}.tile.openstreetmap.org/{z}/{x}/{y}.png'
    // })
    source: new XYZ({
      url: 'https://{a-c}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png',
      maxZoom: 20,
      attributions: '© OpenStreetMap contributors, © CARTO'
    })
  });
  let view = new View({
    // projection: "EPSG:4326", //使用这个坐标系
    center: fromLonLat([104.912777, 34.730746]), //地图中心坐标
    zoom: 4.5 //缩放级别
  });
  state.map = new Map({
    target: target, //绑定dom元素进行渲染
    layers: [tileLayer], //配置地图数据源
    view: view //配置地图显示的options配置（坐标系，中心点，缩放级别等）
  });
};
/**
* 模块名:添加点标记
*/
const mapPoint = () => {
  // 创建点特征
  const pointFeature = new Feature({
    name: "点位",
    id: 1,
    geometry: new Point(fromLonLat([104.043505, 30.58165])),
  });
  //创建style
  pointFeature.setStyle(
    new Style({
      image: new Icon({
        src: "https://smart-garden-manage.oss-cn-chengdu.aliyuncs.com/shexiangtou.png",
        scale: 0.4, //图片大小比例
      }),
    })
  );
  // 创建矢量图层
  const vectorSource = new VectorSource({
    features: [pointFeature], // 添加点特征到图层
  });
  const vectorLayer = new VectorLayer({
    source: vectorSource,
  });

  state.map.addLayer(vectorLayer);
};

/**
* 模块名:实例化弹窗overPlay 地图覆盖元素
*/
const addOverPlay = () => {
  state.overlay = new Overlay({
    element: document.getElementById("popupDiv") as any,
    positioning: "bottom-center",
    stopEvent: false,
    autoPan: true,
    offset: [0, -20],
  });
  state.map.addOverlay(state.overlay);
};
/**
* 模块名:地图鼠标移入事件
*/
const iconTouchstart = () => {
  state.map.on("pointermove", (e: any) => {
    let point = state.map.forEachFeatureAtPixel(
      e.pixel,
      (feature: any) => feature
    ) as any;
    //如果没有点击到标记 这里会打印出undefined
    if (point) {
      //把鼠标光标改一下
      state.map.getTargetElement().style.cursor = "pointer";
      // 显示弹出框
      state.overlay.getElement().style.display = "block";
      //拿到标点参数
      let params = point.getProperties();
      state.popupParams = params;
      // 设置弹窗位置
      let coordinates = point.getGeometry().getCoordinates();
      state.overlay.setPosition(coordinates);
    } else {
      //隐藏弹出框
      state.overlay.getElement().style.display = "none";
      state.popupParams = {};
    }
  });
};
onMounted(() => {
  getOpenLayersMap();
  mapPoint();
  iconTouchstart();
  addOverPlay();
});
</script>
<style scoped>
#map {
  width: 100vw;
  height: 100vh;
}

.popup {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 260px;
  height: 167px;
  background: url('./tooltip.png') no-repeat;
  background-size: 100% 100%;
  padding: 20px;
  color: #fff;
  font-size: 12px;
  line-height: 14px;
}
</style>
