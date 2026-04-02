<script setup>
import { ref, onMounted, watch, computed } from 'vue';

const newTask = ref('');
const tasks = ref([]);
const filter = ref('all');

onMounted(() => {
  const saved = localStorage.getItem('core_tasks');
  if (saved) {
    tasks.value = JSON.parse(saved);
  }
});

watch(tasks, (newVal) => {
  localStorage.setItem('core_tasks', JSON.stringify(newVal));
}, { deep: true });

const addTask = () => {
  if (!newTask.value.trim()) return;
  tasks.value.unshift({
    id: Date.now(),
    title: newTask.value,
    completed: false,
    created_at: new Date().toLocaleDateString()
  });
  newTask.value = '';
};

const deleteTask = (id) => {
  tasks.value = tasks.value.filter(task => task.id !== id);
};

const filteredTasks = computed(() => {
  if (filter.value === 'completed') return tasks.value.filter(task => task.completed);
  if (filter.value === 'pending') return tasks.value.filter(task => !task.completed);
  return tasks.value;
});
</script>

<template>
  <div class="min-h-screen p-6 md:p-16 flex justify-center bg-slate-950">
    <div class="w-full max-w-xl">
      
      <header class="mb-12 border-l-4 border-blue-500 pl-6">
        <h1 class="text-4xl font-black text-white tracking-tight">
          CORE<span class="text-blue-500">TASK</span>
        </h1>
        <p class="text-slate-500 text-xs font-bold uppercase tracking-[0.3em] mt-1">
          Efficiency Engine
        </p>
      </header>

      <div class="mb-10 group">
        <div class="flex items-center bg-slate-900/40 backdrop-blur-xl border border-slate-800 rounded-2xl p-2 focus-within:border-blue-500/50 transition-all duration-500 shadow-2xl">
          <input 
            v-model="newTask" 
            @keyup.enter="addTask"
            placeholder="Input next task..."
            class="flex-1 bg-transparent py-3 px-4 text-slate-100 outline-none placeholder:text-slate-700 font-medium"
          />
          <button @click="addTask" class="bg-blue-600 text-white px-6 py-3 rounded-xl font-bold hover:bg-blue-500 transition-all">
            Add
          </button>
        </div>
      </div>

      <div class="flex gap-2 mb-8">
        <button 
          v-for="f in ['all', 'pending', 'completed']" 
          :key="f"
          @click="filter = f"
          :class="['flex-1 py-2 rounded-lg text-[10px] font-black uppercase tracking-widest border transition-all', 
                   filter === f ? 'bg-blue-600 border-blue-500 text-white' : 'bg-slate-900/30 border-slate-800/50 text-slate-500 hover:border-slate-700']"
        >
          {{ f }}
        </button>
      </div>

      <div class="space-y-3">
        <transition-group name="list">
          <div 
            v-for="task in filteredTasks" 
            :key="task.id"
            class="group flex items-center justify-between p-5 bg-slate-900/20 border border-slate-800/40 rounded-2xl hover:bg-slate-800/30 hover:border-blue-500/30 transition-all duration-300"
          >
            <div class="flex items-center gap-5">
              <div 
                @click="task.completed = !task.completed"
                :class="['w-5 h-5 rounded border-2 transition-all cursor-pointer flex items-center justify-center', 
                         task.completed ? 'bg-blue-500 border-blue-500' : 'border-slate-700 bg-slate-900']"
              >
                <svg v-if="task.completed" xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="4" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"/></svg>
              </div>

              <span :class="['text-base font-medium transition-all', task.completed ? 'text-slate-600 line-through' : 'text-slate-200']">
                {{ task.title }}
              </span>
            </div>

            <button @click="deleteTask(task.id)" class="opacity-0 group-hover:opacity-100 text-slate-600 hover:text-red-400 transition-all">
              <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M18 6 6 18"/><path d="m6 6 12 12"/></svg>
            </button>
          </div>
        </transition-group>
      </div>

      <div v-if="filteredTasks.length === 0" class="text-center py-20 opacity-20 italic text-slate-500">
        No tasks in queue...
      </div>
    </div>
  </div>
</template>

<style>
.list-enter-active, .list-leave-active { transition: all 0.4s ease; }
.list-enter-from { opacity: 0; transform: translateY(10px); }
.list-leave-to { opacity: 0; transform: translateX(20px); }
</style>