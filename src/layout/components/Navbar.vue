<template>
  <div class="navbar">
    <hamburger :is-active="sidebar.opened" class="hamburger-container" @toggleClick="toggleSideBar" />
    <breadcrumb class="breadcrumb-container" />
    <div class="right-menu">
      <el-dropdown class="avatar-container">
        <div class="avatar-wrapper" style="display:flex;height:100%;align-items: center;padding-right:20px;">
          <div style="margin-right:10px;">{{helloName}}</div>
          <div>
            <img src="https://wpimg.wallstcn.com/f778738c-e4f8-4870-b634-56703b4acafe.gif?imageView2/1/w/80/h/80" class="user-avatar" />
            <!-- <i class="el-icon-caret-bottom" /> -->
          </div>
        </div>
        <el-dropdown-menu slot="dropdown" class="user-dropdown">
          <router-link to="/">
            <el-dropdown-item>首页</el-dropdown-item>
          </router-link>

          <el-dropdown-item divided>
            <span style="display:block;" @click="editPassword">修改密码</span>
          </el-dropdown-item>
          <el-dropdown-item divided>
            <span style="display:block;" @click="getConfirm">退出登陆</span>
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>

  </div>
</template>

<script>
import { mapGetters } from "vuex";
import Breadcrumb from "@/components/Breadcrumb";
import Hamburger from "@/components/Hamburger";
import JSEncrypt from "jsencrypt/bin/jsencrypt"; //rsa
import md5 from "js-md5"; //md5

export default {
  components: {
    Breadcrumb,
    Hamburger
  },
  data() {
    return {
      loading: true,
      helloName: "Hello , " + this.$store.state.baseUser.userName
    }
  },
  computed: {
    ...mapGetters(["sidebar"])
  },
  methods: {
    //   修改密码
    editPassword() {
      this.$prompt('请输入密码', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        inputErrorMessage: '密码不正确'
      }).then(({ value }) => {
        if (value) {
          let paramer = md5(this.$store.state.baseUser.userName + md5(value))
          this.axios.patch(this.Global.BASE_URL + "/emp/pwd", paramer).then(response => {
            if (response.data.status == 200) {
              this.$message({
                message: '恭喜你，这是一条成功消息',
                type: 'success'
              })
              console.log(response)
            }
          })
        }
      })
    },
    // 询问框
    getConfirm() {
      this.$confirm("您确定要退出?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.logout();
      })
    },
    toggleSideBar() {
      this.$store.dispatch("toggleSideBar");
    },
    // 退出登录
    logout() {
      let baseUser = this.$store.state.baseUser
      this.axios.get(this.Global.BASE_URL + "/logout").then(response => {
        if (response.status == 200) {
          localStorage.setItem("baseUser", "");
          this.$store.state.baseUser = "";
          this.$router.push({ path: "/login" });
        }
      })
    },

  }
};
</script>

<style lang="scss" scoped>
.navbar {
  height: 50px;
  overflow: hidden;
  position: relative;
  background: #fff;
  box-shadow: 0 1px 4px rgba(0, 21, 41, 0.08);

  .hamburger-container {
    line-height: 46px;
    height: 100%;
    float: left;
    cursor: pointer;
    transition: background 0.3s;
    -webkit-tap-highlight-color: transparent;

    &:hover {
      background: rgba(0, 0, 0, 0.025);
    }
  }

  .breadcrumb-container {
    float: left;
  }

  .right-menu {
    float: right;
    height: 100%;
    &:focus {
      outline: none;
    }
    .right-menu-item {
      display: inline-block;
      padding: 0 8px;
      height: 100%;
      font-size: 18px;
      color: #5a5e66;
      //   vertical-align: text-bottom;

      &.hover-effect {
        cursor: pointer;
        transition: background 0.3s;

        &:hover {
          background: rgba(0, 0, 0, 0.025);
        }
      }
    }

    .avatar-container {
      height: 100%;
      .avatar-wrapper {
        position: relative;
        .user-avatar {
          cursor: pointer;
          width: 35px;
          height: 35px;
          border-radius: 35px;
          display: block;
        }

        // .el-icon-caret-bottom {
        //   cursor: pointer;
        //   position: absolute;
        //   right: -20px;
        //   top: 25px;
        //   font-size: 12px;
        // }
      }
    }
  }
}
</style>