<template>
  <div class="flex justify-center h-100 transition ease-in-out duration-500 lg:h-screen"
    :class="isDarkMode ? 'bg-gray-900' : 'bg-slate-300'">

    <template v-if="isDarkMode">
      <img class="absolute w-screen lg:hidden" src="./assets/bg-mobile-dark.jpg" alt="" />
      <img class="absolute w-screen lg:block hidden" src="./assets/bg-desktop-dark.jpg" alt="" />
    </template>
    <template v-else>
      <img class="absolute w-screen lg:hidden" src="./assets/bg-mobile-light.jpg" alt="" />
      <img class="absolute w-screen lg:block hidden" src="./assets/bg-desktop-light.jpg" alt="" />
    </template>



    <div class="flex flex-col w-[40em] z-10 px-8">
      <div class="flex justify-between py-8 ">
        <header class="text-3xl font-bold text-white tracking-[.25em]">TODO</header>
        <div class="cursor-pointer" @click="changeMode">
          <transition name="fade" mode="out-in">
            <img v-if="isDarkMode" src="./assets/icon-sun.svg" alt="">
            <img v-else src="./assets/icon-moon.svg" alt="">
          </transition>
        </div>
      </div>

      <div>
        <div id="input-box" class="transition ease-in-out duration-500 p-6 rounded-lg flex items-center gap-4 shadow-lg"
          :class="isDarkMode ? 'box-color' : 'bg-slate-200'">
          <div class="w-6 h-6 border-solid border-2 box-circle rounded-full  flex  items-center justify-center">
          </div>
          <input type="text" v-model="inputNewTask" placeholder="Create a new todo..." @keyup.enter="addTask"
            class="text-white outline-none transition ease-in-out duration-500"
            :class="isDarkMode ? 'box-color' : 'bg-slate-200 text-slate-500'">
        </div>
        <div class="mt-4 rounded-lg shadow-lg transition ease-in-out duration-500"
          :class="isDarkMode ? 'box-color text-white' : 'bg-slate-200'">
          <div v-if="tasks.length">
            <draggable v-model="tasks" tag="div" :itemKey="task => task.id" v-bind="dragOptions" @start="drag = true"
              @end="drag = false">
              <template #item="{ element: task }">
                <transition name="fade" mode="out-in">
                  <div class="p-6 flex items-center  cursor-pointer task justify-between" v-if="filterItem(task)">
                    <div class="flex gap-4 " @click="toggleComplete(task.id)">
                      <div class="w-6 h-6 border-solid border-2 box-circle rounded-full flex items-center justify-center "
                        :class="{ 'check-bg': task.completed }">
                        <img v-if="task.completed" src="./assets/icon-check.svg" alt="">
                      </div>
                      <div class="transition ease-in-out duration-100"
                        :class="{ 'completed secondary-font-color ': task.completed }">
                        {{ task.text }}
                      </div>
                    </div>
                    <div @click="clearTask(task.id)">
                      <img src="./assets/icon-cross.svg" alt="">
                    </div>
                  </div>
                </transition>
              </template>
            </draggable>
            <div v-if="filterCriteria === 'completed' && checkForCompleted" class="p-6 task secondary-font-color">
              You haven't completed any task you lazy bish
            </div>
            <div v-if="filterCriteria === 'active' && !checkForActive" class="p-6 task secondary-font-color">
              Congratulations! You don't have any active tasks.
            </div>
            <div class="flex p-6 justify-between task secondary-font-color">
              <div>
                {{ tasks.length }} items left
              </div>
              <div class="cursor-pointer" @click="clearCompeted">
                Clear Completed
              </div>
            </div>
          </div>
          <div v-else class="secondary-font-color p-6">
            You currently have no tasks pending.
          </div>
        </div>
        <div
          class="mt-4 secondary-font-color p-6 rounded-lg flex justify-center gap-6  font-bold transition ease-in-out duration-500 shadow-lg"
          :class="isDarkMode ? 'box-color' : 'bg-slate-200'">
          <button @click="setFilter('all')" :class="{ 'active-filter': filterCriteria === 'all' }">All</button>
          <button @click="setFilter('active')" :class="{ 'active-filter': filterCriteria === 'active' }">
            Active</button>
          <button @click="setFilter('completed')"
            :class="{ 'active-filter': filterCriteria === 'completed' }">Completed</button>
        </div>
      </div>
      <div class="py-8 text-center secondary-font-color font-bold">
        Drag and drop to reorder list
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import draggable from 'vuedraggable'
const dragOptions = computed(() => {
  return {
    animation: 200,
    group: "description",
    disabled: false,
    ghostClass: "ghost"
  }
})
const isDarkMode = ref(true)
const changeMode = () => {
  isDarkMode.value = !isDarkMode.value
}
const setFilter = (filter) => {
  filterCriteria.value = filter
}
const inputNewTask = ref('')
const checkForCompleted = computed(() => {
  return !tasks.value.some(task => task.completed === true)
})

const checkForActive = computed(() => {
  return tasks.value.some(task => task.completed === false)
})

const addTask = () => {
  if (inputNewTask.value.trim() !== '') {
    let newTask = {
      id: tasks.value.length + 1,
      text: inputNewTask.value,
      completed: false
    }
    tasks.value.push({ ...newTask })
    inputNewTask.value = ''
  }
}

const clearTask = (id) => {
  tasks.value = tasks.value.filter(task => task.id !== id);
}

const clearCompeted = () => {
  tasks.value = tasks.value.filter(task => task.completed === false)
}

const toggleComplete = (id) => {
  tasks.value.forEach((task) => {
    if (task.id === id) {
      task.completed = !task.completed;
    }
  })
}

const tasks = ref([
  {
    id: 1,
    text: 'Complete online JavaScript course',
    completed: true
  },
  {
    id: 2,
    text: 'Jog around the park x',
    completed: false
  },
  {
    id: 3,
    text: '10 minutes meditation',
    completed: false
  },
  {
    id: 4,
    text: 'Read for 1 hour',
    completed: false
  },
  {
    id: 5,
    text: 'Pick up groceries',
    completed: false
  },
  {
    id: 6,
    text: 'Complete Todo App on Frontend Mentor',
    completed: false
  }
])

// filter
// handle returning of view
const filterItem = (task) => {
  if (filterCriteria.value === 'completed') {
    if (task.completed) {
      return true
    }
  } else if (filterCriteria.value === 'active') {
    if (!task.completed) {
      return true
    }
  } else {
    return true
  }
}

const filterCriteria = ref('all')

</script>

<style>
.completed {
  text-decoration: line-through;
}

.task:not(:last-child) {
  cursor: pointer
}

.border-top {
  border-top: 1px solid #5D5F7E;
}

.box-color {

  background-color: #25263D;
}

.box-circle {
  border-color: #36384D;
}

.task:not(:first-child) {
  border-top: 1px solid #5D5F7E;
}

.check-bg {
  background: linear-gradient(192deg, hsl(192, 100%, 67%), hsl(280, 87%, 65%));
}

.active-filter {
  color: hsl(220, 98%, 61%)
}

.secondary-font-color {
  color: #5D5F7E;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s, transform 0.5s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}

.bg-mobile-dark {
  background-image: url('./assets/bg-mobile-dark.jpg');
}

.bg-desktop-dark {
  background-image: url('./assets/bg-desktop-dark.jpg');
}

/* .ghost {
  opacity: 0.5;
  background: #c8ebfb;
} */
</style>
