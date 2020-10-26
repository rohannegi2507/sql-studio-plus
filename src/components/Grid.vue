<template>
  <div>
    <table>
      <thead>
        <tr>
          <th
            v-for="key in tableData.columns"
            @click="sortBy(key)"
            :key="key"
            :class="{ active: sortKey == key }"
          >
            {{ key | capitalize }}
            <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
            </span>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="entry in filteredData" :key="entry">
          <td v-for="key in tableData.columns" :key="key">
            {{ entry[key] }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { Component, Watch, Prop, Vue } from "vue-property-decorator";
import Table from "../model";

@Component({
  // other options
  filters: {
    capitalize: function(value) {
      if (!value) return "";
      value = value.toString();
      return value.charAt(0).toUpperCase() + value.slice(1);
    }
  }
})
export default class Grid extends Vue {
  @Prop({ required: true })
  private tableData!: Table;

  @Prop()
  private filterKey!: string;

  sortKey = "";
  mutableTableData = this.tableData;

  sortOrders(key: string) {
    this.sortOrders[key] = 1;
    return this.sortOrders;
  }

  get filteredData() {
    const filterKey = this.filterKey && this.filterKey.toLowerCase();
    const order = this.sortOrders[this.sortKey] || 1;
    let heroes = this.tableData.rows;
    if (filterKey) {
      heroes = heroes.filter(function(row) {
        return Object.keys(row).some(function(key) {
          return (
            String(row[key])
              .toLowerCase()
              .indexOf(filterKey) > -1
          );
        });
      });
    }
    if (this.sortKey) {
      this.tableData.rows = this.tableData.rows
        .slice()
        .sort((a: string, b: string) => {
          a = a[this.sortKey];
          b = b[this.sortKey];
          return (a === b ? 0 : a > b ? 1 : -1) * order;
        });
    }
    return heroes;
  }

  sortBy(key: string) {
    this.sortKey = key;
    this.sortOrders[key] = this.sortOrders[key] * -1;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
table {
  border: 2px solid #42b983;
  border-radius: 3px;
  background-color: #fff;
}

th {
  background-color: #42b983;
  color: rgba(255, 255, 255, 0.66);
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

td {
  background-color: #f9f9f9;
}

th,
td {
  min-width: 120px;
  padding: 10px 20px;
}

th.active {
  color: #fff;
}

th.active .arrow {
  opacity: 1;
}

.arrow {
  display: inline-block;
  vertical-align: middle;
  width: 0;
  height: 0;
  margin-left: 5px;
  opacity: 0.66;
}

.arrow.asc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-bottom: 4px solid #fff;
}

.arrow.dsc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #fff;
}
</style>
