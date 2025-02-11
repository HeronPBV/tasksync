<template>
    <Head title="Dashboard" />
    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl leading-tight">Dashboard</h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div
                    class="bg-white overflow-hidden shadow-sm sm:rounded-lg p-6"
                >
                    <div class="flex items-center justify-between mb-4">
                        <h2 class="text-2xl font-semibold">All Boards</h2>
                        <button
                            @click="showForm = true"
                            class="bg-green-300 text-green-600 px-4 py-2 rounded hover:bg-green-400 font-bold"
                        >
                            New Board
                        </button>
                    </div>

                    <BoardAddForm
                        v-if="showForm"
                        @board-added="handleBoardAdded"
                        @cancel-add="showForm = false"
                    />

                    <BoardList :boards="boards" />
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>

<script lang="ts" setup>
import { Head } from "@inertiajs/vue3";
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import BoardList from "@/Components/Board/BoardList.vue";
import BoardAddForm from "@/Components/Board/BoardAddForm.vue";
import { ref, onMounted, computed } from "vue";
import { useBoardStore } from "@/stores/Board/boardStore";
import { useUserStore } from "@/stores/User/userStore";
import { useColumnStore } from "@/stores/Column/columnStore";
import { useTaskStore } from "@/stores/Task/taskStore";
import type { Board } from "@/interfaces/Board/Board";

const props = defineProps<{
    boards: Board[];
}>();

const boards = computed(() => boardStore.boards);

const boardStore = useBoardStore();
const userStore = useUserStore();
const columnStore = useColumnStore();
const taskStore = useTaskStore();

const showForm = ref(false);

const handleBoardAdded = (newBoard: Board) => {
    showForm.value = false;
};

onMounted(async () => {
    boardStore.boards = [...props.boards];
    boardStore.registerWebSocketEvents();
    columnStore.registerWebSocketEvents();
    taskStore.registerWebSocketEvents();
    await userStore.fetchUser();
});
</script>
