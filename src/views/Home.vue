<template>
  <div>
    <el-container>
      <el-header style="text-align: right; font-size: 12px">
        <el-row :gutter="20">
          <el-col :span="2"
            ><el-button type="text" @click="toBlog()">博客</el-button></el-col
          >
          <el-col :span="2"
            ><el-button type="text" @click="toCalendar()">日历</el-button></el-col
          >
          <el-col :span="2"
            ><el-button type="text" @click="toSchedule()">课表</el-button>
          </el-col>
          <el-col :span="4" :offset="11" v-if="isLogin">
            <div class="username">
              {{ userForm.username }}
            </div>
          </el-col>
          <!-- 
              当登录时
          -->
          <el-col :span="1" :offset="1" v-if="isLogin">
            <el-dropdown>
              <el-button type="mini" style="margin-right: 15px" circle>
                <el-avatar :size="42" icon="el-icon-user-solid"> </el-avatar>
              </el-button>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item @click.native="toSelfCenter"
                  >个人中心</el-dropdown-item
                >
                <el-dropdown-item @click.native="exit"
                  >退出登录</el-dropdown-item
                >
              </el-dropdown-menu>
            </el-dropdown>
          </el-col>
          <!-- 
              当未登录时
            -->
          <el-button-group v-if="!isLogin">
            <el-button type="primary" @click="showLogin"  class="format">登录</el-button>
            <!-- 
              弹出登录dialog
             -->
            <el-dialog
              title="用户登录"
              :visible.sync="loginTableVisble"
              center
              :modal-append-to-body="false"
            >
              <el-form :model="userForm" ref="userForm" :rules="rules">
                <el-form-item
                  label="用户名/邮箱"
                  :label-width="formLabelWidth"
                  prop="username"
                >
                  <el-input
                    v-model="userForm.username"
                    autocomplete="off"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="密码"
                  :label-width="formLabelWidth"
                  prop="password"
                >
                  <el-input
                    type="password"
                    v-model="userForm.password"
                    autocomplete="off"
                  ></el-input>
                </el-form-item>
                <el-form-item>
                  <div class="right">
                    <el-link>
                      <el-button
                        type="text"
                        class="forget-pwd"
                        @click="toFindPwd()"
                        >忘记密码？</el-button
                      >
                    </el-link>
                  </div>
                </el-form-item>
              </el-form>
              <div slot="footer" class="dialog-footer">
                <el-button @click="toCancelDialog">取 消</el-button>
                <el-button type="success" @click="toLogin('userForm')"
                  >确 定</el-button
                >
              </div>
            </el-dialog>

            <el-dialog
              title="找回密码"
              :visible.sync="findPwdVisble"
              center
              :modal-append-to-body="false"
            >
              <el-steps
                :active="findPwd.step"
                align-center
                finish-status="success"
              >
                <el-step title="步骤 1" description="输入注册时的邮箱">
                </el-step>
                <el-step title="步骤 2" description="结果显示"></el-step>
              </el-steps>
              <div v-if="findPwd.step == 0">
                <el-input
                  placeholder="请输入邮箱"
                  prefix-icon="el-icon-message"
                  v-model="findPwd.email"
                >
                </el-input>
                <el-button @click="nextStep" align-center>下一步</el-button>
              </div>
              <div v-if="findPwd.step == 1">
                <div>密码已发送至您的邮箱,请注意查收！</div>
                <el-button @click="nextStep" align-center>下一步</el-button>
              </div>
            </el-dialog>

            <el-button type="primary" @click="showRegister"  class="format">注册</el-button>
            <!-- 
              弹出注册Dialog
             -->
            <el-dialog
              title="用户注册"
              :visible.sync="registerTableVisble"
              center
              :modal-append-to-body="false"
            >
              <el-form :model="userForm" ref="userForm" :rules="rules">
                <el-form-item
                  label="用户名"
                  :label-width="formLabelWidth"
                  prop="username"
                >
                  <el-input
                    v-model="userForm.username"
                    autocomplete="off"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="密码"
                  :label-width="formLabelWidth"
                  prop="password"
                >
                  <el-input
                    type="password"
                    v-model="userForm.password"
                    autocomplete="off"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="确认密码"
                  :label-width="formLabelWidth"
                  prop="checkpassword"
                >
                  <el-input
                    type="password"
                    v-model="userForm.checkpassword"
                    autocomplete="off"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="邮箱"
                  :label-width="formLabelWidth"
                  prop="email"
                >
                  <el-input
                    v-model="userForm.email"
                    autocomplete="off"
                  ></el-input><br/><el-button @click="sendVeriCode('userForm')" class="format-code"
                  >发送验证码</el-button
                > </el-form-item
                >
                <el-form-item label="验证码" :label-width="formLabelWidth">
                  <el-input
                    v-model="userForm.code"
                    autocomplete="off"
                  ></el-input>
                </el-form-item>
              </el-form>
              <div slot="footer" class="dialog-footer">
                <el-button @click="toCancelDialog">取 消</el-button>
                <el-button type="success" @click="toRegister('userForm')"
                  >确 定</el-button
                >
              </div>
            </el-dialog>
          </el-button-group>
        </el-row>
      </el-header>

      <el-main>
        <router-view :toName="userForm.username"></router-view>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import Blog from "./User/Blog";
export default {
  name: "Home",
  components: {
    Blog,
  },
  data() {
    var validatePass2 = (rule, value, callback) => {
      console.log(value);
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.userForm.password) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };
    var validateEmail = (rule, value, callback) => {
      console.log(value);
      if (value === "") {
        callback(new Error("请输入邮箱"));
      }
      callback();
    };
    return {
      isLogin: false, //$route.params.form.username
      loginTableVisble: false, // 登录弹窗判断
      registerTableVisble: false, // 注册弹窗判断
      findPwdVisble: false,
      formLabelWidth: "100px",
      userForm: {
        username: "",
        password: "",
        checkpassword: "",
        email: "",
        code: "",
      },
      findPwd: {
        email: "",
        step: 0,
        message: "",
        sendBit: false,
      },
      // 登录输入限制
      rules: {
        username: [
          {
            required: true,
            message: "请输入用户名",
            trigger: "blur",
          },
          {
            min: 1,
            max: 16,
            message: "长度需在 1 到 16 个字符",
            trigger: "blur",
          },
        ],
        password: [
          {
            required: true,
            message: "请输入密码",
            trigger: "blur",
          },
          {
            min: 6,
            max: 24,
            message: "长度需在 6 到 24 个字符",
            trigger: "blur",
          },
        ],
        checkpassword: [{ validator: validatePass2, trigger: "blur" }],
        email: [{ validator: validateEmail, trigger: "blur" }],
      },
      gridData: [
        {
          title: "个人中心",
        },
        {
          title: "退出登录",
        },
      ],
    };
  },
  methods: {
    // 登出账户
    exit() {
      this.isLogin = false;
      document.cookie = "";
      this.userForm.username = "";
      this.userForm.password = "";
      this.userForm.checkpassword = "";
      this.userForm.email = "";
      this.userForm.code = "";
      localStorage.clear();
      
      this.toBlog();
    },
    // 发送验证码
    sendVeriCode(formName) {
      var form = this.userForm;
      // console.log(form);
      this.$refs[formName].validate((valid) => {
        if (valid) {
          /*
          发送POST请求
          */
          this.$axios
            .post("/sendEmail", {
              email: form.email,
            })
            .then((res) => {
              console.log(res);
              if (res.data.message == "成功") {
                alert("验证码发送成功，请注意查收");
              } else {
                console.log("发送失败，请稍后重试");
              }
            });
        }
      });
    },
    // 跳转到课程表界面
    toSchedule() {
      this.$message({
          message: '功能暂未开放，敬请期待~',
          center: true,
          duration: 2000
        });
    },
    // 跳转到博客界面
    toBlog() {
      this.$router.push("/home");
    },
    // 跳转回主界面
    toCalendar() {
      this.$router.push("/calendar");
    },
    // 跳转到个人中心
    toSelfCenter() {
      this.$router.push("/personalcenter/center");
    },
    // 展示登录dialog
    showLogin() {
      this.loginTableVisble = true;
      this.registerTableVisble = false;
    },
    showRegister() {
      this.registerTableVisble = true;
      console.log(this.userForm);
      this.loginTableVisble = false;
    },
    // 登录
    toLogin(formName) {
      // 判断格式是否符合规则
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // 登录
          this.$axios
            .post("/login", {
              username: this.userForm.username,
              password: this.userForm.password,
            })
            .then((res) => {
              // 登录失败时
              if (res.data.data == null) {
                alert(res.data.message);
              } else {
                //登录成功时
                // 截取username
                var msg = res.data.data;
                var pos1 = msg.search("亲爱的") + 3;
                var pos2 = msg.search("用户");
                var pos3 = pos2 - pos1;
                this.userForm.username = msg.substring(pos1, pos2);
                localStorage.setItem("username", this.userForm.username);
                // 生成本地cookie
                var _date = this.generateCookie();
                document.cookie =
                  "username=" + this.userForm.username + "; expires=" + _date;
                this.isLogin = true;
                this.loginTableVisble = false;
              }
            });
          // 获取用户信息
          this.$axios
            .post("/getUser", {
              username: this.userForm.username,
            })
            .then((res) => {
              // 成功时
              if (res.data != "") {
                // 获取头像
                localStorage.setItem("avatarUrl", this.avatarUrl);
              }
            });
            this.$router.push("/User/Blog");
        } else {
          alert("用户名或密码错误");
          return false;
        }
      });
    },
    // 弹出注册Dialog
    toRegister(formName) {
      var form = this.userForm;
      // console.log(form);
      // 判断格式是否符合规则
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // 用户注册
          this.$axios
            .post("/regist", {
              username: form.username,
              password: form.password,
              email: form.email,
              code: form.code,
            })
            .then((res) => {
              // 注册失败时
              if (res.data.data == null) {
                alert(res.data.message);
              } else if (res.data.message == "成功") {
                // 注册成功时
                alert(res.data.data);
                localStorage.setItem("username", form.username);
                // 生成cookie
                var _date = this.generateCookie();
                document.cookie =
                  "username=" + this.userForm.username + "; expires=" + _date;
                this.isLogin = true;
                this.registerTableVisble = false;
              }
            });
        }
      });
    },
    // 生成cookie
    generateCookie() {
      var _date = new Date();
      _date.setDate(_date.getDate() + 1);
      _date.toGMTString();
      return _date;
    },
    toCancelDialog() {
      this.registerTableVisble = false;
      this.loginTableVisble = false;
      this.userForm.username = localStorage.getItem("username");
      this.userForm.password = "";
      this.userForm.checkpassword = "";
      this.userForm.email = "";
      this.userForm.code = "";
      console.log(this.userForm.checkPassword);
    },
    toFindPwd() {
      this.findPwdVisble = true;
    },
    nextStep() {
      if (this.findPwd.step < 1) {
        if (this.findPwd.email.length > 1) {
          // 找回密码
          this.$axios
            .post("/findPassword", {
              email: this.findPwd.email,
            })
            .then((res) => {
              this.findPwd.message = res.data.message;
              if (this.findPwd.message == "成功") {
                this.findPwd.sendBit = true;
                this.findPwd.step += 1;
              } else {
                alert("邮件发送失败，请检查格式并稍后重试！");
              }
            });
        }
      }
      if (this.findPwd.sendBit) {
        this.findPwd.step += 1;
        this.findPwdVisble = false;
        this.findPwd.message = "";
        this.findPwd.sendBit = false;
        this.findPwd.step = 0;
        this.findPwd.email = "";
      }
    },
  },
  created() {
    var tempName = localStorage.getItem("username");
    // 本地存有用户名时
    if (tempName != null) {
      this.userForm.username = tempName;
      // 获取头像
      this.$axios
        .post("/getUser", {
          username: this.userForm.username,
        })
        .then((res) => {
          // 成功时
          if (res.data != "") {
            // 获取头像
          } else {
            alert("获取头像失败");
          }
        });
      this.isLogin = true;
    } else {
      this.isLogin = false;
    }
  },
};
</script>

<style lang="scss" scoped>
.el-header {
  position: relative;
  background-color: rgb(128, 128, 255);
  color: rgb(255, 255, 255);
  line-height: 60px;
}
.el-main {
  position: absolute;
  left: 100px;
  right: 7px;
  top: 60px;
  bottom: 0;
  overflow-y: scroll;
  margin-top: -2px;
}

.el-button--text {
  color: rgb(255, 255, 255);
  font-size: 20px;
}
.username {
  font-size: 16px;
}
.el-button--primary {
  background-color: #effd27a1;
  font-size: 20px;
}
.user-pop-up {
  background-color: rgb(255, 255, 255);
  border-color: rgb(165, 165, 165);
  border-radius: 5px;
  box-shadow: 0 5px 10px grey;
}
.el-link {
  text-align: center;
}
.login-box {
  width: 500px;
  margin: 150px auto;
  border: 1px solid #dcdfe6;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 10px 20px grey;
  position: absolute;
}
.right {
  text-align: right;
}
.forget-pwd {
  color: rgb(3, 202, 228);
  font-size: 15px;
}
.el-main {
  position: absolute;
  left: 0px;
  right: 7px;
  top: 70px;
  bottom: 0;
  width: 100%;
  height: 90%;
  overflow-y: scroll;
  padding-top: 10px;
}
.format{
  border-radius: 10px;
}
.format-code{
  border-radius: 5px;
  background-color: rgb(247, 247, 247);
}
</style>