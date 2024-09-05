<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" :rules="[val => !!val || '姓名不能為空']"/>
        <q-input v-model="tempData.age" label="年齡" :rules="[val => !!val || '不得為空']"/>
        <q-btn color="primary" class="q-mt-md" @click="createData">新增</q-btn>
      </div>

      <q-table flat bordered ref="tableRef" :rows="blockData" :columns="(tableConfig as QTableProps['columns'])"
        row-key="id" hide-pagination separator="cell" :rows-per-page-options="[0]">
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
            </q-th>
            <q-th></q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td v-for="col in props.cols" :key="col.name" :props="props">
              <div>{{ props.row[col.field] }}</div>
            </q-td>
            <q-td class="text-right" auto-width>
              <q-btn @click="editData(props.row)" size="sm" color="grey-6" round dense icon="edit" class="q-ml-md">
                <q-tooltip>編輯</q-tooltip>
              </q-btn>
              <q-btn @click="deleteData(props.row.id)" size="sm" color="red-6" round dense icon="delete"
                class="q-ml-md">
                <q-tooltip>刪除</q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div class="full-width row flex-center items-center text-primary q-gutter-sm" style="font-size: 18px">
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios';
import { QTableProps } from 'quasar';
import { ref, onMounted } from 'vue';
interface btnType {
  label: string;
  icon: string;
  status: string;
}
const blockData = ref([
  {
    name: 'test',
    age: 25,
  },
]);
const tableConfig = ref([
  {
    label: '姓名',
    name: 'name',
    field: 'name',
    align: 'left',
  },
  {
    label: '年齡',
    name: 'age',
    field: 'age',
    align: 'left',
  },
]);
const tableButtons = ref([
  {
    label: '編輯',
    icon: 'edit',
    status: 'edit',
  },
  {
    label: '刪除',
    icon: 'delete',
    status: 'delete',
  },
]);

const tempData = ref({
  name: '',
  age: '',
});
function handleClickOption(btn, data) {
  // ...
}

// get
async function fetchData() {
  try {
    const response = await axios.get('https://dahua.metcfire.com.tw/api/CRUDTest/a');
    blockData.value = response.data;
  } catch (error) {
    console.error('Error fetching data:', error);
  }
}

// post
async function createData() {
  try {
    const response = await axios.post('https://dahua.metcfire.com.tw/api/CRUDTest', {
      name: tempData.value.name,
      age: tempData.value.age,
    });
    fetchData();
  } catch (error) {
    console.error('Error creating data:', error);
  }
}

// 編輯
async function editData(row) {
  tempData.value = { ...row };
}


// 刪除
async function deleteData(id) {
  try {
    await axios.delete(`https://dahua.metcfire.com.tw/api/CRUDTest/${id}`);
    fetchData();
  } catch (error) {
    console.error('Error deleting data:', error);
  }
}


onMounted(() => {
  fetchData();
});

</script>

<style lang="scss" scoped>
.q-table th {
  font-size: 20px;
  font-weight: bold;
}

.q-table tbody td {
  font-size: 18px;
}
</style>
