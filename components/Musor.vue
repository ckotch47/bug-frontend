<template>
  <section class="content">
    <div class="content_header">
      <span class="content_header-inline">
        <p class="content_header-name">Project name</p>
        <span class="content_header-add" @click="addBugs()" ><p>add</p></span>
      </span>
      <div class="content_header-tabs">
        <ul>
          <li @click="activate(li.id, li.text)" :class="{ active : active_el == li.id }" v-for="li in lista">{{li.text}}</li>
        </ul>
      </div>
    </div>
    <div class="content_main">
      <div class="content-flex_colum">
        <div class="customlist">
          <div class="customlist_group" v-for="(el, ind) in temp">
            <p>Runs: {{el.runs}}</p>
            <div class="customlist-elem" v-for="(elem, index) in el.data" >
              <div class="customlist-elem_label">#{{elem.id}} {{elem.title}}</div>
              <div class="customlist-elem_end">
                <div class="customlist-elem_end__status">
                  <select name="status" id="elem-status" @change="onChange($event, index, ind)" v-if="elem.status">
                    <option
                      v-for="option in optionForBugs"
                      :key="option.id"
                      :value="option.status"
                      :selected="option.status === elem.status"
                    >
                      {{ option.status }}
                    </option>
                  </select>
                </div>
                <div class="customlist-elem_end__action">
                  <span class="edit" @click="editBugs(elem.id)">&#9998;</span>
                </div>
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
              <slot name="body">
                <div class="bugs_midalForm">
                       <span class="bugs_midalForm-inline">
                         <p>name</p>
                            <select name="status" id="elem-statuss" >
                              <option
                                v-for="option in optionForBugs"
                                :key="option.id"
                                :value="option.status"
                                :selected="option.status === bugsDetaildtemp.status"
                              >
                              {{ option.status }}
                            </option>
                          </select>
                        </span>
                  <input :value="bugsDetaildtemp.title"/>
                  <div style="display: contents;" v-if="bugsDetaildtemp.description === null || bugsDetaildtemp.description === undefined">
                    <p>steps</p>
                    <textarea rows="6">{{bugsDetaildtemp.steps}}</textarea>
                    <p>wait</p>
                    <textarea rows="6">{{bugsDetaildtemp.wait}}</textarea>
                    <p>real</p>
                    <textarea rows="6">{{bugsDetaildtemp.real}}</textarea>
                    <p>P.S.</p>
                    <textarea rows="6">{{bugsDetaildtemp.ps}}</textarea>
                  </div>
                  <div style="display: contents;" v-else>
                    <p>description</p>
                    <textarea rows="6">{{bugsDetaildtemp.description}}</textarea>
                  </div>
                </div>
              </slot>
            </div>
            <div class="modal-footer">
              <slot name="footer">
                <p>#{{ bugsDetaildtemp.testscasesID.id }}-> #{{ bugsDetaildtemp.runsID.id }}</p>
                <button class="modal-default-button" @click="showModal = false">
                  OK
                </button>
              </slot>
            </div>
          </div>
        </div>
      </div>
    </modal>
  </section>
</template>

<script>
export default {
  name: "Musor",
  data() {
    return {
      message: 'Welcome to Vue!',
      isActive: false,
      lista:[
        {"id":0,"text":"Runs"},
        {"id":1,"text":"Bugs"},
        {"id":2,"text":"Tests cases"},
        {"id":3,"text":"Fixe"},
        {"id":4,"text":"Autotest"},
      ],
      bugsList:[
        {"runs":"test runs",
          "data":[
            {"id":0,"title":"Bugs Name","status":"open"},
            {"id":1,"title":"Main page don't work","status":"open"},
            {"id":3,"title":"Title in contacts","status":"open"},
            {"id":5,"title":"Phone number don't click","status":"open"},
          ]},
        {"runs":"test runs2",
          "data":[
            {"id":0,"title":"Bugs Name","status":"open"},
            {"id":1,"title":"Main page don't work","status":"open"},
            {"id":3,"title":"Title in contacts","status":"open"},
            {"id":5,"title":"Phone number don't click","status":"open"},
          ]}
      ],
      fixesList:[
        {"runs":"test runs",
          "data":[
            {"id":12,"title":"Bugs Name","status":"closed"},
            {"id":2050,"title":"Main page don't work","status":"closed"},
            {"id":2051,"title":"Title in contacts","status":"closed"},
            {"id":2053,"title":"Phone number don't click","status":"closed"},
          ]},
        {"runs":"test runs2",
          "data":[
            {"id":10,"title":"Bugs Name","status":"closed"},
            {"id":15,"title":"Main page don't work","status":"closed"},
            {"id":16,"title":"Title in contacts","status":"closed"},
            {"id":19,"title":"Phone number don't click","status":"closed"},
          ]}
      ],
      testcase:[ {"runs":"test runs",
        "data":[
          {"id":15,"title":"test case 1e"},
          {"id":19,"title":"test case 1"},
          {"id":20,"title":"test case 1"},
          {"id":23,"title":"test case 1"},
        ]},],
      optionForBugs:[
        {"id":1, "status":"open"},
        {"id":2, "status":"closed"}
      ],
      active_el:1,
      showModal: false,
      temp: this.bugsList,
      bugsDetaild:[
        {
          "id":0,
          "description":null,
          "status":"open",
          "title":"Bugs name",
          "steps":"steps1\nstep2\nsteps 3",
          "wait": "lalala",
          "real": "bebebe",
          "ps": "link",
          "testscasesID":{"id":15,"title":"test case 1e"},
          "runsID":{"id":2,"title":"test runs"}
        },
        {
          "id":1,
          "description":"the lorem imposable",
          "status":"open",
          "title":"Bugs name",
          "steps":"steps1\nstep2\nsteps 3",
          "wait": "lalala",
          "real": "bebebe",
          "ps": "link",
          "testscasesID":{"id":15,"title":"test case 1e"},
          "runsID":{"id":2,"title":"test runs"}
        },
        {
          "id":3,
          "description":null,
          "status":"open",
          "title":"Bugs name new",
          "steps":"steps1\n",
          "wait": "be",
          "real": "la",
          "ps": "link",
          "testscasesID":{"id":15,"title":"test case 1e"},
          "runsID":{"id":2,"title":"test runs"}
        },
        {
          "id":5,
          "description":"the new dscrp",
          "status":"open",
          "title":"Bugs name",
          "steps":"steps1\nstep2\nsteps 3",
          "wait": "lalala",
          "real": "bebebe",
          "ps": "link"
        }],
      bugsDetaildtemp:{},
    };
  },
  methods: {
    activate(el, text){
      this.active_el = el;
      if(text.toLowerCase() === "fixe"){this.temp = this.fixesList}
      if(text.toLowerCase() === "bugs"){this.temp = this.bugsList}
      if(text.toLowerCase() === "tests cases"){this.temp = this.testcase}
    },
    editBugs(id){
      this.bugsDetaildtemp = this.bugsDetaild.filter(elem => elem.id === id)[0];
      this.showModal = !this.showModal;
    },
    showBugs(id){
      alert(id);
    },
    addBugs(){

    },
    onChange(event, id, index) {
      if(event.target.value === "closed")
        this.$delete(this.bugsList[index].bugs, id)
      event.target.value = "open"
    }
  },
  mounted() {
    this.temp = this.bugsList
    console.log(this.temp);

  }
}
</script>

<client-only>


</client-only>
