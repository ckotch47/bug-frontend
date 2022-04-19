<template>
  <div id="app">
      <section class="headers">
        <nav class="headers_nav">
          <a class="headers_nav-links" href="/">Home</a>
        </nav>
      </section>
      <section class="content">
        <div class="content_header">
          <span class="content_header-inline">
            <p class="content_header-name">{{projectName.NAME}}</p>
<!--            <span class="content_header-add" @click="addBugs()" ><p>add</p></span>-->
          </span>
          <div class="content_header-tabs">
            <ul>
              <li @click="activate(li.id, li.text)" :class="{ active : active_el == li.id }" v-for="li in lista">{{li.text}}</li>
            </ul>
          </div>
        </div>
        <Nuxt/>
      </section>
  </div>
</template>


<script>
export default {
  data() {
    return {
      lista: [
        {"id": 0, "text": "Runs", "link":"runs"},
        {"id": 1, "text": "Module", "link":"modules"},
        {"id": 2, "text": "Bugs", "link":"bugs"},
        {"id": 3, "text": "Tests cases", "link":"testscases"},
        {"id": 4, "text": "Fixe", "link":"fixe"},
        {"id": 5, "text": "Autotest", "link":""},
      ],
      active_el:null,
      link: "",
      projectName:"project name",
    }
  },
  methods:{
    activate(el, text) {
      this.active_el = el;
      this.redirect(this.lista[el].link);
    },
    redirect(link){
      this.$router.push(link)
    },
    async getProject(){
      this.projectName = (await this.$axios.get('/project/'+this.$route.params.id)).data
    }
  },
  mounted() {
    let temp = this.$route.path.split('/');
    temp = temp[temp.length-1]
    temp = this.lista.filter(el => el.link === temp)[0]
    this.active_el = temp.id;
    this.getProject()
  }

}
</script>
