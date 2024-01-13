<template lang="">
  <div style="height: 100vh; padding: 40px">
    <div class="row justify-center" v-if="loading">
      <q-spinner-cube color="orange" size="2.5em" />
    </div>
    <div
      class="row justify-center"
      v-else
    >
      <div class="q-pa-md">
      <q-table
        title="Lawyers"
        :rows="lawyers"
        :columns="Lawyerscolumns"
        row-key="name"
      >
      <template v-slot:body="props">
          <q-tr :props="props" @click="onRowClick(props.row)">
            <q-td key="name" :props="props">
              {{ props.row.name }}
            </q-td>
            <q-td key="email" :props="props">
              {{ props.row.email }}
            </q-td>
            <q-td key="lawyer_type" :props="props">
              {{ props.row.lawyer_type.replace(/_/g, " ") }}
            </q-td>
            <q-td key="court_type" :props="props">
              {{ props.row.court_type.replace(/_/g, " ") }}
            </q-td>
            <q-td key="action" :props="props">
              <div class="row">
                <q-btn icon="visibility" size="sm" padding="4px 4px" color="dark"></q-btn>
              </div>
            </q-td>
          </q-tr>
        </template>
      </q-table>
      </div>
      <div class="q-pa-md">
        <q-table
          title="Users"
          :rows="users"
          :columns="columns"
          row-key="name"
        >
        <template v-slot:body="props">
            <q-tr :props="props" @click="onRowClick(props.row)">
              <q-td key="name" :props="props">
                {{ props.row.name }}
              </q-td>
              <q-td key="email" :props="props">
                {{ props.row.email }}
              </q-td>
              <q-td key="role" :props="props">
                {{ props.row.role }}
              </q-td>
              <q-td key="action" :props="props">
                <div class="row">
                  <q-btn icon="visibility" size="sm" padding="4px 4px" color="dark"></q-btn>
                </div>
              </q-td>
            </q-tr>
          </template>
        </q-table>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { onMounted, ref, watch, type Ref } from "vue";
import { useUserStore } from "@/stores/user";
import type { UserState } from "@/stores/user";
import type { Store } from "pinia";
import { storeToRefs } from "pinia";
import axiosApiClient from "@/axios";
import type { ChatUser } from "@/interfaces";
import userPlaceholder from "@/assets/user.svg";
import { useRouter } from "vue-router";
import type { Conversation } from "@/interfaces";
import notify from "@/composables/notify";

const columns = [
  {
    name: 'name',
    required: true,
    label: 'Name',
    align: 'left',
    field: row => row.name,
    format: val => `${val}`,
    sortable: true
  },
  { name: 'email', align: 'center', label: 'Email', field: 'email', sortable: true },
  // { name: 'action', label: 'Action', field: 'action', sortable: false },
]
const Lawyerscolumns = [
  {
    name: 'name',
    required: true,
    label: 'Name',
    align: 'left',
    field: row => row.name,
    format: val => `${val}`,
    sortable: true
  },
  { name: 'email', align: 'left', label: 'Email', field: 'email', sortable: true },
  { name: 'lawyer_type', align: 'left', label: 'Lawyer Type', field: 'lawyer_type' },
  { name: 'court_type', align: 'left', label: 'Court Type', field: 'court_type' },
  // { name: 'action',align: 'left',  label: 'Action', field: 'action', sortable: false },
]
const users = ref([]);
const lawyers = ref([]);
const loading = ref<boolean>(false);
const showImage = (image: string | null | undefined): string => {
  if (image && image != "") {
    if (image.includes("https:")) return image;
    return `${import.meta.env.VITE_BACKEND_URL}/${image}`;
  } else {
    return userPlaceholder;
  }
};

onMounted(async () => {
  await axiosApiClient
    .post(`/admin/get-users`)
    .then(async (res) => {
      users.value = res.data.data;
    })
    .catch(() => { });
  await axiosApiClient
    .post(`/admin/get-lawyers`)
    .then(async (res) => {
      lawyers.value = res.data.data;
    })
    .catch(() => { });

  loading.value = false;
});
</script>
<style lang="scss"></style>
