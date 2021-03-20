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
      <el-form-item>
        <el-button type="primary" @click="onSubmit">Save</el-button>
      </el-form-item>
    </el-form>

    <el-divider><i class="el-icon-star-on"></i></el-divider>

    <el-row>
      <el-col :span="4"></el-col>
      <el-col :span="16">
        <el-table
          :data="questions"
          stripe
          style="width: 100%"
          :row-class-name="tableRowClassName"
        >
          <el-table-column prop="name" label="Name"> </el-table-column>
          <el-table-column prop="asked" label="Asked"> </el-table-column>
          <el-table-column prop="success" label="Success"> </el-table-column>
          <el-table-column label="Operations">
            <template #default="scope">
              <el-button
                size="mini"
                @click="handleEdit(scope.$index, scope.row)"
                type="primary" icon="el-icon-edit"
                >Edit</el-button
              >
              <el-popconfirm title="Are you sure to delete this?" @confirm="handleDelete(scope.$index, scope.row)">
                <template #reference>
                  <el-button size="mini" type="danger" icon="el-icon-delete">Delete</el-button>
                </template>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
      </el-col>
      <el-col :span="4"></el-col>
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
      asked: 0,
      success: 0,
      questions: [],
    };
  },
  methods: {
    handleEdit(index) {
      this.id = this.questions[index].id;
      this.name = this.questions[index].name;
      this.answer = this.questions[index].answer;
      this.link = this.questions[index].link;
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
          this.asked = 0;
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
  },
  mounted() {
    this.getQuestions();
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
