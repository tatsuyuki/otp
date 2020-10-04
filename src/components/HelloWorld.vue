<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br />
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener"
        >vue-cli documentation</a
      >.
    </p>
    <img v-if="comQrCode" :src="comQrCode"><br>
    <label>OTP</label>
    <input v-model="userToken"><br>
        
    <button v-on:click="verify">check</button><br>
    <p>{{status}}</p>
  </div>
</template>

<script>
import qrcode from "qrcode";
import speakeasy from "speakeasy";

export default {
  name: "OTP",
  props: {
    msg: String
  },
  data: () => {
    return {
      secret: null,
      userSecret: {},
      qrCode: null,
      userToken: null,
      status: null,
    };
  },

  computed: {
    comQrCode() {
      return this.qrCode;
    }
  },

  methods: {
    
    generateQrCode(secret) {      
      var qr;
      qrcode.toDataURL(secret.otpauth_url, function(err, data_url) {
        /* this.qrCode = data_url; */        
        qr = data_url;               
      });      
      this.qrCode = qr;
    },
    verify() {      
      var base32Secret;
      if(!this.secret) {
        base32Secret = this.userSecret.temp
      } else {
        base32Secret = this.secret
      }
      var verified = speakeasy.totp.verify({
        secret: base32Secret,
        encoding: 'base32',
        token: this.userToken
      });
      verified? this.status = 'verified' : this.status = 'nope';
      console.log(verified)
      if(verified) {
        this.userSecret.secret = base32Secret
        this.secret = base32Secret
        if(!localStorage.getItem('secret'))
        {
          localStorage.setItem('secret', JSON.stringify(this.secret))
        }
        
      }
    }
  },

  mounted() {
    this.secret = JSON.parse(localStorage.getItem('secret'))
    if(!this.secret) {
      var secret = speakeasy.generateSecret();
      console.log('s: ', secret)
      this.userSecret.temp = secret.base32;
      this.generateQrCode(secret);
    }
  }
};
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
</style>
