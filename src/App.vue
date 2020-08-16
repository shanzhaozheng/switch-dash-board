<template>
    <dev>
        <dev style="padding: 10px">
            <el-input v-model="myLabel" @input="change($event)" placeholder="请输要切换的标签" style="width: 150px"></el-input>
            <el-select v-model="value1" placeholder="请选择">
                <el-option
                        v-for="item in options"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                </el-option>
            </el-select>
            <el-button type="danger" @click="handleLabel(myLabel,value1)">根据标签信息切换</el-button>
            <el-button type="success" @click="clearCookie" plain>重新输入host地址</el-button>
        </dev>
        <el-table
                :data="newTable"
                :span-method="arraySpanMethod"
                border
                style="width: 100%">
            <el-table-column
                    fixed
                    prop="className"
                    label="控制器名称"
                    align="center"
                    width="400">
            </el-table-column>
            <el-table-column
                    prop="status"
                    label="控制器状态"
                    align="center"
                    width="120">
            </el-table-column>
            <el-table-column
                    prop="subField"
                    label="字段信息"
                    align="center">
                <el-table-column
                        prop="fieldName"
                        label="字段名称"
                        align="center"
                        width="200">
                </el-table-column>
                <el-table-column
                        prop="label"
                        label="对象标签"
                        align="center"
                        width="120">
                </el-table-column>
                <el-table-column
                        prop="field_status"
                        label="字段状态"
                        align="center"
                        width="120">
                </el-table-column>
                <el-table-column
                        fixed="right"
                        label="操作"
                        width="100">
                    <template slot-scope="scope">
                        <el-button @click="handleClick(scope.row)" type="text" size="small">切换</el-button>
                    </template>
                </el-table-column>
            </el-table-column>
        </el-table>
        <dev>
            <el-radio v-model="radio" label="1">全部切换至local</el-radio>
            <el-radio v-model="radio" label="2">全部切换至remote</el-radio>
            <el-button type="danger" @click="handleAll(radio)">执行切换</el-button>
        </dev>
    </dev>
</template>

<script>
    import Vue from "vue";

    export default {
        methods: {
            handleAll(doswitch) {
                if (doswitch == 1) {
                    axios.post(this.host + this.url + '/local/all').then(function (response) {
                        console.log(response)
                    })
                } else if (doswitch == 2) {
                    axios.post(this.host + this.url + '/remote/all').then(function (response) {
                        console.log(response)
                    })
                }
                location.reload()
            },
            change(e) {
                this.$forceUpdate()
            },
            handleLabel(label, doswitch) {
                console.log(label)
                console.log(doswitch)
                if (doswitch == "local") {
                    axios.post(this.host + this.url + '/local/' + label).then(function (response) {
                        console.log(response)
                    })
                } else if (doswitch == "remote") {
                    axios.post(this.host + this.url + '/remote/' + label).then(function (response) {
                        console.log(response)
                    })
                }
                location.reload()
            },
            handleClick(row) {
                console.log(row);
                let className = row.className;
                let fieldName = row.fieldName;
                if (row.field_status == "FeignBus") {
                    axios.post(this.host + this.url + '/local/target/' + className + '/' + fieldName).then(function (response) {
                        console.log(response)
                    })
                } else if (row.field_status == "LocalBus") {
                    axios.post(this.host + this.url + '/remote/target/' + className + '/' + fieldName).then(function (response) {
                        console.log(response)
                    })
                }
                location.reload()
            },
            arraySpanMethod({row, column, rowIndex, columnIndex}) {
                const _this = this
                if (columnIndex === 0 || columnIndex === 1) {
                    if (row.flag === 0) {
                        return {
                            rowspan: _this.spanArray[rowIndex],
                            colspan: 1
                        };
                    }else {
                        return {
                            rowspan: 0,
                            colspan: 0
                        }
                    }
                }else{
                    return {
                        rowspan: 1,
                        colspan: 1
                    }
                }
            },
            handleTableData(data, _this) {
                let newArr = [];
                data.forEach(function (item, index) {
                    for (let i = 0; i < item.subField.length; i++) {
                        let current;
                        if (i === 0){
                            current = {
                                className: item.className,
                                status: item.status,
                                fieldName: item.subField[i].fieldName,
                                label: item.subField[i].label,
                                field_status: item.subField[i].status,
                                flag: 0
                            }
                            _this.spanArray.push(item.subField.length)
                        }else {
                            current = {
                                className: item.className,
                                status: item.status,
                                fieldName: item.subField[i].fieldName,
                                label: item.subField[i].label,
                                field_status: item.subField[i].status,
                                flag: 1
                            }
                            _this.spanArray.push(1)
                        }
                        newArr.push(current)
                    }
                })

                console.log(_this.spanArray)
                return newArr;
            },
            setCookie: function (cname, cvalue, exdays) {
                var d = new Date();
                d.setTime(d.getTime() + (exdays * 24 * 60 * 60 * 1000));
                var expires = "expires=" + d.toUTCString();
                console.info(cname + "=" + cvalue + "; " + expires);
                document.cookie = cname + "=" + cvalue + "; " + expires;
                console.info(document.cookie);
            },
            //获取cookie
            getCookie: function (cname) {
                var name = cname + "=";
                var ca = document.cookie.split(';');
                console.log("获取cookie,现在循环")
                for (var i = 0; i < ca.length; i++) {
                    var c = ca[i];
                    console.log(c)
                    while (c.charAt(0) == ' ') c = c.substring(1);
                    if (c.indexOf(name) != -1) {
                        return c.substring(name.length, c.length);
                    }
                }
                return "";
            },
            //清除cookie
            clearCookie: function () {
                this.setCookie("host", "", -1);
                location.reload()

            },
            checkCookie: function () {
                var user = this.getCookie("username");
                if (user != "") {
                    alert("Welcome again " + user);
                } else {
                    user = prompt("Please enter your name:", "");
                    if (user != "" && user != null) {
                        this.setCookie("username", user, 365);
                    }
                }
            }
        },
        data() {
            return {
                host: '',
                url: '/api/course/switch',
                radio: '1',
                options: [{
                    value: 'local',
                    label: 'local'
                }, {
                    value: 'remote',
                    label: 'remote'
                }],
                value1: "local",
                spanArray: [],
                newTable: [],
                tableData: [{
                    className: "com.szz.hello.controller.HelloWord",
                    status: "FeignBus",
                    subField: [{
                        fieldName: "service",
                        label: "A",
                        status: "FeignBus"
                    }]
                }]
            }
        },
        created: function () {
            const _this = this
            let showcookie = _this.getCookie('host');
            if (showcookie == "" || showcookie == null) {
                let host = prompt("请输入一段文字");
                if (host != "") {
                    showcookie = host;
                }
                _this.setCookie('host', showcookie, 7)
            }
            _this.host = showcookie;
            axios.get(_this.host + _this.url + '/print').then(function (response) {
                _this.tableData = response.data
                let data = response.data
                _this.newTable = _this.handleTableData(data, _this)
                console.log(_this.newTable)
            })
        },

    }
</script>