<template>
  <div class="application" id="app">
    <div class="header ml-3 mb-1">SQL Studio Plus</div>
    <div class="application-body">
      <Sidebar class="application-nav"> </Sidebar>
      <div class="application-content">
        <codemirror
          class="editor"
          v-model="text"
          :options="options"
        ></codemirror>
        <div class="mb-2 mt-2">
          <button
            type="button"
            @click="runSql"
            class="btn btn-color btn-secondary ml-2 mr-2"
          >
            Run
            <span
              ><svg
                width="1em"
                height="1em"
                viewBox="0 0 16 16"
                class="bi bi-play-fill"
                fill="currentColor"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  d="M11.596 8.697l-6.363 3.692c-.54.313-1.233-.066-1.233-.697V4.308c0-.63.692-1.01 1.233-.696l6.363 3.692a.802.802 0 0 1 0 1.393z"
                /></svg
            ></span>
          </button>
          <button
            @click="clearAll"
            type="button"
            class="btn  btn-color btn-secondary mr-2"
          >
            Clear All
            <span
              ><svg
                width="1em"
                height="1em"
                viewBox="0 0 16 16"
                class="bi bi-x"
                fill="currentColor"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  fill-rule="evenodd"
                  d="M4.646 4.646a.5.5 0 0 1 .708 0L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 0 1 0-.708z"
                /></svg
            ></span>
          </button>
        </div>
        <nav class="navbar navbar-expand-lg navbar-light bg-light">
          <form class="form-inline my-2 my-lg-0">
            <input
              class="form-control mr-sm-2"
              type="search"
              placeholder="Search"
              aria-label="Search"
            />
            <button class="btn btn-outline-success my-2 my-sm-0" type="submit">
              Search
            </button>
          </form>
        </nav>
        <Grid class="output m-2" :table-data="table" :filter-key="searchQuery">
        </Grid>
        <div
          v-if="showAlert === true"
          class="alert alert-danger m-2"
          role="alert"
        >
          SQL code is not correct.
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Watch, Vue } from "vue-property-decorator";
import "codemirror/mode/javascript/javascript.js";
import "codemirror/theme/base16-dark.css";
import Grid from "./components/Grid.vue";
import { codemirror } from "vue-codemirror";
// require styles
import "codemirror/lib/codemirror.css";
import Sidebar from "./components/Sidebar.vue";
import Table from "./model";

@Component({
  components: {
    codemirror,
    Grid,
    Sidebar
  }
})
export default class App extends Vue {
  //Sample Sql queries with respective data"
  data: Record<string, any> = {
    "SELECT * FROM Atlan.Employees": {
      gridColumns: ["name", "empID"],
      gridData: [
        { name: "Varun", empID: 1 },
        { name: "Krishna", empID: 2 },
        { name: "Amit", empID: 3 },
        { name: "Rohan", empID: 4 }
      ]
    },
    "SELECT * FROM Atlan.Product": {
      gridColumns: ["name", "prodID"],
      gridData: [
        { name: "Data extractor", prodID: 1 },
        { name: "Data Simulator", prodID: 2 },
        { name: "Data Flow", prodID: 3 },
        { name: "Data Generator", prodID: 4 }
      ]
    }
  };
  table: Table = { rows: [], columns: [] };

  options = {
    tabSize: 4,
    mode: "text/javascript",
    theme: "base16-dark",
    lineNumbers: true,
    line: true
  };
  text = "Enter your SQL Code here....";
  searchQuery = "";
  showAlert = false;

  @Watch("text", { immediate: true })
  onValueChnaged(newText: string) {
    this.text = newText;
    this.cleanTableData();
  }
  runSql() {
    if (this.data[this.text] !== undefined) {
      this.table.columns = this.data[this.text].gridColumns;
      this.table.rows = this.data[this.text].gridData;
      this.showAlert = false;
    } else if (this.text === "") {
      this.table.columns = [];
      this.table.rows = [];
      this.showAlert = false;
    } else {
      this.showAlert = true;
    }
  }
  cleanTableData() {
    if (this.data[this.text] === undefined || this.text === "") {
      this.table.columns = [];
      this.table.rows = [];
      this.showAlert = false;
    }
  }
  onCmReady(cm: string) {
    console.log("the editor is readied!", cm);
  }
  onCmFocus(cm: string) {
    console.log("the editor is focus!", cm);
  }
  onCmCodeChange(newCode: string) {
    console.log("this is new code", newCode);
    this.text = newCode;
  }

  clearAll() {
    this.text = "";
    this.runSql();
  }
}
</script>

<style>
.application {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
  background: #1d1e22;
}
.header {
  color: white;
  font-weight: bold;
  min-height: 2vh;
}

.application-body {
  display: flex;
  flex: 1;
}

.application-content {
  flex: 1;
  flex-direction: column;
}

.editor {
  flex: 0 0 50%;
}

.editor-btn {
  flex: 0 0 10%;
}

.output {
  flex: 0 0 30%;
}

.form {
  flex: 0 0 10%;
  background-color: white;
}

.application-nav {
  /* 12em is the width of the columns */
  flex: 0 0 20em;
  background: #5c4084;
}

.search-text {
  font-weight: bold;
}

.btn-color {
  background: #444857;
}
</style>
