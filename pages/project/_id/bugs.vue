<template>
  <div>
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
        <div class="customlist-elem" v-for="(elem, index) in BugsList">
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
    <modal v-if="showModal" @close="showModal = false">
      <div class="modal-mask">
        <div class="modal-wrapper">
          <div class="modal-container">
            <div class="modal-header">
              <slot name="header">
              </slot>
            </div>
            <div class="modal-body">
              <section class="modal-body_left">
                <slot name="body">
                  <div class="bugs_midalForm">
                     <input :value="BugsDetail.NAME" @input="updateValue($event.target.value,'NAME')">
                    <label >
                      <p>description</p>
                      <editor v-model="BugsDetail.DSCRP" />
<!--                      <textarea rows="6" :value="BugsDetail.DSCRP" @input="updateValue($event.target.value,'DSCRP')" ></textarea>-->
                    </label>
                    <label >
                      <p>steps</p>
                      <editor v-model="BugsDetail.STEPS" />
<!--                      <textarea rows="6" :value="BugsDetail.STEPS" @input="updateValue($event.target.value,'STEPS')" ></textarea>-->
                    </label>
                    <label >
                      <p>wait</p>
                      <editor v-model="BugsDetail.WAIT" />
<!--                      <textarea rows="6" :value="BugsDetail.WAIT" @input="updateValue($event.target.value,'WAIT')"></textarea>-->
                    </label>
                    <label >
                      <p>real</p>
                      <editor v-model="BugsDetail.REAL" />
<!--                      <textarea rows="6" :value="BugsDetail.REAL" @input="updateValue($event.target.value,'REAL')"></textarea>-->
                    </label>
                    <label >
                      <p>P.S.</p>
                      <editor v-model="BugsDetail.PScriptum" />
                      <!--                      <textarea rows="6" :value="BugsDetail.PScriptum" @input="updateValue($event.target.value,'PScriptum')"></textarea>-->
                    </label>
                  </div>
                </slot>
              </section>
              <section class="modal-body_right">
                <div class="modal-body_right-head">{{BugsDetail.ModuleName}}</div>
                <div class="modal-body_right-preview" contenteditable="true">
                  <p>{{BugsDetail.NAME}}</p>
                  <span>{{BugsDetail.DSCRP}}</span>
                  <br/>
                  <b>steps</b>
                  <span>{{BugsDetail.STEPS}}</span>
                  <br/>
                  <b>wait</b>
                  <span>{{BugsDetail.WAIT}}</span>
                  <br/>
                  <b>real</b>
                  <span>{{BugsDetail.REAL}}</span>
                  <br/>
                  <b>P.S.</b>
                  <span>{{BugsDetail.PScriptum}}</span>
                </div>
              </section>
            </div>
            <div class="modal-footer">
              <slot name="footer">
                <button class="modal-default-button" @click="saveModal()">
                  OK
                </button>
              </slot>
            </div>
          </div>
        </div>
      </div>
    </modal>

  </div>

</template>

<script>
import Editor from "@/components/Editor";

export default {
  components: {
    Editor,
  },
  name: "bugsPage",
  layout:'projectDetail',
  data() {
    return {
      content: '<p>A Vue.</p>',
      optionForBugs:[
        {"id":1, "status":"open"},
        {"id":2, "status":"closed"}
      ],
      ModuleList:{},
      RunsList:{},
      BugsList:{},
      runs_id:null,
      module_id:null,
      BugsDetail:{},
      showModal: false,
      RunsName:"",
      ModuleName:"",
      showModalEvent:null,
      showModuleSelect: false,
    };
  },
  methods: {
    async renderComp(){
      await this.getRuns();
      this.runs_id = this.RunsList[0].ID
      this.RunsName = this.RunsList[0].NAME;
      await this.getModules(this.runs_id);
      this.module_id = this.ModuleList[0].ID;
      this.ModuleName = this.ModuleList[0].NAME;
      await this.getBugs(this.module_id,this.runs_id);
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
    async getBugs(module, runs){
      const param = {};
      param.status = "open";
      if(module !== 0)
        param.module_id = module;
      if(runs !== 0)
        param.runs_id = runs
      this.BugsList = (await this.$axios.get('/bugs',
        {
          params:  param
        }
      )).data
    },
    async getbugsDetail(id){
      this.showModal = true;
      this.showModalEvent = "update";
      this.BugsDetail = (await this.$axios.get('/bugs/'+id)).data

      // if(this.BugsDetail.module.NAME !== undefined || null)
      //   this.BugsDetail.ModuleName = this.BugsDetail.module.NAME
      // else
      //   this.BugsDetail.ModuleName = ""
      // if(this.BugsDetail.project.NAME !== undefined || null)
      //   this.BugsDetail.ProjectName = this.BugsDetail.project.NAME
    },
    updateValue(value, key){
      this.BugsDetail[key] = value;
    },
    addBugs(){
      this.showModal = true;
      this.showModalEvent = "add";
      this.BugsDetail = {
        "DSCRP": "",
        "NAME": "",
        "PScriptum": "",
        "REAL": "",
        "STATUS": "",
        "STEPS": "",
        "WAIT": "",
        "ProjectName": this.RunsName,
        "ModuleName": this.ModuleName
      }
    },
    async deleteBugs(id){
      let temp = (await this.$axios.delete('/bugs/'+id)).status;
      if(temp === 200){
        this.BugsList.splice(this.BugsList.findIndex(el => el.ID === id),1)
      }
    },
    async onChangeRuns(event){
      this.BugsList = null;
      this.ModuleList = null;
      this.runs_id = parseInt(event.target.value);
      if(this.runs_id !== 0){
        await this.getModules(this.runs_id);
        if(this.ModuleList) {
          this.showModuleSelect = true;
          this.module_id = this.ModuleList[0].ID;
          await this.getBugs(this.ModuleList[0].ID, this.runs_id);
        }
        else {
          this.module_id = 0;
        }
      }
      else{
        this.runs_id = 0;
        this.module_id = 0;
        this.showModuleSelect = false;
        await this.getBugs(this.module_id, this.runs_id)
      }
    },
    onChangeModule(event){
      this.module_id = parseInt(event.target.value);
      this.getBugs(this.module_id,this.runs_id);
    },
    async onSatusChange(event){
      const tempSelect = parseInt(event.target.value);
      if(tempSelect === 2){
        const id = event.target.getAttribute('data_id')
        let temp = (await this.$axios.put('/bugs/change-status/'+id,{
          "status":"closed"
        })).status;
        if(temp === 200){
          event.target.value = 1;
          const index = this.BugsList.findIndex(el => { return el.ID.toString() === id.toString()});
          this.BugsList.splice(index,1)
        }
      }
      return false;
    },
    saveModal(){
      if(this.showModalEvent === "add") { //add bugs
        let temp = this.$axios.post('/bugs', {
          "name": this.BugsDetail.NAME,
          "steps": this.BugsDetail.STEPS,
          "wait": this.BugsDetail.WAIT,
          "real": this.BugsDetail.REAL,
          "ps": this.BugsDetail.PScriptum,
          "state": "open",
          "descriprion": this.BugsDetail.DSCRP,
          "module_id": this.module_id,
          "project_id": this.project_id
        })
        this.showModal = false;
        this.BugsDetail.STATUS = 'open';
        this.BugsList.push(this.BugsDetail)
      }
      else{                              //update bugs
        this.showModal = false;
      }
    }
  },
  beforeMount(){
    this.renderComp()
  },
  mounted() {

  },
 }
</script>

