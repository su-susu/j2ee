<template>
    <el-container>
      <el-main v-if="edit">
            <Update @updateItem='updatedItem' @goBack='goBack' :item='editItemNo'></Update>
        </el-main>
        <el-main v-else>
            <el-col :span='18'>
                <Item @deleteItem='deleteItem' @changeItem='updateItem' v-for="item in itemList" :key="'item'+item.itemId" :item='item'></Item>
            </el-col>
        </el-main>
    </el-container>
</template>

<style scoped>
</style>

<script>
import Item from "~/components/home/Item";
import Cookies from "js-cookie";
import Update from '~/components/home/updateItem'
export default {
  mounted() {
    this.getItemList();
  },
  components: {
    Item,
    Update
  },
  data() {
    return {
      edit:false,
      itemList: [],
      editItemNo:0
    };
  },
  watch: {
    $route(to, from) {
      this.getItemList();
    }
  },
  methods: {
    updatedItem(){
      // this.edit = false;
    },
    goBack(){
      this.edit = false;
    },
    deleteItem(itemId) {
      let index = 0;
      while (this.itemList[index].itemId != itemId) index++;
      this.itemList.splice(index, 1);
    },
    updateItem(itemId) {
      this.editItemNo = itemId;
      this.edit = true;
    },
    async getItemList() {
      let data = {
        query: "getItemList",
        data: {
          userId: Cookies.get("userId"),
          sessionId: Cookies.get("sessionId")
        }
      };
      let response = await this.$axios.send(data);
      if (response.status == 1) {
        this.itemList = response.data.itemList;
      } else if (response.status == 0) {
        this.$message.error("发生错误：" + response.err);
      } else {
        this.$message.error("登录超时！");
        Cookies.remove("userId");
        Cookies.remove("sessionId");
        Cookies.remove("userName");
        this.$router.push({ path: "/" });
      }
    }
  }
};
</script>
