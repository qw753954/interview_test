<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" />
        <q-input v-model="tempData.age" label="年齡" />
        <q-btn color="primary" class="q-mt-md" @click="addData">
          {{ isNew ? '新增' : '更新' }}
        </q-btn>
        <q-btn v-if="!isNew" color="secondary" class="q-mt-md q-ml-md" @click="isNew = true; resetForm();">取消</q-btn>
      </div>

      <q-table
        flat
        bordered
        ref="tableRef"
        :rows="blockData"
        :columns="(tableConfig as QTableProps['columns'])"
        row-key="id"
        hide-pagination
        separator="cell"
        :rows-per-page-options="[0]"
      >
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
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              style="min-width: 120px"
            >
              <div>{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn
                @click="handleClickOption(btn, props.row, props.rowIndex)"
                v-for="(btn, index) in tableButtons"
                :key="index"
                size="sm"
                color="grey-6"
                round
                dense
                :icon="btn.icon"
                class="q-ml-md"
                padding="5px 5px"
              >
                <q-tooltip
                  transition-show="scale"
                  transition-hide="scale"
                  anchor="top middle"
                  self="bottom middle"
                  :offset="[10, 10]"
                >
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div
            class="full-width row flex-center items-center text-primary q-gutter-sm"
            style="font-size: 18px"
          >
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
import { QTableProps, useQuasar } from 'quasar';
import { ref } from 'vue';

interface btnType {
  label: string;
  icon: string;
  status: string;
}
const $q = useQuasar();
const isNew = ref(true);
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

// axios.get('https://demo.mercuryfire.com.tw:49110/crudTest/a')
//   .then(res => {
//     console.log(res)
//     this.blockData = res
//   })

function resetForm() {
  tempData.value = { name: '', age: '' }
}

function addData() {
  let { name, age } = tempData.value;
  name = name.trim()
  age = age.trim()
  let flag = true, errArr = [];

  if (name === '') {
    flag = false;
    errArr.push('姓名不得空白！')
  }
  if (age === '') {
    flag = false;
    errArr.push('年齡不得空白！')
  }
  if (isNaN(Number(age)) || Number(age) < 0) {
    flag = false;
    errArr.push('年齡請輸入正整數！')
  }
  if (!flag) {
    return alert(errArr.join('\n'))
  }
  blockData.value.push({ ...tempData.value });
  isNew.value = true
  resetForm()
}
function handleClickOption(btn, data, index) {
  switch (btn.status) {
    case 'edit':
      isNew.value = false;
      tempData.value.name = data.name;
      tempData.value.age = data.age;
      break
    case 'delete':
      blockData.value.splice(index, 1)
      break
  }

}
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
