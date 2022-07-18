<template>
  <div>
    <div class="container">
      <v-dropdown-select v-model="selectedValue"
        class="container__select"
        url="https://lobster.tools/api/v1/categories"
        @selectValue="selectValue"
      />
      <v-dropdown-select url="https://lobster.tools/api/v1/projects" />
    </div>
    <button @click="showData">Show All Data</button>
  </div>
</template>

<script>
import { reactive } from '@vue/reactivity';
import vDropdownSelect from "./base-components/v-dropdown-select.vue";
export default {
  components: { vDropdownSelect },
  name: "HelloWorld",
  setup() {
    const state = reactive({
      selectedValue: [],
    })

    const selectValue = (value) => {
      state.selectedValue = value;
    }

    const showData = () => {
      console.log(state.selectedValue);
    }

    return{
      state,
      selectValue,
      showData
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container {
  display: flex;
  flex-direction: row;
  justify-content: start;
}

.container__select {
  margin-right: 80px;
}
h3 {
  margin: 40px 0 0;
}
li {
  display: inline-block;
}
a {
  color: #42b983;
}
</style>
