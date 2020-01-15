<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <br>
    <p>
      What are you comparing today:
    </p>
    <div class="container">
      <div>
        <b-form>
          <b-input-group prepend="Base URL:" class="mt-3">
            <b-form-input 
              v-bind:disabled="comparing" 
              v-model="urlBase"
              v-bind:required ="true"
              placeholder="https://www.google.com">
            </b-form-input>
          </b-input-group>

          <b-input-group prepend="Target URL:" class="mt-3">
            <b-form-input 
              v-bind:disabled="comparing" 
              v-model="urlTarget"
              v-bind:required ="true"
              placeholder="https://www.google.com?q=hola">>
            </b-form-input>
            <b-input-group-append>
              <b-button type="submit" variant="outline-success" v-bind:disabled="comparing" v-on:click="analize"> Validate </b-button>
            </b-input-group-append>
          </b-input-group>
        </b-form>
      </div>
    </div>
    <br>
    <p v-if="comparing">Loading results...</p>
    <div class="container results">
      <b-spinner v-if="comparing" label="Spinning"></b-spinner>
      <b-row v-if="showResults" class="detailed-results">
        <b-col class="result sm-6" >
          <p>Base URL</p>
          <b-img-lazy class="image-border" thumbnail v-bind:src="results.message.base.file" fluid alt="Responsive image"></b-img-lazy>
        </b-col>
        <b-col class="result sm-6" >
          <p>Target URL</p>
          <b-img-lazy class="image-border" danger thumbnail v-bind:src="results.message.targets[0].file" fluid alt="Responsive image"></b-img-lazy>
        </b-col>
        <p class="align-center"></p>
      </b-row>
    </div>
    <b-alert class="alert"
      fade
      :show="dismissCountDown"
      dismissible
      :variant="alert.variant"
      @dismissed="dismissCountDown=0"
      @dismiss-count-down="countDownChanged"
    >
      {{this.alert.message}}
    </b-alert>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data : function () {
    return{
      baseURL : process.env.VUE_APP_API_URL,
      alert: {
        message:'default message',
        variant: 'info'
      },
      dismissSecs: 5,
      dismissCountDown: 0,
      comparing: false,
      showResults: false,
      results : "",
      urlBase : "",
      urlTarget : ""
    }
  },
  methods: {
    countDownChanged(dismissCountDown) {
        this.dismissCountDown = dismissCountDown
    },
    showAlert(message, variant) {
      this.alert.message = message;
      this.alert.variant = variant;
      this.dismissCountDown = this.dismissSecs
    }, 
    validateUrl(url) {
       try {
          new URL(url).toString();
          return true;
        } catch (e) {
          return false;
        }
    },
    analize: function () {
      if (!this.validateUrl(this.urlBase) || !this.validateUrl(this.urlTarget)){
        this.showAlert('Please enter valid HTTPS urls', 'warning');
        return false;
      }

      this.comparing = true;

      let url = this.baseURL + '/crosssite';
      // let data = {}

      this.$http.post(url, {
        "browser":"chrome",
        "urls":{
          "base":this.urlBase,
          "targets":[
            this.urlTarget]
        }
      }
      ).then((response) => {
          this.results = response.data;
          this.comparing = false;
          this.showResults = true;
          this.showAlert('Result: ' + this.results.message.targets[0].result + '. Reason: ' + this.results.message.targets[0].notes, 'danger')
      })
    }
  }

}
</script>



<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.results{
  margin-top: 50px;
}

.image-border{
  border-width: 3px;
  max-width: 380px;
}

.alert{
  position: fixed;
  width: 100%;
  top: 0px;
}
</style>
