<template>
  <div class="row mt-4" style="direction: rtl; margin-right: 10px">
    <div class="col-2" style="height: 400px">
      <div class="row">
        <div
          style="
            height: 35px;
            border-radius: 5px;
            width: 70%;
            background-color: rgba(51, 51, 144, 0.726);
            margin-right: 48px;
            margin-top: 35px;
          "
        >
          <p
            style="
              font-size: 14px;
              font-family: Vazir;
              color: aliceblue;
              margin-top: 9px;
            "
          >
            گزارشهای گرافیکی
          </p>
        </div>
      </div>
      <div
        class="row"
        style="
          background-color: rgba(20, 16, 63, 0.656);
          margin-top: 10px;
          height: 150px;
          width: 90%;
          margin-right: 25px;
          border-radius: 5px;
        "
      >
        <div class="row mt-1" style="margin-right: 2px">
          <div style="height: 40px; width: 80%; padding-right: 27px">
            <button
              type="button"
              class="btn btn-primary button-class"
              :class="btn1"
              style="
                width: 120%;
                height: 30px;
                margin-top: 5px;
                padding-top: 5px;
                margin-right: -7px;
              "
              @click="barchart"
            >
              <p style="font-size: 13px; font-family: Vazir">Bar chart</p>
            </button>
          </div>
        </div>
        <div class="row mt-1" style="margin-right: 2px">
          <div style="height: 40px; width: 80%; padding-right: 27px">
            <button
              type="button"
              class="btn btn-primary button-class"
              :class="btn2"
              style="
                width: 120%;
                height: 30px;
                margin-top: 5px;
                padding-top: 5px;
                margin-right: -7px;
              "
              @click="linechart"
            >
              <p style="font-size: 13px; font-family: Vazir">Line chart</p>
            </button>
          </div>
        </div>
        <div class="row mt-1" style="margin-right: 2px">
          <div style="height: 40px; width: 80%; padding-right: 27px">
            <button
              type="button"
              class="btn btn-primary button-class"
              :class="btn3"
              style="
                width: 120%;
                height: 30px;
                margin-top: 5px;
                padding-top: 5px;
                margin-right: -7px;
              "
              @click="radarchart"
            >
              <p style="font-size: 13px; font-family: Vazir">Radar chart</p>
            </button>
          </div>
        </div>
        <div
          v-if="report2"
          class="alert alert-primary"
          role="alert"
          style="
            font-family: Vazir;
            font-size: 12px;
            color: black;
            margin-top: 40px;
          "
        >
          برای مشاهده این گزارش قبل از انتخاب نام دانش آموز بایستی یک درس انتخاب
          شود
        </div>
      </div>
    </div>
    <div class="col-10">
      <div class="row">
        <div class="col-5" style="height: 400px">
          <div class="row" style="height: 10%; font-family: Vazir">
            <div class="col"></div>
            <div class="col"></div>
          </div>
          <div class="row" style="height: 10%">
            <div class="col-5" style="padding-right: 35px">
              <select
                v-model="class_id"
                class="form-select"
                style="font-size: 12px; width: 100%"
              >
                <option selected value="">انتخاب کلاس:</option>
                <option
                  v-for="(item, index) in classes"
                  :key="index"
                  :value="item.id"
                >
                  {{ item.name }}
                </option>
              </select>
            </div>
            <div class="col-4" style="padding-right: 35px">
              <select
                v-if="letLesson"
                v-model="lesson_id"
                class="form-select"
                style="font-size: 12px; font-family: Vazir"
              >
                <option selected value="">درس:</option>
                <option
                  v-for="(item, index) in lessons2"
                  :key="index"
                  :value="item.id"
                >
                  {{ item.lesson_name }}
                </option>
              </select>
            </div>
            <div class="col-3">
              <button
                v-if="reportCommon"
                type="button"
                class="btn btn-primary button-class"
                style="
                  width: 75px;
                  height: 30px;
                  margin-top: 1px;
                  padding-top: 2px;
                "
                @click="searchList"
              >
                جستجو
                <div
                  v-if="buttonLoading"
                  class="spinner-border spinner-grow-sm"
                  role="status"
                ></div>
              </button>
            </div>
          </div>
          <div class="row" style="height: 80%">
            <div class="col">
              <StudentList @graphicShow="graphicReport" :formData2="users" />
            </div>
          </div>
        </div>

        <div v-if="report1" class="col-7 pt-5" style="height: 400px">
          <ReportOne
            :LessonList="lessons"
            :ScoresList="scores"
            :ScoresList2="scores2"
          />
        </div>
        <div v-if="report2" class="col-7 pt-5" style="height: 400px">
          <ReportTwo
            :ScoresList1="scores3"
            :ScoresList2="scores4"
            :ExamList="exam_ids"
          />
        </div>
        <div v-if="report3" class="col-7 pt-5" style="height: 400px">
          <ReportThree
            :LessonList="lessons"
            :ScoresList="scores"
            :ScoresList2="scores2"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ReportOne from "./ReportOne.vue";
import ReportTwo from "./ReportTwo.vue";
import ReportThree from "./ReportThree.vue";
import StudentList from "./StudentsListComponent.vue";
import { ref, watch } from "vue";
import axios from "axios";
export default {
  components: {
    ReportOne,
    ReportTwo,
    ReportThree,
    StudentList,
  },
  setup() {
    const classes = ref([]);
    const scores = ref([]);
    const scores2 = ref([]);
    const scores3 = ref([]);
    const scores4 = ref([]);
    const exam_ids = ref([]);
    const lessons = ref([]);
    const lessons2 = ref([]);
    const lesson_ids = ref([]);
    const users = ref([]);
    const class_id = ref("");
    const lesson_name = ref("");
    const letGraphicShow = ref(false);
    const f_name = ref("");
    const l_name = ref("");
    const grade_id = ref("");
    const user_id = ref("");
    const lesson_id = ref("");
    const report1 = ref(true);
    const report2 = ref(false);
    const report3 = ref(false);
    const btn1 = ref(false);
    const btn2 = ref(false);
    const btn3 = ref(false);
    const reportCommon = ref(false);
    const letLesson = ref(false);

    function getClasses() {
      axios
        .get("http://127.0.0.1:8000/api/school/classroom/classrooms")
        .then(function (response) {
          // handle success
          classes.value = response.data;
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
    }
    getClasses();
    function searchList() {
      axios
        .get(
          `http://127.0.0.1:8000/api/school/user/users-view-by-class-id/${class_id.value}`
        )
        .then(function (response) {
          // handle success
          users.value = response.data;
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
    }
    function graphicReport(graphicShow) {
      letGraphicShow.value = graphicShow[0];
      f_name.value = graphicShow[1];
      l_name.value = graphicShow[2];
      grade_id.value = graphicShow[3];
      user_id.value = graphicShow[4];
      reportCommon.value = false;
      // if (report2.value) {
      //   letLesson.value = true;
      // }
      axios
        .get(
          `http://127.0.0.1:8000/api/school/lesson/lessons/${grade_id.value}`
        )
        .then(function (response) {
          lessons.value = response.data.map((item) => item.lesson_name);
        })
        .catch(function (error) {
          console.log(error);
        });

      axios
        .get(
          `http://127.0.0.1:8000/api/school/exam-user/user/${grade_id.value}/${user_id.value}`
        )
        .then(function (response) {
          scores.value = response.data[0];
          scores2.value = response.data[1];
        })
        .catch(function (error) {
          console.log(error);
        });
      axios
        .get(
          `http://127.0.0.1:8000/api/school/lesson/lessons/${grade_id.value}`
        )
        .then(function (response) {
          // handle success
          lessons2.value = response.data;
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });

      axios
        .get(
          `http://127.0.0.1:8000/api/school/exam-user/user/${grade_id.value}/${user_id.value}/${lesson_id.value}`
        )
        .then(function (response) {
          scores3.value = response.data[0];
          scores4.value = response.data[1];
          exam_ids.value = response.data[2];
        })
        .catch(function (error) {
          console.log(error);
        });
    }

    function barchart() {
      btn1.value = "class2";
      btn2.value = "class1";
      btn3.value = "class1";
      report1.value = true;
      report2.value = false;
      report3.value = false;
      letLesson.value = false;
      //reportCommon.value = false;
    }
    function linechart() {
      btn1.value = "class1";
      btn2.value = "class2";
      btn3.value = "class1";
      report2.value = true;
      report1.value = false;
      report3.value = false;
      letLesson.value = true;
      // reportCommon.value = false;
    }
    function radarchart() {
      btn1.value = "class1";
      btn2.value = "class1";
      btn3.value = "class2";
      report3.value = true;
      report1.value = false;
      report2.value = false;
      letLesson.value = false;
      //reportCommon.value = false;
    }

    watch(
      () => class_id.value,
      () => {
        reportCommon.value = true;
      }
    );
    watch(
      () => lesson_id.value,
      () => {
        reportCommon.value = false;
      }
    );

    return {
      lessons,
      lessons2,
      lesson_ids,
      lesson_id,
      lesson_name,
      classes,
      class_id,
      searchList,
      users,
      letGraphicShow,
      graphicReport,
      f_name,
      l_name,
      grade_id,
      scores,
      scores2,
      scores3,
      scores4,
      user_id,
      exam_ids,
      report1,
      report2,
      report3,
      reportCommon,
      letLesson,
      barchart,
      linechart,
      radarchart,
      btn1,
      btn2,
      btn3,
    };
  },
};
</script>
<style scoped>
.class1 {
  background-color: rgb(31, 158, 212);
}
.class2 {
  background-color: rgb(38, 46, 35);
}
</style>
