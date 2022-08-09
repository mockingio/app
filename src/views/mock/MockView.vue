<template>
  <Header :mocks="mocks" :activeMock="activeMock" v-if="activeMock" />
  <div class="flex min-h-screen">
    <RouterView />
  </div>
</template>

<script setup lang="ts">
import { RouterView, useRoute } from "vue-router";
import { useMockStore } from "@/stores";
import { watch } from "vue";
import { storeToRefs } from "pinia";
import Header from "@/components/mock/header/Header.vue";
import router from "@/router/index";

const route = useRoute();
const { fetchMocks, setActiveMock, setDefaultActiveMock } = useMockStore();
const { mocks, error, activeMock } = storeToRefs(useMockStore());

watch(
  () => [route.params.id, route.name, activeMock.value?.data.id],
  ([newId, name, activeId]) => {
    if (newId && newId !== activeId) {
      setActiveMock(newId as string);
      return;
    }

    if (!activeId) {
      setDefaultActiveMock();
      return;
    }

    if (!name) {
      router.push({
        name: "routes-view",
        params: {
          id: activeId as string,
        },
      });
    }
  }
);

fetchMocks().then(() => {
  if (route.params.id) {
    setActiveMock(route.params.id as string);
  } else {
    setDefaultActiveMock();
  }
});
</script>
