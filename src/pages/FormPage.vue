<template>
  <q-page class="flex justify-center row">
    <div class="col-12">
      <q-stepper
        v-model="step"
        ref="stepper"
        contracted
        color="primary"
        animated
        style="height: 100%"
      >
        <q-step
          class="col-12"
          :name="1"
          title="Create an ad group"
          caption="Optional"
          icon="create_new_folder"
          :done="step > 1"
        >
          <div class="flex flex-center column">
            <div class="row inline" style="width: 100%">
              <q-select
                v-model="preferred"
                :options="optionsID"
                dense
                outlined
                :rules="[(val) => noEmpty(val)]"
              />

              <q-input
                dense
                outlined
                v-model.trim="idNumber"
                @keypress="isNumber($event)"
                label="Inserte su cédula"
                :mask="automatedMask"
                style="width: 80%"
                class="q-ml-xs"
                :rules="[(val) => noEmpty(val)]"
                :disable="preferred === null || preferred === ''"
              />
            </div>
            <q-stepper-navigation>
              <q-btn
                @click="$refs.stepper.next()"
                color="primary"
                label="Siguiente"
                :disable="idNumber === null"
              />
            </q-stepper-navigation>
          </div>
        </q-step>

        <q-step
          class="col-12"
          :name="2"
          title="Create an ad group"
          caption="Optional"
          icon="create_new_folder"
          :done="step > 2"
        >
          <div class="flex flex-center column">
            <q-select
              class="q-my-xl"
              label="Tipo de mensaje"
              transition-show="jump-up"
              transition-hide="jump-up"
              filled
              v-model="inputType"
              :options="options1"
              style="width: 300px"
            />
            <q-stepper-navigation>
              <q-btn
                @click="$refs.stepper.next()"
                color="primary"
                label="Siguiente"
                :disable="inputType === null"
              />
              <q-btn
                v-if="step > 1"
                flat
                color="primary"
                @click="$refs.stepper.previous()"
                label="Atras"
                class="q-ml-sm"
              />
            </q-stepper-navigation>
          </div>
        </q-step>

        <q-step
          class="col-12"
          :name="3"
          title="Select campaign settings"
          icon="settings"
          :done="step > 3"
        >
          <div class="flex flex-center column">
            <q-select
              class="q-my-xl"
              filled
              v-model="denunce"
              label="Seleccione o Filtre un proyecto"
              :options="options"
              emit-value
              map-options
              style="width: 300px"
            >
            </q-select>
            <q-stepper-navigation class="flex flex-center">
              <q-btn
                @click="$refs.stepper.next()"
                color="primary"
                label="Siguiente"
                :disable="denunce === null"
              />
              <q-btn
                v-if="step > 2"
                flat
                color="primary"
                @click="$refs.stepper.previous()"
                label="Atras"
                class="q-ml-sm"
              />
            </q-stepper-navigation>
          </div>
        </q-step>

        <q-step
          class="col-12"
          :name="4"
          title="Create an ad"
          icon="add_comment"
        >
          <div class="flex flex-center column">
            <div style="width: 100%">
              <q-input
                class="q-my-xs"
                v-model="commentA"
                label="Deje su mensaje..."
                type="textarea"
                outlined
              />
            </div>
            <q-stepper-navigation class="flex flex-center">
              <q-btn
                @click="insert() && $router.push('/EndPage')"
                color="primary"
                label="Enviar"
                :disable="commentA === ''"
              />
              <q-btn
                v-if="step > 3"
                flat
                color="primary"
                @click="$refs.stepper.previous()"
                label="Atras"
                class="q-ml-sm"
              />
            </q-stepper-navigation>
          </div>
        </q-step>

        <template v-slot:message>
          <q-banner
            v-if="step === 1"
            class="bg-green-8 text-white text-subtitle1 q-px-lg"
            style="text-shadow: 1px 1px black"
          >
            Seleccione el tipo mensaje a comunicar:
          </q-banner>
          <q-banner
            @click="prueba()"
            v-else-if="step === 2"
            class="bg-green-8 text-white text-subtitle1 q-px-lg"
            style="text-shadow: 1px 1px black"
          >
            Seleccione el proyecto al cual esta dedicando el mensaje:
          </q-banner>
          <q-banner
            v-else-if="step === 3"
            class="bg-green-8 text-white text-subtitle1 q-px-lg"
            style="text-shadow: 1px 1px black"
          >
            Redacte el mensaje a comunicar:
          </q-banner>
        </template>
      </q-stepper>
    </div>
  </q-page>
</template>

<script>
import { ref } from "vue";
import { defineComponent } from "vue";
import { reactive, computed } from "vue";
import axios from "axios";
import Swal from "sweetalert2";
import { useQuasar } from "quasar";

const mask = ref(null);
const preferred = ref("");
const idNumber = ref(null);

const automatedMask = computed(() => {
  if (preferred.value === "") {
    mask.value = "";
    idNumber.value = null;
  }
  if (preferred.value === "V") {
    mask.value = "########";
    idNumber.value = null;
  }
  if (preferred.value === "E") {
    mask.value = "##########";
    idNumber.value = null;
  }
  if (preferred.value === "J") {
    mask.value = "#########";
    idNumber.value = null;
  }
  return mask.value;
});

const personsArray = [];
const usersArray = [];
//const stringOptions = ["Google", "Facebook", "Twitter", "Apple", "Oracle"];

export default defineComponent({
  name: "FormPage",
  beforeMount() {
    axios.get("http://localhost:5000/persons").then(function (response) {
      response.data.forEach((element) => {
        personsArray.push(element);
      });
      console.log(personsArray);
      //console.log(projectArray[0].projectName);
    });
    axios.get("http://localhost:5000/users").then(function (response) {
      response.data.forEach((element) => {
        usersArray.push(element);
      });
      console.log(usersArray);
      //console.log(projectArray[0].projectName);
    });
  },
  setup() {
    //const options = ref(projectArray);
    const commentA = ref("");
    //const projectID = ref(null);
    const mailboxID = ref(null);

    //const options = ref(projectArray)

    return {
      //module: projectArray,
      userID: ref(null),
      personID: ref(null),
      denunce: ref(null),
      mailboxID,
      step: ref(1),
      inputType: ref(null),
      //model2: ref(null),
      commentA,
      isNumber(e) {
        let char = String.fromCharCode(e.keyCode); // Get the character
        if (/^[0-9]+$/i.test(char)) return true; // Match with regex
        else e.preventDefault(); // If not match, don't add to input text
      },
      idNumber,
      automatedMask,
      preferred,
      optionsID: ["", "V", "E", "J"],

      /*onSubmit(values) {
        console.log(JSON.stringify(values, null, 2));
      },*/
      noEmpty(value) {
        // if the field is empty
        if (!value) {
          return "Debe Rellenar este campo.";
        }

        // All is good
        return true;
      },

      options: [
        "Desviación de la asistencia",
        "Fraude",
        "Extorsión",
        "Explotación o Abuso Sexual",
        "Trato deficiente en la distribucion",
        "Distibuciones no cumples las condiciones adecuadas",
        "Otro",
      ],

      //stringOptions,

      options1: [
        "Comentario",
        "Sugerencia",
        "Queja",
        "Reclamo",
        "Denuncia",
        "inquietudes",
      ],

      /*filterFn(val, update) {
        if (val === "") {
          update(() => {
            projectArray.forEach((element) => {
              options.value.projectName = element.projectName;
            });
          });
          return;
        }

        update(() => {
          const needle = val.toLowerCase();
          options.value.projectName = stringOptions.filter(
            (v) => v.toLowerCase().indexOf(needle) > -1
          );
        });
      },*/
      async insert() {
        personsArray.forEach((element) => {
          if (preferred.value.concat("-", idNumber.value) === element.cedula) {
            personID.value = element.id;
          }
        });

        usersArray.forEach((element) => {
          if (personID.value === element.id) {
            userID.value = element.id;
          }
        });

        await axios
          .post("http://localhost:5000/mailbox", {
            inputType: inputType.value,
            inputStatus: "Recibido",
          })
          .then(function (response) {
            console.log(response, "Esto es mailbox");
          })
          .catch(function (error) {
            console.log(error, "error en mailbox");
            /*$q.notify({
              type: "negative",
              message: "Error al registrar Usuario.",
              caption: "Por favor, revise los datos a ingresar.",
            });*/
          });
        await axios
          .post("http://localhost:5000/association_one", {
            commentA: commentA.value,
            //project: projectID.value,
            users: userID.value,
            persons: personID.value,
            mailbox: mailboxID.value,
          })
          .then(function (response) {
            console.log(response, "Esto es mailbox");
            Swal.fire({
              icon: "success",
              title: `Mensaje enviado con exito`,
              showConfirmButton: false,
              timer: 5000,
              position: "bottom-end",
              timerProgressBar: true,
              toast: true,
              showCloseButton: true,
            });
            window.location.href = "http://localhost:9080/EndPage";
          })
          .catch(function (error) {
            console.log(error, "error en mailbox");
            Swal.fire({
              icon: "error",
              title: "Ha ocurrido un error al enviar el mensaje",
              showConfirmButton: false,
              timer: 5000,
              position: "bottom-end",
              timerProgressBar: true,
              toast: true,
              showCloseButton: true,
            });
          });
      },
    };
  },
});
</script>
