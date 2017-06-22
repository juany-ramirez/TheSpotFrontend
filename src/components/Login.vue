<template>
  <div class="container">
    <div class="section"></div>
    <main>
      <center>
        <div class="section"></div>
        <div class="container">

          <div class="z-depth-1 grey lighten-4 row" style="display: inline-block; padding: 32px 48px 0px 48px; border: 1px solid #EEE;">
            <form class="col s12" method="post">
              <div class='row'>
                <div class='col s12'>
                </div>
              </div>
              <div class='row'>
                <div class='input-field col s12'>
                  <input class='validate' type='text' name='username' id='username' />
                  <label for='username'>Usuario</label>
                </div>
              </div>
              <div class='row'>
                <div class='input-field col s12'>
                  <input class='validate' type='password' name='password' id='password' />
                  <label for='password'>Contraseña</label>
                </div>
                <label style='float: right;'>
                  <a class='registro'><router-link to="/registrar"><b>Crear cuenta nueva</b></router-link></a>
                </label>
              </div>
              <br>
              <center>
                <div class='row'>
                  <a v-on:click="login()" class="col s12 btn-large waves-effect waves-light btn -blue" >Login</a>
                </div>
              </center>
            </form>
          </div>
        </div>
      </center>

      <div class="section"></div>
      <div class="section"></div>
    </main>
  </div>
</template>

<script>
import baseUrl from '../../config'
export default {
  name : 'login',
  data() {
    return {
      usuario: {
        usuario: '',
        contrasena:''
      },
      valid: true
    }
  },
  mounted() {
  },
  methods : {
    verify(){
      var message='';
      if(this.usuario.usuario.length==0){
        this.valid=false;
        message+='-Usuario no puede ser vacio\n';
      }
      if(this.usuario.contrasena.length==0){
        this.valid=false;
        message+='-Contraseña no puede ser vacia\n';
      }
      if(/^[a-zA-z0-9]+$/.test(this.usuario.usuario) && /^[a-zA-z0-9]+$/.test(this.usuario.contrasena)){
        this.valid=true;
      }else{
        message+='-Usuario y contraseña solo deben tener letras y numeros';
        this.valid=false;
      }
      if(this.valid){
        if(this.valid){
          this.$http.post('https://vast-escarpment-20960.herokuapp.com/login',this.usuario, { headers: { 'Access-Control-Allow-Origin': true }}).then((response)=>{
            if(response.body.success){
              swal({
                title: 'Bienvenido(a)!',
                type: 'success'
              });
              localStorage.setItem('usuario',JSON.stringify({usuario: response.body.usuario, scope: response.body.scope}));
              alert(JSON.parse(localStorage.getItem('usuario')).usuario);
              this.$router.push('/menu');
            }else{
              if(response.body.tipo==='length' || response.body.tipo==='null'){
                message+='-Usuario no encontrado. Verifique sus credenciales';
              }else if(response.body.tipo==='err'){
                message+='-Ocurrio un error con la BD. Verifique su coneccion a internet e intente de nuevo';
              }
              swal({
                title: 'Login falló!',
                text: 'Razones:\n'+message,
                type: 'error'
              });
            }
          });
        }
      }else{
        swal({
          title: 'Login falló!',
          text: 'Razones:\n'+message,
          type: 'error'
        });
        this.valid=true;
      }
    },
    login(){
      this.$http.post(`${baseUrl.uri}/login`, this.usuario).then((response)=>{
        this.$router.push('/');
        swal("Bienvenido!", response.body.usuario.toUpperCase() ,"success");
      });
    },
    register(){
      console.log("ddddd");
      this.usuario.scope = ['cliente'];
      this.$http.post(`${baseUrl.uri}/register`, this.usuario).then((response)=>{
        this.$router.push('/home');
        swal("Se ha creado tu usuario", response.body.usuario, "success");
      });
    }
  },
  beforeMount(){
    if(JSON.parse(localStorage.getItem('usuario'))!=null){
      localStorage.removeItem('usuario');
      this.$http.put('https://vast-escarpment-20960.herokuapp.com/logout', { headers: { 'Access-Control-Allow-Origin': true }}).then((response)=>{
        alert('Cookie borrada!');
      });
    }
  }
}
</script>

<style scoped>
.registro{
  font-size: 15px;
  font-family: 'Source Sans Pro', sans-serif;

}
.row{
  color: black;
}
.registro:hover{
  color: #06152F !important;
  text-decoration: underline;
}
.-white{
  background-color: #F4F0EA;
  color: black;
}
.-lightblue{
  background-color: #5994AA;
  color: #fff;
}
.-blue{
  background-color: #06152F;
  color: #fff;
}
.-red{
  background-color: #FF0B00;
  color: #fff;
}
.-black{
  background-color: #262626;
  color: #fff;
}

</style>
