<template>
  <div class="q-pa-md q-gutter-sm">
    <q-dialog v-model="data">
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
        <q-separator />

        <q-card-actions align="right">
          <q-btn flat dense label="Close" color="primary" v-close-popup />
          <q-btn
            flat
            :label="`${actionType} User`"
            color="primary"
            type="submit"
            @click="updateData()"
          />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script setup>
import { ref } from "vue";

let isModalOpened = ref(false);

const props = defineProps({
  isOpen: { type: Boolean, default: false },
  data: Object,
});
</script>
