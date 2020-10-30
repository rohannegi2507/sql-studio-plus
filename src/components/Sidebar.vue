<template>
  <div class="flex" data-spy="scroll">
    <button
      type="button"
      class="btn btn-primary connect"
      data-toggle="modal"
      data-target="#exampleModal"
      v-show="isUserSignIn === false"
    >
      Connect
    </button>
    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">SQL STUDIO PLUS</h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <form>
              <div class="form-group row">
                <label for="userId" class="col-sm-2 col-form-label"
                  >User Id</label
                >
                <div class="col-sm-10">
                  <input
                    type="text"
                    v-model="userId"
                    class="form-control"
                    id="userId"
                  />
                </div>
              </div>
              <div class="form-group row">
                <label for="serverName" class="col-sm-2 col-form-label"
                  >Server Name</label
                >
                <div class="col-sm-10">
                  <input
                    type="text"
                    v-model="serverName"
                    class="form-control"
                    id="serverName"
                  />
                </div>
              </div>
            </form>
          </div>
          <span class="connect-error" v-show="showConnectError"
            >Please enter correct credentials</span
          >
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-dismiss="modal"
            >
              Close
            </button>
            <button
              type="button"
              :data-dismiss="dismissModal"
              class="btn btn-primary"
              @click="verifyDatabaseUser"
            >
              Connect
            </button>
          </div>
        </div>
      </div>
    </div>

    <ul class="list-group database" v-show="isUserSignIn === true">
      <li
        class="list-group-item"
        v-for="item in databasesList"
        :key="item.database"
      >
        <button
          class="btn btn-secondary dropdown-toggle"
          @click="expandList(item.database)"
          href="#"
          role="button"
          id="dropdownMenuLink"
          data-toggle="dropdown"
          aria-haspopup="true"
          aria-expanded="false"
        >
          {{ item.database }}
        </button>

        <ul
          class="list-group"
          aria-labelledby="dropdownMenuLink"
          v-if="showMap[item.database] === true"
        >
          <li
            class="list-group-item"
            v-for="item in tableList"
            :key="item.table"
          >
            {{ item.table }}
          </li>
        </ul>
      </li>
    </ul>

    <button
      v-show="isUserSignIn === true"
      type="button"
      class="btn btn-primary logout-btn"
      @click="logout"
    >
      Logout
    </button>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component
export default class Sidebar extends Vue {
  databasesList: any = [
    { database: "Atlan" },
    { database: "Factset" },
    { database: "Hexagon" }
  ];
  tableList: any = [{ table: "Product" }, { table: "Employees" }];
  userId = "";
  serverName = "";
  isUserSignIn = false;
  showConnectError = false;
  dismissModal = "modal"; // modal means Dissmiss the modal if connection is successful

  showMap: Record<string, boolean> = {
    Atlan: false,
    Factset: false,
    Hexagon: false
  };

  expandFlag = true;

  expandList(id: string): boolean {
    if (this.expandFlag == false) {
      this.expandButton(id);
      this.expandFlag = true;
    } else {
      this.collapseButton(id);
      this.expandFlag = false;
    }
    return this.expandFlag;
  }

  expandButton(id: string) {
    this.showMap[id] = true;
  }

  collapseButton(id: string) {
    this.showMap[id] = false;
  }

  verifyDatabaseUser() {
    if (this.userId == "rnegi" && this.serverName === "rnegi") {
      this.isUserSignIn = true;
    } else {
      this.showConnectError = true;
      this.dismissModal = "";
    }
  }
  logout() {
    this.isUserSignIn = false;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.flex {
  display: flex;
  flex-direction: column;
}
.connect {
  flex: 0 1 2%;
}
.logout-btn {
  flex: 0 1 2%;
}
.database {
  flex: 1 1 96%;
  background: #5c4084;
}
.connect-error {
  text-align: center;
  color: red;
  font-size: 14px;
}
</style>
