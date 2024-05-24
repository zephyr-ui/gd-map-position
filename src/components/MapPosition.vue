<script setup>
window._AMapSecurityConfig = {
  securityJsCode: "", // 安全密钥
};
import AMapLoader from "@amap/amap-jsapi-loader";
import { defineEmits, onBeforeUnmount, ref } from "vue";

let map = ref(null)
const emit = defineEmits('change')

const initMap = async () => {
  const AMap = await AMapLoader.load({
    key: "", // 申请好的Web端开发者Key
    version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
    plugins: ["AMap.Scale", "AMap.PlaceSearch", "AMap.AutoComplete"], // 需要使用的的插件
    AMapUI: {
      plugins: ["misc/PositionPicker"],
    },
  })
  AMapUI.loadUI(["misc/PositionPicker"], function (PositionPicker) {
    map.value = new AMap.Map("draw-station-container", {
      viewMode: "3D", //是否为3D地图模式
      zoom: 15, //初始化地图级别
      center: [115.15, 36.29], //初始化地图中心点位置
    });

    const auto = new AMap.AutoComplete({
      input: 'tipinput'
    });

    const placeSearch = new AMap.PlaceSearch({ map: map.value });

    auto.on("select", (e) => {
      placeSearch.setCity(e.poi.adcode);
      placeSearch.search(e.poi.name);
    });

    const positionPicker = new PositionPicker({
      mode: "dragMap", //设定为拖拽地图模式，可选'dragMap'、'dragMarker'，默认为'dragMap'
      map: map.value
    });

    // 注册监听
    positionPicker.on("success", (positionResult) => {
      emit('change', positionResult)
    });

    positionPicker.start();
  });
}

initMap()

onBeforeUnmount(() => {
  if (map.value) map.value.destroy(); // 销毁地图
})

</script>

<template>
  <div class="map-position">
    <div id="draw-station-container"></div>
    <div id="myPageTop">
      <input ref="tipinput" placeholder="请输入搜索内容" id="tipinput" />
    </div>
  </div>
</template>

<style scoped>
.map-position {
  position: relative;

  #draw-station-container {
    padding: 0px;
    margin: 0px;
    width: 100%;
    height: calc(100vh);
  }

  .input {
    margin-bottom: 10px;

    .el-input {
      width: 220px;
    }
  }
}

.button-group {
  position: absolute;
  bottom: 20px;
  right: 20px;
  font-size: 12px;
  padding: 10px;
}

.button-group .button {
  height: 28px;
  line-height: 28px;
  background-color: #0D9BF2;
  color: #FFF;
  border: 0;
  outline: none;
  padding-left: 5px;
  padding-right: 5px;
  border-radius: 3px;
  margin-bottom: 4px;
  cursor: pointer;
}

.button-group .inputtext {
  height: 26px;
  line-height: 26px;
  border: 1px;
  outline: none;
  padding-left: 5px;
  padding-right: 5px;
  border-radius: 3px;
  margin-bottom: 4px;
  cursor: pointer;
}

#tip {
  background-color: #fff;
  padding-left: 10px;
  padding-right: 10px;
  position: absolute;
  font-size: 12px;
  right: 10px;
  top: 20px;
  border-radius: 3px;
  border: 1px solid #ccc;
  line-height: 30px;
}

.amap-info-content {
  font-size: 12px;
}

#myPageTop {
  position: absolute;
  top: 0px;
  right: 10px;
  margin: 10px auto;
  padding: 6px;
  font-family: "Microsoft Yahei", "微软雅黑", "Pinghei";
  font-size: 14px;
}

#myPageTop label {
  margin: 0 20px 0 0;
  color: #666666;
  font-weight: normal;
}

#myPageTop input {
  width: 170px;
}

#myPageTop .column2 {
  padding-left: 25px;
}
</style>
