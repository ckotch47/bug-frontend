<template>
  <div class="content_main">
    <select name="status" id="runs_list" >
      <option
        v-for="option in RunsList"
        :key="option.ID"
        :value="option.NAME"
        :selected="option.id === RunsList[0].ID"
      >
        {{ option.NAME }}
      </option>
    </select>



    <div class="content-flex_colum">
      <div class="customlist">

        <div class="customlist-elem" v-for="(elem, index) in ModuleList" >
          <div class="customlist-elem_label">#{{elem.ID}} {{elem.NAME}}</div>
          <div class="customlist-elem_end">
            <div class="customlist-elem_end__status">

            </div>
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
  name: "modules",
  layout: 'projectDetail',
  ModuleList:{},
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
      await this.getModules(this.RunsList[0].ID);
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
    async getModules(runs){
      this.ModuleList = (await this.$axios.get('/modules',
        {
          params: {
            runs_id: runs
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


