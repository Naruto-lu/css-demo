<template>
<div class="integration-earth" ref="integrationEarth">
</div>
</template>

<script>
/* eslint-disable */
import FGL from '@vislab/FGL'
import left from './static/left.jpg'
import right from './static/right.jpg'
import top from './static/top.jpg'
import bottom from './static/bottom.jpg'
import front from './static/front.jpg'
import back from './static/back.jpg'
import ball from './static/ball.jpg'
import duangImg from './static/duang.png'
import clouds from './static/clouds.jpg'
import smoke from './static/kaspersky_smoke.jpg'
import earthMap from './static/Earth_Color_8k.jpg'
import axios from 'axios'
import './style.less'
console.log(left, right, top, bottom, front, back, ball, duangImg, clouds, smoke, earthMap)
export default {
  name: 'integration-earth',
  data() {
    return {
      scene: '',
      alive: null, // 自转on/off
      earthControl: null,
      Radius: 85,
      uuid: '',
      adcode: '',
      sourceLnglat: [],
      organizationName: ''
    }
  },
  components: {},
  computed: {
  },
  watch: {
    integrationMapDataType(val) {
      console.log(val, 'val---watch---------')
      this.removeAreaData()
      this.removeAttackData()
      this.removeFallHostData()
      this.removeMalwareData()
      this.uuid = ''
      this.adcode = ''
      this.organizationName = ''
      if (typeof val == 'string') {
        switch (val) {
          case 'attacker':
            this.getAttackData()
          break
          case 'malware':
            this.getMalwareData()
          break
          case 'fall_host':
            this.getFallHostData()
          break
          default:
            this.getAttackData()
            this.getFallHostData()
            this.getMalwareData()
            this.getFallHostData()
        }
      } else if (typeof val == 'object') {
        switch (Object.keys(val)[0]) {
          case 'adcode':
            this.adcode = Object.values(val)[0]
            this.getAreaData(this.adcode)
            break
          case 'uuid':
            this.uuid = Object.values(val)[0]
            this.getAttackData()
            break
          case 'name':
            this.organizationName = Object.values(val)[0]
            this.getFallHostData()
        }
      }
    },
    earthRotateState(val) {
      this.alive = val
      if (this.alive) {
        this.rotateFn()
      }
    }
  },
  methods: {

    // 自转--On、Off
    toggleRotation() {
      this.alive = !this.alive
      if (this.alive) {
        this.rotateFn()
      }
    },

    // 转动
    rotateFn() {
      if (this.alive) {

        this.rotate = this.earthControl.rotate({ y: -10, x: 0 }, this.rotateFn, 1)

        // this.rotate = this.earthControl.rotate({ y: 1, x: 0 }, this.rotateFn, 1)
      } else {
        console.log(this.integrationMapDataType, 'ddddddd')
        console.log(this.sourceLnglat, 'sourrrrrr')
        this.earthControl.viewTo([100, 35, 302])

        // let lngLatVector3 = this.scene.worldPosToScreenPx([116, 20])
        // let y = lngLatVector3.y
        // let x = lngLatVector3.x
        // console.log(this.integrationMapDataType, 'integrationMapDataType--integrationMapDataType')
        // this.rotate = this.earthControl.rotate({ y: y, x: x }, () => {}, 1)
      }
    },

    // 恢复
    toggleDefault() {
      this.alive = false
      this.earthControl.viewTo([100, 35, 302])
    },

    // 攻击者数据
    getAttackData() {
      axios.get('/situation/global_scene/map_point', {
        params: {
          'uuid': this.uuid
        }
      }).then(res => {
        // let data = res.data.data.attack_point
        let data = [
          {
            "source": [
              "113.25",
              "23.1167"
            ],
            "target": [
              "112.548879",
              "37.870590"
            ]
          },
          {
            "source": [
              "113.25",
              "23.1167"
            ],
            "target": [
              "118.796877",
              "32.060255"
            ]
          },
          {
            "source": [
              "113.25",
              "23.1167"
            ],
            "target": [
              "108.366543",
              "22.817002"
            ]
          }
        ]

        // 动画 飞线 打点
        let biu = new FGL.THREE.Anime.Biu()
        this.biu = biu
        this.scene.add(biu)

        let duang = new FGL.THREE.Anime.Duang()
        this.duang = duang
        this.scene.add(duang)

        let len = data.length
        for (let i = 0; i < len; i++) {
          let {source, target} = data[i]
          source.forEach((v, i) => {
            this.sourceLnglat = v
            source[i] = parseFloat(v)
          })
          target.forEach((v, i) => {
            target[i] = parseFloat(v)
          })
          let duangColor = 0xffffff * Math.random()
          let biuColor = 0x8c0000

          /* 源 */
          duang.create({
            during: 2,
            times: Infinity,
            color: 0xff0000,
            size: 6,
            to: source,
            radius: this.Radius
          })

          /* 目的 */
          duang.create({
            during: 2,
            delay: 0.2,
            times: Infinity,
            color: duangColor,
            size: 5,
            to: target,
            radius: this.Radius
          })
          biu.create({
            during: 2,
            delay: 0.2,
            times: Infinity,
            color: biuColor,
            thickness: 0.5, // 线条粗细
            crest: 5, // 飞线弧度
            length: 0.5,
            to: target,
            from: source,
            radius: this.Radius
          })
        }
      }).catch(err => {
        let data = [
          {
            "source": [
              "113.25",
              "23.1167"
            ],
            "target": [
              "112.548879",
              "37.870590"
            ]
          },
          {
            "source": [
              "113.25",
              "23.1167"
            ],
            "target": [
              "118.796877",
              "32.060255"
            ]
          },
          {
            "source": [
              "113.25",
              "23.1167"
            ],
            "target": [
              "108.366543",
              "22.817002"
            ]
          }
        ]

        // 动画 飞线 打点
        let biu = new FGL.THREE.Anime.Biu()
        this.biu = biu
        this.scene.add(biu)

        let duang = new FGL.THREE.Anime.Duang()
        this.duang = duang
        this.scene.add(duang)

        let len = data.length
        for (let i = 0; i < len; i++) {
          let {source, target} = data[i]
          source.forEach((v, i) => {
            this.sourceLnglat = v
            source[i] = parseFloat(v)
          })
          target.forEach((v, i) => {
            target[i] = parseFloat(v)
          })
          let duangColor = 0xffffff * Math.random()
          let biuColor = 0x8c0000

          /* 源 */
          duang.create({
            data: {test: 1},
            during: 2,
            times: Infinity,
            color: 0xff0000,
            size: 8,
            to: source,
            radius: this.Radius
          })

          /* 目的 */
          duang.create({
            during: 2,
            delay: 0.2,
            times: Infinity,
            color: duangColor,
            size: 6,
            to: target,
            radius: this.Radius
          })
          
          // duang.create({
          //   data: data
          // })

          // duang的click事件
          let t
          duang.on('click', ({target}) => {
            if (target) {
              t = target

              // let d = target.getProperty('data')
              // console.log(d, 'ddddd')

              console.log(target.getProperty('data'), 'dhdhdh')
              target.setProperty('color', '0xeeeeee')
            }
            console.log(t, 'tttt')
          })

          biu.create({
            during: 2,
            delay: 0.2,
            times: Infinity,
            color: biuColor,
            thickness: 1.5, // 线条粗细
            crest: 5, // 飞线弧度
            length: 0.5,
            to: target,
            from: source,
            radius: this.Radius
          })
        }
        console.log(err, 'err')
      })
    },

    // 区域分布
    getAreaData(value) {
      axios.get('/situation/global_scene/region_distribute').then(res => {

        // 圆环光锥
        let cone = new FGL.THREE.Anime.Cone()
        this.cone = cone
        this.scene.add(cone)
        let to = []
        if (value) {
          res.data.data.forEach(item => {
            if (item.adcode == this.adcode) to.push(item)
          })
        } else {
          to = res.data.data
        }
        to.forEach((item, index) => {
          item.center[0] = Number(item.center[0])
          item.center[1] = Number(item.center[1])
          cone.create({
            times: Infinity,
            size: 2,
            height: 8,
            color: 0xFE5805,
            colorLight: 0x082bff,
            to: item.center,
            radius: this.Radius
          })
        })
      }).catch(err => { console.log(err, 'err') })
    },

    // 恶意软件数据
    getMalwareData() {
      axios.get('/situation/global_malware/malware_json').then(res => {
        let arrTo = []
        res.data.data.earth_statistic.forEach(item => {
          arrTo.push(Object.values(item)[0])
        })

        // 径向光锥
        // let hu = new FGL.THREE.Anime.Hu()
        // this.hu = hu
        // this.scene.add(hu)
        // arrTo.forEach(item => {
        //   let tmp = item[0]
        //   item[0] = Number(item[1])
        //   item[1] = Number(tmp)
        //   hu.create({
        //     color: 0xffffff * Math.random(),
        //     during: 1,
        //     times: Infinity,
        //     height: 35,
        //     to: item,
        //     radius: this.Radius,
        //     hexRadius: 3,
        //     scaleMin: 0,
        //     scaleMax: 2,
        //     scaleStep: 0.04
        //   })
        // })

        console.log(arrTo, 'arr-to--')

        // 圆环光锥
        let cone = new FGL.THREE.Anime.Cone()
        this.cone = cone
        this.scene.add(cone)
        arrTo.forEach(item => {
          let tempArr = []
          tempArr[0] = Number(item[1])
          tempArr[1] = Number(item[0])
          cone.create({
            times: Infinity,
            size: 2, // 大小
            height: 8, // 高度
            color: 0xFE5805,
            colorLight: 0x082bff,
            to: tempArr,
            radius: this.Radius
          })
        })
      }).catch(err => { console.log(err, 'err') })
    },

    // 失陷主机数据
    getFallHostData() {
      axios.get('/situation/global_scene/host_point', {
        params: {
          name: this.organizationName
        }
      }).then(res => {
        let data = res.data.data

        // 动画 飞线 打点
        let fallHostbiu = new FGL.THREE.Anime.Biu()
        this.fallHostbiu = fallHostbiu
        this.scene.add(fallHostbiu)

        let fallHostduang = new FGL.THREE.Anime.Duang()
        this.fallHostduang = fallHostduang
        this.scene.add(fallHostduang)

        let len = data.length
        for (let i = 0; i < len; i++) {
          let {source, target} = data[i]
          source.forEach((v, i) => {
            this.sourceLnglat = v
            source[i] = parseFloat(v)
          })
          target.forEach((v, i) => {
            target[i] = parseFloat(v)
          })
          let duangColor = 0xffffff * Math.random()
          let biuColor = 0xb50000

          /* 源 */
          fallHostduang.create({
            during: 2,
            times: Infinity,
            color: 0xff0000,
            size: 6,
            to: source,
            radius: this.Radius
          })

          /* 目的 */
          fallHostduang.create({
            during: 2,
            delay: 0.5,
            times: Infinity,
            color: duangColor,
            size: 5,
            to: target,
            radius: this.Radius
          })
          fallHostbiu.create({
            during: 2,
            delay: 0.5,
            times: Infinity,
            color: biuColor,
            thickness: 0.5, // 线条粗细
            crest: 5, // 飞线弧度
            length: 0.5,
            to: target,
            from: source,
            radius: this.Radius
          })
        }
      }).catch(err => { console.log(err) })
    },

    // 数据清空
    removeAttackData() {
      this.scene.remove(this.biu)
      this.scene.remove(this.duang)
    },
    removeFallHostData() {
      this.scene.remove(this.fallHostbiu)
      this.scene.remove(this.fallHostduang)
    },
    removeAreaData() {
      this.scene.remove(this.cone)
    },
    removeMalwareData() {

      // this.scene.remove(this.hu)
      this.scene.remove(this.cone)
    }
  },
  created() {},
  mounted() {
    this.alive = this.earthRotateState
    this.scene = new FGL.Scene(this.$refs.integrationEarth, {
      camera: {

        /* 相机坐标 */
        position: {
          z: -240,
          y: 170,
          x: -50
        }

        /* 相机上方向 */
        // up: {
        //   x: 0,
        //   y: 1,
        //   z: 1
        // }

        /* 相机视点 */
        // lookAt: {
        //   x: 0,
        //   y: -20,
        //   z: 0
        // }
      },
      scene: {
        position: {
          x: 0,
          y: 0,
          z: 0
        }
      },
      events: {
        openClick: true
      }
    })

    // 天空盒
    let skyBox = new FGL.SkyBox({
      boxSize: 1500,
      maps: [left, right, top, bottom, front, back]
    })
    this.scene.add(skyBox)

    let Radius = this.Radius

    // 普通mesh地球
    let earth = new FGL.THREE.Earth.MeshShaderEarth({
      material: {
        map: earthMap,
        useTerrian: true,
        useShadow: false
      },
      geometry: {
        radius: Radius,
        height: 0
      }
    })
    this.scene.add(earth)

    // 球形光晕 外发光
    let halo = new FGL.THREE.Halo.OutterHalo({
      geometry: {
        radius: Radius + 22
      },
      material: {
        color: 0x6fd5f0,
        opacity: 0.8
      }
    })
    this.scene.add(halo)

    // 烟雾
    let smokeLayer = new FGL.SkySmoke({
      radius: Radius,
      map: smoke,
      color: '#043e42',
      opacity: 1
    })
    this.scene.add(smokeLayer)

    // 线地球
    let line = new FGL.THREE.Earth.GeojsonLineEarth({
      material: {
        useTerrian: true,
        color: 0x6FD5F0
      },
      geometry: {
        radius: Radius,
        height: 0
      }
    })
    this.scene.add(line)
    let earthControl = new FGL.THREE.Controller(this.scene)
    this.earthControl = earthControl

    this.rotateFn()
    this.getAttackData()
    this.getMalwareData()
    this.getFallHostData()

    // this.getAreaData() // 默认不获取区域分布打点数据
  },
  beforeDestroy() {
    clearInterval(this.interval)
    this.alive = false
    this.scene.destroy()
  }
}
</script>
