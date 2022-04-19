<template>
  <section class="content content-flex">
    <div class="projectbox" v-for="elem in this.projectsList" id="elem.id" @click="redirect('/project/'+elem.ID)">
      <div class="projectbox_header">
        <div class="projectbox_header-image">
          <img :src="elem.IMAGE" v-if="elem.IMAGE" />
          <img src="https://upload.wikimedia.org/wikipedia/commons/a/ac/No_image_available.svg" v-else />
        </div>
        <div class="projectbox_header-text">
          <p>{{elem.NAME}}</p>
        </div>
      </div>
      <div class="projectbox_body">
        <div class="projectbox_body-elem">
          <div class="project_body-elem__label">Runs</div>
          <div class="project_body-elem__count">{{elem.runs.length}}</div>
        </div>
        <div class="projectbox_body-elem">
          <div class="project_body-elem__label">Bugs</div>
          <div class="project_body-elem__count">{{elem.bugs.length}}</div>
        </div>

      </div>
    </div>
  </section>
</template>

<script>


export default {
  name: 'indexPage',
  layout:'default',
  data() {
    return {
      no_img: "https://upload.wikimedia.org/wikipedia/commons/a/ac/No_image_available.svg",
      projectsList: [],

    }
  },
  methods: {
    redirect(link){
      this.$router.push(link+'/runs')
    },
    async getProjectList(){
      this.projectsList = (await this.$axios.get('/project')).data
    },

  },
  beforeMount(){
    this.getProjectList();
  }
}
</script>
