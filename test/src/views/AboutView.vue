<!-- eslint-disable no-unused-vars -->
<script setup>
import { ref ,computed,watch} from 'vue';
import  {data}  from './data.js'

/***** Helper  Functions ******/
function print(v){
  console.log(v); 
}
/***** -END- Helper  Functions ******/

/***** Request States Values ******/
const requestData = ref(data);
/***** -END- Request States Values ******/

/***** UI States Values ******/
const isTableAllSelected = ref(false);
const isRoleRowIndexSelected = ref([]);
const rolesRowIndexs = ref([]);
const permissionIds = ref([]);
/***** -END- UI States Values ******/

/***** Compute Values ******/
const premissionTitles = computed(() => {
  return Object.keys(requestData.value);
});
/***** -END- Compute Values ******/

/***** Events Functions ******/
function eventChangeAllRolesAndPermissions() {
  isTableAllSelected.value = !isTableAllSelected.value; 
  if(isTableAllSelected.value){
    //change roles check boxs 
    for (const key in requestData.value) {
      rolesRowIndexs.value.push(key);  
      processSelectPermissionsCheckBoxs(key); 
    }
  }else {
    rolesRowIndexs.value= [];  
    permissionIds.value = []; 
  }
}

function eventChangeRole(event , key) {

}

function eventChangePermission(id, i, event) {

}
/***** -END- Events Functions ******/

/***** Event Processes *****/
function processSelectPermissionsCheckBoxs (roleKey){
  let permissionsList = requestData.value[roleKey]
  permissionsList.forEach(element => {
    permissionIds.value.push(element.id); 
  });
}
/***** -END- Event Processes *****/

/***** Watchs ******/
/***** -END- Watchs ******/

</script>

<!-- ##### UI/HTML ##### -->
<template>
  <input type="checkbox" class="border-b py-2 font-weight-bold" v-model="isTableAllSelected" @click="eventChangeAllRolesAndPermissions"
          label="Select All" />
        <table class="permission-table text-no-wrap">
          <template v-for="(group, groupName, index) in data" :key="groupName">
            <tr>
              <td>
                <label for="">{{ groupName }}</label>
                <input type="checkbox" class="d-block" v-model="rolesRowIndexs" :value="groupName" @click="eventChangeRole($event , groupName)" />
              </td>
              <td v-for="permission in group" :key="permission.id">
                <label for="">{{ permission.func }}</label>
                <input type="checkbox" class="d-block" v-model="permissionIds" @click="eventChangePermission(permission.id, index, $event)"
                  :value="permission.id" :label="permission.func" />
              </td>
            </tr>
          </template>
        </table>
</template>
<!-- ##### UI/HTML ##### -->