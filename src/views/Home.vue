<template>
  <div class="home">
    <div class="homeBox">
      <div class="login">
        用户名:
        <input type="text" v-model="username" v-show="Name1" />
        <span v-show="Name2">{{ username }}</span>
        <br />
        <button @click="Btn">{{ msg }}</button>
      </div>
      <div class="content">
        <div v-show="ContentShow1">
          <span
            v-for="(item, index) in dataList"
            :key="index"
            v-show="index == Index"
          >
            <h3>{{ item.title }}</h3>
            <p v-for="(items, index) in item.options" :key="index">
              <input
                type="radio"
                :checked="index == currIndex ? true : false"
                @change="change(index)"
              />{{ items }}
            </p>
          </span>
          <button @click="add" :disabled="disabled">{{ msg2 }}</button
          >{{ res + "后切换" }}
          <div class="isIndex">
            共有{{ Index2 }}/{{ dataList.length }}道题,答对{{ yes }},答错{{
              no
            }}
          </div>
        </div>
        <div v-show="ContentShow2">
          没有题了,重做一遍.....<button @click="redo">重做一遍</button>
        </div>
        <div class="color" v-show="ColorShow"></div>
      </div>
    </div>
  </div>
</template>

<script>
import DataList from "@/assets/data.json";
export default {
  name: "Home",
  components: {},
  data() {
    return {
      ColorShow: true,
      msg: "登录",
      msg2: "提交答案",
      username: "",
      Name1: true,
      Name2: false,
      dataList: DataList.data,
      Index: 0,
      Index2: 1,
      disabled: false,
      yes: 0,
      no: 0,
      currIndex: -1,
      res: 3,
      ContentShow1: true,
      ContentShow2: false,
    };
  },
  methods: {
    Btn() {
      if (this.msg == "登录") {
        if (this.username == "") {
          alert("用户名不能为空");
        } else {
          if (this.username.length >= 6 && this.username.length <= 18) {
            this.ColorShow = false;
            this.msg = "退出";
            this.Name1 = false;
            this.Name2 = true;
          } else {
            alert("用户名错误");
          }
        }
      } else {
        this.ColorShow = true;
        this.msg = "登录";
        this.Name1 = true;
        this.Name2 = false;
        this.username = "";
        this.Index = 0;
        this.Index2 = 1;
        this.yes = 0;
        this.no = 0;
        this.ContentShow1 = true;
        this.ContentShow2 = false;
        this.res = 3;
        this.currIndex = -1;
      }
    },
    add() {
      if (this.msg2 == "提交答案") {
        if (this.currIndex == this.dataList[this.Index].right) {
          this.msg2 = "答案正确";
          this.disabled = true;
          this.yes++;
          let time = setInterval(() => {
            this.res--;
            if (this.res == 0) {
              this.msg2 = "提交答案";
              this.disabled = false;
              this.Index++;
              this.Index2++;
              clearInterval(time);
              this.res = 3;
              this.currIndex = -1;
              if (this.Index2 > 4) {
                this.ContentShow1 = false;
                this.ContentShow2 = true;
              }
            }
          }, 1000);
        } else {
          this.msg2 = "答案错误";
          this.no++;
          this.disabled = true;
          let time = setInterval(() => {
            this.res--;
            if (this.res == 0) {
              this.msg2 = "提交答案";
              this.disabled = false;
              this.Index++;
              this.Index2++;
              clearInterval(time);
              this.res = 3;
              this.currIndex = -1;

              if (this.Index2 > 4) {
                this.ContentShow1 = false;
                this.ContentShow2 = true;
              }
            }
          }, 1000);
        }
      } else {
        this.msg2 = "提交答案";
        this.Index2 = 1;
        this.Index = 0;
      }
    },
    change(index) {
      this.currIndex = index;
      console.log(this.currIndex);
    },
    redo() {
      this.Index = 0;
      this.Index2 = 1;
      this.yes = 0;
      this.no = 0;
      this.ContentShow1 = true;
      this.ContentShow2 = false;
      this.res = 3;
      this.currIndex = -1;
    },
  },
};
</script>
<style lang="scss" scoped>
.home {
  width: 100%;
  .homeBox {
    width: 100%;
    margin-top: 100px;
    display: flex;
    .login {
      width: 50%;
      height: 350px;
      padding: 1%;
      border: 1px solid red;
    }
    .content {
      width: 50%;
      height: 350px;
      padding: 1%;
      position: relative;
      border: 1px solid red;
      .color {
        width: 100%;
        height: 100%;
        position: absolute;
        top: 0;
        left: 0;
        background-color: rgba(0, 0, 0, 0.9);
      }
      .isIndex {
        position: absolute;
        bottom: 0px;
        left: 10px;
      }
    }
  }
}
</style>
