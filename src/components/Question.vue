<template>
  <el-container style="border: 1px solid #eee">
    <el-header>
      <h1>Questions</h1>
    </el-header>
    <el-form ref="form" label-width="150px" label-position="right">
      <el-form-item label="Name">
        <el-input v-model="name"></el-input>
      </el-form-item>
      <el-form-item label="Description">
        <el-input type="textarea" v-model="answer"></el-input>
      </el-form-item>
      <el-form-item label="Link">
        <el-input type="url" v-model="link"></el-input>
      </el-form-item>
      <el-row>
        <el-col span="4">
        <el-form-item label="Technique">
          <el-select
            v-model="techniqueId"
            placeholder="please select the related technique"
          >
            <el-option
              v-for="technique in techniques"
              :key="technique.id"
              :label="technique.name"
              :value="technique.id"
            >
            </el-option>
          </el-select>
        </el-form-item>
        </el-col>
      </el-row>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">Save</el-button>
      </el-form-item>
    </el-form>

    <el-divider><i class="el-icon-star-on"></i></el-divider>

    <el-row>
      <el-col :span="24">
        <el-table
          :data="questions"
          stripe
          style="width: 100%"
          :row-class-name="tableRowClassName"
        >
          <el-table-column prop="name" label="Name" width="1400px">
          </el-table-column>
          <el-table-column prop="asked" label="Asked" width="100px">
          </el-table-column>
          <el-table-column prop="success" label="Success" width="100px">
          </el-table-column>
          <el-table-column label="Operations" width="200px">
            <template #default="scope">
              <el-button
                size="mini"
                @click="handleEdit(scope.$index, scope.row)"
                type="primary"
                icon="el-icon-edit"
                >Edit</el-button
              >
              <el-popconfirm
                title="Are you sure to delete this?"
                @confirm="handleDelete(scope.$index, scope.row)"
              >
                <template #reference>
                  <el-button size="mini" type="danger" icon="el-icon-delete"
                    >Delete</el-button
                  >
                </template>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
      </el-col>
    </el-row>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      id: null,
      name: "",
      answer: "",
      link: "",
      techniqueId: null,
      asked: 0,
      success: 0,
      questions: [],
      techniques: [],
    };
  },
  methods: {
    handleEdit(index) {
      this.id = this.questions[index].id;
      this.name = this.questions[index].name;
      this.answer = this.questions[index].answer;
      this.link = this.questions[index].link;
      if (this.questions[index].techniqueId) {
        this.techniqueId = this.questions[index].techniqueId;
      }
      this.asked = this.questions[index].asked;
      this.success = this.questions[index].success;
    },
    handleDelete(index) {
      fetch(
        "https://bjj-app-30893-default-rtdb.firebaseio.com/questions/" +
          this.questions[index].id +
          ".json",
        {
          method: "DELETE",
          headers: {
            "Content-Type": "application/json",
          },
        }
      ).then(() => {
        this.name = "";
        this.answer = "";
        this.link = "";
        (this.techniqueId = null), (this.asked = 0);
        this.success = 0;
        this.getQuestions();
      });
    },
    getQuestions() {
      fetch(
        "https://bjj-app-30893-default-rtdb.firebaseio.com/questions.json",
        {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
          },
        }
      )
        .then((response) => response.json())
        .then((data) => {
          this.questions = [];
          for (const id in data) {
            let question = data[id];
            question.id = id;
            this.questions.push(question);
          }
        });
    },
    tableRowClassName({ rowIndex }) {
      if (rowIndex === 1) {
        return "warning-row";
      } else if (rowIndex === 3) {
        return "success-row";
      }
      return "";
    },
    onSubmit() {
      if (this.id != null) {
        fetch(
          "https://bjj-app-30893-default-rtdb.firebaseio.com/questions/" +
            this.id +
            ".json",
          {
            method: "PUT",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              name: this.name,
              answer: this.answer,
              asked: this.asked,
              success: this.success,
              link: this.link,
              techniqueId: this.techniqueId,
            }),
          }
        ).then(() => {
          this.name = "";
          this.answer = "";
          this.link = "";
          this.techniqueId = null;
          this.asked = 0;
          this.success = 0;
          this.getQuestions();
        });
      } else {
        fetch(
          "https://bjj-app-30893-default-rtdb.firebaseio.com/questions.json",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              name: this.name,
              answer: this.answer,
              link: this.link,
              techniqueId: this.techniqueId,
              asked: 0,
              success: 0,
            }),
          }
        ).then(() => {
          this.name = "";
          this.answer = "";
          this.link = "";
          this.asked = 0;
          this.success = 0;
          this.getQuestions();
        });
      }
    },
    getTechniques() {
      fetch(
        "https://bjj-app-30893-default-rtdb.firebaseio.com/techniques.json",
        {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
          },
        }
      )
        .then((response) => response.json())
        .then((data) => {
          this.techniques = [];
          for (const id in data) {
            let technique = data[id];
            technique.id = id;
            this.techniques.push(technique);
          }
        });
    },
  },
  mounted() {
    this.getQuestions();
    this.getTechniques();
  },
};
</script>

<style scoped>
.el-table .warning-row {
  background: oldlace;
}

.el-table .success-row {
  background: #f0f9eb;
}
</style>
