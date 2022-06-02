<template>
  <h3>Agregar Nuevo Articulo</h3>
  <br>
  <div class="row">
      <div class="col-3">
        <label for="sku">SKU</label>
        <input id="sku" type="text" class="form-control" v-model="sku">
      </div>
      <div class="col-3">
        <label for="name">Name</label>
        <input id="name" type="text" class="form-control" v-model="name">
      </div>
      <div class="col-3">
          <label for="quantity">Quantity</label>
            <input id="quantity" type="text" class="form-control" v-model="quantity">
      </div>
      <div class="col-3">
        <label for="price">Price</label>
        <input id="price" type="text" class="form-control" v-model="price">
      </div>
  </div>
</template>

<script>
export default {

}
</script>

<style>

</style>