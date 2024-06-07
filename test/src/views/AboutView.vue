<script setup>
import { ref ,computed,watch} from 'vue';
import  {data}  from './data.js'


console.log(data);


const selectedPermissions = ref([]);
const rowGroup = ref([]);



const permissions = ref(data);

const premissionTitles = computed(() => {
  return Object.keys(permissions.value);
});

const premissionRowsLength = computed(() => {
  return Object.keys(permissions.value).map(group => permissions.value[group].length);
});

const premissionRows = computed(() => {
  return Object.keys(permissions.value).map(group => permissions.value[group]);
});



const isSelectAll = ref(false);
const refPermissionForm = ref();
const clickedId = ref()
function getId(id, i, event) {
  clickedId.value = id

}

const remainingDataRef = ref([])

watch(selectedPermissions, (newSelectedPermissions) => {
  // Iterate through each group
  for (const group in permissions.value) {
    if (permissions.value.hasOwnProperty(group)) {
      const groupPermissionIds = permissions.value[group].map(permission => permission.id);
      const allGroupPermissionsSelected = groupPermissionIds.every(permissionId => newSelectedPermissions.includes(permissionId));

      // If all permissions in the group are selected, ensure the group is in rowGroup
      if (allGroupPermissionsSelected) {
        if (!rowGroup.value.includes(group)) {
          rowGroup.value.push(group);
        }
      } else {

        // If not all permissions in the group are selected, remove the group from rowGroup
        const groupIndex = rowGroup.value.indexOf(group);
        if (groupIndex !== -1) {
          let remainingData = []
          remainingData = groupPermissionIds.filter((item => item !== clickedId.value))
       
          rowGroup.value.splice(groupIndex, 1);
      


          remainingDataRef.value = remainingData

        }
      }
    }
  }

  // Handle select all checkbox
  isSelectAll.value = rowGroup.value.length === premissionTitles.value.length;
}, { deep: true });

watch(rowGroup, () => {
  const newSelectedPermissions = [];
  rowGroup.value.forEach(group => {
    const groupPermissionIds = permissions.value[group].map(permission => permission.id);
    newSelectedPermissions.push(...groupPermissionIds);
  });
  selectedPermissions.value = newSelectedPermissions;
  selectedPermissions.value.push(...remainingDataRef.value)

  remainingDataRef.value = []
  isSelectAll.value = rowGroup.value.length === premissionTitles.value.length;
}, { deep: true });

function handelSelectAll() {
  if (!isSelectAll.value) {
    rowGroup.value = premissionTitles.value.slice(); // Select all groups
    selectedPermissions.value = [];
    rowGroup.value.forEach(group => {
      const groupPermissionIds = permissions.value[group].map(permission => permission.id);
      selectedPermissions.value.push(...groupPermissionIds);
    });
  } else {
    rowGroup.value = [];
    selectedPermissions.value = [];
  }
}





</script>

<template>
  <input type="checkbox" class="border-b py-2 font-weight-bold" v-model="isSelectAll" @click="handelSelectAll"
          label="Select All" />

        <table class="permission-table text-no-wrap">

          <template v-for="(group, groupName, index) in data" :key="groupName">
            <tr>
              <td>
                <label for="">{{ groupName }}</label>
                <input type="checkbox" class="d-block" v-model="rowGroup" :value="groupName" />
              </td>
              <td v-for="permission in group" :key="permission.id">
                <label for="">{{ permission.func }}</label>
                <input type="checkbox" class="d-block" v-model="selectedPermissions" @click="getId(permission.id, index, $event)"
                  :value="permission.id" :label="permission.func" />
              </td>
            </tr>
          </template>
        </table>
</template>

<style>

</style>
