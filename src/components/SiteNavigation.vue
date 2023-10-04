<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav
      class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">The Local Weather</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <i
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="toggleModal"
        ></i>
        <i
          class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="addCity"
          v-if="route.query.preview"
        ></i>
      </div>

      <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
        <div class="text-black">
          <h1 class="text-2xl mb-4">เกี่ยวกับ</h1>
          <p class="mb-4">
            The Local Weather คุณสามารถใช้เพื่อตรวจสอบ ติดตาม และทำนาย
            สภาพอากาศของเมืองที่คุณได้เลือกไว้
          </p>
          <h2 class="text-2xl">วิธีการใช้งาน :</h2>
          <ol class="list-decimal list-inside mb-4">
            <li>
              ค้นหาเมื่องหรือสถานที่ที่คุณอยู่
              โดยการใส่ชื่อเมื่องของคุณที่ช่องค้นหา
            </li>
            <li>
              เลือกเมืองของคุณที่พบจากผลการค้นหา
              โดยจะแสดงสภาพอากาศปัจจุบันของเมืองที่คุณเลือก
            </li>
            <li>
              สามารถเลือกติดตามเมืองที่คุณต้องการโดยกดเครื่องหมาย '+'
              ได้ที่ด้านบนขวาของเว็บไซต์
              โดยจะแสดงผลของเมืองที่คุณเลือกไว้ที่เว็บไซต์
              เมื่อมีการเข้าใช้งานอีกครั้ง
            </li>
          </ol>

          <h2 class="text-2xl">การลบเมืองที่คุณเลือก</h2>
          <p>
            ถ้าหากไม่ต้องการที่จะติดตามสภาพอากาศในเมืองของคุณ
            สามารถทำการยกเลิกการติดตามได้อย่างง่ายโดยเลือกเมื่องของคุณที่อยู่ในหน้าแรกของเว็บไซต์
            จากนั้นสามารถเลือกตัวเลือกในการลบได้
          </p>
        </div>
      </BaseModal>
    </nav>
  </header>
</template>

<script setup>
import { ref } from "vue";
import { uid } from "uid";
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModal from "./BaseModal.vue";

const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }

  const locationOj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };

  savedCities.value.push(locationOj);
  localStorage.setItem("savedCities", JSON.stringify(savedCities.value));

  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationOj.id;
  router.replace({ query });
};

const modalActive = ref(null);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
};
</script>
