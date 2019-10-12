<template>
  <div id="app">
    <div class="container" v-if="this.jsonData.length > 0">
      <div class="page-header">
        <h1>Anno</h1>
        <h2>已对数量：{{checkNum}}</h2>
      </div>
      <div class="jumbotron">
        <div class="row">
          <div class="col-md-2">
            <p>ID：</p>
          </div>
          <div class="col-md-10">
            <p>{{jsonData[showIndex].id}}</p>
          </div>
        </div>
        <div class="row">
          <div class="col-md-2">
            <p>文本：</p>
          </div>
          <div class="col-md-10">
            <p>{{jsonData[showIndex]['ori-sentence']}}</p>
          </div>
        </div>
        <div class="row">
          <div class="col-md-2">
            <p>触发词：</p>
          </div>
          <div class="col-md-10">
            <!-- <p>{{jsonData[showIndex].trigger['pos']}}</p> -->
            <div class="col-md-2">
            <textarea
              v-model="jsonData[showIndex].trigger['text']"
              class="form-control"
              rows="1"
            ></textarea>
          </div>
          <div class="col-md-3">
            <textarea
              v-model="jsonData[showIndex].trigger['pos']"
              class="form-control"
              rows="1"
            ></textarea>
          </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-2">
            <p>事件类型：</p>
          </div>
          <div class="col-md-10">
            <p>{{jsonData[showIndex]['event-type']}}</p>
          </div>
        </div>
        <div class="row">
          <div class="col-md-2">
            <p>ARG1：</p>
          </div>
          <div class="col-md-2">
            <textarea
              v-model="jsonData[showIndex].arguments.core[0].label"
              class="form-control"
              rows="1"
            ></textarea>
          </div>
          <div class="col-md-3">
            <textarea
              v-model="jsonData[showIndex].arguments.core[0].pos_list"
              class="form-control"
              rows="1"
            ></textarea>
          </div>
          <div class="col-md-4">
            <textarea
              v-model="jsonData[showIndex].arguments.core[0].text_list"
              class="form-control"
              rows="1"
            ></textarea>
          </div>
        </div>
        <div class="row">
          <div class="col-md-2">
            <p>AGR2：</p>
          </div>
          <div class="col-md-2">
            <textarea
              v-model="jsonData[showIndex].arguments.core[1].label"
              class="form-control"
              rows="1"
            ></textarea>
          </div>
          <div class="col-md-3">
            <textarea
              v-model="jsonData[showIndex].arguments.core[1].pos_list"
              class="form-control"
              rows="1"
            ></textarea>
          </div>
          <div class="col-md-4">
            <textarea
              v-model="jsonData[showIndex].arguments.core[1].text_list"
              class="form-control"
              rows="1"
            ></textarea>
          </div>
        </div>
      </div>
      <div class="jumbotron">
        <div class="row">
          <div class="col-md-2">
            <p>类型：</p>
          </div>
          <div class="col-md-10">
            <div class="radio">
              <label>
                <input
                  type="radio"
                  name="optionsRadios"
                  id="optionsRadios1"
                  value="1"
                  v-model="jsonData[showIndex].type_correct"
                />
                类型正确
              </label>
              <label>
                <input
                  type="radio"
                  name="optionsRadios"
                  id="optionsRadios2"
                  value="0"
                  v-model="jsonData[showIndex].type_correct"
                />
                类型错误
              </label>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-2">
            <p>情感分类：</p>
          </div>
          <div class="col-md-10">
            <div class="radio">
              <label>
                <input
                  type="radio"
                  name="optionsRadios1"
                  id="optionsRadios1"
                  value="1"
                  v-model="jsonData[showIndex].emotion"
                />
                开心
              </label>
              <label>
                <input
                  type="radio"
                  name="optionsRadios1"
                  id="optionsRadios2"
                  value="2"
                  v-model="jsonData[showIndex].emotion"
                />
                失望
              </label>
              <label>
                <input
                  type="radio"
                  name="optionsRadios1"
                  id="optionsRadios2"
                  value="3"
                  v-model="jsonData[showIndex].emotion"
                />
                沮丧
              </label>
              <label>
                <input
                  type="radio"
                  name="optionsRadios1"
                  id="optionsRadios2"
                  value="0"
                  v-model="jsonData[showIndex].emotion"
                />
                无
              </label>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-6">
            <button
              type="button"
              class="btn btn-success btn-lg"
              @click="showIndex= showIndex - 1"
              :disabled="showIndex <= 0"
            >上一项</button>
            <button type="button" class="btn btn-success btn-lg" @click="saveFile()">保存</button>
            <button
              type="button"
              class="btn btn-success btn-lg"
              @click="showIndex= showIndex + 1"
              :disabled="showIndex >= jsonData.length - 1"
            >下一项</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { saveAs } from "file-saver";
export default {
  name: "app",
  data() {
    return {
      jsonData: [],
      showIndex: 0,
      checkNum: 0,
    };
  },
  components: {},
  mounted() {
    this.getFile();
  },
  methods: {
    getFile() {
      let requestURL = "./data.json";
      let request = new XMLHttpRequest();
      request.open("GET", requestURL);
      request.responseType = "text";
      request.send();
      request.onload = () => {
        let res = request.response;
        this.jsonData = JSON.parse(res);
      };
    },
    saveFile() {
      this.jsonData = this.jsonData.map((data)=>{
        if(typeof(data.arguments.core[0].pos_list) === 'string') {
          data.arguments.core[0].pos_list = data.arguments.core[0].pos_list.split(',');
        }
        return data;
      })
      const finalData = JSON.stringify(this.jsonData);
      // const data = new Blob([], { type: "" })
      var file = new File([JSON.stringify(this.jsonData)], "result.json", {
        type: "text/plain;charset=utf-8"
      });
      saveAs(file);
    }
  },
  watch: {    
    jsonData:{
      handler(newVal){
        let num = 0;
        newVal.forEach((data)=>{
          if(data.type_correct==='1') {
            num+=1;
          }
        });
        this.checkNum = num;
      },      
      deep: true    //深度监听
    }
  },
  destroyed() {
    this.saveFile();
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
