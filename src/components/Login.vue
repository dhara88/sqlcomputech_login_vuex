  <template>
    <div class="container" style="margin-top: 3%;">
      <div class="card">
          <div class="card-header">
              <h3>Login</h3>
          </div>
        <div class="card-body">
            <form class="login" @submit.prevent="login">
                <div class="form-group">
                    <input type="text" placeholder="Username" class="form-control" v-model="username" autofocus    v-on:init="validEmail=false;emailDirty=false;" v-on:input=" validateEmail(); emailDirty =true;"/> 
                    <span v-if="!validEmail && emailDirty" style="color:red"> Only Alphabets,Numbers,Underscore and Space are Allowed</span>   
                </div>
                <div class="form-group">
                    <input type="password" placeholder="Password" class="form-control" v-model="password" autofocus  v-on:init="validPwd=true;pwdDirty=false;"  v-on:input="validPwd=password!=''; pwdDirty =true;"/>
                    <span v-if="!validPwd && pwdDirty" style="color:red">Required*</span>     
                </div> 
              <div class="form-group">   
                  <input type="submit" @click="login" value="Login" class="btn btn-primary" :disabled="((!validEmail)  ||  (!validPwd))"/>
                  <p v-if="msg" style="color:red">{{ msg }}</p>
              </div>
            </form>
        </div>
     </div>
  </div>
</template>
<script>
/* eslint-disable vue/no-unused-components */ 
import AuthService from '@/services/AuthService.js';

export default 
{
	data:function(){
		return {
			username : "",
			password : "",
      msg:'',
      validEmail:false,
      emailDirty:'',
      validPwd:false,
      pwdDirty:''
		}
	},
	methods: {
	async login() {
      try {
        const credentials = {
          email: this.username,
          password: this.password
        };
        const response = await AuthService.login(credentials);
        const tokenParse = JSON.parse(atob(response.access_token.split('.')[1]));
        const token = response.access_token;
		    const user = tokenParse.email;
		    // console.log("t",token,user, response)
        this.$store.dispatch('login', { token, user });
        this.$router.push('/profile');
      } catch (errror) {
        this.msg = 'Invalid Username or Password!!';
      }
    },
		   validateEmail(){
          var expr = new RegExp("^[a-zA-Z0-9_ @.]*$");   
          this.validEmail = expr.test(this.username);
          if(!this.username){
              this.validEmail = false; 
          }
          return this.validEmail ; 
      }  
  },
}
</script>
