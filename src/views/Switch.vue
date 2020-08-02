<template>
    <el-table
            :data="dataSource"
            :border="true"
            :header-cell-style="{ 'font-weight': 'normal', 'text-align': 'center' }"
            :cell-style="{ 'text-align': 'center' }"
            size="mini"
            style="width: 100%"
            :span-method="arraySpanMethod"
    >
        <el-table-column type="index" width="40"></el-table-column>
        <el-table-column prop="product_name" label="产品名称" width="180"></el-table-column>
        <el-table-column prop="cap_name" label="能力名称" width="180"></el-table-column>
        <el-table-column prop="2019-12" label="2019-12" width="80"></el-table-column>
        <el-table-column prop="2020-01" label="2020-01" width="80"></el-table-column>
        <el-table-column prop="2020-02" label="2020-02" width="80"></el-table-column>
    </el-table>
</template>

<script>
    export default {
        name: "Switch",
        data(){
            return{
                msg:[
                    {
                        product_name: "aaaa",
                        cap_name: "na1",
                        "2019-12": 1,
                        "2020-01": 1,
                        "2020-02": 0
                    },
                    {
                        product_name: "aaaa",
                        cap_name: "na2",
                        "2019-12": 0,
                        "2020-01": 1,
                        "2020-02": 1
                    },
                    {
                        product_name: "bbbb",
                        cap_name: "nb1",
                        "2019-12": 1,
                        "2020-01": 1,
                        "2020-02": 0
                    },
                    {
                        product_name: "bbbb",
                        cap_name: "nb2",
                        "2019-12": 0,
                        "2020-01": 1,
                        "2020-02": 1
                    },
                    {
                        product_name: "bbbb",
                        cap_name: "nb3",
                        "2019-12": 0,
                        "2020-01": 1,
                        "2020-02": 1
                    },
                ]
            }
        },
        created() {
            const _this = this
            axios.get('http://localhost:8181/print').then(function (response) {
                _this.msg = response.data;
            })
        }
    }
</script>

<style scoped>

</style>