<template>
     <div class="form-group">
          <label for="componentDescription">Field Description</label>
          <input v-model="ResDesc" type="text" class="form-control" id="componentDescription"  placeholder="B Field">
          <small id="componentHelp" class="form-text text-muted">Enter a simple descriptor for your field</small>
          <br>
          <label for="Temp">Reservoir Temperature (R)</label>
          <input v-model="ResTemp" type="text" class="form-control" id="Temp" placeholder="343">
          <br>
          <label for="Temp">Reservoir Fluid Gravity</label>
          <input v-model="ResFluidGrav" type="text" class="form-control" id="Grav" placeholder="0.66">
          <br>
          <p> {{ResDesc}} {{ResTemp}} {{ResFluidGrav}} </p>
        </div>
</template>


<script>
export default {
    name: 'ResInputs',
    data() { return {
        ResDesc: 'Field A',
        ResTemp: 343,
        ResFluidGrav: 0.66
    }

    }
}
</script>
