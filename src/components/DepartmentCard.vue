<template>
  <div>
    <div>

    </div>
    <div style="text-align: center">
      <el-transfer
        style="text-align: left; display: inline-block"
        v-model="value"
        filterable
        :render-content="renderFunc"
        :titles="['国家列表', '张汉东']"
        :button-texts="['移回', '归属']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChange"
        :data="data">
      </el-transfer>
    </div>

    <div style="text-align: center">
      <el-transfer
        style="text-align: left; display: inline-block"
        v-model="value4"
        filterable
        :render-content="renderFunc"
        :titles="['国家列表', '王涵民']"
        :button-texts="['移回', '归属']"
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
      }
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
