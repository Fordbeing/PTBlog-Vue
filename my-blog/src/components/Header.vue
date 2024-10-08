<template>
  <el-menu
    :default-active="activeIndex"
    class="el-menu-demo"
    mode="horizontal"
    @select="handleSelect"
  >
    <!-- Home Button -->
    <el-menu-item class="home" index="12" @click="goHome">Home</el-menu-item>

    <!-- Logo (just as text here, can be replaced with an image if needed) -->
    <el-menu-item index="2" class="logo-item">
      <h1 class="logo">PTBlog</h1>
    </el-menu-item>

    <!-- Category Dropdown -->
    <el-sub-menu index="13" class="category" @select="handleSelectArticle">
      <template #title><span class="category-title">Category</span></template>
      <el-menu-item 
        v-for="(category, index) in categoryData" 
        :key="index" 
        :index="String(index)" 
        class="category-item"  
        @click="selectCategory(category.name)">
        {{ category.name }}
      </el-menu-item>
    </el-sub-menu>

    <!-- Tree Hole Button -->
    <el-menu-item index="14" class="treehole" @click="goTreeHole">Tree Hole</el-menu-item>

    <!-- Person Button -->
    <el-menu-item index="15" class="person" @click="goPerson">Person</el-menu-item>

    <!-- Search Input -->
    <div class="search-container">
      <el-input placeholder="Search..." v-model="searchQuery" class="search-input" />
      <el-button class="search-button" icon="el-icon-search" @click="handleSearch">Search</el-button>
    </div>
  </el-menu>
</template>

<script setup>
import { useRouter } from 'vue-router';
import { ref, onMounted, getCurrentInstance } from 'vue';
import { useAllDataStore } from '@/stores'

const { proxy } = getCurrentInstance()

const dataStore = useAllDataStore()

const searchQuery = ref('');
const router = useRouter();

// 设置 activeIndex 为 null 或空字符串，确保没有菜单项被默认选中
const activeIndex = ref(null);

const goHome = () => {
  dataStore.updateCategory('');
  router.push('/');
  activeIndex.value = null; // 点击 Home 时取消选中
};

const goTreeHole = () => {
  router.push('/treehole');
  activeIndex.value = null; // 点击 Home 时取消选中
};

const goPerson = () => {
  router.push('/person');
  activeIndex.value = null; // 点击 Home 时取消选中
};

const handleSelect = (key, keyPath) => {
  dataStore.updateCategory('');
  activeIndex.value = key; // 更新选中项
}

const handleSelectArticle = (key, keyPath) => {
  const selectedCategory = categoryData.value.find((_, i) => i === parseInt(key));
  if (selectedCategory) {
    dataStore.updateCategory(selectedCategory.name);
    activeIndex.value = key; // 更新选中项
  }
}

const selectCategory = (category) => {
  dataStore.updateCategory(category);
  router.push('/');
  activeIndex.value = null; // 点击分类后取消选中状态
};

const handleSearch = () => {
  console.log('Searching for: ', searchQuery.value)
}

const categoryData = ref([]);

const getCategoryInfo = async () => {
  const data = await proxy.$api.getCategoryInfo();
  console.log(data);
  categoryData.value = data;
}

onMounted(() => {
  getCategoryInfo();
})
</script>


<style scoped>
.category-item {
  width: 200px;
}

.el-menu-demo {
  background-color: #282c34;
  color: #ffffff;
  display: flex;
  align-items: center;
}

.category-title {
  color: #a15353;
  font-size: 20px;
  font-weight: bold;
  text-align: center;
}

.home {
  width: 10%;
}

.category {
  width: 18%;
  justify-content: center;
}

.treehole {
  width: 18%;
}

.person {
  width: 18%;
}

.el-menu-item {
  color: #a15353;
  font-size: 20px;
  font-weight: bold;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.el-menu-item:hover {
  box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.2);
}

.logo-item {
  text-align: center;
  width: 10%;
}

.logo {
  font-size: 24px;
  font-weight: bold;
  background: linear-gradient(135deg, #975997, #b8bda8);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  color: transparent;
}

.search-container {
  display: flex;
  align-items: center;
  width: 20%;
}

.search-input {
  max-width: 250px;
  margin-right: 10px;
}
.el-menu-demo {
  border: none;
  box-shadow: none;
}


.search-button {
  background: linear-gradient(45deg, #424446, #8a5779);
  color: #201b1b;
  border: none;
  height: 40px;
  border-radius: 20px;
  font-weight: bold;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  transition: all 0.3s ease;
}

.search-button:hover {
  background: linear-gradient(45deg, #34393f, #409eff);
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.3);
  color: #7e15a8;
}
</style>
