<template>
  <a-button
    class="editable-add-btn"
    style="margin-bottom: 8px"
    @click="handleAdd"
    >Add</a-button
  >
  <a-table bordered :data-source="dataSource" :columns="columns">
    <template #bodyCell="{ column, text, record }">
      <template v-if="column.dataIndex !== 'operation'">
        <div class="editable-cell">
          <div
            v-if="isEditing(record, column.dataIndex)"
            class="editable-cell-input-wrapper"
          >
            <a-input
              v-model:value="editableData[record.key][column.dataIndex]"
              @blur="handleBlur(record.key, column.dataIndex)"
              @pressEnter="handleBlur(record.key, column.dataIndex)"
            />
            <check-outlined
              class="editable-cell-icon-check"
              @click="handleBlur(record.key, column.dataIndex)"
            />
          </div>
          <div v-else class="editable-cell-text-wrapper">
            {{ text || " " }}
            <edit-outlined
              class="editable-cell-icon"
              @click="edit(record.key, column.dataIndex)"
            />
          </div>
        </div>
      </template>
      <template v-else>
        <a-popconfirm
          v-if="dataSource.length"
          title="Sure to delete?"
          @confirm="onDelete(record.key)"
        >
          <a>Delete</a>
        </a-popconfirm>
      </template>
    </template>
  </a-table>
</template>

<script setup lang="ts">
import { computed, reactive, ref } from "vue";
import type { Ref, UnwrapRef } from "vue";
import { CheckOutlined, EditOutlined } from "@ant-design/icons-vue";
import { cloneDeep } from "lodash-es";

interface DataItem {
  key: string;
  name: string;
  age: number;
  address: string;
  editing: { [key: string]: boolean }; // Thêm trường này để theo dõi trạng thái chỉnh sửa của từng ô
}

const columns = [
  {
    title: "name",
    dataIndex: "name",
    width: "30%",
  },
  {
    title: "age",
    dataIndex: "age",
  },
  {
    title: "address",
    dataIndex: "address",
  },
  {
    title: "operation",
    dataIndex: "operation",
  },
];
const dataSource: Ref<DataItem[]> = ref([
  {
    key: "0",
    name: "Edward King 0",
    age: 32,
    address: "London, Park Lane no. 0",
    editing: {}, // Khởi tạo trạng thái chỉnh sửa của từng ô là false
  },
  {
    key: "1",
    name: "Edward King 1",
    age: 32,
    address: "London, Park Lane no. 1",
    editing: {},
  },
]);
const count = computed(() => dataSource.value.length + 1);
const editableData: UnwrapRef<Record<string, DataItem>> = reactive({});

const edit = (key: string, dataIndex: string) => {
  dataSource.value.find((item) => item.key === key)!.editing[dataIndex] = true; // Đặt trạng thái chỉnh sửa của ô là true khi click vào edit
};
const save = (key: string, dataIndex: string) => {
  dataSource.value.find((item) => item.key === key)!.editing[dataIndex] = false; // Đặt trạng thái chỉnh sửa của ô là false khi lưu giá trị
};

const onDelete = (key: string) => {
  dataSource.value = dataSource.value.filter((item) => item.key !== key);
};
const handleAdd = () => {
  const newData = {
    key: `${count.value}`,
    name: `Edward King ${count.value}`,
    age: 32,
    address: `London, Park Lane no. ${count.value}`,
    editing: {}, // Khởi tạo trạng thái chỉnh sửa của từng ô là false khi thêm mới
  };
  dataSource.value.push(newData);
};

const handleBlur = (key: string, dataIndex: string) => {
  save(key, dataIndex); // Lưu giá trị khi blur ra khỏi ô chỉnh sửa
};
const isEditing = (record: DataItem, dataIndex: string) => {
  return record.editing[dataIndex];
};
</script>

<style lang="less" scoped>
/* Phần CSS không thay đổi */
</style>
