<template>
  <div class="row" style="height: 30%">
    <div class="col">
      <div
        style="
          background-color: rgb(237, 237, 227);
          margin-left: 30px;
          height: 95%;
          border-radius: 6px;
          padding-top: 15px;
          padding-left: 5px;
          padding-right: 5px;
        "
      >
        <ClassProgForm
          @formData="createProg"
          :button-loading="loading"
          button-text="ایجاد برنامه جدید"
          button-class="btn btn-primary"
          :post="post"
        />
      </div>
    </div>
  </div>
  <div
    class="row"
    style="
      height: 60%;
      overflow-y: scroll;
      margin-top: 5px;
      border: 1px solid white;
      border-radius: 5px;
      background-color: rgb(57, 57, 60);
      opacity: 0.7;
      filter: alpha(opacity=100);
    "
  >
    <div class="col" v-if="!spinner">
      <div
        class="spinner-border text-danger"
        role="status"
        style="width: 100px; height: 100px; margin-top: 100px"
      >
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
    <div v-else class="col">
      <table
        class="table table-bordered"
        style="
          font-family: Vazir;
          font-size: smaller;
          text-align: center;
          margin-top: 10px;
          margin-left: 3px;
          direction: rtl;
          width: 100%;
        "
      >
        <thead>
          <tr
            class="sticky"
            style="
              background-color: cornflowerblue;
              text-align: center;
              font-size: smaller;
              color: rgb(254, 254, 255);
            "
          >
            <th>--</th>
            <th>کد برنامه</th>
            <th>نام کلاس</th>
            <th>نام درس</th>
            <th>مقطع تحصیلی</th>
            <th>روز هفته</th>
            <th>ساعت شروع</th>
            <th>ساعت پایان</th>
            <th>--</th>
            <th>--</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item, index) in progs"
            :key="index"
            style="text-align: right; font-size: 12px; color: aliceblue"
            v-bind:class="{ 'selected-row': index === selectedRowIndex }"
          >
            <td style="width: 5%; padding-top: 10px">
              <a v-on:click="selectRow(index)" href="#"
                ><img
                  style="width: 20px; height: 20px; border-radius: 5px"
                  src="../../../../public/select.jpg"
              /></a>
            </td>
            <td style="width: 5%; padding-top: 10px">{{ item.id }}</td>
            <td style="width: 17%; padding-top: 15px">{{ item.name }}</td>
            <td style="width: 18%; padding-top: 15px">
              {{ item.lesson_name }}
            </td>
            <td style="width: 15%; padding-top: 15px"></td>
            <td v-if="item.dayOfWeek == 0" style="width: 8%; padding-top: 15px">
              شنبه
            </td>
            <td v-if="item.dayOfWeek == 1" style="width: 8%; padding-top: 15px">
              یکشنبه
            </td>
            <td v-if="item.dayOfWeek == 2" style="width: 8%; padding-top: 15px">
              دوشنبه
            </td>
            <td v-if="item.dayOfWeek == 3" style="width: 8%; padding-top: 15px">
              سه شنبه
            </td>
            <td v-if="item.dayOfWeek == 4" style="width: 8%; padding-top: 15px">
              چهارشنبه
            </td>
            <td v-if="item.dayOfWeek == 5" style="width: 8%; padding-top: 15px">
              پنج شنبه
            </td>
            <td style="width: 8%; padding-top: 15px">{{ item.time_start }}</td>
            <td style="width: 8%; padding-top: 15px">{{ item.time_end }}</td>
            <td style="width: 8%">
              <router-link
                class="btn btn-success button-table-class"
                :to="{ name: 'editProg', params: { id: item.id } }"
              >
                اصلاح
              </router-link>
            </td>
            <td style="width: 8%">
              <button
                @click="deleteProg(item.id)"
                class="btn btn-danger button-table-class"
              >
                حذف
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
  <div class="row" style="height: 10%"></div>
</template>

<script>
import { ref, reactive } from "vue";
import axios from "axios";
import Swal from "sweetalert2";
import { useRoute } from "vue-router";
import ClassProgForm from "@/components/Forms/ClassProgFormComponent.vue";

export default {
  data() {
    return {
      selectedRowIndex: -1,
    };
  },
  methods: {
    selectRow(index) {
      console.log(index);
      this.selectedRowIndex = index;
    },
  },
  components: {
    ClassProgForm,
  },
  setup() {
    const form = reactive({
      class_id: "",
      lesson_id: "",
      DayOfWeek: "",
      time_start: "",
      time_end: "",
      dayOfWeekErrorText: "",
      classIdErrorText: "",
      lessonIdErrorText: "",
      timeStartErrorText: "",
      timeEndErrorText: "",
    });
    const loading = ref(false);
    const progs = ref([]);
    const post = ref({});
    const route = useRoute();
    const spinner = ref(false);

    function createProg(formData) {
      loading.value = true;
      axios
        .post("http://127.0.0.1:8000/api/school/class-program/store", {
          lesson_id: formData.lesson_id,
          dayOfWeek: formData.DayOfWeek,
          time_start: formData.time_start,
          time_end: formData.time_end,
          class_id: formData.class_id,
        })
        .then(function () {
          getProgs();
          loading.value = false;
          form.class_id = "";
          form.lesson_id = "";
          form.DayOfWeek = "";
          form.time_start = "";
          form.time_end = "";
          Swal.fire({
            title: "ذخیره شد",
            text: " این برنامه با موفقیت در پایگاه داده ثبت گردید",
            icon: "success",
            confirmButtonText: "Ok",
            position: "top",
          });
        })
        .catch(function (error) {
          loading.value = false;
          console.log(error);
          Swal.fire({
            title: "پیغام خطا",
            text: "مشکلاتی در مورد ثبت اطلاعات در پایگاه داده مشاهده گردیده",
            icon: "error",
            confirmButtonText: "Ok",
            position: "top",
          });
        });
    }
    function getProgs() {
      axios
        .get("http://127.0.0.1:8000/api/school/class-program/programs")
        .then(function (response) {
          // handle success
          spinner.value = true;
          form.class_id = "";
          progs.value = response.data;
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
    }
    function getProg() {
      console.log(route.params.id);
      axios
        .get(`http://127.0.0.1:8000/api/school/class-prog/${route.params.id}`)
        .then(function (response) {
          // handle success
          post.value = response.data;
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
    }
    function deleteProg(id) {
      Swal.fire({
        title: "آیا مطمئن هستید؟",
        text: "امکان تغییر نظرتان در آینده وجود نخواهد داشت",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "بله ، حذف شود",
        position: "top",
      }).then((result) => {
        // getProgs();
        if (result.isConfirmed) {
          axios
            .delete(
              `http://127.0.0.1:8000/api/school/class-program/remove/${id}`
            )
            .then(function () {
              Swal.fire({
                title: "Thanks!",
                text: `برنامه کلاسی با کد (${id}) با موفقیت حذف گردید`,
                icon: "success",
                confirmButtonText: "Ok",
                position: "top",
              });
              getProgs();
            })
            .catch(function (error) {
              console.log(error);
            });
        }
      });
    }

    getProg();
    getProgs();

    return {
      spinner,
      deleteProg,
      post,
      progs,
      createProg,
      loading,
    };
  },
};
</script>

<style>
.swal2-content {
  font-size: 14px;
  font-family: Vazir;
}
.swal2-title {
  font-size: 16px;
  font-family: Vazir;
}
.validation-text {
  font-size: 12px;
  font-family: Vazir;
  margin-top: 1px;
}
.button-table-class {
  font-size: 12px;
  width: 95%;
  height: 30px;
}
.grade-window {
  background-color: rgb(237, 237, 227);
  margin-left: 30px;
  height: 95%;
  border-radius: 6px;
  padding-top: 10px;
  padding-left: 5px;
  padding-right: 5px;
}
.selected-row {
  background-color: rgb(94, 119, 148);
  color: rgb(234, 241, 19) !important;
  font-size: 16px !important;
}
</style>
