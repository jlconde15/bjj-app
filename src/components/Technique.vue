<template>
  <el-container style="border: 1px solid #eee">
    <el-header>
      <h1>BJJ Technique</h1>
    </el-header>
    <el-main>
      <el-form
        ref="form"
        :model="form"
        label-width="150px"
        label-position="right"
      >
        <el-form-item label="Name">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="Description">
          <el-input
            type="textarea"
            v-model="form.description"
            rows="10"
          ></el-input>
        </el-form-item>
        <el-form-item label="Links">
          <el-input type="textarea" v-model="form.links"></el-input>
        </el-form-item>
        <el-row>
          <el-col span="4">
            <el-form-item label="Available for">
              <el-checkbox-group v-model="form.available">
                <el-checkbox label="Gi" name="available"></el-checkbox>
                <el-checkbox label="No Gi" name="available"></el-checkbox>
              </el-checkbox-group>
            </el-form-item>
          </el-col>
          <el-col span="4">
            <el-form-item label="Category">
              <el-select
                v-model="form.category"
                placeholder="please select the category"
              >
                <el-option
                  v-for="category in categories"
                  :key="category"
                  :label="category"
                  :value="category"
                >
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col span="8">
            <el-form-item label="My Position">
              <el-select
                v-model="form.myPosition"
                placeholder="Select"
                style="width: 100%;"
              >
                <el-option-group
                  v-for="group in positions"
                  :key="group.label"
                  :label="group.label"
                >
                  <el-option
                    v-for="item in group.options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  >
                  </el-option>
                </el-option-group>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col span="8">
            <el-form-item label="Opponent Position">
              <el-select
                v-model="form.opponentPosition"
                placeholder="Select"
                style="width: 100%;"
              >
                <el-option-group
                  v-for="group in positions"
                  :key="group.label"
                  :label="group.label"
                >
                  <el-option
                    v-for="item in group.options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  >
                  </el-option>
                </el-option-group>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <el-collapse>
        <el-collapse-item title="Steps">
          <el-container>
            <aside style="width: 700px">
              <el-table
                :data="steps"
                :default-sort="{ prop: 'order', order: 'asccending' }"
                stripe
                style="width: 700px"
              >
                <el-table-column prop="order" label="Order" width="80">
                </el-table-column>
                <el-table-column
                  prop="description"
                  label="Description"
                  width="450"
                >
                </el-table-column>
                <el-table-column label="Operations" width="120">
                  <template #default="scope">
                    <el-button
                      size="mini"
                      @click="editStep(scope.$index, scope.row)"
                      type="primary"
                      icon="el-icon-edit"
                    ></el-button>
                    <el-button
                      size="mini"
                      @click="deleteStep(scope.$index, scope.row)"
                      type="danger"
                      icon="el-icon-delete"
                    ></el-button>
                  </template>
                </el-table-column>
              </el-table>
            </aside>
            <main style="width: 1200px">
              <el-form :inline="true" :model="formStep" label-position="top">
                <el-form-item label="Order" style="width: 80px;">
                  <el-input v-model="formStep.order"></el-input>
                </el-form-item>
                <el-form-item
                  label="Description"
                  style="width: 850px; margin-left: 20px"
                >
                  <el-input
                    type="textarea"
                    v-model="formStep.description"
                  ></el-input>
                </el-form-item>
                <el-form-item style="margin-top:70px">
                  <el-button type="primary" @click="addStep"
                    >Add Step</el-button
                  >
                </el-form-item>
              </el-form>
            </main>
          </el-container>
        </el-collapse-item>
      </el-collapse>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">Create</el-button>
        <el-button @click="clearForm">Cancel</el-button>
      </el-form-item>
    </el-main>
    <el-row>
      <el-col :span="24"
        ><div class="grid-content bg-purple-dark">Techniques</div></el-col
      >
    </el-row>
    <el-row>
      <el-col :span="24">
        <el-table
          :data="techniques"
          stripe
          style="width: 100%"
          :row-class-name="tableRowClassName"
        >
          <el-table-column prop="name" label="Name"> </el-table-column>
          <el-table-column label="Operations">
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
      steps: [],
      techniques: [],
      categories: [
        "Takedown",
        "Escape",
        "Submission",
        "Choke",
        "Sweep",
        "Mount",
        "Side control",
        "Back Take",
        "Tourtle",
      ],
      positions: [
        {
          label: "doing...",
          options: [
            {
              value: "doing Side control",
              label: "doing Side control",
            },
            {
              value: "doing Mount",
              label: "doing Mount",
            },
            {
              value: "standing",
              label: "standing",
            },
            {
              value: "tourtle",
              label: "tourtle",
            },
          ],
        },
        {
          label: "in...",
          options: [
            {
              value: "in Side control",
              label: "in Side control",
            },
            {
              value: "in Mount",
              label: "in Mount",
            },
            {
              value: "tourtle",
              label: "tourtle",
            },
          ],
        },
      ],
      formStep: {
        id: null,
        order: 1,
        description: "",
      },
      form: {
        name: "",
        description: "",
        available: [],
        myPosition: "",
        opponentPosition: "",
        links: "",
      },
    };
  },
  methods: {
    deleteStep(index, row) {
      this.steps = this.steps.filter((step) => step.id !== row.id);
      this.formStep.order = this.steps.length;
    },
    editStep(index, row) {
      this.formStep = { ...row };
    },
    addStep() {
      let step = this.steps.find((step) => step.id == this.formStep.id);
      if (step) {
        step.order = this.formStep.order;
        step.description = this.formStep.description;
      } else {
        this.steps.push({
          id: Date.now(),
          order: this.formStep.order,
          description: this.formStep.description,
        });
      }
      this.formStep.id = null;
      this.formStep.order = this.steps.length + 1;
      this.formStep.description = null;
    },
    onSubmit() {
      const body = this.form;
      body.steps = this.steps;
      if (this.id != null) {
        fetch(
          "https://bjj-app-30893-default-rtdb.firebaseio.com/techniques/" +
            this.id +
            ".json",
          {
            method: "PUT",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(body),
          }
        ).then(() => {
          this.clearForm();
          this.getTechniques();
        });
      } else {
        fetch(
          "https://bjj-app-30893-default-rtdb.firebaseio.com/techniques.json",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(body),
          }
        ).then(() => {
          this.clearForm();
          this.getTechniques();
        });
      }
    },
    clearForm() {
      this.id = null;
      this.form.name = "";
      this.form.description = "";
      this.form.available = [];
      this.form.category = "";
      this.form.myPosition = "";
      this.form.opponentPosition = "";
      this.form.links = "";
      this.steps = [];
      this.formStep.order = 1;
    },
    handleEdit(index) {
      console.log(this.techniques[index]);
      this.id = this.techniques[index].id;
      this.form = { ...this.techniques[index] };
      this.steps = { ...this.techniques[index].steps };
      this.steps = [];
      for (const id in this.techniques[index].steps) {
        let step = this.techniques[index].steps[id];
        step.id = id;
        this.steps.push(step);
      }
      this.formStep.order = this.steps.length + 1;
    },
    handleDelete(index) {
      fetch(
        "https://bjj-app-30893-default-rtdb.firebaseio.com/techniques/" +
          this.techniques[index].id +
          ".json",
        {
          method: "DELETE",
          headers: {
            "Content-Type": "application/json",
          },
        }
      ).then(() => {
        this.form.name = "";
        this.form.description = "";
        this.form.available = [];
        this.form.myPosition = "";
        this.form.opponentPosition = "";
        this.steps = [];
        this.getTechniques();
      });
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
    this.getTechniques();
  },
};
</script>

<style scoped>
.el-row {
  margin-bottom: 20px;
}
.el-col {
  border-radius: 4px;
}
.bg-purple-dark {
  background: #99a9bf;
}
.bg-purple {
  background: #d3dce6;
}
.bg-purple-light {
  background: #e5e9f2;
}
.grid-content {
  border-radius: 4px;
  min-height: 36px;
  padding-top: 10px;
}
.row-bg {
  padding: 10px 0;
  background-color: #f9fafc;
}
</style>
