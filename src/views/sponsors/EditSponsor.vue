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
                v-model="sponsor.name"
                type="text"
                class="form-control"
                id="txtName"
                placeholder="Escreve o nome do patrocinador"
                required
              />
            </div>
            <div class="form-group">
              <input
                v-model="sponsor.level"
                type="text"
                class="form-control"
                id="txtLevel"
                placeholder="Indica o nível do patrocinador"
                required
              />
            </div>
            <div class="form-group">
              <input
                v-model="sponsor.contributions"
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
              :to="{ name: 'listSponsors' }"
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
import { EDIT_SPONSOR } from "@/store/sponsors/sponsor.constants";
import router from "@/router";
import HeaderPage from "@/components/HeaderPage.vue";
import { mapGetters } from "vuex";

export default {
  name: "EditSponsor",
  components: {
    HeaderPage
  },
  data: () => {
    return {
      sponsor: {}
    };
  },
  computed: {
    ...mapGetters("sponsor", ["getSponsorById", "getMessage"])
  },
  methods: {
    update() {
      this.$store.dispatch(`sponsor/${EDIT_SPONSOR}`, this.$data.sponsor).then(
        () => {
          this.$alert(this.getMessage, "Patrocinador atualizado!", "success");
          router.push({ name: "listSponsors" });
        },
        err => {
          this.$alert(`${err.message}`, "Erro", "error");
        }
      );
    }
  },
  created() {
    const sponsor = this.getSponsorById(this.$route.params.sponsorId);
    if (sponsor) {
      this.sponsor = sponsor;
    } else {
      this.$alert("Patrocinador não encontrado!", "Erro", "error");
      router.push({ name: "listSponsors" });
    }
  }
};
</script>
