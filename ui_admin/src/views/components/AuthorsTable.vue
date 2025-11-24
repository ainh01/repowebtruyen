<script setup>
import { ref, onMounted, computed, onBeforeUnmount } from 'vue';
import { getUsers, setUserActive } from '../../server/OtruyenAPI'; 

const userList = ref([]);
const currentPage = ref(1);
const itemsPerPage = 10;
const showMenu = ref(null);

const loadUsersTable = async () => {
  try {
    const response = await getUsers();
    userList.value = response;
    console.log("Truyen list loaded:", response);
  } catch (error) {
    console.error("Lỗi khi tải danh sách truyện", error);
  }
};

const setUser = async (userId, activeStatus) => {
  try {
    const response = await setUserActive(userId, activeStatus); 
    
    if (response && response.message) {
      console.log(`Đã cập nhật trạng thái: ${userId} => ${activeStatus}`);
    } else {
      console.error("Không có thông tin trả về từ API.");
    }
  } catch (error) {
    console.error("Lỗi khi cập nhật trạng thái:", error);
  }
};


const totalPages = computed(() => Math.ceil(userList.value.length / itemsPerPage));

const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return userList.value.slice(start, end);
});

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};

const toggleMenu = (index) => {
  showMenu.value = showMenu.value === index ? null : index;
};

const setSwitch = async (index, activeStatus) => {
  const user = userList.value[index];
  if (!user || !user.email) return;

  user.active = activeStatus; // Cập nhật giao diện ngay lập tức
  await setUser(user.email, activeStatus); // Gọi API
  showMenu.value = null;
};

onMounted(() => {
  loadUsersTable();
  document.addEventListener('click', handleClickOutside);
});

onBeforeUnmount(() => {
  document.removeEventListener('click', handleClickOutside);
});

const handleClickOutside = (event) => {
  const menuElement = document.querySelector('.menu-box');
  const buttonElement = event.target.closest('button');
  if (menuElement && !menuElement.contains(event.target) && !buttonElement) {
    showMenu.value = null;
  }
};

</script>

<template>
  <div class="card">
    <div class="card-header pb-0">
      <h6>Users table</h6>
    </div>
    <div class="card-body px-0 pt-0 pb-2">
      <div class="table-responsive p-0">
        <table class="table align-items-center mb-0">
          <thead>
            <tr>
              <th
                class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7"
              >
                User
              </th>
              <th
                class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7"
              >
                Status
              </th>
              <th
                class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7"
              >
                Created at
              </th>
              <th class="text-secondary opacity-7"></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in paginatedData" :key="index">
              <td>
                <div class="d-flex px-2 py-1">
                  <div>
                    <img
                      src="../../assets/img/team-2.jpg"
                      class="avatar avatar-sm me-3"
                      alt="user1"
                    />
                  </div>
                  <div class="d-flex flex-column justify-content-center">
                    <h6 class="mb-0 text-sm">{{ item.username }}</h6>
                    <p class="text-xs text-secondary mb-0">
                      {{ item.email }}
                    </p>
                  </div>
                </div>
              </td>
              <td class="align-middle text-center text-sm">
                <span :class="{'badge badge-sm bg-gradient-secondary': item.active === false , 'badge badge-sm bg-gradient-success': item.active === true}">
                  {{ item.active }}
                </span>
              </td>
              <td class="align-middle text-center">
                <span class="text-secondary text-xs font-weight-bold"
                  >{{ item.created_at }}</span
                >
              </td>
              <td class="align-middle">
                <button class="btn btn-link text-secondary mb-0" @click="toggleMenu(index)" aria-haspopup="true" aria-expanded="false">
                  Edit
                </button>
                <div v-if="showMenu === index" class="menu-box">
                  <button @click="setSwitch(index, true)">
                    <span class="me-2 text-xs font-weight-bold">True</span>
                  </button>
                  <button @click="setSwitch(index, false)">
                    <span class="me-2 text-xs font-weight-bold">False</span>
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
        <div class="pagination-controls">
          <button @click="prevPage" :disabled="currentPage === 1"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="currentColor"><path d="M10.8284 12.0007L15.7782 16.9504L14.364 18.3646L8 12.0007L14.364 5.63672L15.7782 7.05093L10.8284 12.0007Z"></path></svg></button>
          <span> {{ currentPage }} / {{ totalPages }}</span>
          <button @click="nextPage" :disabled="currentPage === totalPages"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="currentColor"><path d="M13.1717 12.0007L8.22192 7.05093L9.63614 5.63672L16.0001 12.0007L9.63614 18.3646L8.22192 16.9504L13.1717 12.0007Z"></path></svg></button>
        </div>
      </div>
    </div>
  </div>
</template>
