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

