<template lang="">
  <div style="min-height: 100vh; padding: 40px">
    <div class="row justify-center" v-if="loading">
      <q-spinner-cube color="orange" size="2.5em" />
    </div>
    <div class="row q-col-gutter-x-md" v-else>
      <div class="col col-md-5">
        <q-card>
          <q-card-section>
            <p class="text-h4">{{ userCase.title }}</p>
            <p class="q-mb-none">
              Created on:
              {{ moment(userCase.createdAt).format(" MMM DD, YYYY") }}
            </p>
            <p class="q-mb-none">
              Status:
              <strong>{{
                (userCase.status == 0 && "pending") ||
                (userCase.status == 1 && "In progress") ||
                (userCase.status == 2 && "Rejected") ||
                (userCase.status == 3 && "Closed")
              }}</strong>
            </p>
            <p class="q-mb-none">Description: {{ userCase.description }}</p>
          </q-card-section>
        </q-card>

        <q-card
          class="q-mt-md"
          v-if="userCase.status == 3"
        >
          <q-card-section class="column">
            <p class="text-h6 text-center">Reopen this case.</p>
            <q-btn
              label="Reopen"
              no-caps
              color="dark"
              @click="changeCaseStatus(1)"
            ></q-btn>
          </q-card-section>
        </q-card>

        <q-card class="q-mt-md" v-if="userCase.status == 1">
          <q-card-section class="column">
            <p class="text-h6 text-center">Close this case.</p>
            <q-btn
              label="Close"
              no-caps
              color="green"
              @click="changeCaseStatus(3)"
            ></q-btn>
          </q-card-section>
        </q-card>
        <q-card
          class="q-mt-md"
          v-if="
            userCase.reviews.length > 0
          "
        >
          <q-card-section class="column">
            <p class="text-h6 text-center">User Review</p>
            <p class="row items-center"><span>Rating:</span> 
              <q-rating
                v-model="userCase.reviews[0].rating"
                size="2em"
                color="orange"
                readonly
              />
            </p>
            <p>Feedback: {{ userCase.reviews[0].feedback }}</p>
          </q-card-section>
        </q-card>
        <q-card
          class="q-mt-md"
          v-if="
            (userCase.status == 1 || userCase.status == 3)
          "
        >
          <q-card-section class="column">
            <div class="row justify-between items-center">
              <p class="text-h6">Case Documents</p>
            </div>
            <div class="">
            <div
              class=""
              v-for="(document, index) in userCase.documents"
              :key="index"
            >
              <p class="q-mb-none">
                Uploaded at:
                {{ moment(document.createdAt).format("MMM DD, YYYY - hh:mm a") }}
              </p>
              <p>Download:<a target="_blank" :href="baseBackendUrl+'/'+document.document">{{ document.document.split("public/user/case/documents/")[1].slice(0,4)+"..."+document.document.split("public/user/case/documents/")[1].slice(document.document.split("public/user/case/documents/")[1].length-8,document.document.split("public/user/case/documents/")[1].length) }}</a>
                <!-- <q-btn size="sm" icon="download" color="red" @click="downloadFile(baseBackendUrl+'/'+document.document, document.document.split('public/user/case/documents/')[1])"></q-btn> -->
              </p>
            </div>
            </div>
          </q-card-section>
        </q-card>
      </div>
      <div class="col col-md-6">
        <div class="row q-gutter-x-md">
          <div>
            <q-card style="height: 100%">
              <q-card-section>
                <p class="text-h6">User Details</p>

                <q-avatar size="80px">
                  <img :src="showImage(userCase.user.profile_image)" />
                </q-avatar>
                <p class="q-mb-none text-capitalize">
                  <strong>Name:</strong> {{ userCase.lawyer.name }}
                </p>
                <p class=""><strong>Email:</strong> {{ userCase.lawyer.email }}</p>
              </q-card-section>
            </q-card>
          </div>
          <div class="col">
            <q-card style="height: 100%">
              <q-card-section>
                <div class="row q-col-gutter-x-xl">
                  <div class="">
                    <p class="text-h6">Lawyer Details</p>

                    <q-avatar size="80px">
                      <img :src="showImage(userCase.lawyer.profile_image)" />
                    </q-avatar>
                  </div>

                  <div class="col">
                    <p class="q-mb-none text-capitalize">
                      <strong>Name:</strong> {{ userCase.lawyer.name }}
                    </p>
                    <p class="q-mb-none">
                      <strong>Email:</strong> {{ userCase.lawyer.email }}
                    </p>
                    <p class="q-mb-none text-capitalize">
                      <strong>Type:</strong> {{ userCase.lawyer.lawyer_type }}
                    </p>
                    <p class="text-capitalize">
                      <strong>Court:</strong> {{ userCase.lawyer.court_type }}
                    </p>
                  </div>
                </div>
              </q-card-section>
            </q-card>
          </div>
        </div>
        <q-card class="q-mt-md">
          <q-card-section>
            <div class="row justify-between items-center">
              <p class="text-h6">Updates</p>
            </div>

            <div
              class=""
              v-for="(hearing, index) in userCase.hearings"
              :key="index"
            >
              <p class="q-mb-none">
                Update at:
                {{ moment(hearing.createdAt).format("MMM DD, YYYY - hh:mm a") }}
              </p>
              <p>Notes: {{ hearing.note }}</p>
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import axiosApiClient from "@/axios";
import userPlaceholder from "@/assets/user.svg";
import { ref, onMounted } from "vue";
import { useRoute } from "vue-router";
import moment from "moment";
import { useUserStore } from "@/stores/user";

const baseBackendUrl = import.meta.env.VITE_BACKEND_URL;
const userStore = useUserStore();
const route = useRoute();
const userCase = ref({});
const loading = ref(true);
const showImage = (image: any) => {
  if (image && image != "") {
    if (image.includes("https:")) return image;
    return `${import.meta.env.VITE_BACKEND_URL}/${image}`;
  } else {
    return userPlaceholder;
  }
};

const changeCaseStatus = async (status: any) => {
  await axiosApiClient
    .post("admin/case/change-status", { status, case_id: route.params.id })
    .then((res) => {
      userCase.value.status = status;
    });
};
onMounted(async () => {
  await axiosApiClient
    .post("admin/case/get", { case_id: route.params.id })
    .then((res) => {
      userCase.value = res.data.data;
    });
  loading.value = false;
});
</script>
<style lang=""></style>
