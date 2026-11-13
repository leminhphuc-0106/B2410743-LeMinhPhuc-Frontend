
<template>
  <div class="page container">
    <!-- Thanh tìm kiếm -->
    <div class="row">
      <div class="col-12 col-md-10 mx-auto">
        <InputSearch v-model="searchText" />
      </div>
    </div>

    <!-- Nội dung chính -->
    <div class="row mt-4">
      <!-- Danh sách liên hệ -->
      <div class="col-12 col-md-6 mb-3">
        <h4 class="mb-3">
          Danh bạ
          <i class="fas fa-address-book"></i>
        </h4>

        <ContactList
          v-if="filteredContactsCount > 0"
          :contacts="filteredContacts"
          v-model:activeIndex="activeIndex"
        />

        <p v-else>Không có liên hệ nào.</p>

        <!-- Các nút chức năng -->
        <div class="row g-2 mt-3">
          <div class="col-12 col-sm-6">
            <button
              class="btn btn-primary btn-sm w-100"
              @click="refreshList()"
            >
              <i class="fas fa-redo"></i>
              Làm mới
            </button>
          </div>

          <div class="col-12 col-sm-6">
            <button
              class="btn btn-success btn-sm w-100"
              @click="goToAddContact"
            >
              <i class="fas fa-plus"></i>
              Thêm mới
            </button>
          </div>

          <div class="col-12">
            <button
              class="btn btn-danger btn-sm w-100"
              @click="removeAllContacts"
            >
              <i class="fas fa-trash"></i>
              Xóa tất cả
            </button>
          </div>
        </div>
      </div>

      <!-- Chi tiết liên hệ -->
      <div class="col-12 col-md-6 mb-3">
        <div v-if="activeContact">
          <h4 class="mb-3">
            Chi tiết liên hệ
            <i class="fas fa-address-card"></i>
          </h4>

          <ContactCard :contact="activeContact" />
        </div>
      </div>
    </div>
  </div>
</template>



<script>
import ContactCard from "@/components/ContactCard.vue";
import InputSearch from "@/components/InputSearch.vue";
import ContactList from "@/components/ContactList.vue";
import ContactService from "@/services/contact.service";

export default {
  components: {
    ContactCard,
    InputSearch,
    ContactList,
  },

  data() {
    return {
      contacts: [],
      activeIndex: -1,
      searchText: "",
    };
  },

  watch: {
    // Giám sát thay đổi của searchText.
    // Bỏ chọn liên hệ đang được chọn khi tìm kiếm.
    searchText() {
      this.activeIndex = -1;
    },
  },

  computed: {
    // Chuyển thông tin liên hệ thành chuỗi để tìm kiếm.
    contactStrings() {
      return this.contacts.map((contact) => {
        const { name, email, address, phone } = contact;
        return [name, email, address, phone].join("");
      });
    },

    // Lọc danh sách liên hệ theo từ khóa tìm kiếm.
    filteredContacts() {
      if (!this.searchText) return this.contacts;

      return this.contacts.filter((_contact, index) =>
        this.contactStrings[index].includes(this.searchText)
      );
    },

    // Lấy thông tin liên hệ đang được chọn.
    activeContact() {
      if (this.activeIndex < 0) return null;

      return this.filteredContacts[this.activeIndex];
    },

    // Đếm số lượng liên hệ sau khi lọc.
    filteredContactsCount() {
      return this.filteredContacts.length;
    },
  },

  methods: {
    // Lấy toàn bộ danh sách liên hệ từ API.
    async retrieveContacts() {
      try {
        this.contacts = await ContactService.getAll();
      } catch (error) {
        console.log(error);
      }
    },

    // Làm mới danh sách và bỏ chọn liên hệ.
    refreshList() {
      this.retrieveContacts();
      this.activeIndex = -1;
    },

    // Xóa toàn bộ danh sách liên hệ.
    async removeAllContacts() {
      if (confirm("Bạn muốn xóa tất cả Liên hệ?")) {
        try {
          await ContactService.deleteAll();
          this.refreshList();
        } catch (error) {
          console.log(error);
        }
      }
    },

    // Chuyển đến trang thêm liên hệ mới.
    goToAddContact() {
      this.$router.push({ name: "contact.add" });
    },
  },

  // Tải danh sách liên hệ khi component được khởi tạo.
  mounted() {
    this.refreshList();
  },
};
</script>


<style scoped>
.page {
  text-align: left;
  max-width: 1000px;
  margin: 0 auto;
  padding: 15px;
}
</style>
