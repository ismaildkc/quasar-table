<template>
  <q-page class="flex flex-center row">
    <!-- Grid Table -->
    <q-table
      class="q-table q-table-max-height col-10 col-lg-8"
      title="Users"
      :rows="users"
      :columns="columns"
      row-key="name"
      :filter="filter"
      v-model:pagination="qTableConf.pagination"
    >
      <template v-slot:top-right>
        <q-input
          outlined
          dense
          debounce="300"
          v-model="filter"
          placeholder="Search"
        >
          <template v-slot:append>
            <q-icon name="search" />
          </template>
        </q-input>

        <q-btn class="q-ml-sm" color="primary" icon="add" @click="addUser()" />
      </template>

      <template v-slot:body-cell-avatar="props">
        <q-td key="avatar" :props="props">
          <q-avatar>
            <q-img :src="props.row.avatar" />
          </q-avatar>
        </q-td>
      </template>

      <template v-slot:body-cell-action="props">
        <q-td :props="props">
          <q-btn
            color="negative"
            round
            icon="delete"
            @click="deleteUser(props.row.id)"
          />
        </q-td>
      </template>

      <template v-slot:body-cell-view="props">
        <q-td :props="props">
          <q-btn
            color="secondary"
            round
            icon="edit"
            @click="updateUser(props.row.id)"
          />
        </q-td>
      </template>
    </q-table>

    <div class="q-pa-md q-gutter-sm">
      <q-dialog v-model="isModalOpened">
        <q-card>
          <!-- Modal Header -->
          <q-card-section>
            <div
              class="text-h6 text-uppercase"
              v-html="`${actionType} User`"
            ></div>
          </q-card-section>

          <q-separator />

          <!-- Modal Content -->
          <template v-if="actionType != 'delete'">
            <q-card-section style="max-height: 50vh" class="scroll">
              <div class="row flex">
                <q-input
                  class="col-12 q-mb-sm"
                  autofocus
                  outlined
                  v-model="userDetail.name"
                  :rules="[
                    (val) => (val && val.length > 0) || 'Please type something',
                  ]"
                  label="Name"
                />
                <q-input
                  class="col-12 q-mb-sm"
                  outlined
                  v-model="userDetail.country"
                  :rules="[
                    (val) => (val && val.length > 0) || 'Please type something',
                  ]"
                  label="Country"
                />
              </div>

              <!-- Modal Footer -->
              <div class="row flex justify-between q-mt-xs">
                <q-input
                  class="col-12 q-mb-sm"
                  outlined
                  type="email"
                  v-model="userDetail.email"
                  :rules="[(val) => !!val || 'Email is missing', isEmailValid]"
                  label="E-mail"
                />
              </div>
            </q-card-section>
          </template>
          <template v-else>
            <q-card-section class="q-pt-sm" v-model="userDetail.name">
              <p class="text-body1">
                <span class="text-weight-bold">{{ userDetail.name }}</span> user
                will be deleted
              </p>
            </q-card-section>
          </template>
          <q-separator />

          <q-card-actions align="right">
            <q-btn flat dense label="Close" color="primary" v-close-popup />
            <q-btn
              flat
              :label="`${actionType} User`"
              color="primary"
              type="submit"
              @click="mutateData()"
            />
          </q-card-actions>
        </q-card>
      </q-dialog>
    </div>
  </q-page>
</template>

<script setup>
import { defineComponent, ref, onMounted } from "vue";
import { useQuasar, Notify } from "quasar";
import api from "src/services/axios";

const $q = useQuasar();
const qTableConf = ref({
  pagination: {
    sortBy: "id",
    descending: true,
    page: 1,
    rowsPerPage: 10,
  },
});
const filter = ref("");
let isModalOpened = ref(false);
let actionType = ref("");
let promptValue = ref("");

let userDetail = ref([]);

const users = ref([]);
const columns = ref([
  {
    name: "id",
    align: "center",
    label: "Id",
    field: "id",
    sortable: true,
    sort: (a, b) => parseInt(a, 10) - parseInt(b, 10),
  },
  {
    name: "avatar",
    align: "center",
    label: "Avatar",
    field: "avatar",
  },
  {
    name: "name",
    align: "left",
    label: "Name",
    field: "name",
  },
  {
    name: "country",
    align: "left",
    label: "Country",
    field: "country",
  },
  {
    name: "email",
    align: "left",
    label: "E-mail",
    field: "email",
  },
  { name: "action", label: "Delete", field: "action" },
  { name: "view", label: "Update", field: "view" },
  // {
  //   name: "name",
  //   required: true,
  //   label: "Dessert (100g serving)",
  //   align: "left",
  //   field: (row) => row.name,
  //   format: (val) => `${val}`,
  //   sortable: true,

  //   sort: (a, b) => parseInt(a, 10) - parseInt(b, 10),
  // },
]);

const fetchUsers = async () => {
  await api.get("/users").then((response) => {
    users.value = response.data;
  });
};

const getUserDetail = async (id) => {
  return await api
    .get("/users/" + id)
    .then((response) => {
      return response.data;
    })
    .catch((error) => {
      console.log(error);
    });
};

const addUser = () => {
  isModalOpened.value = true;
  actionType.value = "add";

  $q.notify({
    spinner: true,
    message: "Please wait...",
    timeout: 2000,
  });
};

const deleteUser = async (value) => {
  // isModalOpened.value = true;
  // actionType.value = "delete";
  userDetail.value = await getUserDetail(value);

  console.log(userDetail.value.name);

  $q.dialog({
    title: "Delete User",
    message: `Would you like to delete <span> ${userDetail.value.name} <span> user?`,
    cancel: true,
    persistent: true,
  }).onOk(() => {
    api
      .delete("/users/" + value)
      .then((response) => {
        clearData(response);
      })
      .catch((error) => {
        console.log(error);
      });
  });
};

const updateUser = async (value) => {
  isModalOpened.value = true;
  actionType.value = "update";
  userDetail.value = await getUserDetail(value);
};

const mutateData = (value) => {
  const thisUser = { ...userDetail.value };
  console.log(thisUser);

  if (actionType.value == "add") {
    api.post("/users", thisUser).then((response) => {
      clearData(response);
    });
  } else if (actionType.value == "update") {
    api.put("/users/" + thisUser.id, thisUser).then((response) => {
      clearData(response);
    });
  } else if (actionType.value == "delete") {
    api
      .delete("/users/" + thisUser.id)
      .then((response) => {
        clearData(response);
      })
      .catch((error) => {
        console.log(error);
      });
  }
};

const clearData = (data) => {
  console.log(data);
  userDetail.value = "";
  isModalOpened.value = false;
  fetchUsers();
};

// Email validation
const isEmailValid = (value) => {
  const emailPattern =
    /^(?=[a-zA-Z0-9@._%+-]{6,254}$)[a-zA-Z0-9._%+-]{1,64}@(?:[a-zA-Z0-9-]{1,63}\.){1,8}[a-zA-Z]{2,63}$/;
  return emailPattern.test(value) || "Invalid email";
};

onMounted(fetchUsers);
</script>
