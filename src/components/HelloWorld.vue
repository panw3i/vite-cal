<template>
  <div>
    <el-form label-position="left" label-width="150px">
      <el-form-item label="贷款总额（万元）：">
        <el-input v-model="principal" placeholder="贷款总额"></el-input>
      </el-form-item>
      <el-form-item label="年利率（原）：">
        <el-input v-model="originalRate" placeholder="年利率">
          <template #suffix>%</template>
        </el-input>
      </el-form-item>
      <el-form-item label="年利率（新）：">
        <el-input v-model="newRate" placeholder="年利率">
          <template #suffix>%</template>
        </el-input>
      </el-form-item>
      <el-form-item label="贷款期限（月）：">
        <el-input v-model="term" placeholder="贷款期限">
          <template #suffix>月</template>
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="calculate">计算</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="month" label="月份"></el-table-column>
      <el-table-column prop="originalBenxi" label="等额本息（原利率）"></el-table-column>
      <el-table-column prop="newBenxi" label="等额本息（新利率）"></el-table-column>
      <el-table-column prop="originalBenjin" label="等额本金（原利率）"></el-table-column>
      <el-table-column prop="newBenjin" label="等额本金（新利率）"></el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      principal: '',
      originalRate: '',
      newRate: '',
      term: '',
      tableData: []
    };
  },
  methods: {
    calculateMonthlyPayment(p, r, t) {
      return (p * r * Math.pow(1 + r, t)) / (Math.pow(1 + r, t) - 1);
    },
    calculate() {
      const principal = this.principal * 10000;
      const originalRate = this.originalRate / 100 / 12;
      const newRate = this.newRate / 100 / 12;
      const term = parseInt(this.term);

      let originalBenxi = this.calculateMonthlyPayment(principal, originalRate, term);
      let newBenxi = this.calculateMonthlyPayment(principal, newRate, term);

      let originalMonthlyPrincipal = principal / term;
      let newMonthlyPrincipal = principal / term;

      let tableData = [];

      for (let month = 1; month <= term; month++) {
        let originalMonthlyInterest = (principal - (month - 1) * originalMonthlyPrincipal) * originalRate;
        let newMonthlyInterest = (principal - (month - 1) * newMonthlyPrincipal) * newRate;

        let originalBenjin = originalMonthlyPrincipal + originalMonthlyInterest;
        let newBenjin = newMonthlyPrincipal + newMonthlyInterest;

        tableData.push({
          month: month,
          originalBenxi: originalBenxi.toFixed(2),
          newBenxi: newBenxi.toFixed(2),
          originalBenjin: originalBenjin.toFixed(2),
          newBenjin: newBenjin.toFixed(2),
        });
      }

      this.tableData = tableData;
    }
  }
};
</script>
