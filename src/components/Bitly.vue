<template>
  <section class="blocapp">

    <header class="header">
      <h1>Bitly</h1>
      <div :class="{'lds-ellipsis' : isLoading}"><div></div><div></div><div></div><div></div></div>
      <input class="new-bloc" type="text" placeholder="Paste long url here..." v-model="longUrl" @keyup.enter="controlDatas" :class="{'error-danger' : errorInput}">
      
    </header>

    <div class="main">
          <table-urls :taburl="taburl" v-on:removeURL="removeURL"></table-urls>
    </div>

  </section>
</template>

<script>
  const BITLY_URL   = 'https://api-ssl.bitly.com/v3/shorten?';
  const BITLY_LOGIN = "xxxxxxxxxxxxx";
  const BITLY_KEY   = "xxxxxxxxxxxxx";

  var urlsStorage = {
     // get localStorage datas
     fetch: function() {
        var localUrl = JSON.parse(localStorage.getItem('longUrl') || '[]');
        return localUrl;
     },

     // Save urls into the localStorage
     save: function (taburl) {
		    localStorage.setItem('longUrl', JSON.stringify(taburl));
		 }

 }

  function validURL(url) {
    return /^(https?|ftp):\/\/(((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:)*@)?(((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5]))|((([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.)+(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.?)(:\d*)?)(\/((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)+(\/(([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)*)*)?)?(\?((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)|[\uE000-\uF8FF]|\/|\?)*)?(\#((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)|\/|\?)*)?$/i.test(url);
  }
  

  import axios from 'axios';
  import TableUrls from './table-urls.vue';

  export default {
    data () {
      return {
        taburl: urlsStorage.fetch(),
        longUrl: '',
        shortenUrl: '',
        errorInput: false,
        isLoading: false
      }
    },

    methods: {
      controlDatas() {
        if (this.longUrl != '' && !validURL(this.longUrl)) 
        {
          this.errorInput = true;
        }
        else
        {
          this.errorInput = false;
          this.isLoading  = true;
          this.shortenURL();
        }
      },

      addURL() {
        this.taburl.push({
          uid: this.taburl.length,
          longUrl: this.longUrl,
          shortenUrl: this.shortenUrl
        });

        urlsStorage.save(this.taburl);
        this.longUrl    = '';
        this.shortenUrl = '';
      },

      removeURL(index) {
        //this.taburl = this.taburl.filter(i => i !== url);
        this.taburl.splice(index, 1);
        urlsStorage.save(this.taburl);  //SaveURL
      },
      
      shortenURL() {
        var self = this;
        axios.get(BITLY_URL, {
					params : {
				        "format": "json",
				        "apiKey": BITLY_KEY,
				        "login": BITLY_LOGIN,
				        "longUrl": this.longUrl
				    }
			  })
        .then(function(response) {
          if (response.status === 200 && Object.keys(response.data.data).length > 0)
          {
            //console.log(response.data.data);
            //console.log('longURL: ' + response.data.data.long_url);
            //console.log('shortenUrl: ' + response.data.data.url);
            self.longUrl    = (response.data.data.long_url != '') ? response.data.data.long_url : '';
            self.shortenUrl = (response.data.data.url != '')      ? response.data.data.url      : '';
            self.isLoading  = false;
            self.addURL();
          }
          else
          {
            console.log('Oops, something went wrong, please try again!');
          }
        })
        .catch(function (error) {
          console.log('Error: ', error);
        });
      },

      onCopy: function (e) {
          alert('You just copied: ' + e.text)
      },

      onError: function (e) {
          alert('Failed to copy URLs!')
      },

    },
    components: {
      TableUrls
    }
  }
  </script>

<style src="./custom.css"></style>