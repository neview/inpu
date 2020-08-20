<template>
  <div class="login-container">
    <!--左上角的图片-->
    <div class="l-img">
      <img src="@/assets/login/bgi-ov1.webp" alt />
    </div>
    <!--右上角的图片-->
    <div class="r-img">
      <img src="@/assets/login/bgi-rec.jpg" alt />
    </div>
    <el-form
      ref="loginForm"
      :model="loginForm"
      :rules="loginRules"
      class="login-form"
      auto-complete="on"
      label-position="left"
    >
      <!--智慧用电上面蓝色方块-->
      <div class="f-1">
        <img src="@/assets/login/rec-blue.jpg" alt />
      </div>

      <!--智慧用电上面紫色小方块-->
      <div class="f-2">
        <img src="@/assets/login/rec-purple.jpg" alt />
      </div>

      <!--智慧用电管理系统-->
      <div class="title-container">
        <img src="@/assets/login/智慧用电管理系统@2x.jpg" alt />
      </div>
      <!--welocome admin-->
      <div class="wd">
        <span>WELOCOME! Admin</span>
      </div>
      <!--为您的用电安全保驾护航-->
      <div class="hq">
        <span>为您的用电安全保驾护航</span>
      </div>

      <el-form-item prop="username">
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>
        <el-input
          ref="username"
          v-model="loginForm.username"
          placeholder="Username"
          name="username"
          type="text"
          tabindex="1"
          auto-complete="on"
        />
      </el-form-item>

      <el-form-item prop="password">
        <span class="svg-container">
          <svg-icon icon-class="password" />
        </span>
        <el-input
          :key="passwordType"
          ref="password"
          v-model="loginForm.password"
          :type="passwordType"
          placeholder="Password"
          name="password"
          tabindex="2"
          auto-complete="on"
          @keyup.enter.native="handleLogin"
        />
        <span class="show-pwd" @click="showPwd">
          <svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" />
        </span>
      </el-form-item>
      <!--忘记密码-->
      <div class="wjma">
        <img src="@/assets/login/忘记密码@2x.jpg" alt />
      </div>

      <el-button
        :loading="loading"
        type="primary"
        style="width:100%;margin-bottom:30px;"
        @click.native.prevent="handleLogin"
      >Login</el-button>

      <!--input框右边的图片-->
      <div class="dm">
        <img src="@/assets/login/draw-main.webp" alt />
      </div>

      <!--input框右边的图片下的 黄色方块-->
      <div class="dm-ye">
        <img src="@/assets/login/rec-yellow@2x.jpg" alt />
      </div>

      <!-- <div class="tips">
        <span style="margin-right:20px;">username: admin</span>
        <span>password: any</span>
      </div>-->
    </el-form>
    <!--四个小圆球-->
    <div class="q-1">
      <img src="@/assets/login/oval-blue1.webp" alt />
    </div>
    <div class="q-2">
      <img src="@/assets/login/bgi-ov2.webp" alt />
    </div>
    <div class="q-3">
      <img src="@/assets/login/bgi-ov.webp" alt />
    </div>
    <div class="q-4">
      <img src="@/assets/login/oval-purple10.png" alt />
    </div>
  </div>
</template>

<script>
import { validUsername } from "@/utils/validate";

export default {
  name: "Login",
  data() {
    const validateUsername = (rule, value, callback) => {
      if (!validUsername(value)) {
        callback(new Error("Please enter the correct user name"));
      } else {
        callback();
      }
    };
    const validatePassword = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error("The password can not be less than 6 digits"));
      } else {
        callback();
      }
    };
    return {
      loginForm: {
        username: "admin",
        password: "111111",
      },
      loginRules: {
        username: [
          { required: true, trigger: "blur", validator: validateUsername },
        ],
        password: [
          { required: true, trigger: "blur", validator: validatePassword },
        ],
      },
      loading: false,
      passwordType: "password",
      redirect: undefined,
    };
  },
  created() {},
  watch: {
    $route: {
      handler: function (route) {
        this.redirect = route.query && route.query.redirect;
      },
      immediate: true,
    },
  },
  methods: {
    showPwd() {
      if (this.passwordType === "password") {
        this.passwordType = "";
      } else {
        this.passwordType = "password";
      }
      this.$nextTick(() => {
        this.$refs.password.focus();
      });
    },
    handleLogin() {
      this.$refs.loginForm.validate((valid) => {
        if (
          this.loginForm.username ||
          /^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{8,16}$/.test(
            this.loginForm.password
          ) ||
          this.loginForm.password.length >= 6
        ) {
          if (valid) {
            this.loading = true;
            this.$store
              .dispatch("user/login", this.loginForm)
              .then(() => {
                this.$router.push({ path: this.redirect || "/" });
                this.loading = false;
              })
              .catch(() => {
                this.loading = false;
              });
          } else {
            console.log("error submit!!");
            return false;
          }
        }
      });
    },
  },
};
</script>

<style lang="scss">
/* 修复input 背景不协调 和光标变色 */
/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

$bg: #283443;
$light_gray: #889aa4;
$cursor: #889aa4;

@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
  .login-container .el-input input {
    color: $cursor;
  }
}

/* reset element-ui css */
.login-container {
  .el-input {
    display: inline-block;
    height: 30px;
    width: 85%;

    input {
      border: 0px;
      -webkit-appearance: none;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      color: $light_gray;
      height: 60px;
      caret-color: $cursor;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px $bg inset !important;
        -webkit-text-fill-color: $cursor !important;
      }
    }
  }

  .el-form-item {
    border: 1px solid #889aa4;
    border-radius: 50px;
    color: #454545;
    width: 250px;
    height: 45px;
    margin-left: 100px;
  }
}
</style>

<style lang="scss" scoped>
$bg: #c7ebfe;
$dark_gray: #889aa4;
$light_gray: #eee;

.login-container {
  min-height: 100%;
  width: 100%;
  background-color: $bg;
  overflow: hidden;
  .l-img {
    width: 350px;
    height: 363px;
    float: left;
    display: inline-block;
    img {
      width: 100%;
      height: 100%;
    }
  }
  .r-img {
    width: 216px;
    height: 165px;
    float: right;
    display: inline-block;
    img {
      width: 216px;
      height: 165px;
    }
  }

  .login-form {
    position: absolute;
    top: 75px;
    left: 100px;
    width: 1336px;
    height: 572px;
    max-width: 100%;
    overflow: hidden;
    background: white;
    z-index: 10;
    -webkit-transform: translateY(-3px);
    -ms-transform: translateY(-3px);
    transform: translateY(-3px);
    -webkit-box-shadow: 0 0 6px #999;
    box-shadow: 0 0 6px #999;
    .f-1 {
      width: 200px;
      height: 60px;
      float: left;
      img {
        width: 200px;
        height: 60px;
      }
    }
    .f-2 {
      float: left;
      width: 120px;
      height: 31px;
      img {
        width: 120px;
        height: 31px;
      }
    }
    .wd,
    .hq {
      margin: 10px 0 10px 100px;
    }
    .wd {
      font-size: 30px;
      font-weight: bold;
    }
    .hq {
      span {
        color: #6e7770;
      }
    }
    .wjma {
      margin-left: 280px;
    }
    .el-button {
      width: 250px !important;
      height: 45px;
      margin-left: 100px;
      border-radius: 50px;
      margin-top: 20px;
    }
    .dm {
      width: 700px;
      height: 450px;
      position: absolute;
      top: 30px;
      right: 30px;
      background: url("../../assets/login/drawbg@2x.jpg") no-repeat no-repeat;
      background-size: 600px 350px;
      background-position: 0px 85px;
      img {
        width: 535px;
        height: 380px;
        margin-left: 80px;
      }
    }
    .dm-ye {
      width: 165px;
      height: 87px;
      position: absolute;
      bottom: -4px;
      right: 0;
    }
  }

  .tips {
    font-size: 14px;
    color: #fff;
    margin-bottom: 10px;

    span {
      &:first-of-type {
        margin-right: 16px;
      }
    }
  }

  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    // vertical-align: middle;
    width: 30px;
    display: inline-block;
  }

  .title-container {
    margin-top: 100px;
    img {
      width: 404px;
      height: 46px;
      margin-left: 100px;
    }
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
  }
}
.q-1,
.q-2,
.q-3,
.q-4 {
  z-index: 99;
}
.q-1 {
  position: absolute;
  bottom: 200px;
  left: 78px;
}
.q-2 {
  position: absolute;
  top: 46px;
  left: 520px;
}
.q-3 {
  position: absolute;
  bottom: 60px;
  left: 110px;
}
.q-4 {
  position: absolute;
  bottom: 45px;
  left: 380px;
}
</style>
