<template>
    <div>
        <el-row>
        <el-col :offset="2" :span="18">
        <div style="text-align: left">
        <el-steps :active="active" finish-status="success">
            <el-step title="配置数据"></el-step>
            <el-step title="配置特征"></el-step>
            <el-step title="配置模型"></el-step>
            <el-step title="生成报告"></el-step>
        </el-steps>
        </div>

        <div style="margin-top: 12px;" class="data" v-if="active === 0">
            <el-select v-model="data" placeholder="请选择">
                <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                </el-option>
            </el-select>
        </div>
        <div style="margin-top: 12px;" class="feature" v-if="active === 1">
            <el-select v-model="feature" multiple placeholder="请选择">
                <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                </el-option>
            </el-select>
        </div>
        <div style="margin-top: 12px;" class="model" v-if="active === 2">
            <el-select v-model="model" multiple placeholder="请选择">
                <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                </el-option>
            </el-select>
        </div>
        <div style="margin-top: 12px;" class="report" v-if="active === 4">
            <h3>整体报告</h3>
            <el-row v-show="show">
                <el-col :span="12" :offset="6">
                    <el-table
                        :data="tableData"
                        style="width: 100%">
                        <el-table-column
                            prop="algo"
                            label="算法"
                            width="180">
                        </el-table-column>
                        <el-table-column
                            prop="precision"
                            label="Precision"
                            width="180">
                        </el-table-column>
                        <el-table-column
                            prop="recall"
                            label="Recall"
                            width="180">
                        </el-table-column>
                        <el-table-column
                            prop="f1score"
                            label="F1-score"
                            width="180">
                        </el-table-column>
                    </el-table>
                </el-col>
            </el-row>
        </div>

        <el-button style="margin-top: 24px;" @click="back">上一步</el-button>
        <el-button style="margin-top: 24px;" @click="next">下一步</el-button>
        </el-col>
        </el-row>
    </div>
</template>

<script>
export default {
  data () {
    return {
      active: 0,
      config: [],
      options: [{
        value: '选项1',
        label: '黄金糕'
      }, {
        value: '选项2',
        label: '双皮奶'
      }, {
        value: '选项3',
        label: '蚵仔煎'
      }, {
        value: '选项4',
        label: '龙须面'
      }, {
        value: '选项5',
        label: '北京烤鸭'
      }],
      data: '',
      feature: '',
      model: '',
      show: false
    }
  },
  methods: {
    next () {
      if (this.active < 4) {
        this.active++
        if (this.active === 4) {
          this.getConfig()
        }
      }
    },
    back () {
      if (this.active > 0) this.active--
    },
    async getConfig () {
      this.modal()
      let res = await this.$axios
        .get(`http://localhost:8081/report`, {
          headers: {
            'Content-Type': 'application/json;charset=utf-8'
          }})
        .catch(function (error) { // 请求失败处理
          console.log(error)
        })
      this.config = res.data
      console.log('config ' + this.config)
      this.tableData = [{
        algo: 'Random Forest',
        precision: this.config.rfPrecision,
        recall: this.config.rfRecall,
        f1score: this.config.rfF1Score
      }, {
        algo: 'XGBoost',
        precision: this.config.xgbPrecision,
        recall: this.config.xgbRecall,
        f1score: this.config.xgbF1Score
      }, {
        algo: 'lightGBM',
        precision: this.config.lgbPrecision,
        recall: this.config.lgbRecall,
        f1score: this.config.lgbF1Score
      }]
      this.show = true
    },
    modal () {
      this.$notify({
        title: '成功',
        message: '系统配置成功',
        type: 'success'
      })
    }
  }
}
</script>
