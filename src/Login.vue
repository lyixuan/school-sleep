<template>
  <div id="login">
    <div class="head">
      <img src="./assets/img-com/logo.png" height="65"
           style="vertical-align: middle;margin-top: -10px;margin-right: 10px;"/>
      {{app_name}}
    </div>
    <div class="content">
      <div class="pic" style="position: relative">
        <i>统计分析</i>
        <i>实时监控</i>
        <i>灵活管理</i>
        <span class="sp1"><img height="218" src="./assets/img-com/g2.png"/></span>
        <span class="sp2"><img height="233" src="./assets/img-com/g1.png"/></span>
        <span class="sp3"><img height="209" src="./assets/img-com/g3.png"/></span>
      </div>
      <div class="wrap">
        <div class="icon"><img src="./assets/img-com/login_icon.png"/></div>
        <div class="ying"></div>
        <div class="ying g2"></div>
        <div class="input-wrapper">
          <input type="text" v-focus v-model="user_code" placeholder="账号">
        </div>
        <div class="input-wrapper">
          <input type="password" v-model="password" @keyup.enter="submit" placeholder="密码">
          <span class="error" :class="{show:isShow}">用户名或密码错误</span>
        </div>
        <div class="button-wrapper">
          <button class="sign-button" @click="submit">登录</button>
        </div>

      </div>
    </div>
    <div class="foot">Copyrignt © 2016-2017 All Rights Reserved</div>
  </div>
</template>

<script>
  export default {
    name: 'login',
    data(){
      return {
        user_code: '',
        password: '',
        app_name: '智能睡眠检测系统',
        isShow: false
      }
    },
    mounted(){
      this.getInit()
    },
    methods: {
      getInit(){
        this.$resource(P_LOGIN + 'init.php').get().then((response) => {
          let r_data = response.body.data;
          this.app_name = r_data.app_name
        })
      },
      submit(){

        let param = {
          user_code: this.user_code,
          password: this.password
        };
        this.isShow = false;
        this.$resource(P_LOGIN + 'login.php').save({}, param).then((response) => {
          if (response.body.code != 200) {
            this.isShow = true
          } else {
            window.sessionStorage.setItem('u_session', JSON.stringify(response.body.data))
            window.location.href = window.location.href.substring(0, window.location.href.indexOf(window.location.pathname) + 1) + 'app.html#/dashboard'
          }
        }, (response) => {
          console.log(response.body)
        })
      },
    }
  }
</script>

<style>
  body {
    color: #555555;
    margin: 0;
    padding: 0;
    text-rendering: optimizeLegibility;
    -moz-osx-font-smoothing: grayscale;
    font-size: 14px;
    font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", "微软雅黑", Arial, sans-serif;
    background: #fff;
    box-sizing: border-box;
  }

  .head {
    width: 100%;
    height: 140px;
    line-height: 180px;
    border-bottom: 1px solid #E8EAEB;
    color: #2291C6;
    font-size: 35px;
    padding-left: 100px;
    box-sizing: border-box;
  }

  .content {
    position: relative;
    box-sizing: border-box;
    margin-top: 30px;
    width: 100%;
    height: 520px;
    /*opacity: 0.8;*/
    background: rgba(25, 138, 194, 1);
    /*background-image: url(./assets/img-com/bgii.jpg);*/
    /*background-repeat: repeat;*/
    /*background-position: center 0;*/
  }

  .foot {
    margin-top: 50px;
    text-align: center;
    margin-bottom: 30px;
  }

  .pic {
    height: 520px;
    position: absolute;
    float: left;
    padding: 20px;
    box-sizing: border-box;
  }

  .pic > i {
    position: absolute;
    color: #ffffff;
    font-size: 28px;
    width: 140px;
    font-style: normal;
  }

  .pic > i:nth-child(1) {
    left: 140px;
    bottom: 26%;
  }

  .pic > i:nth-child(2) {
    left: 420px;
    top: 26%;
  }

  .pic > i:nth-child(3) {
    left: 700px;
    bottom: 26%;
  }

  .pic>span{
    position: absolute;
  }
  .pic > .sp1 {
    top: 10%;
    left: 90px;
  }

  .pic > .sp2 {
    top: 38%;
    left: 350px;
  }

  .pic > .sp3 {
    top: 10%;
    left: 650px;
  }

  @media screen and (max-width: 1460px) {
    .content {
      height: 420px;
    }

    .pic {
      height: 420px;
    }
  }

  .pic > span:hover img {
    height: 235px;
  }

  .icon {
    position: absolute;
    top: 20px;
    left: 135px;
  }

  .wrap {
    height: 380px;
    width: 350px;
    /*opacity: .2;*/
    background: #48A1CF;
    margin-top: -30px;
    border-radius: 0 0 10px 10px;
    float: right;
    margin-right: 50px;
    padding: 70px 15px 15px 15px;
    box-sizing: border-box;
    position: relative;
  }

  @media screen and (min-width: 1650px) {
    .wrap {
      margin-right: 100px;
    }
  }

  @media screen and (min-width: 1650px) {
    .wrap {
      height: 430px;
      width: 400px;
      margin-right: 200px;
    }

    .icon {
      left: 155px;
    }
  }

  .ying {
    width: 40px;
    height: 40px;
    background: #48A1CF;
    opacity: .7;
    position: absolute;
    top: 8px;
    left: -13px;
    transform: rotate(-60deg);
    -ms-transform: rotate(-60deg); /* IE 9 */
    -moz-transform: rotate(-60deg); /* Firefox */
    -webkit-transform: rotate(-60deg); /* Safari 和 Chrome */
    -o-transform: rotate(-60deg); /* Opera */
    z-index: -1;

  }

  .g2 {
    left: auto;
    right: -13px;
    transform: rotate(60deg);
    -ms-transform: rotate(60deg); /* IE 9 */
    -moz-transform: rotate(60deg); /* Firefox */
    -webkit-transform: rotate(60deg); /* Safari 和 Chrome */
    -o-transform: rotate(60deg); /* Opera */
  }

  .input-wrapper {
    padding: 15px;
  }

  .input-wrapper input {
    padding: 1em .8em;
    width: 100%;
    box-sizing: border-box;
    margin: 0;
    line-height: 19px;
    border: 0;
    border-radius: 4px;
    background: #fff;
    color: #555;
    font-size: 14px;
  }

  input:focus {
    outline: none;
  }

  input::-webkit-input-placeholder, textarea::-webkit-input-placeholder {
    color: #555;
    opacity: .5;
    font-size: 14px;
  }

  input:-moz-placeholder, textarea:-moz-placeholder {
    color: #555;
    opacity: .5;
    font-size: 14px;
  }

  input::-moz-placeholder, textarea::-moz-placeholder {
    color: #555;
    opacity: .5;
    font-size: 14px;
  }

  input:-ms-input-placeholder, textarea:-ms-input-placeholder {
    color: #555;
    opacity: .5;
    font-size: 14px;
  }

  input:-webkit-autofill {
    -webkit-box-shadow: 0 0 0px 1000px white inset;
  }

  input:-webkit-autofill {
    background-color: #fff !important;
  }

  .button-wrapper {
    padding: 15px;
  }

  .sign-button {
    margin-top: 10px;
    background: #1D8CE0;
    box-shadow: none;
    border: 0;
    border-radius: 4px;
    line-height: 50px;
    color: #fff;
    display: block;
    padding: 0;
    width: 100%;
    font-size: 18px;
    letter-spacing: 10px;
    text-align: center;
    text-shadow: 0 1px 1px rgba(0, 0, 0, .2);
    outline: 0;
    cursor: pointer;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
  }

  .sign-button:hover {
    background: #20A0FF;
  }

  .error {
    color: #CC3333;
    position: absolute;
    display: none;
  }

  .show {
    display: block;
  }
</style>
