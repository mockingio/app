<template>
  <nav v-if="activeMock">
    <div class="flex rounded-md shadow-sm">
      <div class="relative flex items-stretch flex-grow focus-within:z-10">
        <div
          class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none"
        >
          <SearchIcon class="h-5 w-5 text-gray-400" aria-hidden="true" />
        </div>
        <input
          type="text"
          name="search"
          :value="search"
          @input="change"
          class="focus:ring-indigo-500 focus:border-indigo-500 block w-full rounded-none rounded-l-md pl-10 sm:text-sm bg-transparent block w-full min-w-0 sm:text-sm border-gray-300 dark:border-slate-800"
          placeholder="Search text..."
        />
      </div>
      <button
        type="button"
        @click="addNew"
        class="-ml-px hover:text-green-500 dark:hover:text-green-500 dark:text-slate-400 relative inline-flex items-center px-4 py-2 dark:border-slate-700 border rounded-r-md border-gray-300 text-xs font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1"
      >
        <PlusIcon class="h-5 w-5" aria-hidden="true" />
      </button>
    </div>
    <RouteListItem
      v-for="route in routers"
      :route="route"
      :mock="activeMock"
      :key="route.id"
    />
  </nav>
</template>

<script setup lang="ts">
import { useMockStore, type Mock, type Route } from "@/stores";
import { PlusIcon, SearchIcon } from "@heroicons/vue/outline";
import { v4 } from "uuid";
import { ref, watch } from "vue";
import RouteListItem from "./RouteListItem.vue";

const { createRoute } = useMockStore();
const props = defineProps({
  activeMock: { type: Object as () => Mock, required: true },
});

const search = ref("");
const routers = ref(props.activeMock.data.routes);

watch(
  (): [Route[], string] => [props.activeMock.data.routes, search.value],
  ([r, term]) => {
    routers.value = r.filter(
      (item) =>
        item.path.toLowerCase().includes(term) ||
        item.description.toLowerCase().includes(term)
    );
  }
);

const change = (evt: any) => {
  if (typeof evt === "string") {
    search.value = evt;
  } else if (typeof evt === "object") {
    search.value = evt.target.value;
  }
};

const addNew = async () => {
  await createRoute(props.activeMock.data.id, {
    id: v4(),
    path: "/ver",
    method: "GET",
    description: `hello world ${props.activeMock.data.routes.length || 1}`,
    responses: [
      {
        id: v4(),
        status: 200,
        headers: {
          "Content-Type": "application/json",
        },
        body: '{\n"success": true\n}',
      },
    ],
  });

  search.value = "";
};
</script>
