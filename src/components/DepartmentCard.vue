<template>
  <div style="text-align: center">
    <el-transfer
      style="text-align: left; display: inline-block"
      v-model="value"
      filterable
      :left-default-checked="[2, 3]"
      :right-default-checked="[1]"
      :render-content="renderFunc"
      :titles="['国家列表', '张大富翁']"
      :button-texts="['移回', '归属']"
      :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
      @change="handleChange"
      :data="data">
      <el-button class="transfer-footer" slot="left-footer" size="small">操作</el-button>
      <el-button class="transfer-footer" slot="right-footer" size="small">操作</el-button>
    </el-transfer>
  </div>
</template>

<style>
.transfer-footer {
  margin-left: 20px;
  padding: 6px 5px;
}
</style>

<script>
export default {
  data() {
    const generateData = _ => {
      const data = [];
      const cities = ['中国', '日本', '美国', '俄罗斯', '比利时', '西班牙', '洪都拉斯'];
      const pinyin = ['zhongguo', 'riben', 'meiguo', 'eluosi', 'bilishi', 'xibanya', 'hongdulasi'];
      cities.forEach((city, index) => {
        data.push({
          label: city,
          key: index,
          pinyin: pinyin[index]
        });
      });
      return data;
    };
    return {
      data: generateData(),
      value: [1],
      value4: [1],
      renderFunc(h, option) {
        return <span>{ option.label }</span>;
      }
    };
  },

  methods: {
    handleChange(value, direction, movedKeys) {
      console.log(value, direction, movedKeys);
    }
  }
};
</script>
