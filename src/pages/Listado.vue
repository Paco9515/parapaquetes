<template>
  <br>
  <h3>Revision de Orden</h3>
  <h5>Numero: {{ order.number }}</h5>
  <div style="float: right;">
    <button type="button" class="btn btn-success" @click="showModal = true">Agregar</button>
  </div>
  <table class="table table-sm table-striped">
    <thead>
        <tr>
            <th>SKU</th>
            <th>NAME</th>
            <th>QUANTITY</th>
            <th>PRICE</th>
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

  <Teleport to="body">
    <!-- use the modal component, pass in the prop -->
    <modal :show="showModal" @close="showModal = false">
      <template #header>
        <h3>custom header</h3>
      </template>
    </modal>
  </Teleport>

</template>

<script>
  import axios from "axios"; 
  import Modal from '../components/Modal.vue'
  
        
  export default {
    components: {
      Modal
    },
    props: {
      sku:{
        type: String, 
        required: true
      },
      name: {
        type: String, 
        required: true
      },
      quantity: {
        type: Number, 
        required: true
      },
      price: {
        type: Number, 
        required: true
      },
    },
    data() {
      return {
        url : "https://eshop-deve.herokuapp.com/api/v2/orders",
        header: "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJwUGFINU55VXRxTUkzMDZtajd ZVHdHV3JIZE81cWxmaCIsImlhdCI6MTYyMDY2Mjk4NjIwM30.lhfzSXW9_TC67SdDKyD bMOYiYsKuSk6bG6XDE1wz2OL4Tq0Og9NbLMhb0LUtmrgzfWiTrqAFfnPldd8QzWvgVQ",
        order: [],
        showModal: false
      }
    },
    methods: {
      listarProductos(){
        axios.get(this.url, { 'headers': { 'Authorization': this.header } })
        .then((response) => {
            this.order = response.data.orders[0];
            console.log(response.data.orders[0])
        })
        .catch((error) => {
            console.log(error)
        })
      },
      agregarItem() {
          console.log("modal");
      },
      
    },
    created(){
      this.listarProductos()
    }


  };
      
</script>

<style scoped>
  button{
    width: 100px;
    height: 30px;
    padding: 0px;
  }
  .colOptions{
    width: 120px;
  }
</style>
