<template>
  <div class="about">
    <!-- <h1>This is an about page</h1> -->
    <el-row>
      <el-col :span="16" :offset="5">
        <div class="grid-content bg-purple-dark">
          <QueryParams @childData="childData" :tableData="tableData"></QueryParams>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import QueryParams from '@/components/QueryParams.vue'; // @ is an alias to /src

@Component({
  components: {
    QueryParams,
  },
})
export default class About extends Vue {
  private tableData = '{"aaaaa": "heTJjZ3TW8H1qtbiw4fSk","1":"2"}'

  private childData(msg: any) {
    console.log('666', msg);
  }
}
</script>
