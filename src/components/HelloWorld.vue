<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <el-tree :data="ownersTreeNodeData" default-expand-all empty-text="No data"></el-tree>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      errored: false,
      loading: false,
      allOwners: [], // all the owners returned from backend
      maleOwners: [], // all the male owners reuturned from backend
      femaleOwners: [], // all the female owners returned from backend
      ownersTreeNodeData: [] // data structure for the el-tree
    };
  },
  mounted() {},
  methods: {
    getOwners() {
      this.loading = true;
      axios
        .get("http://5c92dbfae7b1a00014078e61.mockapi.io/owners", {
          timeout: 30000
        })
        .then(response => {
          this.allOwners = response.data;
          console.log(this.allOwners);
        })
        .catch(error => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => {
          this.loading = false;
          // transform the owners info
        });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
