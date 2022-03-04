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
            <template slot-scope="scope">
              <el-input-number clearable size="mini" v-model="scope.row.debt" :step = 50></el-input-number>
            </template>
          </el-table-column>
          <el-table-column
            prop="owe"
            label="应还（自动计算）"
            width="180">
            <template slot-scope="scope">
              <p>{{Math.round(scope.row.debt * 1.1)}}</p>
            </template>
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
            <template slot-scope="scope">
              <el-input clearable size="mini" v-model="scope.row.random"></el-input>
              <el-button size="mini" @click="handleRandom(scope.$index)">计算</el-button>
            </template>
          </el-table-column>
          <el-table-column
            prop="actualAsset"
            label="实际总资产（自动计算）"
            width="180">
            <template slot-scope="scope">
              <p>{{scope.row.cash - Math.round(scope.row.debt * 1.1)}}</p>
            </template>
          </el-table-column>
        </el-table>
      </template>
    </div>
<!--    <p style="text-align: center; margin: 20px 0 20px">国家归属</p>-->
    <el-autocomplete
      v-model="state"
      :fetch-suggestions="querySearchAsync"
      placeholder="请输入内容"
      @select="handleSelect"
    ></el-autocomplete>
    <el-button style="text-align: center; margin: 20px 0 20px" size="mini" @click="handleReleaseForbidden">禁用/解除</el-button>
    <div style="text-align: center">
      <el-transfer
        style="text-align: left; display: inline-block;"
        v-model="valueA"
        filterable
        :render-content="renderFunc"
        :titles="['国家列表', '张汉东']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChangeCountryBelongByA"
        :data="data">

      </el-transfer>
      <el-transfer
        style="text-align: left; display: inline-block;"
        v-model="valueAImpawn"
        filterable
        :render-content="renderFunc"
        :titles="['张汉东可抵押', '已抵押']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChangeCountryImpawnByA"
        :data="dataAImpawn">
      </el-transfer>
    </div>
<!--    <div><el-divider></el-divider></div>-->
    <div style="text-align: center">
      <el-transfer
        style="text-align: left; display: inline-block;"
        v-model="valueB"
        filterable
        :render-content="renderFunc"
        :titles="['国家列表', '王涵民']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChangeCountryBelongByB"
        :data="data">
      </el-transfer>

      <el-transfer
        style="text-align: left; display: inline-block;"
        v-model="valueBImpawn"
        filterable
        :render-content="renderFunc"
        :titles="['王涵民可抵押', '已抵押']"
        :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}'
      }"
        @change="handleChangeCountryImpawnByB"
        :data="dataBImpawn">
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
          label: city.name,
          key: city.name + index,
          disabled: false,
          original: city
        });
      });
      return data;
    };
    return {
      data: generateData(),
      dataAImpawn: [],
      dataBImpawn: [],
      valueA: [-1],
      valueB: [-1],
      valueAImpawn: [],
      valueBImpawn: [],
      disableAll: true,
      state: '',
      allCountry: [],
      timeout: null,
      renderFunc(h, option) {
        return <span>{ option.label }</span>;
      },
      tableData: [{
        name: '张汉东',
        cash: 800,
        debt: 0,
        owe: 0,
        country: 0,
        countryInImpawn: 0,
        random: 0,
        actualAsset: 0
      }, {
        name: '王涵民',
        cash: 800,
        debt: 0,
        owe: 0,
        country: 0,
        countryInImpawn: 0,
        random: 0,
        actualAsset: 0
      }]
    };
  },

  methods: {
    handleChangeCountryBelongByA(value, direction, movedKeys) {
      movedKeys.forEach(obj => {
        let idx = this.data.findIndex(cur => cur.key === obj)
        this.dataAImpawn.push(JSON.parse(JSON.stringify(this.data[idx])))
        this.data[idx].disabled = true
      })
      if (direction === 'right') {
        this.tableData[0].country += movedKeys.length
      } else if (direction === 'left') {
        this.tableData[0].country -= movedKeys.length
      }
    },
    handleChangeCountryBelongByB(value, direction, movedKeys) {
      movedKeys.forEach(obj => {
        let idx = this.data.findIndex(cur => cur.key === obj)
        this.dataBImpawn.push(JSON.parse(JSON.stringify(this.data[idx])))
        this.data[idx].disabled = true
      })
      if (direction === 'right') {
        this.tableData[1].country += movedKeys.length
      } else if (direction === 'left') {
        this.tableData[1].country -= movedKeys.length
      }
    },
    handleChangeCountryImpawnByA(value, direction, movedKeys) {
      if (direction === 'right') {
        movedKeys.forEach(obj => {
          let idx = this.data.findIndex(cur => cur.key === obj)
          this.tableData[0].cash += this.data[idx].original.impawn
          this.tableData[0].actualAsset += this.data[idx].original.impawn
        })
        this.tableData[0].countryInImpawn += movedKeys.length
      } else if (direction === 'left') {
        movedKeys.forEach(obj => {
          let idx = this.data.findIndex(cur => cur.key === obj)
          this.tableData[0].cash -= this.data[idx].original.impawn
          this.tableData[0].actualAsset -= this.data[idx].original.impawn
        })
        this.tableData[0].countryInImpawn -= movedKeys.length
      }
    },
    handleChangeCountryImpawnByB(value, direction, movedKeys) {
      if (direction === 'right') {
        movedKeys.forEach(obj => {
          let idx = this.data.findIndex(cur => cur.key === obj)
          this.tableData[1].cash += this.data[idx].original.impawn
          this.tableData[1].actualAsset += this.data[idx].original.impawn
        })
        this.tableData[1].countryInImpawn += movedKeys.length
      } else if (direction === 'left') {
        movedKeys.forEach(obj => {
          let idx = this.data.findIndex(cur => cur.key === obj)
          this.tableData[1].cash -= this.data[idx].original.impawn
          this.tableData[1].actualAsset -= this.data[idx].original.impawn
        })
        this.tableData[1].countryInImpawn -= movedKeys.length
      }
    },
    handleRandom(index) {
      // console.log(this.tableData[index].actualAsset)
      // this.tableData[index].actualAsset = this.tableData[index].actualAsset + parseInt(this.tableData[index].random)
      this.tableData[index].cash = this.tableData[index].cash + parseInt(this.tableData[index].random)
    },
    handleReleaseForbidden() {
      this.disableAll = !this.disableAll
      this.data.forEach(obj => {
        obj.disabled = this.disableAll
      })
    },
    querySearchAsync(queryString, cb) {
      var countries = this.allCountry;
      var results = queryString ? countries.filter(this.createStateFilter(queryString)) : countries;
      console.log("results", results)
      cb(results);
      // clearTimeout(this.timeout);
      setTimeout(() => {
        cb(results);
      }, 1000);
    },
    createStateFilter(queryString) {
      return (state) => {
        console.log(queryString)
        return (state.value.indexOf(queryString) === 0);
      };
    },
    handleSelect(item) {
      console.log(item);
    }
  },
  mounted() {
    const data = []
    const cities = countryList
    cities.forEach((city, index) => {
      data.push({
        value: city.name,
      });
    });
    this.allCountry = data
  }
};
</script>
