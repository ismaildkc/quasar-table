<template>
  <q-page class="flex flex-center row">
    <!-- Grid Table -->
    <q-table
      class="q-table q-table-max-height col-10 col-lg-8"
      title="Users"
      :rows="rows"
      :columns="columns"
      row-key="name"
      :filter="filter"
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

        <q-btn class="q-ml-sm" color="primary" icon="add" @click="openModal" />
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
  </q-page>
</template>

<script setup>
import { ref } from "vue";

let isModalOpened = ref(false);
let actionType = ref("");
const filter = ref("");
const props = defineProps({
  rows: Object,
  columns: Object,
  configuration: Array,
  filter: Object,
});

// const openModal = (type) => {
//   isModalOpened.value = true;
//   actionType.value = type;
// };

const emit = defineEmits(["update", "getUserDetail", "openModal"]);

const updateData = (type, data) => {
  emit("update", [type, data]);
};

const userDetail = () => {
  emit("getUserDetail", "");
};

const openModal = () => {
  emit("openModal", "");
};
</script>