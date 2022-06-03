<template>

<!-- DIV LISTA DE ORDENES -->
  <div class="body" v-if="divOrders">
    <br>
    <h3>Lista de Ordenes</h3>
    <!-- DIV LOADING -->
      <div v-if="loading">
            <img :src="img_loader_url" alt="loading" style="width: 50px;display:block;margin:auto;">
      </div>
    <!--  -->
    <!-- DIV DE LA TABLA -->
        <table class="border table" style="margin: 0px;margin-top: 15px;">
            <thead>
              <tr>
                  <th class="border" style="width: 30%;">Orden</th>
                  <th class="border" style="width: 30%;">Total</th>
                  <th class="border" style="width: 30%;">Estatus</th>
                  <th class="border" style="width: 10%;">&nbsp;</th>
              </tr>
          </thead>
        </table>
        <div class="border divTable">
            <table class="table table-sm table-striped" style="margin: 0px;">
                <tbody>
                    <tr v-for="order in orders">
                        <td class="border" style="width: 30%;">{{ order.number }}</td>
                        <td class="border" style="width: 30%;">{{ order.totals.total }}</td>
                        <td class="border" style="width: 30%;">{{ order.payment.status }}</td>
                        <td class="border" style="width: 10%;"><button class="btn btn-primary btnTable" @click="showOrder(order);">Ver</button></td>
                    </tr>
                </tbody>
            </table>
        </div>
    <!--  -->
  </div>
<!--  -->

<!-- DIV LISTA DE ITEMS -->
  <div class="body" v-if="divItmes">
    <br>
    <h3>Revision de Orden</h3>
    <h5>Numero: {{ order.number }}</h5>
    <!-- DIV LOADING -->
      <div v-if="loading">
            <img :src="img_loader_url" alt="loading" style="width: 50px;display:block;margin:auto;">
      </div>
    <!--  -->

    <!-- BOTON AGREGAR -->
      <div style="float: right;">
        <button type="button" class="btn btn-primary" @click="backOrders()">Regresar</button>
        &nbsp;
        <button type="button" class="btn btn-success" @click="showForm()">Agregar Item</button>
      </div>
    <!--  -->
    <br>
    <!-- DVI DE LA TABLA -->
      <table class="table border" style="margin: 0px;margin-top: 15px;">
        <thead>
              <tr>
                  <th style="width: 20%;">SKU</th>
                  <th style="width: 50%;">Nombre</th>
                  <th style="width: 15%;">Cantidad</th>
                  <th style="width: 15%;">Precio</th>
              </tr>
          </thead>
      </table>
      <div class="border divTable" >
        <table class="table table-sm table-striped" style="margin: 0px;">          
          <tbody>
              <tr v-for="itemOrder in order.items">
                  <td>{{ itemOrder.sku }}</td>
                  <td>{{ itemOrder.name }}</td>
                  <td>{{ itemOrder.quantity }}</td>            
                  <td>{{ itemOrder.price }}</td>
              </tr>
          </tbody>
        </table>
      </div>
    <!--  -->

    <!-- DIV TOTAL ORDEN -->
      <div class="row" style="padding: 5px 0px 0px 0px;margin: 0px;background-color: #f3f3f3;">
        <div style="width: 85%;"><b style="float: right;">Total de la orden:</b></div>
        <div style="width: 15%;"> {{ total_order }} </div>
      </div>
    <!--  -->
    <br>
    <!-- bOTON PAGO -->
      <div style="float: right;">
        <button class="btn btn-success" @click="payOrder();">Pagar Orden</button>
      </div>
    <!--  -->
    
  </div> 
<!--  -->

<!-- DIV FORM -->
  <div class="body" v-if="divForm">
      <br>
      <h3>Agregar Nuevo Articulo</h3>
      <br>
      <!-- BOTON VOLVER -->
        <div style="float: right;">
          <button class="btn btn-primary" @click="hideForm()">Volver</button>
        </div>
      <!--  -->
      <br>
      <!-- INPUTS -->
        <div class="row">
          <div class="col-3">
            <label for="sku"><b>SKU</b></label>
            <input id="sku" name="sku" type="text" class="form-control" v-bind:class="{ errorInput: alert_sku }" v-model="sku" required>
            <small v-if="alert_sku" class="text-danger">*SKU bligatorio</small>
          </div>
          <div class="col-3">
            <label for="name"><b>Nombre</b></label>
            <input id="name" name="name" type="text" class="form-control" v-bind:class="{ errorInput: alert_name }" v-model="name" required>
            <small v-if="alert_name" class="text-danger">*Nombre bligatorio</small>
          </div>
          <div class="col-3">
              <label for="quantity"><b>Cantidad</b></label>
                <input id="quantity" name="quantity" type="text" class="form-control" v-bind:class="{ errorInput: alert_quantity }" v-model="quantity" required>
                <small v-if="alert_quantity" class="text-danger">*Cantidad bligatorio</small>
          </div>
          <div class="col-3">
            <label for="price"><b>Precio</b></label>
            <input id="price" name="price" type="text" class="form-control" @keypress="isNumberKeyDecimal()" v-bind:class="{ errorInput: alert_price }" v-model="price" required>
            <small v-if="alert_price" class="text-danger">*Precio bligatorio</small>
          </div>
        </div>
      <!--  -->
      <br>
      <!-- DIV BOTON GUARDAR -->
        <div style="float: right;">
          <button class="btn btn-success" @click="addItem()">Guardar</button>
        </div>
      <!--  -->
  </div>
<!--  -->
</template>

<script>
  import axios from "axios";
  import Swal from 'sweetalert2'
        
  export default {
    name:'List',
    data() {
      return {
        img_loader_url: require("../assets/images/preloader.gif"),
        url : "https://eshop-deve.herokuapp.com/api/v2/orders",
        header: "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJwUGFINU55VXRxTUkzMDZtajd ZVHdHV3JIZE81cWxmaCIsImlhdCI6MTYyMDY2Mjk4NjIwM30.lhfzSXW9_TC67SdDKyD bMOYiYsKuSk6bG6XDE1wz2OL4Tq0Og9NbLMhb0LUtmrgzfWiTrqAFfnPldd8QzWvgVQ",
        order: [],        
        divOrders: true,
        divItmes: false,        
        divForm: false,
        loading: false,
        sku: "",
        alert_sku: false,
        name: "",
        alert_name: false,
        quantity: "",
        alert_quantity: false,
        price: "",
        alert_price: false,
        total_order: 0
      }
    },
    methods: {
      ordersList(){
          this.loading = true;
          axios.get(this.url, { 'headers': { 'Authorization': this.header } })
          .then((response) => {
              this.orders = response.data.orders;
              console.log(this.orders);
              this.loading = false;
          })
          .catch((error) => {
              console.log(error)
              this.loading = false;
          })        
      },

      showOrder(order){
        this.divOrders = false;
        this.divItmes = true;
        this.divForm = false;
        this.order = order;
        this.price_accumulated();       
      },

      showForm(){
        this.divOrders = false
        this.divItmes = false
        this.divForm = true;        
      },

      hideForm(){
        this.sku = "";
        this.alert_sku = false;
        this.name = "";
        this.alert_name = false;
        this.quantity = "";
        this.alert_quantity = false;
        this.price = "";
        this.alert_price = false;

        this.divForm = false;
        this.divOrders = false;
        this.divItmes = true;        

        this.price_accumulated();
      },

      addItem() {
        const sku = this.sku;
        const name = this.name;
        const quantity = this.quantity;
        const price = this.price;

        if(sku=="" || name=="" || quantity=="" || price==""){
          if (sku=="") { this.alert_sku = true } else { this.alert_sku = false }
          if (name=="") { this.alert_name = true } else { this.alert_name = false }
          if (quantity=="") { this.alert_quantity = true } else { this.alert_quantity = false }
          if (price=="") { this.alert_price = true } else { this.alert_price = false }
          if (isNan(price)) { this.alert_price = true } else { this.alert_price = false }
          return
        }

        const new_item = {
          sku: sku,
          name: name,
          quantity: quantity,
          price: parseFloat(price).toFixed(2)
        };

        this.order.items.push(new_item);

        this.alert_sku = false;
        this.alert_name = false;
        this.alert_quantity = false;
        this.alert_price = false;

        Swal.fire('Exito', 'Se agrego el producto correctamente.', 'success').then((result) => {
          this.hideForm();
        })        
      },

      payOrder() {
        Swal.fire({
          title: '¿Seguro que quieres pagar esta Orden por: $'+this.total_order+'?',
          showDenyButton: true,
          confirmButtonText: 'Sí',
          confirmButtonColor: 'green',
          denyButtonText: `No`,
          icon: `warning`
        }).then((result) => {
          if (result.isConfirmed) {
            Swal.fire('Exito', 'Se pago la orden correctamente.', 'success')
          } else if (result.isDenied) {
            
          }
        })
      },

      price_accumulated() {
        let accumulated = 0;
        this.order.items.map((item) =>{
          let price = item.price;
          price = parseFloat(price).toFixed(2);
          price = parseFloat(price);
          accumulated = parseFloat(accumulated) + price;
          accumulated =  Intl.NumberFormat('es-MX',{currency:'MXN',minimumFractionDigits: 2,maximumFractionDigits: 2}).format(accumulated);
        })
        this.total_order = accumulated;
      },

      isNumberKeyDecimal ($event) {

        let keyCode = ($event.keyCode ? $event.keyCode : $event.which);

        if ((keyCode < 48 || keyCode > 57) && (keyCode !== 46 || this.price.indexOf('.') != -1)) { // 46 is dot
          $event.preventDefault();
        }

        if(this.price!=null && this.price.indexOf(".")>-1 && (this.price.split('.')[1].length > 1)){
          $event.preventDefault();
        }
      },

      backOrders(){
        this.divForm = false;        
        this.divItmes = false;
        this.divOrders = true;
      }
      
    },
    created(){
      this.ordersList()
    },


  };
      
</script>

<style scoped>
  .body{
    width: 80%;
    margin-left: 10%;
  }
  button{
    width: 120px;
    height: 30px;
    padding: 0px;
  }
  .btnTable{
    width: 50px;
    height: 30px;
    padding: 0px;
    display: block;
    margin: auto;
  }
  .colOptions{
    width: 120px;
  }
  .errorInput{
    border-color: red;
    background-color: white;
  }
  .divTable {
        margin-top: 0px;
        overflow: auto;
        height: 300px;
        overflow: auto; /*Firefox*/
        overflow: overlay;
    }
    /*Webkit*/
    .divTable::-webkit-scrollbar {
        display: none;
        width: 10px;
        height: 10px;
        background-color: lightblue;
    }

    .divTable:hover::-webkit-scrollbar {
        display: initial;  
    }
    .divTable::-webkit-scrollbar-thumb {
        background-color: #09C;
    }
</style>
