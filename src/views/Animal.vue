<template>
  <section class="page-section">
    <b-container>
      <HeaderPage :title="animal.name" />
      <b-row>
        <b-col>
          <b-card class="overflow-hidden" style="max-width: 1540px; ">
            <b-row>
              <b-col md="6" align="center">
                <b-card-img
                  :src="
                    photoLinks[currentImageIndex]
                      ? photoLinks[currentImageIndex].url
                      : ''
                  "
                  class="rounded-0 mb-2"
                ></b-card-img>
                <br />
                <b-button
                  variant="danger"
                  class="mr-2"
                  @click="previousImage()"
                  :disabled="photoLinks.length <= 1"
                >
                  <i class="fas fa-arrow-alt-circle-left"></i> </b-button
                >&nbsp;{{ currentImageIndex + 1 }} de
                {{ photoLinks.length }}&nbsp;
                <b-button
                  variant="danger"
                  class="mr-2"
                  @click="nextImage()"
                  :disabled="photoLinks.length <= 1"
                >
                  <i class="fas fa-arrow-alt-circle-right"></i>
                </b-button>
              </b-col>
              <b-col md="6">
                <b-card-body :title="animal.name + ' (' + animal.group + ')'">
                  <b-card-text align="left">
                    <!-- Level -->
                    <i class="fas fa-medal fa-lg ml-2"></i>
                    {{ animal.level }}
                    <!-- Evaluation -->
                    <i class="fas fa-star fa-lg ml-2"></i>
                    {{ (animal.evaluation || []).length }}
                    <!-- Comments -->
                    <i
                      class="fas fa-comment fa-lg ml-2"
                      @click="showComments()"
                    ></i>
                    {{ (animal.comments || []).length }}
                    <!-- Ranking -->
                    <i class="fas fa-clipboard-list fa-lg ml-2"></i>
                    3
                  </b-card-text>
                  <b-card-text>
                    <b>Descrição:</b>
                    {{ animal.description }}
                  </b-card-text>
                  <b-card-text>
                    <b>Comentários:</b>
                    <b-alert
                      show
                      variant="secondary"
                      v-for="comment in this.animal.comments"
                      :key="comment._id"
                    >
                      <b>{{ getNameById(comment.user) }}</b>
                      ({{ setCurrentDateTime(comment.date) }})
                      <br />
                      {{ comment.body }}
                    </b-alert>
                  </b-card-text>
                  <b-card-text>
                    <b>Comentário:</b>
                    <div class="form-group">
                      <textarea
                        id="txtDescription"
                        class="form-control"
                        placeholder="escreve descrição"
                        cols="30"
                        rows="3"
                        v-model="comment"
                        required
                      ></textarea>
                      <br />
                      <b-button
                        variant="danger"
                        class="mr-2"
                        @click="setComment()"
                      >
                        <i class="fas fa-comments"></i> COMENTAR
                      </b-button>
                      <b-button
                        variant="outline-success"
                        class="mr-2"
                        @click="showVideo(animal.links[1].url)"
                        :hidden="!animal.links[1].url"
                      >
                        <i class="fas fa-video"></i> VER VÍDEO
                      </b-button>

                      <router-link
                        :to="{ name: 'animals' }"
                        tag="b-button"
                        variant="outline-danger"
                        align="center"
                        class="mr-2"
                      >
                        <i class="fas fa-arrow-alt-circle-left"></i> VOLTAR
                      </router-link>
                    </div>
                  </b-card-text>
                </b-card-body>
              </b-col>
            </b-row>
          </b-card>
        </b-col>
      </b-row>
    </b-container>
  </section>
</template>

<script>
import HeaderPage from "@/components/HeaderPage.vue";
import { EDIT_ANIMAL } from "@/store/animals/animal.constants";
import { mapGetters } from "vuex";
import router from "@/router";
export default {
  name: "Animal",
  components: {
    HeaderPage
  },
  data: function() {
    return {
      animal: "",
      comment: "",
      currentImageIndex: 0
    };
  },
  computed: {
    ...mapGetters("animal", ["getAnimalsById", "getMessage"]),
    ...mapGetters("auth", ["getProfile"]),
    ...mapGetters("user", ["getNameById"]),
    ...mapGetters(["getUserLevelByPoints"]),
    photoLinks() {
      if (
        !this.animal ||
        !this.animal.links ||
        !Array.isArray(this.animal.links)
      ) {
        return [];
      }
      return this.animal.links.filter(link => link.types === "photo");
    }
  },
  methods: {
    previousImage() {
      if (this.photoLinks && this.photoLinks.length > 0) {
        this.currentImageIndex =
          (this.currentImageIndex - 1 + this.photoLinks.length) %
          this.photoLinks.length;
      }
    },
    nextImage() {
      if (this.photoLinks && this.photoLinks.length > 0) {
        this.currentImageIndex =
          (this.currentImageIndex + 1) % this.photoLinks.length;
      }
    },
    showVideo(videoUrl) {
      this.$fire({
        title: "<strong>Vídeo</strong>",
        icon: "info",
        html: `
          <iframe width='460' height='315' 
            src='${videoUrl}' frameborder='0' 
            allow='accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture'
            allowfullscreen></iframe> 
          `,
        focusConfirm: false,
        confirmButtonText: '<i class="fa fa-thumbs-up"></i> OK'
      });
    },
    showComments() {
      this.$fire({
        title: "<strong>Comentários</strong>",
        icon: "info",
        html: this.generateComments(),
        focusConfirm: false,
        confirmButtonText: '<i class="fa fa-thumbs-up"></i> OK'
      });
    },
    generateComments() {
      let comments = "";
      for (let comment of this.animal.comments) {
        comments += `
        <b-alert show variant="secondary">
          
          
          <b>${comment.user ? comment.user.name : ""}</b> (${comment.date})<br>
          ${comment.body}  
        </b-alert>
        <br><br>
          `;
      }
      return comments;
    },
    setComment() {
      // Validate that comment is not empty
      if (!this.comment || this.comment.trim() === "") {
        this.$alert(
          "Por favor, escreva um comentário antes de submeter!",
          "Aviso",
          "warning"
        );
        return;
      }

      this.animal.comments.push({
        body: this.comment,
        user: this.getProfile._id,
        date: this.getCurrentDateTime()
      });
      this.$store.dispatch(`animal/${EDIT_ANIMAL}`, this.animal).then(
        () => {
          this.$alert(`Obrigado pelo comentário!`, "Comentários", "success");
          this.comment = ""; // Clear the comment field after successful submission
        },
        err => {
          this.$alert(`${err.message}`, "Erro", "error");
        }
      );
    },
    getCurrentDateTime() {
      const today = new Date();
      const year = today.getFullYear();
      const month = String(today.getMonth() + 1).padStart(2, "0");
      const day = String(today.getDate()).padStart(2, "0");
      const hours = String(today.getHours()).padStart(2, "0");
      const minutes = String(today.getMinutes()).padStart(2, "0");
      return `${year}-${month}-${day} ${hours}:${minutes}`;
    },
    setCurrentDateTime(commentDate) {
      const date = new Date(commentDate);
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, "0");
      const day = String(date.getDate()).padStart(2, "0");
      const hours = String(date.getHours()).padStart(2, "0");
      const minutes = String(date.getMinutes()).padStart(2, "0");
      return `${year}-${month}-${day} ${hours}:${minutes}`;
    }
  },
  created() {
    const animal = this.getAnimalsById(this.$route.params.animalId);
    if (animal) {
      this.animal = animal;
      // Initialize currentImageIndex to 0 (first photo)
      this.currentImageIndex = 0;
      // Reset index if it's out of bounds for photo links
      this.$nextTick(() => {
        if (this.currentImageIndex >= this.photoLinks.length) {
          this.currentImageIndex = 0;
        }
      });
    } else {
      this.$alert("Animal não encontrado!", "Erro", "error");
      router.push({ name: "animals" });
    }
  }
};
</script>
