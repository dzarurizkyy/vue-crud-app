<template>
  <div class="container">
    <div>
      <!-- Table -->
      <div>
        <!-- Content Title -->
        <h2 class="my-3 py-3 h2 font-weight-bold">List Posts</h2>
        <table class="table">
          <!-- Column Title -->
          <thead class="thead-dark text-center">
            <tr>
              <th scope="col">No</th>
              <th scope="col">Title</th>
              <th scope="col">Description</th>
              <th scope="col">Action</th>
            </tr>
          </thead>
          <!-- Column Data -->
          <tbody>
            <tr v-for="(item, index) in items">
              <th scope="row" class="text-center">{{index + 1}}</th>
              <td>{{item.title}}</td>
              <td>{{item.desc}}</td>
              <td class="d-flex justify-content-center align-items-center h-100">
                <input type="button" 
                       class="btn btn-primary btn-sm align-items-center mx-1 p-2" 
                       value="Update" 
                       @click="getDataById(item.id)" >
                <input type="button" 
                       class="btn btn-danger btn-sm align-items-center mx-1 p-2" 
                       value="Delete" 
                       @click="deleteData(item.id)">
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <hr class="mt-5"/>
      <!-- Form -->
      <div>
        <h2 class="my-3 py-3 h2 font-weight-bold">Form Add Post</h2>
        <hr class="mb-4"/>
        <form @submit.prevent="onHandleSubmit">
          <!-- Title Form -->
          <div class="form-group">
            <label class="form-label">Title</label>
            <input type="title" 
                   class="form-control" 
                   placeholder="Enter title"
                   v-model="inputTitle">
          </div>
          <!-- Description Form -->
          <div class="form-group mt-4">
            <label class="form-label">Description</label>
            <textarea class="form-control overflow-hidden" 
                      placeholder="Enter description"
                      v-model="inputDesc"
                      style="height:160px; resize: none;">
            </textarea>
          </div>
          <div class="d-flex align-items-center my-4">
            <!-- Submit Button -->
            <button type="submit" 
                    class="btn ml-0 mr-1" 
                    :disabled="inputTitle == ''"
                    :class="isUpdate  ? 'btn-info' : 'btn-primary'">
              {{ isUpdate ? 'Update' : 'Submit'}}
            </button>
            <!-- Cancel Button -->
            <button v-if="isUpdate" 
                    type="button" 
                    class="btn btn-danger mx-2"
                    @click="clearInput">
              Cancel
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
  // Import Package
  import { onMounted, ref } from "vue";
  import axios from "axios";

  // URL API
  const url = "http://localhost:3000";

  const items = ref([]);
  const itemsById = ref([]);
  const inputTitle = ref("");
  const inputDesc = ref("");
  const isUpdate = ref(false);

  let nextID; 

  const clearInput = () => {
    inputTitle.value = "";
    inputDesc.value = "";
    itemsById.value = "";
    isUpdate.value = false;
  };

  // Get All Data
  const getData = async () => {
    try {
      const response = await axios.get(`${url}/posts`);
      items.value = response.data;
      nextID = String(items.value.length + 1);
    } catch(error) {
      console.log(error);
    }
  };

  // Get Data By Id 
  const getDataById = async(id) => {
    try {
      const response = await axios.get(`${url}/posts/${id}`);
      itemsById.value = response.data;
      
      isUpdate.value = true;
      inputTitle.value = itemsById.value.title;
      inputDesc.value = itemsById.value.desc;
    } catch(error) {
      console.log(error);
    }
  };

  const onHandleSubmit = async () => {
    try {
      if(isUpdate.value == false) {
        // Create New Data
        await axios.post(`${url}/posts`, {
          id: nextID,
          title: inputTitle.value,
          desc: inputDesc.value
        });
  
        clearInput();
        alert("Successfully add new data");
        getData();
      } else {
        // Update Data
        await axios.put(`${url}/posts/${itemsById.value.id}`, {
          title: inputTitle.value,
          desc: inputDesc.value
        }); 
        
        alert(`Successfully update data from line ${itemsById.value.id}`);
        getData();
      };
    } catch(error) {
      console.log(error);
    }
  };

  // Delete Data
  const deleteData = async(id) => {
    try {
      await axios.delete(`${url}/posts/${id}`);

      getData();
      alert(`Successfully delete data from line ${id}`);
    } catch(error) {
      console.log(error);
    }
  };

  onMounted(() => {
    getData();
  });
</script>
