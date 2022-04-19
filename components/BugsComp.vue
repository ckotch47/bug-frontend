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

    <select name="status" id="modules_list" >
      <option
        v-for="option in ModuleList"
        :key="option.ID"
        :value="option.NAME"
        :selected="option.id === ModuleList[0].ID"
      >
        {{ option.NAME }}
      </option>
    </select>

    <div class="content-flex_colum">
      <div class="customlist">

        <div class="customlist-elem" v-for="(elem, index) in BugsList" >
          <div class="customlist-elem_label">#{{elem.ID}} {{elem.NAME}}</div>
          <div class="customlist-elem_end">
            <div class="customlist-elem_end__status">
              <select name="status" id="elem-status" v-if="elem.STATUS">
                <option
                  v-for="option in optionForBugs"
                  :key="option.id"
                  :value="option.status"
                  :selected="option.status === elem.STATUS"
                >
                  {{ option.status }}
                </option>
              </select>
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
  name: "BugsComp",
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
      await this.getBugs(this.ModuleList[0].ID,this.RunsList[0].ID);
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
    async getBugs(module, runs){
      this.BugsList = (await this.$axios.get('/bugs',
        {
          params: {
            module_id: module,
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

