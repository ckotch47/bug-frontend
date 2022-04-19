<template>
  <div class="content_main">
    <div class="content-flex_colum">
      <div class="customlist">
        <div class="customlist-elem" v-for="(elem, index) in RunsList">
          <div class="customlist-elem_label">#{{elem.ID}} {{elem.NAME}}</div>
          <div class="customlist-elem_end">
            <div class="customlist-elem_end__action">
              <!--                  <span class="edit" >&#9998;</span>-->
            </div>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "RunsComp",
  data() {
    return {
      optionForBugs:[
        {"id":1, "status":"open"},
        {"id":2, "status":"closed"}
      ],
      ModuleList:{},
      RunsList:{},
      BugsList:{},
    };
  },
  methods: {
    async renderComp(){
      await this.getRuns();

    },
    async getRuns(){
      this.RunsList = (await this.$axios.get('/runs',
        {
          params: {
            project_id: this.$route.params.id
          }
        }
      )).data

    },

  },

  beforeMount(){
    this.renderComp()
  }
}
</script>
