<template>

<!-- DIV LISTA -->
  <div class="body" v-if="list">
    <br>
    <h3>Revision de Orden</h3>
    <h5>Numero: {{ order.number }}</h5>
    <!-- DIV LOADING -->
      <div v-if="loading">
            <img src="../assets/preloader.gif" alt="loading" style="width: 50px;display:block;margin:auto;">
      </div>
    <!--  -->

    <!-- BOTON AGREGAR -->
      <div style="float: right;">
        <button type="button" class="btn btn-success" @click="form = true;list = false">Agregar Item</button>
      </div>
    <!--  -->
    <br>
    <!-- DVI DE LA TABLA -->
      <div class="border" style="margin-top: 15px;height: 300px;">
        <table class="table table-sm table-striped">
          <thead>
              <tr>
                  <th style="width: 20%;">SKU</th>
                  <th style="width: 50%;">Nombre</th>
                  <th style="width: 15%;">Cantidad</th>
                  <th style="width: 15%;">Precio</th>
              </tr>
          </thead>
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
  <div class="body" v-if="form">
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
            <input id="price" name="price" type="text" class="form-control" v-bind:class="{ errorInput: alert_price }" v-model="price" required>
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
    name:'list',
    data() {
      return {
        url : "https://eshop-deve.herokuapp.com/api/v2/orders",
        header: "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJwUGFINU55VXRxTUkzMDZtajd ZVHdHV3JIZE81cWxmaCIsImlhdCI6MTYyMDY2Mjk4NjIwM30.lhfzSXW9_TC67SdDKyD bMOYiYsKuSk6bG6XDE1wz2OL4Tq0Og9NbLMhb0LUtmrgzfWiTrqAFfnPldd8QzWvgVQ",
        order: [],
        form: false,
        list: true,
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
      prodcutList(){
        this.loading = true;
        axios.get(this.url, { 'headers': { 'Authorization': this.header } })
        .then((response) => {
            this.order = response.data.orders[0];
            this.loading = false;
            this.price_accumulated();
        })
        .catch((error) => {
            console.log(error)
            this.loading = false;
        })        
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

        this.form = false;
        this.list = true;

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
      }
      
    },
    created(){
      this.prodcutList()
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
  .colOptions{
    width: 120px;
  }
  .errorInput{
    border-color: red;
    background-color: white;
  }
</style>
