<template>
  <q-page class="flex flex-center">
    <GridTable :columns="columns" :rows="users" />
    <ModalDialog :isConfirmOpened="isConfirmOpened" />

    <button @click="isConfirmOpened = true">Open</button>
  </q-page>
</template>

<script setup>
import { ref, onMounted } from "vue";
import api from "src/services/axios";
import GridTable from "src/components/GridTable.vue";
import ModalDialog from "src/components/ModalDialog.vue";

const isConfirmOpened = ref(false);

const users = ref([]);
const columns = ref([
  {
    name: "id",
    align: "center",
    label: "Id",
    field: "id",
    sortable: true,
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

onMounted(fetchUsers);
</script>