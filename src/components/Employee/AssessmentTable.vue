<template>

  <div class="searchBar">
    <!-- Filter Search -->
      <div class="input-group mb-5">
        <input type="search" class="form-control" v-model='searchQuery' placeholder="Search here..." aria-label="Recipient's username" aria-describedby="button-addon2">
      </div>
  </div>

<table id="tableComponent" class="table table-bordered table-striped">
  <thead>
    <tr>
      <!-- loop through each value of the fields to get the table header -->
      <th  v-for="field in fields" :key='field' @click="sortTable(field)" > 
        {{field}} <i class="bi bi-sort-alpha-down" aria-label='Sort Icon'></i>
       </th>
    </tr>
  </thead>
  <tbody>
      <!-- Loop through the list get the each assessment data -->
      <tr v-for="item in filteredList" :key='item'>
      <td v-for="field in fields" :key='field'>{{item[field]}}</td>
    </tr>
  </tbody>
</table> 
</template>
<script>
import {computed,ref} from "vue";
// Importing  the lodash library 
import { sortBy} from 'lodash';

export default {
  name: 'TableComponent',
  props:{
      assessmentData:{
          type: Array,
      },
      fields:{
          type: Array,
      }
  },
  
  setup(props) {
    let sort = ref(false);
    let updatedList =  ref([])
    let searchQuery = ref("");
    
        // a function to sort the table
    const sortTable = (col) => {
      sort.value = true
       // Use of _.sortBy() method
      updatedList.value = sortBy(props.assessmentData,col)
      }

    const sortedList = computed(() => {
      if (sort.value) {
         return updatedList.value
      }
      else{
         return props.assessmentData;
      }
      });


      // Filter Search
      const filteredList = computed(() => {
          return sortedList.value.filter((product) => {
            return (
              product.emailAdd.toLowerCase().indexOf(searchQuery.value.toLowerCase()) != -1
            );
          });
});   
      

  return {sortedList, sortTable,searchQuery,filteredList}
  }
 
}
</script>
<style scoped>
table th:hover {
        background:#f2f2f2;
      }
</style>
