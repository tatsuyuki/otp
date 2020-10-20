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
        
    <button v-on:click="verify">check secret</button><br>
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
    tempSecret() {
      this.userSecret.temp = this.secret.base32;
      console.log("temp", this.userSecret.temp);      
    },
    generateQrCode(secret) {
      console.log('s', secret)
      var qr;
      qrcode.toDataURL(secret.otpauth_url, function(err, data_url) {
        /* this.qrCode = data_url; */
        console.log('dataurl', data_url);
        qr = data_url;               
      });
      console.log('qr', qr);
      this.qrCode = qr;
    },
    verify() {
      console.log(this.userToken);
      var base32Secret = this.userSecret.temp
      var verified = speakeasy.totp.verify({
        secret: base32Secret,
        encoding: 'base32',
        token: this.userToken
      });
      verified? this.status = 'verified' : this.status = 'nope';
      console.log(verified)
    }
  },

  mounted() {
    var secret = speakeasy.generateSecret();
    this.secret = secret;
    console.log("secret", this.secret);
    this.tempSecret();
    console.log(this)
    this.generateQrCode(secret);
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
