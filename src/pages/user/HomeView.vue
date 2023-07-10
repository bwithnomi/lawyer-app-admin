<template lang="">
  <div style="height: 100vh; padding: 40px">
    <div class="row q-col-gutter-md">
      <div class="col col-md-3">
        <q-card class="bg-grey-5">
          <q-card-section>
            <div class="row">
              <p class="text-h2 q-mb-none">{{ counts.users }}</p>
              <p>Total Users</p>
            </div>
          </q-card-section>
        </q-card>
      </div>
      <div class="col col-md-3">
        <q-card class="bg-yellow-5">
          <q-card-section>
            <div class="row">
              <p class="text-h2 q-mb-none">{{ counts.lawyers }}</p>
              <p>Total Lawyers</p>
            </div>
          </q-card-section>
        </q-card>
      </div>
      <div class="col col-md-3">
        <q-card class="bg-green-5">
          <q-card-section>
            <div class="row">
              <p class="text-h2 q-mb-none">{{ counts.cases }}</p>
              <p>Total Cases</p>
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import axiosApiClient from "@/axios";
import { ref, onMounted, computed } from "vue";

const counts = ref({
  users: 0,
  lawyers: 0,
  cases: 0,
})

onMounted(() => {
  axiosApiClient.get("/admin/dashboard-counts").then((res) => {
    counts.value = res.data.data;
  });
});
</script>
<style lang="">
  
</style>