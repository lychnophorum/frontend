<template>
  <div>
    <el-table :data="data" border>
      <el-table-column label="执行结果" prop="status" align="center" width="120" sortable>
        <template scope="{ row }">
          <el-tag :type="row.status === 0 ? 'danger' : row.status === 1 ? 'success' : 'warning'" size="small">
            {{ row.status === 0 ? '失败' : row.status === 1 ? '成功' : '跳过' }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="测试用例" prop="name" align="center" show-overflow-tooltip />
      <el-table-column label="开始时间" prop="startTime" align="center" show-overflow-tooltip sortable />
      <el-table-column label="结束时间" prop="endTime" align="center" show-overflow-tooltip sortable />
      <el-table-column label="耗时" align="center" show-overflow-tooltip>
        <template scope="{ row }">
          {{ parseInt(new Date(row.endTime) - new Date(row.startTime)) / 1000 + ' 秒' }}
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center">
        <template scope="{ row }">
          <el-button @click="clickDetailInfo(row)" size="small" type="text">详细信息</el-button>
          <el-button @click="$router.push({ name: 'UpdateTestcaseAction', params: { actionId: row.id }})" size="small" type="text">编辑用例</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-drawer
      size="1300px"
      :title="testcase.name"
      :visible.sync="showDrawer">
      <div style="padding: 5px">
        <el-row>
          <el-col v-if="testcase.videoUrl" :span="6">
            <video :src="testcase.videoUrl" width="100%" controls="controls">浏览器不支持video标签</video>
          </el-col>
          <el-col :span="(testcase.videoUrl) ? 18 : 24">
            <el-table :data="testcase.steps" border max-height="500px">
              <el-table-column prop="number" label="步骤" align="center" width="100" />
              <el-table-column prop="name" label="步骤名" align="center" show-overflow-tooltip />
              <el-table-column prop="startTime" label="开始时间" align="center" width="180" show-overflow-tooltip />
              <el-table-column prop="endTime" label="结束时间" align="center" width="180" show-overflow-tooltip />
              <el-table-column label="耗时" align="center" width="180" show-overflow-tooltip>
                <template scope="{ row }">
                  {{ row.endTime ? parseInt(new Date(row.endTime) - new Date(row.startTime)) / 1000 + '秒' : '-' }}
                </template>
              </el-table-column>
              <el-table-column label="状态" align="center" width="90">
                <template scope="{ row }">
                  <el-tag :type="row.endTime ? 'success' : row.startTime ? 'danger' : 'info'" style="width: 65px;">
                    {{ row.endTime ? '完成' : row.startTime ? '失败' : '未执行' }}
                  </el-tag>
                </template>
              </el-table-column>
            </el-table>
          </el-col>
        </el-row>
        <el-row>
          <el-col v-if="testcase.failImgUrl" :span="6">
            <el-image :src="testcase.failImgUrl" :preview-src-list="[testcase.failImgUrl]" width="100%" />
          </el-col>
          <el-col v-if="testcase.failInfo" :span="18">
            <codemirror v-model="testcase.failInfo" :options="cmOptions" />
          </el-col>
        </el-row>
      </div>
    </el-drawer>
  </div>
</template>

<script>
import 'codemirror/mode/clike/clike.js'
import 'codemirror/theme/base16-dark.css'
export default {
  props: {
    data: Array
  },
  data() {
    return {
      showDrawer: false,
      cmOptions: {
        mode: 'clike',
        theme: 'base16-dark',
        lineNumbers: true,
        readOnly: true
      },
      testcase: {}
    }
  },
  methods: {
    clickDetailInfo(row) {
      this.testcase = row
      this.showDrawer = true
    }
  }
}
</script>

<style>
  .el-drawer__body {
    height: 100%;
    overflow: auto;
  }
  .CodeMirror {
    height: auto;
    font-size: 10px;
  }
</style>
