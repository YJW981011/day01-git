<template>
  <div class="home">
    <button @click="lock">{{ locks }}</button>
    <p>
      所在城市：
      <select v-model="city" :disabled="disabled">
        <option
          v-for="(item, index) in cityList"
          :key="index"
          :value="item.name"
        >
          {{ item.name }}
        </option>
      </select>
    </p>
    <p>用户名:<input type="text" v-model="username" :disabled="disabled" /></p>
    <p>年龄:<input type="text" v-model="age" :disabled="disabled" /></p>
    <button @click="add" :disabled="disabled">{{ msg }}</button>
    <p>
      用户名搜索:<input
        type="text"
        placeholder="输入关键字"
        @input="searchs"
        v-model="search"
        :disabled="disabled"
      />
    </p>
    <p>
      城市搜索:<input
        type="text"
        placeholder="输入关键字"
        @input="searchscity"
        v-model="cityss"
        :disabled="disabled"
      />
    </p>
    <p>
      年龄搜索:
      <input
        type="text"
        placeholder="输入最小年龄"
        :disabled="disabled"
        v-model.number="Xage"
      />
      -
      <input
        type="text"
        placeholder="输入最大年龄"
        :disabled="disabled"
        v-model.number="Dage"
      />
      <button :disabled="disabled" @click="searchage">搜索</button>
      <button :disabled="disabled" @click="Reset">重置</button>
    </p>
    <table border="1px">
      <tr>
        <td>选择</td>
        <td>ID</td>
        <td>用户名</td>
        <td @click="ageSort">年龄{{ ages }}</td>
        <td>所在城市<span @click="CitySort">👆👇</span></td>
        <td>操作</td>
      </tr>
      <tr
        v-for="(item, index) in DataList"
        :key="index"
        :class="{ even: index % 2 == 0, odd: index % 2 !== 0 }"
      >
        <td>
          <input
            type="checkbox"
            :checked="item.check"
            :disabled="disabled"
            @change="change(item)"
          />
        </td>
        <td>{{ item.id }}</td>
        <td>{{ item.name }}</td>
        <td>{{ item.age }}</td>
        <td>{{ item.city }}</td>
        <td>
          <button :disabled="disabled" @click="Modify(index)">修改</button
          ><button :disabled="disabled" @click="del(index)">删除</button>
        </td>
      </tr>
    </table>
    <button @click="remove">选中删除</button>
  </div>
</template>

<script>
import citylist from "@/assets/data.json";
export default {
  name: "Home",
  components: {},
  data() {
    return {
      msg: "提交",
      locks: "锁定",
      cityList: citylist.data,
      city: "",
      age: "",
      username: "",
      num: 0,
      DataList: [],
      DataList2: [],
      Index: -1,
      disabled: false,
      res: 3,
      ages: "👆",
      search: "",
      cityss: "",
      Dage: "",
      Xage: "",
    };
  }, 
  methods: {
    //添加
    add() {
      if (this.username == "" || this.age == "" || this.city == "") {
        alert("任何框框都不能为空");
      } else {
        if (this.msg == "提交") {
          this.num++;
          var obj = {
            id: this.num,
            name: this.username,
            age: this.age,
            city: this.city,
            check: false,
          };
          this.DataList.push(obj);
          this.DataList2.push(obj);
          this.username = "";
          this.city = "";
          this.age = "";
        } else {
          var obj = {
            id: this.DataList[this.Index].id,
            name: this.username,
            age: this.age,
            city: this.city,
            check: false,
          };
          this.DataList.splice(this.Index, 1, obj);
          this.username = "";
          this.city = "";
          this.age = "";
          this.msg = "提交";
        }
      }
    },
    //删除
    del(index) {
      this.DataList.splice(index, 1);
      this.DataList2 = this.DataList;
    },
    Modify(index) {
      this.Index = index;
      this.username = this.DataList[index].name;
      this.city = this.DataList[index].city;
      this.age = this.DataList[index].age;
      this.msg = "确认修改";
    },
    lock() {
      if (this.locks == "锁定") {
        this.disabled = true;
        this.locks = "解锁";
      } else if (this.locks == "解锁") {
        this.locks = this.res + "秒后解锁";
        let time = setInterval(() => {
          console.log(this.res);

          this.res--;
          this.locks = this.res + "秒后解锁";
          if (this.res == 0) {
            this.locks = "锁定";
            this.disabled = false;
            clearInterval(time);
            this.res = 3;
          }
        }, 1000);
      }
    },
    //选中删除
    remove() {
      this.DataList = this.DataList.filter((item) => {
        if (item.check == false) {
          return item;
        }
      });
      this.DataList2 = this.DataList;
    },
    change(item) {
      if (item.check == false) {
        item.check = true;
      } else if (item.check == true) {
        item.check = false;
      }
    },
    //年龄排序
    ageSort() {
      if (this.ages == "👆") {
        this.DataList.sort((a, b) => {
          return b.age - a.age;
        });
        this.ages = "👇";
      } else if (this.ages == "👇") {
        this.DataList.sort((a, b) => {
          return a.age - b.age;
        });
        this.ages = "👆";
      }
    },
    //用户名搜索
    searchs() {
      this.DataList = this.DataList2.filter((item) => {
        if (item.name.includes(this.search)) {
          return item;
        }
      });
    },
    //城市搜索
    searchscity() {
      this.DataList = this.DataList2.filter((item) => {
        if (item.city.includes(this.cityss)) {
          return item;
        }
      });
    },
    //年龄搜索
    searchage() {
      this.DataList = this.DataList2.filter((item) => {
        return item.age >= this.Xage && item.age <= this.Dage;
      });
    },
    //重置
    Reset() {
      this.DataList = this.DataList2;
      this.Dage = "";
      this.Xage = "";
    },
    //城市排序
    CitySort() {
      this.DataList = this.DataList2.sort((item1, item2) => {
        return item1.city.localeCompare(item2.city);
      });
      console.log(this.DataList);
    },
  },
};
</script>
<style lang="scss" scoped>
.home {
  padding: 5px;
  table {
    width: 80%;
    text-align: center;
    border-collapse: collapse;
    tr {
      height: 40px;
    }
  }
}
.even {
  background-color: aqua;
}
.odd {
  background-color: rgb(255, 0, 157);
}
</style>