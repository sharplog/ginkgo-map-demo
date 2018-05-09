<template>
  <div>
    <div>
      <button @click="setZoom">zoom({{ zoom }})</button>
      <button @click="setCenter">c({{ center[0] }})</button>
      <button @click="setMarkers">重置点标记</button>
      <button @click="toggleGroup">隐藏/显示组</button>
      <button @click="rmMakerLine">删单个点线面</button>
      <button @click="rmAll">删所有点线面</button>
      <button @click="createMarker">新建点</button>
      <button @click="editMarker">修改点</button>
      <button @click="createLine">新建线</button>
      <button @click="editLine">修改线</button>
      <button @click="createPolygon">新建面</button>
      <button @click="editPolygon">编辑面</button>
      <button @click="createCircle">新建圆</button>
      <button @click="editCircle">修改圆</button>
      <button @click="createRect">新建矩形</button>
      <button @click="editRect">修改矩形</button>
      <button @click="cancelEdit">取消编辑</button>
      <button @click="finishEdit">完成编辑</button>
      <button @click="playback">轨迹回放</button>
      <button @click="pause">暂停</button>
      <button @click="resume">继续</button>
      <button @click="stop">停止</button>
      <button @click="restart">重放</button>
      <button @click="speedUp">加速</button>
      <button @click="speedDown">减速</button>
      <button @click="clearback">清除轨迹</button>
      <!-- 画线面时，设置样式。回放时能够控制 -->
      <br/>
      <span style="background: white">【{{ poses }}| {{ address }} | {{ radius }}】</span>
    </div>
    <ginkgo-map ref="map" class="gingo-map" :gmapObj.sync="gmap" :options="mapOptions" :zoom.sync="zoom" :center.sync="center"
        :markers="markers" :polylines="polylines" :polygons="polygons" :circles="circles" :rectangles="rectangles" :texts="texts"
        :trackData="trackData" :trackOptions="trackOptions" :tracker.sync="tracker"
        :editData.sync="editData" :editer.sync="editer" :imageLayers="imageLayers">
    </ginkgo-map>
  </div>
</template>

<script lang="ts">
import {Component, Vue} from 'vue-property-decorator'

@Component
export default class Map extends Vue {
  gmap: any = {}
  zoom: number = 11
  center: number[] = [117.12224, 36.67429]
  tracker: any = {}
  speed: number = 500
  editer: any = {}
  editData: any = {}
  editMarkerOpt: any = {}
  editMarkerOpt2: any = {
    mode: 'dragMarker',
    icon: '//webapi.amap.com/ui/1.0/assets/position-picker2.png', // 图片地址
    size: [48, 48], // 要显示的点大小，将缩放图片
    ancher: [24, 40] // 锚点的位置，即被size缩放之后，图片的什么位置作为选中的位置
  }
  showGroup: boolean = true
  
  get poses (): string {
    if (this.editData.position) return this.editData.position.join(',')
    if (this.editData.path) return this.editData.path.join(',')
  }

  get address (): string {
    return this.editData.address
  }

  get radius (): string {
    return this.editData.radius
  }

  testClick (p) {
    alert(p)
  }

  mounted () {
    // 供在信息窗体中调用的方法
    let win: any = window
    win.gmapCallback = function (type) {
      alert(type)
    }
  }

  // 定义一个带事件的信息窗体
  createInfo () {
    let info = document.createElement('div')
    let a = document.createElement('a')
    a.innerHTML = '点击事件'
    a.onclick = this.testClick
    info.appendChild(a)
 
    return info
  }

  toggleGroup () {
    if (this.showGroup) this.gmap.hideOverlayGroup('group1')
    else this.gmap.showOverlayGroup('group1')
    this.showGroup = !this.showGroup
  }

  createMarker () {
    this.editer.createMarker(this.editMarkerOpt)
  }

  editMarker () {
    this.editer.editMarker('mk1', this.editMarkerOpt2)
  }

  createLine () {
    this.editer.createPolyline({})
  }

  editLine () {
    this.editer.editPolyline('line1')
  }

  createPolygon () {
    this.editer.createPolygon({fillColor: 'red'})
  }

  editPolygon () {
    this.editer.editPolygon('gon1')
  }

  createCircle () {
    this.editer.createCircle({fillColor: 'red'})
  }

  editCircle () {
    this.editer.editCircle('circle1')
  }

  createRect () {
    this.editer.createRectangle({fillColor: 'yellow'})
  }

  editRect () {
    this.editer.editRectangle('rect1')
  }

  cancelEdit () {
    this.editer.cancelEdit()
  }

  finishEdit () {
    this.editer.finishEdit()
  }

  markers: any[] = [
    { id: 'mk1',
      position: [117.12224, 36.67429],
      // icon: 'https://www.baidu.com/img/baidu_jgylogo3.gif',
      label: '测试点1',
      title: '测试title1',
      message: '哈哈！It\'s me!',
      group: 'group1'
    },
    { id: 'mk2',
      position: [117.32224, 36.67429],
      icon: 'https://www.baidu.com/img/baidu_jgylogo3.gif',
      label: '测试点2',
      message: this.createInfo(),
      group: 'group2'
    },
    { id: 'mk3',
      position: [117.42224, 36.67429],
      icon: 'https://www.baidu.com/img/baidu_jgylogo3.gif',
      label: '测试点3',
      group: 'group1'
    },
    { id: 'mk4',
      position: [117.52224, 36.67429],
      icon: 'https://www.baidu.com/img/baidu_jgylogo3.gif',
      label: '测试点4',
      group: 'group2'
    }
  ]
  polylines: any[] = [
    { id: 'line1',
      path: [
        [117.12224, 36.67429],
        [117.32224, 36.72429],
        [117.42224, 36.67429]
      ],
      strokeWeight: 10,
      cursor: 'pointer',
      message: '哈哈！It\'s me!',
      outlineColor: 'red',
      strokeStyle: 'dashed',
      strokeDasharray: [15, 2, 2, 2],
      showDir: true,
      group: 'group1'
    }  
  ]
  
  polygons: any[] = [
    { id: 'gon1',
      path: [
        [116.82224, 36.67429],
        [116.82224, 36.77429],
        [116.87224, 36.80429],
        [116.92224, 36.77429],
        [116.92224, 36.67429]
      ],
      strokeWeight: 2,
      cursor: 'pointer',
      message: '哈哈！It\'s polygon!',
      fillColor: 'blue',
      fillOpacity: 0.5,
      strokeStyle: 'dashed',
      strokeDasharray: [15, 2, 2, 2],
      lightenOpacity: 0.9,
      lightenColor: 'green',
      group: 'group1'
    }  
  ]
  
  circles: any[] = [
    { id: 'circle1',
      center: [117.02224, 36.72429],
      radius: 7000,
      strokeWeight: 2,
      cursor: 'pointer',
      message: '哈哈！It\'s circle!',
      fillColor: 'yellow',
      // fillOpacity: 0.5,
      strokeStyle: 'dashed',
      strokeDasharray: [15, 2, 2, 2],
      lightenOpacity: 0.6,
      group: 'group1'
    }  
  ]
  
  rectangles: any[] = [
    { id: 'rect1',
      southWest: [117.12224, 36.72429],
      northEast: [117.27224, 36.80429],
      radius: 7000,
      strokeWeight: 2,
      cursor: 'pointer',
      message: '哈哈！It\'s rectangle!<br/><a href="javascript:void(0)" onClick="gmapCallback(123)">点击事件</a>',
      fillColor: 'green',
      fillOpacity: 0.5,
      strokeStyle: 'dashed',
      strokeDasharray: [15, 2, 2, 2],
      lightenColor: 'blue',
      group: 'group1'
    }  
  ]
  
  texts: any[] = [
    /*
    {
      id: 'text1',
      text: '测试文本',
      position: [117.12224, 36.67429],
      angle: 30,
      message: '我是测试文本',
      style: {
        fontSize: '20px'
      }
    }
    */
  ]
  
  imageLayers: any[] = [
    {
      id: 'imageLayer1',
      url: 'http://localhost:8080/static/img/dongwuyuan.jpg',
      southWest: [116.327911, 39.939229],
      northEast: [116.342659, 39.946275],
      opacity: 0.7,
      zooms: [14, 18]
    }
  ]

  trackOptions = {
    // 轨迹线的样式，默认不显示
    lineStyle: {},
    
    // 走过的轨迹线的样式
    linePassedStyle: { strokeStyle: 'red' },
    
    // 回放器的样式
    navigatorStyle: {
      // width: 32, // 巡航器形状宽度，指定了icon后，可以不指定宽度和高度，采用icon的原始大小

      // height: 15, // 巡航器形状高度

      initRotateDegree: 90,
      icon: '/static/img/car.png'
    },
    
    // 循环播放
    loop: false,
    
    // 巡航速度，单位千米/小时
    speed: this.speed,
    
    // 加载了数据后是否自动进行回放，默认自动回放
    autoStart: true
  }
  
  trackData = {
  }

  trackData1 = {
    paths: [{
      name: '行驶路线1',
      path: [
        {time: '10:10:00',
          speed: 1000,
          position: [117.121815, 36.686896]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.121557, 36.68638]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.1216, 36.685519]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.121686, 36.685244]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.121858, 36.684797]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.122115, 36.684384]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.122287, 36.684246]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.122587, 36.68373]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.122802, 36.683489]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.123789, 36.682181]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.123059, 36.681802]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.122373, 36.681389]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.121643, 36.681183]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.121858, 36.680701]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.122673, 36.679565]
        }
      ]
    },
    {
      name: '行驶路线2',
      linePassedStyle: { strokeStyle: 'green' },
      path: [
        {time: '10:10:00',
          speed: 1000,
          position: [117.122673, 36.679565]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.122973, 36.679393]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.124218, 36.677569]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.12572, 36.675366]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.127222, 36.673404]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.126449, 36.672957]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.126407, 36.673026]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.125656, 36.672905]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.125012, 36.672991]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.124475, 36.672922]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.123617, 36.672871]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.122351, 36.672699]
        },
        {time: '10:10:00',
          speed: 1000,
          position: [117.11748, 36.672062]
        }]
    }]
  }
  
  mapOptions: any = {
    resizeEnable: true,
    zoom: 11,
    center: [117.12224, 36.67429],
    mapType: 0,
    scaleOffset: [40, 40],
    scalePosition: 'LB'
  }
  
  setZoom () {
    this.zoom = 13
  }
  
  setCenter () {
    this.center = [116.397428, 39.90923]
  }
  
  setMarkers () {
    this.markers = [
      { id: 'mk1',
        position: [117.12224, 38.67429],
        icon: 'https://www.baidu.com/img/baidu_jgylogo4.gif',
        label: '新测试点1',
        title: '新测试title1',
        message: '哈哈！It\'s me too!',
        group: 'group1'
      },
      { id: 'mk5',
        position: [117.32224, 38.67429],
        icon: 'https://www.baidu.com/img/baidu_jgylogo3.gif',
        label: '测试点2',
        message: '哈哈！It\'s me too!!!',
        group: 'group2'
      }
    ]
  }
  
  rmMakerLine () {
    // this.$refs.map.setZoom ()
    this.gmap.removeMarker('mk1')
    this.gmap.removePolyline('line1')
    this.gmap.removePolygon('gon1')
    this.gmap.removeCircle('circle1')
    this.gmap.removeRectangle('rect1')
  }
  
  // 通过设置数据的方式删除原来画的点线面
  rmAll () {
    this.markers = []
    this.polylines = []
    this.polygons = null
    this.circles = null
    
    // let undefined: any
    this.rectangles = undefined
    this.texts = null
  }

  playback () { this.trackData = this.trackData1 }
  clearback () {
    // this.tracker.clear() 不需要这个
    this.trackData = null
  }
  pause () {
    this.tracker.pause()
  } 
  resume () { this.tracker.resume() }
  stop () { this.tracker.stop() }
  start () { this.tracker.start() }
  restart () { this.tracker.restart() }
  speedUp () {
    this.speed += 100
    this.tracker.setSpeed(this.speed)
  }
  speedDown () {
    if (this.speed < 100) return
    this.speed -= 100
    this.tracker.setSpeed(this.speed)
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.gingo-map {
  height: 600px;
}
</style>
