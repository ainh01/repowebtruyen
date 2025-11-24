<script setup>
import { ref, onMounted, computed, onBeforeUnmount } from 'vue';
import { getHomeAdmin, setHomeClient } from '../../server/OtruyenAPI'; 

const truyenList = ref([]);
const currentPage = ref(1);
const itemsPerPage = 10;
const showMenu = ref(null);

const loadTruyenList = async () => {
  try {
    const response = await getHomeAdmin();
    truyenList.value = response.data;
    console.log("Truyen list loaded:", response.data);
  } catch (error) {
    console.error("Lỗi khi tải danh sách truyện", error);
  }
};

const setHomeClientAPI = async (comicId, switchStatus) => {
  try {
    const response = await setHomeClient(comicId, switchStatus); 
    
    if (response && response.message) {
      console.log(`Đã cập nhật trạng thái: ${comicId} => ${switchStatus}`);
    } else {
      console.error("Không có thông tin trả về từ API.");
    }
  } catch (error) {
    console.error("Lỗi khi cập nhật trạng thái:", error);
  }
};


const totalPages = computed(() => Math.ceil(truyenList.value.length / itemsPerPage));

const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return truyenList.value.slice(start, end);
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

const setSwitch = async (index, switchStatus) => {
  const comic = truyenList.value[index];
  if (!comic || !comic._id) return;

  comic.switch = switchStatus; // Cập nhật giao diện ngay lập tức
  await setHomeClientAPI(comic._id, switchStatus); // Gọi API
  showMenu.value = null;
};

onMounted(() => {
  loadTruyenList();
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

const baseUrl = 'https://otruyenapi.com/uploads/comics/';
</script>


<template>
  <div class="card mb-4">
    <div class="card-header pb-0">
      <h6>Projects table (Home)</h6>
    </div>
    <div class="card-body px-0 pt-0 pb-2">
      <div class="table-responsive p-0">
        <table class="table align-items-center justify-content-center mb-0">
          <thead>
            <tr>
              <th
                class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7"
              >
                Project
              </th>
              <th
                class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7"
              >
                Status
              </th>
              <th
                class="text-uppercase text-secondary text-xxs font-weight-bolder text-center opacity-7 ps-2"
              >
                Completion
              </th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in paginatedData" :key="index">
              <td>
                <div class="d-flex px-2">
                  <div>
                    <img
                      :src="`${baseUrl}${item.thumb_url}`"
                      class="avatar avatar-sm rounded-circle me-2"
                      :alt="item.name"
                    />
                  </div>
                  <div class="my-auto">
                    <h6 class="mb-0 text-sm">{{ item.name }}</h6>
                  </div>
                </div>
              </td>
              <td class="align-middle text-center text-sm">
                <span :class="{'badge badge-sm bg-gradient-secondary': item.switch === 'Offline', 'badge badge-sm bg-gradient-success': item.switch === 'Online'}">
                  {{ item.switch }}
                </span>
              </td>
              <td class="align-middle text-center">
                <span class="me-2 text-xs font-weight-bold">{{ item.status }}</span>
              </td>
              <td class="align-middle position-relative">
                <button class="btn btn-link text-secondary mb-0" @click="toggleMenu(index)" aria-haspopup="true" aria-expanded="false">
                  <i class="fa fa-ellipsis-v text-xs" aria-hidden="true"></i>
                </button>
                <div v-if="showMenu === index" class="menu-box">
                  <button @click="setSwitch(index, 'Offline')">
                    <span class="me-2 text-xs font-weight-bold">OFF</span>
                  </button>
                  <button @click="setSwitch(index, 'Online')">
                    <span class="me-2 text-xs font-weight-bold">ON</span>
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



<style>
.pagination-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

.pagination-controls button {
  margin: 0 5px;
  border: none;
  background: white;
  color: #2dce89; 
  cursor: pointer;
  transition: all 0.3s ease-in-out; 
}

.pagination-controls button:hover {
  transform: scaleX(1.3);
}

.menu-box {
  position: absolute;
  width: max-content;
  background-color: white;
  border: none;
  padding: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  border-radius: 10px;
  z-index: 9999;
}

.menu-box button {
  display: block;
  width: 100%;
  border: none;
  background-color: #f9f9f9;
  cursor: pointer;
  text-align: left;
  border-radius: 5px;
}

.menu-box button:hover {
  background-color: #f1f1f1;
}

.position-relative {
  position: relative; /* Đảm bảo menu-box được căn theo nút */
}
</style>
