<template>
  <div class="q-pa-md">
    <q-checkbox
      v-model="checkedTmp"
      @update:model-value="
        (value, evt) => updateModelAfterTransitionEnd(value, evt, 'checked')
      "
      >Checked</q-checkbox
    >
    <q-table
      :title="title"
      :rows="rows"
      :columns="columns"
      row-key="id"
      :filter="filter"
      :filter-method="filterMethod"
      :rows-per-page-options="rowsPerPageOptions"
      selection="multiple"
      v-model:selected="selected"
    >
      <template v-slot:top-right>
        <q-input
          borderless
          dense
          debounce="300"
          v-model="search"
          placeholder="Search"
        >
          <template v-slot:append>
            <q-icon name="search" />
          </template>
        </q-input>
      </template>
    </q-table>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
// import EssentialLink from "components/EssentialLink.vue";
import { faker } from "@faker-js/faker";
// import { getCurrentInstance } from "vue";

const columns = [
  {
    name: "id",
    required: true,
    field: "id",
    label: "id",
  },
  {
    name: "desc",
    required: true,
    label: "Dessert (100g serving)",
    align: "left",
    field: (row) => row.name,
    format: (val) => `${val}`,
    sortable: true,
  },
  { name: "checked", label: "Checked", field: "checked" },
  {
    name: "calories",
    align: "center",
    label: "Calories",
    field: "calories",
    sortable: true,
  },
  { name: "fat", label: "Fat (g)", field: "fat", sortable: true },
  { name: "carbs", label: "Carbs (g)", field: "carbs" },
  { name: "random1", label: "random1", field: "random1" },
  { name: "random2", label: "random2", field: "random2" },
  { name: "random3", label: "random3", field: "random3" },
  { name: "random4", label: "random4", field: "random4" },
];

const rows = [
  {
    id: 0,
    name: "Frozen Yogurt",
    checked: false,
    calories: 159,
    fat: 6.0,
    carbs: 24,
    random1: 1,
    random2: 2,
    random3: 3,
    random4: 4,
  },
];

export default defineComponent({
  name: "MainLayout",

  components: {},
  data() {
    return {
      checked: false,
      checkedTmp: false,
      rowsPerPageOptions: [0],
      search: "",
      columns: columns,
      rows: rows,
      filteredRowsCount: 0,
      selected: [],
      n: 0,
      refChebox: "",
    };
  },
  setup() {
    for (var n = 1; n < 5000; ++n) {
      rows.push({
        id: n,
        name: faker.name.fullName(),
        calories: Math.floor(Math.random() * 10),
        checked: Math.random() > 0.5,
        fat: Math.floor(Math.random() * 10),
        carbs: Math.floor(Math.random() * 10),
        random1: faker.name.fullName(),
        random2: faker.name.fullName(),
        random3: faker.name.fullName(),
        random4: faker.name.fullName(),
      });
    }

    return {};
  },
  created() {
    this.$bus.on("some-event", this.changeChecked);
  },
  computed: {
    title() {
      return `Treats ${this.filteredRowsCount}`;
    },
    filter() {
      return {
        checked: this.checked,
        search: this.search,
      };
    },
  },
  methods: {
    filterMethod(rows, terms, cols, getCellValue) {
      let filteredRows = rows;
      if (terms.checked) {
        filteredRows = rows.filter((row) => row.checked == terms.checked);
      }
      if (terms.search) {
        filteredRows = rows.filter((row) => row.name.includes(terms.search));
      }
      this.filteredRowsCount = filteredRows.length;
      return filteredRows;
    },
    updateModelAfterTransitionEnd(value, evt, name) {
      console.log("updateModelAfterTransitionEnd", value, evt, name);
      const self = this;
      const fn = (event) => {
        console.log("Transition ended");
        event.currentTarget.removeEventListener("transitionend", fn);
        self[name] = value;
        console.log("value", value);
      };
      evt.currentTarget.addEventListener("transitionend", fn, { once: true });
    },
  },
});
</script>
