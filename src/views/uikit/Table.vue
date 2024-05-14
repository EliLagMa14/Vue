<template>
  <div class="grid">
    <div class="col-12">
      <div class="card">
        <panel header="Datos de los Alumnos" style="height: 100%">
          <di class="col-12">
            <div class="card">
              <div class="flex justify-content-between flex-column sm:flex-row">
                <InputText
                  v-model="nombreEstudiante"
                  type="text"
                  placeholder="Nombre del estudiante"
                />
                <InputText v-model="direccion" type="text" placeholder="direccion" />
                <InputText v-model="telefono" type="number" placeholder="telefono" />
                <br />
                <Button
                  type="button"
                  icon="pi pi-check"
                  label="Registrar"
                  severity="help"
                  @click="RegistrarE()"
                />
              </div>
            </div>
          </di>
          <DataTable
            v-bind="value"
            :value="estudiantes"
            showGridlines
            paginator
            :rows="5"
            tableStyle="min-width: 50rem"
          >
            <!-- :rowsPerPageOptions="[5, 10, 20, 50]" -->
            <Column field="id" header="ID" sortable style="width: 20px" />
            <Column field="nombre" header="NOMBRE" style="width: 550px" />
            <Column field="direccion" header="DIRECCION" style="width: 110px" />
            <Column field="telefono" header="TELEFONO" style="width: 110px" />
            <Column header="ACCIONES" :exportable="false" style="min-width: 8rem">
              <template #body="slotProps">
                <Button
                  icon="pi pi-pencil"
                  outlined
                  rounded
                  class="mr-2"
                  @click="editAlumno(slotProps.data)"
                />
                <Button
                  icon="pi pi-trash"
                  outlined
                  rounded
                  severity="danger"
                  @click="confirmDeleteAlumno(slotProps.data.id)"
                />
              </template>
            </Column>
          </DataTable>
        </panel>

        <Dialog
          v-model:visible="editDialogVisible"
          :style="{ width: '450px' }"
          header="Editar Estudiante"
          :modal="true"
          class="p-fluid"
        >
          <div class="field">
            <label for="nombre">Nombre</label>
            <InputText
              v-model="editedStudent.nombre"
              type="text"
              placeholder="Nombre del Estudiante..."
            />
          </div>
          <div class="field">
            <label for="direccion">Direccion</label>
            <InputText
              v-model="editedStudent.direccion"
              type="text"
              placeholder="Direccion"
            />
          </div>
          <div class="field">
            <label for="telefono">Telefono</label>
            <InputText
              v-model="editedStudent.telefono"
              type="number"
              placeholder="Telefono"
            />
          </div>
          <template #footer>
            <Button label="Guardar" icon="pi pi-save" @click="saveStudent" />
            <Button
              label="Cancelar"
              icon="pi pi-times"
              class="p-button-secondary"
              @click="cancelEdit"
            />
          </template>
        </Dialog>
        <Toast />
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";

export default {
  data() {
    return {
      editDialogVisible: false,
      estudiantes: [],
      nombreEstudiante: null,
      direccion: null,
      telefono: null,
      editedStudent: {
        id: null,
        nombre: "",
        direccion: "",
        telefono: "",
      },
      message: "No has ingresado los campos correctos",
    };
  },
  mounted() {
    axios
      .get("http://localhost:8090/api/v1/estudiante", {
        headers: {
          "Access-Control-Allow-Origin": "*",
        },
      })
      .then((response) => {
        // console.log(response);
        this.estudiantes = response.data;
      })
      .catch((error) => {
        console.log(error);
      });
  },

  methods: {
    RegistrarE() {
      if (this.nombreEstudiante === null) {
        alert(this.message);
      } else {
        if (this.direccion === null) {
          alert(this.message);
        } else {
          if (this.telefono === null) {
            alert(this.message);
          } else {
            axios
              .post(`http://localhost:8090/api/v1/estudiante`, {
                nombre: this.nombreEstudiante,
                direccion: this.direccion,
                telefono: this.telefono,
              })
              .then((response) => {
                // console.log(response);
                this.estudiantes = response.data;
                location.reload();
              })
              .catch((error) => {
                console.log(error);
                z;
              });
          }
        }
      }
    },
    confirmDeleteAlumno(id) {
      axios
        .delete(`http://localhost:8090/api/v1/estudiante/${id}`)
        .then((response) => {
          location.reload();
        })
        .catch((error) => {
          console.log(error);
        });
    },
    editAlumno(student) {
      this.editedStudent = { ...student };
      this.editDialogVisible = true;
    },
    cancelEdit() {
      this.editDialogVisible = false;
    },
    saveStudent() {
      axios
        .post(`http://localhost:8090/api/v1/estudiante/${this.editedStudent.id}`, {
          nombre: this.editedStudent.nombre,
          direccion: this.editedStudent.direccion,
          telefono: this.editedStudent.telefono,
        })
        .then((response) => {
          console.log("Estudiante actualizado:", response.data);
          this.editDialogVisible = false;
          location.reload();
        })
        .catch((error) => {
          console.error("Error al actualizar estudiante:", error);
        });
    },
  },
};
</script>

<style scoped lang="scss">
@import "@/assets/demo/styles/badges.scss";
::v-deep(.p-datatable-frozen-tbody) {
  font-weight: bold;
}
::v-deep(.p-datatable-scrollable .p-frozen-column) {
  font-weight: bold;
}
</style>
