<template>
  <el-tabs :tab-position="tabPosition" v-model="activeTab" @tab-click="handleClick">
    <el-tab-pane label="key-value edit" name="key-value">
      <el-table
        ref="multipleTable"
        :data="data"
        tooltip-effect="dark"
        style="width: 100%"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column label="KEY" width="120">
          <template slot-scope="scope">
            <el-input v-model="scope.row.key" placeholder="请输入内容"></el-input>
          </template>
        </el-table-column>
        <el-table-column prop="value" label="VALUE" width="120">
          <template slot-scope="scope">
            <el-input v-model="scope.row.value" placeholder="请输入内容"></el-input>
          </template>
        </el-table-column>
        <el-table-column prop="discription" label="DISCRIPTION" show-overflow-tooltip>
          <template slot-scope="scope">
            <el-input v-model="scope.row.discription" placeholder="请输入内容"></el-input>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div style="text-align: center;margin-top:10px;">
        <el-button size="mini" type="primary" @click="handleAdd()">增加参数</el-button>
        <!-- <el-button @click="toggleSelection(data)">切换第二、第三行的选中状态</el-button> -->
      </div>
    </el-tab-pane>
    <el-tab-pane label="Bulk edit" name="Bulk">
      <el-input
        type="textarea"
        :autosize="{ minRows: 2, maxRows: 4}"
        placeholder="请输入内容，内容格式要求为JSON对象"
        v-model="tableDataParams"
      ></el-input>
    </el-tab-pane>
  </el-tabs>
</template>

<script lang="ts">
import { Component, Vue, Watch, Prop, Emit } from 'vue-property-decorator';
import { RouteRecord } from 'vue-router';
import { ElTable } from 'element-ui/types/table';

@Component
export default class QueryParams extends Vue {
  @Prop({ default: '' }) private tableData!: '';

  private data: { [index: string]: any } = [];
  private selectedData: { [index: string]: any } = [];
  private activeTab = 'Bulk';
  private tabPosition = 'right';
  private textarea3 = '';
  private multipleSelection = [];
  private tableDataParams = '';

  // 监听父组件传值
  @Watch('tableData')
  private ontableDataChange(val: string, oldVal: string) {
    if (this.tableData) {
      this.tableDataParams = this.tableData;
      if (this.activeTab === 'key-value') {  // JSON对象类型编辑
        this.keyValue2bulk();
      }
    } else {
      this.tableDataParams = '';
      this.keyValue2bulk();
    }
  }

  // 选中，取消选中行
  private toggleSelection(rows: any) {
    this.$nextTick(function() {
      if (rows) {
        rows.forEach((row: any) => {
          (this.$refs.multipleTable as ElTable).toggleRowSelection(row);
        });
      } else {
        (this.$refs.multipleTable as ElTable).clearSelection();
      }
    });

  }

  // 选中每行重新赋值selectedData，selectedData为array
  private handleSelectionChange(val: any) {
    this.multipleSelection = val;
    this.selectedData = val;
    let tData = '';
    let tableJsons: { [index: string]: any } = {};
    for (const i in this.selectedData) {
      if (this.selectedData.hasOwnProperty) {
        const c = this.selectedData[i].key;
        const oobj: { [index: string]: any } = {};
        oobj[c] = this.selectedData[i].value;
        if (tableJsons) {
          tableJsons = Object.assign(tableJsons, oobj);
        } else {
          tableJsons = oobj;
        }
      }
    }
    tData = JSON.stringify(tableJsons);
    this.$emit('childData', tData);
  }
  // // 子组件改变tabledata时返回给父组件，功能未实现
  // @Emit()
  // private tableDataRefresh(tDataK: any, tDataV: any ) {
  //   // this.tableData = JSON.stringify(this.selectedData);
  //   // this.handleSelectionChange()
  //   this.selectedData = tDataK;
  // }
  private handleDelete(index: any, row: any) {
    this.data.splice(index, 1);
  }
  private handleAdd() {
    this.data.push({});
    this.$nextTick(function() {
      (this.$refs.multipleTable as ElTable).toggleRowSelection(this.data[this.data.length - 1]);
    });
  }

  // 参数的两种编辑模式内容类型转换 start
  private bulk2keyValue() {  // JSON对象类型编辑
    // console.info(this.selectedData)
    if (this.activeTab === 'Bulk') {
      if (this.selectedData.length > 0) {
        this.data = this.selectedData;
      }
      this.tableDataParams = '';
      let tableJsons: { [index: string]: any } = {};
      for (const i in this.data) {
        if (this.data.hasOwnProperty) {
          const c = this.data[i].key;
          const oobj: { [index: string]: any } = {};
          oobj[c] = this.data[i].value;
          if (tableJsons) {
            tableJsons = Object.assign(tableJsons, oobj);
          } else {
            tableJsons = oobj;
          }
        }
      }
      this.tableDataParams = JSON.stringify(tableJsons);
    }
  }
  private keyValue2bulk() { // key-value类型编辑
    if (this.activeTab === 'key-value') {
      this.data = [];
      if (typeof this.tableDataParams === 'string' && this.tableDataParams.length > 0) {
        const obj = JSON.parse(this.tableDataParams);
        for (const i in obj) {
          if (obj.hasOwnProperty) {
            this.data.push({
              key: i,
              value: obj[i],
            });
          }
        }
        this.selectedData = this.data;
        this.toggleSelection(this.data);
      }
    }
  }
  // 参数的两种编辑模式内容类型转换 end
  private handleClick() {
    if (this.activeTab === 'Bulk') {  // JSON对象类型编辑
      this.bulk2keyValue();
    } else {  // key-value类型编辑
      this.keyValue2bulk();
    }
  }

}
</script>

<style lang="scss" scoped>
</style>
