<template>
  <div>
    <div>
      <template>
        <el-table
          :data="tableData"
          style="width: 100%">
          <el-table-column
            prop="name"
            label="姓名"
            width="180">
          </el-table-column>
          <el-table-column
            prop="cash"
            label="现金（初始默认）"
            width="180">
          </el-table-column>
          <el-table-column
            prop="debt"
            label="借贷（手动）"
            width="180">
          </el-table-column>
          <el-table-column
            prop="owe"
            label="应还（自动计算）"
            width="180">
          </el-table-column>
          <el-table-column
            prop="country"
            label="拥有国家（自动计算）"
            width="180">
          </el-table-column>
          <el-table-column
            prop="countryInImpawn"
            label="抵押中国家（自动计算）"
            width="180">
          </el-table-column>
          <el-table-column
            prop="random"
            label="随机事件（手动）"
            width="180">
          </el-table-column>
          <el-table-column
            prop="actualAsset"
            label="实际总资产（自动计算）"
            width="180">
          </el-table-column>
        </el-table>
      </template>
    </div>
    <p style="text-align: center; margin: 20px 0 20px">国家归属</p>
    <div style="text-align: center">
      <el-transfer
        style="text-align: left; display: inline-block; float: left"
        v-model="value"
        filterable
        :render-content="renderFunc"
        :titles="['国家列表', '张汉东']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChange"
        :data="data">
      </el-transfer>
      <el-transfer
        style="text-align: left; display: inline-block; float: right"
        v-model="value"
        filterable
        :render-content="renderFunc"
        :titles="['张汉东', '抵押列表']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChange"
        :data="data">
      </el-transfer>
    </div>
<!--    <div><el-divider></el-divider></div>-->
    <div style="text-align: center">
      <el-transfer
        style="text-align: left; display: inline-block; float: left"
        v-model="value4"
        filterable
        :render-content="renderFunc"
        :titles="['国家列表', '王涵民']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChange"
        :data="data">
      </el-transfer>

      <el-transfer
        style="text-align: left; display: inline-block; float: right"
        v-model="value4"
        filterable
        :render-content="renderFunc"
        :titles="['王涵民', '抵押列表']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChange"
        :data="data">
      </el-transfer>
    </div>
  </div>
</template>

<style>
.transfer-footer {
  margin-left: 20px;
  padding: 6px 5px;
}
</style>

<script>
import countryList from "../common/countryList"
export default {
  data() {
    const generateData = _ => {
      const data = [];
      const cities = countryList
      cities.forEach((city, index) => {
        data.push({
          label: city,
          key: city + index,
          disabled: false
        });
      });
      return data;
    };
    return {
      data: generateData(),
      value: [-1],
      value4: [-1],
      text: 'hello',
      renderFunc(h, option) {
        return <span>{ option.label }</span>;
      },
      tableData: [{
        name: '张汉东',
        cash: 800,
        debt: 100,
        owe: 110,
        country: 3,
        countryInImpawn: 2,
        random: 0,
        actualAsset: 690
      }, {
        name: '王涵民',
        cash: 800,
        debt: 100,
        owe: 110,
        country: 3,
        countryInImpawn: 2,
        random: 0,
        actualAsset: 690
      }]
    };
  },

  methods: {
    handleChange(value, direction, movedKeys) {
      movedKeys.forEach(obj => {
        let idx = this.data.findIndex(cur => cur.key === obj)
        this.data[idx].disabled = true
      })
    }
  }
};
</script>
