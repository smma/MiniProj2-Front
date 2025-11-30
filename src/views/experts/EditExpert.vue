<template>
  <section class="page-section">
    <b-container>
      <HeaderPage title="Editar Patrocinador" />

      <!--FORM-->
      <b-row>
        <b-col cols="2"></b-col>
        <b-col cols="8">
          <form @submit.prevent="update">
            <div class="form-group">
              <input
                v-model="expert.name"
                type="text"
                class="form-control"
                id="txtName"
                placeholder="Escreve o nome do patrocinador"
                required
              />
            </div>
            <div class="form-group">
              <input
                v-model="expert.profession"
                type="text"
                class="form-control"
                id="txtLevel"
                placeholder="Indica o nível do patrocinador"
                required
              />
            </div>
            <div class="form-group">
              <input
                v-model="expert.yearsOfExperience"
                type="number"
                min="1"
                class="form-control"
                id="numContributions"
                placeholder="Indica o valor das contribuições"
                required
              />
            </div>

            <button type="submit" class="btn btn-outline-success mr-2">
              <i class="fas fa-save"></i> ATUALIZAR
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
import { EDIT_EXPERT } from "@/store/experts/expert.constants";
import router from "@/router";
import HeaderPage from "@/components/HeaderPage.vue";
import { mapGetters } from "vuex";

export default {
  name: "EditExpert",
  components: {
    HeaderPage
  },
  data: () => {
    return {
      expert: {}
    };
  },
  computed: {
    ...mapGetters("expert", ["getExpertById", "getMessage"])
  },
  methods: {
    update() {
      this.$store.dispatch(`expert/${EDIT_EXPERT}`, this.$data.expert).then(
        () => {
          this.$alert(this.getMessage, "Especialista atualizado!", "success");
          router.push({ name: "listExperts" });
        },
        err => {
          this.$alert(`${err.message}`, "Erro", "error");
        }
      );
    }
  },
  created() {
    this.expert = this.getExpertById(this.$route.params.expertId);
  }
};
</script>
