<template>
  <div :class="root">
    <div :class="root + '__button-container'">
      <input
        v-model="state.selectedValue"
        :class="root + '__button-container--dropdown-input'"
        @click="state.isShow = !state.isShow"
      />
      <button :class="root + '__button-container--delete-button'" @click="clearAll">X</button>
    </div>
    <div v-if="state.isShow" :class="root + '__sub-menu'">
      <a v-text="'select all'" key="" @click="checkAll" />
      <input
        v-model="state.search"
        placeholder="search"
        @change="getData(state.search)"
      />
    </div>
    <div v-if="state.isShow" :class="root + '__list'">
      <ul>
        <li v-for="(item, index) in state.items" :key="index">
          <input
            v-model="item.isChecked"
            type="checkbox"
            :id="'checkbox-' + index"
            @click="selectFilter(item)"
          />
          <label :for="'checkbox-' + index">{{ item.title }}</label>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { onMounted, reactive } from "vue";
import axios from "axios";

export default {
  name: "v-dropdown-select",
  props: {
    url: { type: String, required: true },
  },
  emits: ['selectValue'],
  setup(props, context) {
    const root = "v-dropdown-select";
    const token = "BT3HK2NpCnyrKiDo";
    const state = reactive({
      selectedValue: [],
      isShow: false,
      items: [],
      search: "",
    });

    onMounted(() => {
      getData();
    });

    const clearAll = () => {
      state.items.forEach(x => x.isChecked = false);
      state.selectedValue = [];
      state.search = "";
      state.isShow = false;
      context.emit('selectValue', state.selectedValue);
      getData();
    }

    const selectFilter = (item) => {
      if (state.selectedValue.includes(item.title)) {
        state.items.find((x) => x.title === item.title).isChecked = false;
        state.selectedValue = state.selectedValue.filter(
          (x) => x !== item.title
        );
        context.emit("selectValue", state.selectedValue);
      } else {
        state.items.find((x) => x.title === item.title).isChecked = true;
        state.selectedValue.push(item.title);
        context.emit("selectValue", state.selectedValue);
      }
    };

    const checkAll = () => {
      state.items.forEach((item) => {
        if (!state.selectedValue.includes(item.title)) {
          state.items.forEach((x) => (x.isChecked = true));
          state.selectedValue.push(item.title);
          context.emit("selectValue", state.selectedValue);
        }
      });
    };

    const getData = async (searchValue = "") => {
      await axios
        .post(
          props.url,
          { search: `${searchValue}`, limit: 0, offset: 0 },
          {
            headers: {
              Authorization: `Bearer ${token}`,
              "Content-Type": "multipart/form-data",
            },
          }
        )
        .then((response) => {
          state.items = response.data.message.data;
          state.items.forEach((x) => (x.isChecked = false));
        });
    };
    return {
      state,
      root,
      selectFilter,
      getData,
      checkAll,
      clearAll
    };
  },
};
</script>

<style scoped>
.v-dropdown-select {
  display: flex;
  flex-direction: column;
  width: 303px;
}

.v-dropdown-select__sub-menu {
  margin: 0;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

.v-dropdown-select__list {
  border: 1px solid black;
  border-radius: 5px;
  width: 303px;
  height: 200px;
  overflow: auto;
}

.v-dropdown-select__button-container {
  width: 300px;
  position: relative;
}

ul {
  padding-left: 5px;
}

li {
  margin: 0;
  list-style: none;
}

.v-dropdown-select__button-container--dropdown-input {
  margin: 0px;
  padding: 0px;
  width: 100%;
  outline: none;
  height: 30px;
  border-radius: 5px;
}

.v-dropdown-select__button-container--delete-button {
  position: absolute;
  top: 0;
  border-radius: 5px;
  right: 0px;
  z-index: 2;
  border: none;
  top: 2px;
  height: 30px;
  cursor: pointer;
  color: #000;
  background-color: #fff;
  transform: translateX(2px);
}
</style>