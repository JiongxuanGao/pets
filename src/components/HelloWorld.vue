<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <el-divider />
    <el-select
      v-model="petType"
      filterable
      allow-create
      default-first-option
      loading-text="Loading..."
      no-match-text="No data found."
      placeholder="Please choose pet type."
      @change="getOwners()"
      :loading="loading"
    >
      <el-option v-for="item in petTypes" :key="item.value" :label="item.label" :value="item.value"></el-option>
    </el-select>
    <el-divider />
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
      petType: "",
      petTypes: [
        {
          value: "Cat",
          label: "Cat"
        },
        {
          value: "Dog",
          label: "Dog"
        },
        {
          value: "Fish",
          label: "Fish"
        }
      ],
      allOwners: [], // all the owners returned from backend
      maleOwners: [], // all the male owners after filtering
      femaleOwners: [], // all the female owners after filtering
      ownersTreeNodeData: [] // data structure for the el-tree
    };
  },
  mounted() {},
  methods: {
    getOwners() {
      this.reset();
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
          if (this.allOwners) {
            console.log("Owners count:" + this.allOwners.length);
            var owner;
            for (owner of this.allOwners) {
              if (owner) {
                console.log("Owner name=" + owner.name + ", gender=" + owner.gender + ", age=" + owner.age);
                if (owner.pets) {
                  var pet;
                  for (pet of owner.pets) {
                    console.log("---pet name=" + pet.name + ", type=" + pet.type);
                    if (this.petType.toUpperCase() === pet.type.toUpperCase()) {
                      if ("Male".toUpperCase() === owner.gender.toUpperCase()) {
                        // male owner
                        if (!this.maleOwners.includes(owner.name)) {
                          this.maleOwners.push(owner.name);
                        }
                      } else if (
                        "Female".toUpperCase() === owner.gender.toUpperCase()
                      ) {
                        // female owner
                        if (!this.femaleOwners.includes(owner.name)) {
                          this.femaleOwners.push(owner.name);
                        }
                      } else {
                        console.log("Unknow gender " + owner.gender + "!!!");
                      }
                    } else {
                      console.log(pet.type + " is not " + this.petType + "!!!");
                    }
                  }
                }
              }
            }
            // Alphabetize the lists in alphabetical order
            this.maleOwners.sort();
            this.femaleOwners.sort();
            console.log("Male owners:" + this.maleOwners);
            console.log("Female owners:" + this.femaleOwners);
          }
        });
    },
    reset() {
      this.allOwners = [];
      this.maleOwners = [];
      this.femaleOwners = [];
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
