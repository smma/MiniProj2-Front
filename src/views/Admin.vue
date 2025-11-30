<template>
  <section class="page-section">
    <b-container fluid>
      <HeaderPage title="Gestão do Animalec" />
      <b-row>
        <b-col cols="2" offset="3">
          <AdminOptionBox
            routeName="listUsers"
            iconName="users"
            title="Utilizadores"
          />
        </b-col>
        <b-col cols="2">
          <AdminOptionBox
            routeName="listAnimals"
            iconName="dog"
            title="Animais"
          />
        </b-col>
        <b-col cols="2">
          <AdminOptionBox
            routeName="listQuizzes"
            iconName="file-alt"
            title="Quizzes"
          />
        </b-col>
      </b-row>
      <b-row class="mt-3">
        <b-col cols="2" offset="3">
          <AdminOptionBox
            routeName="listQuestions"
            iconName="question-circle"
            title="Questões"
          />
        </b-col>
        <b-col cols="2">
          <AdminOptionBox
            routeName="listSponsors"
            iconName="hand-holding-usd"
            title="Patrocinadores"
          />
        </b-col>
        <b-col cols="2">
          <AdminOptionBox
            routeName="listExperts"
            iconName="brain"
            title="Especialistas"
          />
        </b-col>
      </b-row>
    </b-container>
  </section>
</template>
<script>
import HeaderPage from "@/components/HeaderPage.vue";
import AdminOptionBox from "@/components/AdminOptionBox.vue";

export default {
  components: {
    HeaderPage,
    AdminOptionBox
  }
};
</script>
