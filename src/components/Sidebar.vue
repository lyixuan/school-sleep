<template>
  <div class="sidebar" id="sidebar">
    <div class="menu logo-menu" @click="changeSidebar">
      <i class="el-icon-more"></i>
    </div>

    <router-link to="/dashboard" v-for="item in checkMenuList" v-if="item == 1" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-jiankong.png"/>
        <span>公寓监控概览</span>
      </div>
    </router-link>
    <router-link to="/bed_state" v-for="item in checkMenuList" v-if="item == 2" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-chuang.png"/>
        <span>床位状态监控</span>
      </div>
    </router-link>
    <router-link to="/alarm_24monitor" v-for="item in checkMenuList" v-if="item == 3" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-baojing.png" style="width: 22px;margin-left: -2px;"/>
        <span>24小时报警监控</span>
      </div>
    </router-link>
    <router-link to="/sleep_24monitor" v-for="item in checkMenuList" v-if="item == 4" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-shuimian.png"/>
        <span>24小时睡眠监控</span>
      </div>
    </router-link>
    <router-link to="/alarm_search" v-for="item in checkMenuList" v-if="item == 5" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-bjjiansuo.png"/>
        <span>报警检索</span>
      </div>
    </router-link>
    <router-link to="/report_search" v-for="item in checkMenuList" v-if="item == 6" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-jiansuo.png"/>
        <span>报告检索</span>
      </div>
    </router-link>
    <router-link to="/people_manage" v-for="item in checkMenuList" v-if="item == 7" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-people.png"/>
        <span>学生管理</span>
      </div>
    </router-link>
    <router-link to="/level_manage" v-for="item in checkMenuList" v-if="item == 8" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-level.png"/>
        <span>级别管理</span>
      </div>
    </router-link>
    <router-link to="/options" v-for="item in checkMenuList" v-if="item == 9" :key="item">
      <div class="menu">
        <img class="icon" src="../assets/img-com/icon-set.png"/>
        <span>账号权限配置</span>
      </div>
    </router-link>
  </div>
</template>

<script>
  export default {
    name: 'sidebar',
    data () {
      return {
        rIsShow: 1,
        isClose: false,
        checkMenuList: []
      }
    },
    mounted(){
      this.getMenuRights()
    },
    methods: {
      getMenuRights(){
        let _this = this;
        let u_session = JSON.parse(window.sessionStorage.getItem('u_session'))
        let user_code = u_session.user_code
        this.$resource(P_PRIVILEGE + 'user_roles').get({user_code: user_code}).then((response) => {
          if (response.body.code == 200) {
            let r = response.body.data;
            let tmp = []
            for (var i = 0; i < r.length; i++) {
              (function (arg) {
                _this.$resource(P_PRIVILEGE + 'role_rights').get({role_id: r[arg]}).then((response) => {
                  if (response.body.code == 200) {
                    tmp = tmp.concat(response.body.data)
                  } else {
                    _this.alertMsg("warning", '获取角色权限信息有误')
                  }
                  _this.checkMenuList = []
                  for (var f = 0; f < tmp.length; f++) {
                    if ( _this.checkMenuList.indexOf(tmp[f]) == -1) {
                      _this.checkMenuList.push(tmp[f])
                    }
                  }
                })
              })(i);//调用时参数
            }
          } else {
            this.alertMsg("warning", '获取菜单权限有误')
          }
        })
      },
      changeSidebar () {
        let view = document.getElementById("view-wrap")
        let sidebar = document.getElementById("sidebar")
        let menus = document.getElementsByClassName("menu")
        if (this.isClose) {
          let width = 56
          var timer = setInterval(fn, 2)

          function fn() {
            width += 5
            view.style = "padding-left:" + width + "px;"
            if (width == 166) {
              clearInterval(timer)
            }
          }

          let swidth = 50
          var stimer = setInterval(sfn, 2)

          function sfn() {
            swidth += 5
            sidebar.style = "width:" + swidth + "px;"
            for (let i = 0; i < menus.length; i++) {
              menus[i].style = "width:" + swidth + "px;"
            }
            if (swidth == 160) {
              clearInterval(stimer)
            }
          }

        } else {
          view.style = "padding-left:56px;"
          let width = 166
          var timer2 = setInterval(fn2, 2)

          function fn2() {
            width -= 5
            view.style = "padding-left:" + width + "px;"
            if (width == 56) {
              clearInterval(timer2)
            }
          }

          let swidth = 160
          var stimer2 = setInterval(sfn2, 2)

          function sfn2() {
            swidth -= 5
            sidebar.style = "width:" + swidth + "px;"
            for (let i = 0; i < menus.length; i++) {
              menus[i].style = "width:" + swidth + "px;"
            }
            if (swidth == 50) {
              clearInterval(stimer2)
            }
          }
        }
        this.isClose = !this.isClose
      }
    }
  }
</script>

<style scoped>
  * {
    box-sizing: border-box;
  }

  .sidebar {
    position: fixed;
    top: 50px;
    left: 0;
    bottom: 0;
    width: 160px;
    background: #324157;
    z-index: 200;
  }

  .menu {
    height: 40px;
    line-height: 40px;
    width: 160px;
    background: #4A5064;
    text-align: left;
    padding-left: 15px;
    color: #fff;
    overflow: hidden;
    margin-top: 1px;
  }

  .menu span {
    color: #ddd;
  }

  .menu:hover span {
    color: #fff;
  }

  a.router-link-active .menu {
    background: #20A0FF;
  }

  a.router-link-active .menu span {
    color: #fff;
  }

  .icon {
    margin-right: 5px;
    width: 18px;
    vertical-align: text-bottom;
  }

  .logo-menu {
    text-align: center;
    padding-right: 12px;
    cursor: pointer;
    height: 30px;
    line-height: 30px;
  }

  .rShow {
    opacity: 1;
  }
</style>
