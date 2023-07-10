<template lang="">
  <div style="min-height: 100vh; padding: 40px">
    <div class="row justify-center" v-if="loading">
      <q-spinner-cube color="orange" size="2.5em" />
    </div>
    <div
      class="row justify-center"
      v-else
    >
      <div class="q-pa-md">
        <q-table
          title="Cases"
          :rows="cases"
          :columns="columns"
          row-key="name"
        >
        <template v-slot:body="props">
            <q-tr :props="props" @click="onRowClick(props.row)">
              <q-td key="title" :props="props">
                {{ props.row.title.slice(0, 20)+(props.row.title.length > 20 ? "..." : "") }}
              </q-td>
              <q-td key="user" :props="props">
                {{ props.row.user[0].name }}
                <p class="text-grey-7 q-mb-none">{{ props.row.user[0].email }}</p>
              </q-td>
              <q-td key="lawyer" :props="props">
                {{ props.row.lawyer[0].name }}
                <p class="text-grey-7 q-mb-none">{{ props.row.lawyer[0].email }}</p>
              </q-td>
              <q-td key="lawyer_type" :props="props">
                {{ props.row.lawyer[0].lawyer_type.replace(/_/g, " ") }}
              </q-td>
              <q-td key="court_type" :props="props">
                {{ props.row.lawyer[0].court_type.replace(/_/g, " ") }}
              </q-td>
              <q-td key="description" :props="props">
                {{ props.row.description.slice(0, 50)+(props.row.description.length > 50 ? "..." : "") }}
              </q-td>
              <q-td key="action" :props="props">
                <div class="row">
                  <q-btn icon="visibility" size="sm" padding="4px 4px" color="dark" :to="{name: 'case-detail',params: { id: props.row._id },}"></q-btn>
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
  { name: 'title', align: 'left', label: 'Title', field: 'title', sortable: true },
  { name: 'description', align: 'left', label: 'Description', field: 'description', sortable: true },
  {
    name: 'user',
    required: true,
    label: 'User',
    align: 'left',
    field: row => row.name,
    format: val => `${val}`,
    sortable: true
  },
  { name: 'lawyer', align: 'left', label: 'Lawyer', field: 'lawyer', sortable: true },
  { name: 'lawyer_type', align: 'left', label: 'Lawyer Type', field: 'lawyer_type' },
  { name: 'court_type', align: 'left', label: 'Court Type', field: 'court_type' },
  { name: 'action', align: 'left', label: 'Action', field: 'action', sortable: false },
]
const cases = ref([]);
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
    .post(`/admin/get-cases`)
    .then(async (res) => {
      cases.value = res.data.data;
    })
    .catch(() => { });

  loading.value = false;
});
</script>
<style lang="scss"></style>
