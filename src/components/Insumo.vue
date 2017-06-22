<template>
  <div id="root">
    <h2>
      Insumo
      <a class="btn-floating btn-small btn tooltipped -red" data-position="top" data-delay="50" id="boton" data-tooltip="Para modificar primero tienes que hacer click en  el boton de modificar en la tabla">
        <i class="material-icons left">help</i>Update
      </a>
    </h2>
    <table class="table centered">
			<thead>
				<tr>
					<th>Nombre</th>
					<th>Inventario</th>
					<th>idProveedor</th>
          <th>Modificar</th>
					<th>Borrar</th>
				</tr>
			</thead>
			<tbody>
				<tr v-for="insumo in insumos">
					<td >{{insumo.nombre}}</td>
          <td >{{insumo.inventario}}</td>
          <td >{{insumo.idProveedor}}</td>
          <td>
						<a v-on:click="startToModifyInsumo(insumo)" class="btn-floating btn-small waves-effect waves-light grey darken-4"><i class="material-icons">arrow_downward</i></a>
					</td>
					<td>
						<a v-on:click="deleteInsumo(insumo._id)" class="btn-floating btn-small waves-effect waves-light red"><i class="material-icons">delete</i></a>
					</td>
				</tr>
			</tbody>
		</table>
    <br>
    <ul id="tabs-swipe-demo" class="tabs">
      <li class="tab col s3"><a class="active" v-on:click="tabControl('test-swipe-1')" href="#test-swipe-1">Crear</a></li>
      <li class="tab col s3"><a v-on:click="tabControl('test-swipe-2')" href="#test-swipe-2">Modificar</a></li>
    </ul>
    <div class="row">
        <div class="input-field col s12">
          <input v-on:input="insumo.nombre" type="text" v-model="insumo.nombre" :disabled="loading"  id="Nombre">
          <label for="Nombre">Nombre</label>
        </div>
        <div class="input-field col s6">
          <input v-on:input="insumo.tipo" v-model="insumo.tipo" :disabled="loading"  id="Tipo" type="text" class="validate">
          <label for="Tipo">Tipo</label>
        </div>
        <div class="input-field col s6">
          <input v-on:input="insumo.inventario" type="number" v-model="insumo.inventario" :disabled="loading"  id="Inventario">
          <label for="Inventario">Inventario</label>
        </div>
        <div class="row">
          <form class="col s12">
            <div class="row">
              <div class="input-field col s12">
                <textarea v-on:input="insumo.descripcion" v-model="insumo.descripcion" :disabled="loading"  id="Descripcion" type="text"  class="materialize-textarea"></textarea>
                <label for="Descripcion">Descripción</label>
              </div>
            </div>
          </form>
        </div>
        <div class="row -white" id="contenedorTablaExterna">
          <div class="col s6" >
            <h5>Seleccionar ID Proveedor:</h5><p>(hacer click en el nombre deseado)</p>
            <hr>
            <ul v-for="proveedor in proveedores">
              <li><i class="material-icons left">pages</i>{{proveedor.nombre}}
                <a v-on:click="newProveedor(proveedor._id)" class="btn-floating btn-small waves-effect waves-light black secondary-content">
                  <i class="material-icons">skip_next</i>
                </a>
              </li>
            </ul>
          </div>
          <div class="input-field col s6 importance ">
            <br>
              <div  id="idProveedor">
                <a v-on:click="borrarProveedor()" class="waves-effect waves-light">
                  <i class="material-icons">delete</i>
                </a> {{idProv}}  {{nombreProv}}
              </div>
          </div>
        </div>
      </div>
  	  <div id="test-swipe-1" class="col s12">
        <a class="waves-effect waves-light btn-large " v-on:click="createInsumo" :disabled="loading" id="boton">
          <i class="material-icons left">create</i>Crear
        </a>
      </div>
  	  <div id="test-swipe-2" class="col s12">
        <div class="card">
            Atención: Los cambios realizados no se guardan hasta que haga click en el botón de update.
        </div>
				<a class="waves-effect waves-light btn-large" v-on:click="modifyInsumo" :disabled="loading" id="boton">
					<i class="material-icons left">update</i>Update
        </a>
  		</div>
  </div>
</template>

<script>
import baseUrl from '../../config'
export default {
  name: 'insumo',
  data(){
    return{
      insumos: [],
			insumo:{},
			loading: false,
      idModificar: '',
      idProv: 'N/A',
      nombreProv: '',
      selectedTab: 'test-swipe-1',
      proveedor:{},
      proveedores: []
    }
  },
  methods: {
      getInsumo(){
				this.$http.get(`${baseUrl.uri}/insumos`).then((response)=>{
					this.insumos=response.body;
				});
			},
      newProveedor(proveedor_id){
        this.idProv = proveedor_id;
      },
      borrarProveedor(){
        this.idProv = 'N/A';
      },
			createInsumo(){
				this.loading=true;
        this.insumo.idProveedor = this.idProv;
				this.$http.post(`${baseUrl.uri}/insumos/create`,this.insumo)
				.then((response)=>{
					this.loading=false;
					if(response.body.success){
            this.insumo= {};
						sweetAlert("Creado con exito!", "Los cambios estan en la tabla", "success");
						this.getInsumo();
					}else{
						sweetAlert("Oops...", "Error al crear", "error");
					}
				});
			},
      tabControl(idTab){
        if(idTab === 'test-swipe-1'){
          this.idModificar = '';
          this.selectedTab= 'test-swipe-1';
          this.insumo= {};
        }else{
          if(this.idModificar === ''){
            swal("Recuerda!",
            "Para modificar primero tienes que hacer click en  el boton de modificar en la tabla");
          }
        }
      },
      startToModifyInsumo(insumo){
        this.selectedTab= 'test-swipe-2';
        this.idModificar = insumo._id;
        this.insumo = insumo;
        this.idProv = insumo.idProveedor;
        $('ul.tabs').tabs('select_tab', 'test-swipe-2');
        Materialize.updateTextFields();
			},
      modifyInsumo(){
        this.loading=true;
        if(this.idModificar!=''){
          Materialize.updateTextFields();
          this.insumo.idProveedor = this.idProv;
          this.$http.put(`${baseUrl.uri}/insumos/update/`+this.idModificar,this.insumo)
  				.then((response)=>{
  					if(response.body.success){
              this.getInsumo();
              this.loading=false;
  						sweetAlert("Modificado con exito!", "Los cambios estan en la tabla", "success");
              this.insumo= {};
  					}else{
  						sweetAlert("Oops...", "Error al modificar", "error");
              this.loading=false;
            }
  				});
        }
      },
      deleteInsumo(idInsumo){
          this.loading=true;
          this.$http.delete(`${baseUrl.uri}/insumos/delete/`+idInsumo)
            .then((response)=>{
            this.loading=false;
            if(response.body.success){
              this.getInsumo();
              sweetAlert("Deleted!", "Los cambios estan en la tabla", "success");
            }else{
              sweetAlert("Oops...", "Error al eliminar", "error");
            }
          });
			},
      getProveedores(){
        this.$http.get(`${baseUrl.uri}/proveedores`).then((response)=>{
					this.proveedores=response.body;
				});
      }
  },
  beforeMount(){
    this.getInsumo();
    this.getProveedores();
	},
  mounted() {
      $('ul.tabs').tabs();
      $('select').material_select();
      $('.tooltipped').tooltip({delay: 50});
  },
  watch: {
    idProv: function (val) {
      if(val!='N/A'){
        this.nombreProv = '';
      }else {
        this.$http.get(`${baseUrl.uri}/proveedor/searchbyid/{_id}`).then((response)=>{
          console.log(exito);
					this.nombreProv = response.body.proveedor.nombre;
				});
      }
      console.log('por lo menos');
    }
  }
}
</script>

<style scoped>
  .importance{
    color: #06152F !important;
    opacity: 0.7;
    text-align: center;
    font-family: 'Roboto', sans-serif !important;
    font-weight: lighter !important;
    font-size: 20px !important;

  }
  .collection-item{
    color: black;
  }
  .card{
    padding: 10px;
    font-family: 'Roboto', sans-serif;
    font-weight: lighter;
    background-color: black;
    font-size: 15px;
    color: white;
  }
  #contenedorTablaExterna{
    border-radius: 5px;
  }
  td{
    font-family: 'Source Sans Pro', sans-serif;
  }
  th {
    color: white;
    background:#5994AA;
    border-bottom:4px solid #9ea7af;
    border-right: 1px solid #343a45;
    font-size:25px;
    font-weight: 100;
    padding:24px;
    text-align:left;
    text-shadow: 0 1px 1px rgba(0, 0, 0, 0.1);
    vertical-align:middle;
  }
  .table thead{
		font-family: 'Roboto', sans-serif;
		font-weight: bold;
		font-size: 30px;
	}
	.table{
    color: black;
		font-family: 'Spectral', serif;
		font-size: 20px;
		background: white;
	  border-radius:3px;
	  border-collapse: collapse;
	  height: 320px;
	  padding:5px;
	  width: 100%;
	  box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
	  animation: float 5s infinite;
	}


  #homeCard {
    height: 230px;
    display: -webkit-box;
    -webkit-box-pack: center;
    -webkit-box-align: center;
    text-align: center;
    font-family: 'Roboto', sans-serif;
    font-size: 20px;
    color: #06152F;
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
  #TOOLTIP-ID.backdrop {
    background-color: red;
  }
  h4{

  }
  #root{
    font-family: 'Playfair Display';
    font-weight: bold;
    color: white;
  }

    #test-swipe-1 {
        /*background-color: #E6E2AF;*/
        color: #4C1B1B;
        padding-left: 50px;
        padding-right: 50px;
        padding-top: 20px;
        padding-bottom: 30px;
        text-align: center;
    }

    #test-swipe-2 {
        /*background-color: #F6E497;*/
        color: #4C1B1B;
        padding-left: 50px;
        padding-right: 50px;
        padding-top: 20px;
        padding-bottom: 30px;
        text-align: center;
    }


    .tabs .indicator {
        background-color: #A7A37E !important;
        color: #4C1B1B !important;
    }

    .tabs {
        background-color: #FF0B00 !important;
        color: #4C1B1B !important;
        font-family: 'Spectral', serif;
        font-weight: bold;

        border-radius: 3px;
    }

    .tabs .tab a.active {
        color: white;
    }

    .tabs .tab a:hover {
        color: gray;
    }

    .tabs .tab a {
        color: #4C1B1B;
    }


    #boton {
        background-color: #5994AA;

    }


    /* label focus color */
    input{
      font-family: 'Roboto', sans-serif;
      color: white;
      font-weight: normal;
      font-size:17px;
    }
    textarea{
      font-family: 'Roboto', sans-serif;
      color: white;
      font-weight: normal;
      font-size:17px;
    }
    label{
        font-size:17px;
      font-family: 'Roboto', sans-serif;
      font-weight: normal;
    }
    .input-field input:focus+label {
        color: #5994AA !important;
    }
    /* label underline focus color */

    .row .input-field input:focus {
        border-bottom: 1px solid #5994AA !important;
        box-shadow: 0 1px 0 0 #5994AA !important
    }

    .input-field input:focus+label {
        color: #5994AA !important;
    }
    /* label underline focus color */

    .row .input-field input:focus {
        border-bottom: 1px solid #5994AA !important;
        box-shadow: 0 1px 0 0 #5994AA !important
    }

    .input-field textarea:focus+label {
        color: #5994AA !important;
    }
    /* label underline focus color */

    .row .input-field textarea:focus {
        border-bottom: 1px solid #5994AA !important;
        box-shadow: 0 1px 0 0 #5994AA !important
    }
</style>
