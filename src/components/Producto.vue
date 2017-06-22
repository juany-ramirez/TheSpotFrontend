<template>
    <div id="root">
      <h2>
        Producto
        <a class="btn-floating btn-small btn tooltipped -red" data-position="top" data-delay="50" id="boton" data-tooltip="Para modificar primero tienes que hacer click en  el boton de modificar en la tabla">
          <i class="material-icons left">help</i>Update
        </a>
      </h2>
      <table class="table centered">
  			<thead>
  				<tr>
  					<th>Nombre</th>
  					<th>id Bebida</th>
  					<th>id Producto Elaborado</th>
            <th>Descripcion</th>
            <th>Precio</th>
            <th>Modificar</th>
  					<th>Borrar</th>
  				</tr>
  			</thead>
  			<tbody>
  				<tr v-for="producto in productos">
  					<td >{{producto.nombre}}</td>
  					<td >{{producto.idBebida}}</td>
  					<td >{{producto.idProducto_Elaborado}}</td>
            <td >{{producto.descripcion}}</td>
  					<td >{{producto.precio}}</td>
            <td>
  						<a v-on:click="startToModifyProducto(producto)" class="btn-floating btn-small waves-effect waves-light grey darken-4"><i class="material-icons">arrow_downward</i></a>
  					</td>
  					<td>
  						<a v-on:click="deleteProducto(producto._id)" class="btn-floating btn-small waves-effect waves-light red"><i class="material-icons">delete</i></a>
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
          <div class="input-field col s6">
            <input v-on:input="producto.nombre" type="text" v-model="producto.nombre" :disabled="loading"  id="Nombre">
            <label for="Nombre">Nombre</label>
          </div>
          <div class="input-field col s6">
            <input v-on:input="producto.precio" v-model="producto.precio" :disabled="loading"  id="Precio" type="number" class="validate">
            <label for="Precio">Precio</label>
          </div>
          <div class="row">
            <form class="col s12">
              <div class="row">
                <div class="input-field col s12">
                  <textarea v-on:input="producto.descripcion" v-model="producto.descripcion" :disabled="loading"  id="Descripcion" type="text"  class="materialize-textarea"></textarea>
                  <label for="Descripcion">Descripción</label>
                </div>
              </div>
            </form>
          </div>
          <div class="row -white" id="contenedorTablaExterna">
            <div class="switch">
              <h5>Seleccionar ID ElaboracionDetail:</h5>

              <span>
                <p>(Escoger tipo de producto)</p>
                <label>
                  Bebida
                  <input type="checkbox" v-model="checked">
                  <span class="lever"></span>
                  Comida
                </label>
                <p>{{checked}} </p>
              </span>
              <hr>
              <p>(Hacer click en el nombre deseado)</p>
              <hr>
            </div>
            <div v-if="Boolproducto_elab" class="col s6" >
              <ul v-for="producto_elab in productos_elaborados">
                <li><i class="material-icons left">pages</i>{{producto_elab.nombre}}
                  <a v-on:click="newElaboracionDetail(producto_elab._id)" class="btn-floating btn-small waves-effect waves-light black secondary-content">
                    <i class="material-icons">skip_next</i>
                  </a>
                </li>
              </ul>
            </div>
            <div v-if="!Boolproducto_elab" class="col s6" >
              <ul v-for="bebida in bebidas">
                <li><i class="material-icons left">pages</i>{{bebida.nombre}}
                  <a v-on:click="newElaboracionDetail(bebida._id)" class="btn-floating btn-small waves-effect waves-light black secondary-content">
                    <i class="material-icons">skip_next</i>
                  </a>
                </li>
              </ul>
            </div>
            <div class="input-field col s6 importance ">
              <br>
                <div  id="idElaboracionDetail">
                  <a v-on:click="borrarElaboracionDetail()" class="waves-effect waves-light">
                    <i class="material-icons">delete</i>
                  </a> {{idElabDetail}}
                </div>
            </div>
          </div>
        </div>
    	  <div id="test-swipe-1" class="col s12">
          <a class="waves-effect waves-light btn-large " v-on:click="createProducto" :disabled="loading" id="boton">
            <i class="material-icons left">create</i>Crear
          </a>
        </div>
    	  <div id="test-swipe-2" class="col s12">
          <div class="card">
              Atención: Los cambios realizados no se guardan hasta que haga click en el botón de update.
          </div>
  				<a class="waves-effect waves-light btn-large" v-on:click="modifyProducto" :disabled="loading" id="boton">
  					<i class="material-icons left">update</i>Update
          </a>
    		</div>
    </div>
  </template>

    <script>
    import baseUrl from '../../config'
    export default {
      name: 'producto',
      data(){
        return{
          productos: [],
    			producto:{},
          Boolproducto_elab: true,
    			loading: false,
          idModificar: '',
          idElabDetail: 'N/A',
          selectedTab: 'test-swipe-1',
          bebida:{},
          bebidas: [],
          producto_elaborado:{},
          productos_elaborados: [],
          checked: true
        }
      },
      methods: {
          getProducto(){
    				this.$http.get(`${baseUrl.uri}/productos`).then((response)=>{
    					this.productos=response.body;
    				});
    			},
          newElaboracionDetail(elab_detail){
            this.idElabDetail = elab_detail;
          },
          borrarElaboracionDetail(){
            this.idElabDetail = 'N/A';
          },
    			createProducto(){
    				this.loading=true;
            if(checked === true){
              this.producto.idProducto_Elaborado = this.idElabDetail;
              this.producto.idBebida = 'N/A';
            }else{
              this.producto.idProducto_Elaborado = 'N/A';
              this.producto.idBebida = this.idElabDetail;
            }
    				this.$http.post(`${baseUrl.uri}/productos/create`,this.producto)
    				.then((response)=>{
    					this.loading=false;
    					if(response.body.success){
                this.producto= {};
    						sweetAlert("Creado con exito!", "Los cambios estan en la tabla", "success");
    						this.getProducto();
    					}else{
    						sweetAlert("Oops...", "Error al crear", "error");
    					}
    				});
    			},
          tabControl(idTab){
            if(idTab === 'test-swipe-1'){
              this.idModificar = '';
              this.selectedTab= 'test-swipe-1';
              this.producto= {};
            }else{
              if(this.idModificar === ''){
                swal("Recuerda!",
                "Para modificar primero tienes que hacer click en  el boton de modificar en la tabla");
              }
            }
          },
          startToModifyProducto(producto){
            this.selectedTab= 'test-swipe-2';
            this.idModificar = producto._id;
            this.producto = producto;
            this.idElabDetail = producto.idElaboracionDetail;
            $('ul.tabs').tabs('select_tab', 'test-swipe-2');
            Materialize.updateTextFields();
    			},
          modifyProducto(){
            this.loading=true;
            if(this.idModificar!=''){
              Materialize.updateTextFields();
              this.producto.idElaboracionDetail = this.idElabDetail;
              this.$http.put(`${baseUrl.uri}/productos/update/`+this.idModificar,this.producto)
      				.then((response)=>{
      					if(response.body.success){
                  this.getProducto();
                  this.loading=false;
      						sweetAlert("Modificado con exito!", "Los cambios estan en la tabla", "success");
                  this.producto= {};
      					}else{
      						sweetAlert("Oops...", "Error al modificar", "error");
                  this.loading=false;
                }
      				});
            }
          },
          deleteProducto(idProducto){
              this.loading=true;
              this.$http.delete(`${baseUrl.uri}/productos/delete/`+idProducto)
                .then((response)=>{
                this.loading=false;
                if(response.body.success){
                  this.getProducto();
                  sweetAlert("Deleted!", "Los cambios estan en la tabla", "success");
                }else{
                  sweetAlert("Oops...", "Error al eliminar", "error");
                }
              });
    			},
          getElaboracionDetailes(){
            this.$http.get(`${baseUrl.uri}/bebidas`).then((response)=>{
    					this.bebidas=response.body;
    				});
            this.$http.get(`${baseUrl.uri}/productos_elaborados`).then((response)=>{
    					this.productos_elaborados=response.body;
    				});
          }
      },
      beforeMount(){
        this.getProducto();
        this.getElaboracionDetailes();
    	},
      mounted() {
          $('ul.tabs').tabs();
          $('select').material_select();
          $('.tooltipped').tooltip({delay: 50});
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
        padding: 2px;
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
