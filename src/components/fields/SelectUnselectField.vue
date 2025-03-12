<template>
    <div class="select-unselect-field customize-component">
      <div class="list">
        <h3>Opciones Activas</h3>
        <div class="box">
          <div
            v-for="(option, index) in activeOptions"
            :key="index"
            class="active"
            @click="moveToInactive(index)"
          >
            {{ option.label }}
          </div>          
      </div>
      </div>
  
      <div class="list">
        <h3>Opciones Inactivas</h3>
        <div class="box">
            <div
            v-for="(option, index) in inactiveOptions"
            :key="index"
            class="inactive"
            @click="moveToActive(index)"
          >
            {{ option.label }}
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { defineProps, defineEmits, reactive, watch } from 'vue';
  
  const props = defineProps({
    field: Object,
    modelValue: [String, Array]
  });
  
  const emit = defineEmits(['update']);
  
  const activeOptions = reactive([...props.field.options]); 
  const inactiveOptions = reactive([]); 
  
  watch(() => props.modelValue, (newVal) => {
    activeOptions.length = 0;
    inactiveOptions.length = 0;
  
    newVal.forEach(optionId => {
      const option = props.field.options.find(opt => opt.id === optionId);
      if (option) {
        activeOptions.push(option);
      }
    });
  
    props.field.options.forEach(option => {
      if (!newVal.includes(option.id)) {
        inactiveOptions.push(option);
      }
    });
  });
  
  const moveToInactive = (index) => {
    const option = activeOptions.splice(index, 1)[0];
    inactiveOptions.push(option);
    emitUpdate();
  };
  
  const moveToActive = (index) => {
    const option = inactiveOptions.splice(index, 1)[0];
    activeOptions.push(option);
    emitUpdate();
  };
  
  const emitUpdate = () => {
    const selectedIds = inactiveOptions.map(option => option.id); 
    emit('update', { id: props.field.id, value: selectedIds });
  };
  
  </script>
  
  <style scoped>
  .select-unselect-field {
    display: flex; 
    gap: 20px;  
  }

  .list {
    width:50%;
  }

  h3 {
    margin: 0px;
  }

  .box {
    background: black;
    border: solid 1px #858585;
    border-radius: 5px;
    padding: 10px;
    cursor: pointer;
    height: 150px;
    overflow-y: auto;
    text-align: left;
  }

  .active {
    text-decoration: none;
  }
  
  .inactive {
    text-decoration: line-through;
  }
  </style>
  