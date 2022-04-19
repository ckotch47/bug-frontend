<template>
  <div class="content_main">
    <div style="display: flex; justify-content: space-between">
      <span>
        <select name="runs" id="runs_list" @change="onChangeRuns($event)" >
          <option
            v-for="option in RunsList"
            :key="option.ID"
            :value="option.ID"
            :selected="option.id === RunsList[0].ID"
          >
            {{ option.NAME }}
          </option>
        </select>
        <select name="module" id="modules_list"  @change="onChangeModule($event)" v-if="showModuleSelect">
          <option
            v-for="option in ModuleList"
            :key="option.ID"
            :value="option.ID"
            :selected="option.id === ModuleList[0].ID"
          >
            {{ option.NAME }}
          </option>
        </select>
      </span>
      <span @click="addBugs()">add</span>
    </div>

    <div class="content-flex_colum">
      <div class="customlist">
        <div class="customlist-elem" v-for="(elem, index) in testCasesList">
          <div class="customlist-elem_label"  @click="getbugsDetail(elem.ID)">#{{elem.ID}} {{elem.NAME}}</div>
          <div class="customlist-elem_end">
            <div class="customlist-elem_end__status">
              <select name="status" id="elem-status" :data_id="elem.ID" v-if="elem.STATUS" @change="onSatusChange($event)">
                <option
                  v-for="option in optionForBugs"
                  :key="option.id"
                  :value="option.id"
                  :selected="option.status === elem.STATUS"
                >
                  {{ option.status }}
                </option>
              </select>
            </div>
            <div class="customlist-elem_end__action">
              <!--                  <span class="edit" >&#9998;</span>-->
              <span @click="deleteBugs(elem.ID)">x</span>
            </div>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "testsCases",
  layout: 'projectDetail',
  data(){
    return{
      ModuleList:{},
      RunsList:{},
      testCasesList:{},
      runs_id:null,
      module_id:null,
      showModal: false,
      RunsName:"",
      ModuleName:"",
      showModalEvent:null,
      showModuleSelect: false,
    }
  },
  methods:{
    async renderComp(){
      await this.getRuns();
      this.runs_id = this.RunsList[0].ID
      this.RunsName = this.RunsList[0].NAME;
      await this.getModules(this.runs_id);
      this.module_id = this.ModuleList[0].ID;
      this.ModuleName = this.ModuleList[0].NAME;
      await this.getTestCases(this.module_id,this.runs_id);
    },
    async getRuns(){
      this.RunsList = (await this.$axios.get('/runs',
        {
          params: {
            project_id: this.$route.params.id
          }
        }
      )).data;
      this.RunsList.unshift({"ID":0,"NAME":"select runs"})
    },
    async getModules(runs){
      const param ={};
      if(runs !== 0) {
        param.runs_id = runs;
        this.ModuleList = (await this.$axios.get('/modules',
          {
            params: param
          }
        )).data;
        this.ModuleList.unshift({"ID":0,"NAME":"select module"})
      }else{
        this.ModuleList = [{"ID":0,"NAME":"select"}]
      }
    },
    async getTestCases(module, runs){
      const param = {};
      if(module !== 0)
        param.module_id = module;
      if(runs !== 0)
        param.runs_id = runs
      this.testCasesList = (await this.$axios.get('/cases',
        {
          params:  param
        }
      )).data
    },
  },
  beforeMount(){
    this.renderComp()
  },
}
</script>

