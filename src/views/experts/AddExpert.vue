<template>
  <section class="page-section">
    <b-container>
      <HeaderPage title="Adicionar Especialista" />

      <!--FORM-->
      <b-row>
        <b-col cols="2"></b-col>
        <b-col cols="8">
          <form @submit.prevent="add">
            <div class="form-group">
              <input
                v-model="name"
                type="text"
                class="form-control"
                id="txtName"
                placeholder="Escreve o nome do especialista"
                required
              />
            </div>
            <div class="form-group">
              <input
                v-model="profession"
                type="text"
                class="form-control"
                id="txtProfession"
                placeholder="Indica a profissão do especialista"
                required
              />
            </div>
            <div class="form-group">
              <input
                v-model="yearsOfExperience"
                type="number"
                min="1"
                class="form-control"
                id="numYears"
                placeholder="Indica os anos de experiência"
                required
              />
            </div>

            <button type="submit" class="btn btn-outline-success mr-2">
              <i class="fas fa-save"></i> GRAVAR ESPECIALISTA
            </button>
            <router-link
              :to="{ name: 'listExperts' }"
              tag="button"
              class="btn btn-outline-danger"
              ><i class="fas fa-window-close"></i> CANCELAR</router-link
            >
          </form>
        </b-col>
        <b-col cols="2"></b-col>
      </b-row>
    </b-container>
  </section>
</template>

<script>
import { ADD_EXPERT } from "@/store/experts/expert.constants";
import router from "@/router";
import HeaderPage from "@/components/HeaderPage.vue";
import { mapGetters } from "vuex";

export default {
  name: "AddExpert",
  components: {
    HeaderPage
  },
  data: () => {
    return {
      name: "",
      profession: "",
      yearsOfExperience: 0
    };
  },
  computed: {
    ...mapGetters("expert", ["getMessage"])
  },
  methods: {
    add() {
      this.$store.dispatch(`expert/${ADD_EXPERT}`, this.$data).then(
        () => {
          this.$alert(this.getMessage, "Especialista adicionado!", "success");
          router.push({ name: "listExperts" });
        },
        err => {
          this.$alert(`${err.message}`, "Erro", "error");
        }
      );
    }
  }
};
</script>
