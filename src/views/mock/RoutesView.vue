<template>
  <div class="flex-none w-72 border-r border-gray-200 dark:border-slate-800">
    <RouteList :activeMock="activeMock" v-if="activeMock" />
  </div>
  <div class="flex-1 bg-gray-100 dark:bg-slate-900 overflow-auto">
    <RouteDetail :mock="activeMock" :route="activeRoute" v-if="activeRoute" />
  </div>
</template>

<script setup lang="ts">
import RouteDetail from "@/components/mock/route/RouteDetail.vue";
import RouteList from "@/components/mock/route/RouteList.vue";
import router from "@/router/index";
import { useMockStore } from "@/stores";
import { storeToRefs } from "pinia";
import { provide, watch } from "vue";
import { useRoute } from "vue-router";

const { activeMock, activeRoute } = storeToRefs(useMockStore());
const { setActiveRoute, setDefaultActiveRoute } = useMockStore();

const route = useRoute();

watch(
  () => [route.params.routeId, route.name, activeMock.value?.data.id],
  ([newId]) => {
    // TODO: We should have the constant for all routeName
    if (route.name === "routes-view") {
      if (!route.params.routeId && route.params.id && activeRoute.value?.id) {
        router.push({
          name: "route-view",
          params: {
            id: route.params.id as string,
            routeId: activeRoute.value.id as string,
          },
        });

        return;
      }
    }

    if (newId) {
      setActiveRoute(newId as string);
      return;
    }

    if (!newId && !activeRoute.value?.id) {
      setDefaultActiveRoute();
      return;
    }
  },
  {
    immediate: true,
  }
);

provide("mock", activeMock);
provide("route", activeRoute);
</script>
