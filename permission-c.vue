<template>
    <div>
        <div class="app-page-body">
            <div class="app-table-actions" style="position: relative">
                <el-button icon="el-icon-delete" disabled>批量删除</el-button>
                <div class="pull-right">
                    <el-form :inline="true" ref="searchForm" class="pull-left">
                        <el-form-item :label="item.label" :prop="item.vModel" v-for="(item, index) in searchFields">
                            <el-input v-model="searchForm[item.vModel]" :placeholder="item.label"
                                      v-if="item.tag === 'el-input'"
                            />
                            <el-select v-model="searchForm[item.vModel]"
                                       v-if="item.tag === 'el-select' || item.tag === 'el-radio-group' || item.tag === 'el-checkbox-group'"
                                       :multiple="item.tag === 'el-checkbox-group'"
                            >
                                <el-option :label="option.label" :value="option.value"
                                           v-for="option in item.options"
                                ></el-option>
                            </el-select>
                            <el-cascader v-model="searchForm[item.vModel]" v-if="item.tag === 'el-cascader'"
                                         :options="item.options"
                            ></el-cascader>
                            <el-date-picker v-model="searchForm[item.vModel]"
                                            v-if="item.tag === 'el-date-picker'"
                            ></el-date-picker>
                            <el-time-picker v-model="advancedSearchForm[item.vModel]"
                                            v-if="item.tag === 'el-time-picker'"
                            ></el-time-picker>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" icon="el-icon-search"></el-button>
                        </el-form-item>
                    </el-form>
                    <el-button class="pull-right left-1" type="text"
                               @click="advancedSearchWrap = !advancedSearchWrap"
                               v-if="advancedSearchChecked.length"
                    >高级查询
                    </el-button>
                </div>
                <el-collapse-transition>
                    <div v-show="advancedSearchWrap && advancedSearchChecked.length" class="super-search-wrap">
                        <el-form class="super-search-form" :model="advancedSearchForm" ref="advancedSearchForm"
                                 label-position="right" label-width="100px"
                        >
                            <el-row>
                                <el-col :span="8" v-for="(item, index) in advancedSearchFields">
                                    <el-form-item :label="item.label" :prop="item.vModel">
                                        <el-input v-model="advancedSearchForm[item.vModel]"
                                                  :placeholder="item.label"
                                                  v-if="item.tag === 'el-input'"
                                        />
                                        <el-select v-model="advancedSearchForm[item.vModel]"
                                                   v-if="item.tag === 'el-select' || item.tag === 'el-radio-group' || item.tag === 'el-checkbox-group'"
                                                   :multiple="item.tag === 'el-checkbox-group'" style="width: 100%"
                                        >
                                            <el-option :label="option.label" :value="option.value"
                                                       v-for="option in item.options"
                                            ></el-option>
                                        </el-select>
                                        <el-cascader v-model="advancedSearchForm[item.vModel]"
                                                     v-if="item.tag === 'el-cascader'"
                                                     :options="item.options" style="width: 100%"
                                        ></el-cascader>
                                        <el-date-picker v-model="advancedSearchForm[item.vModel]"
                                                        v-if="item.tag === 'el-date-picker'" style="width: 100%"
                                        ></el-date-picker>
                                        <el-time-picker v-model="advancedSearchForm[item.vModel]"
                                                        v-if="item.tag === 'el-time-picker'" style="width: 100%"
                                        ></el-time-picker>
                                    </el-form-item>
                                </el-col>
                                <el-col :span="8">
                                    <el-form-item>
                                        <el-button type="primary">查询</el-button>
                                        <el-button>重置</el-button>
                                    </el-form-item>
                                </el-col>
                            </el-row>
                        </el-form>
                    </div>
                </el-collapse-transition>
            </div>
            <el-table
                :data="dataList"
                fit
                highlight-current-row
            >
                <el-table-column
                    type="selection"
                    width="55"
                    align="center"
                ></el-table-column>
                <el-table-column :prop="item.vModel" :label="item.label" v-for="item in tableColumns">
                </el-table-column>
                <el-table-column label="操作" width="200px;">
                    <template slot-scope="scope">
                        <span v-permission="['overallmerit:model:dispose']">
                <el-link type="primary" @click="edit(scope.row)">编辑</el-link>
                <el-divider direction="vertical"></el-divider>
              </span><span v-permission="['overallmerit:model:dispose']">
                <el-link type="primary" @click="deleteData(scope.row)">删除</el-link>
                        <!--<el-divider direction="vertical"></el-divider>-->
              </span>
                    </template>
                </el-table-column>
            </el-table>
            <!--<pagination v-show="total>0" :total="total" :page.sync="pageParams.page" :limit.sync="pageParams.limit"-->
            <!--            @pagination="getList"-->
            <!--&gt;</pagination>-->
        </div>
    </div>
</template>


<script>
// import { state, mutations } from '../form/store/store'

const validFieldTag = ['el-input', 'el-select', 'el-cascader', 'el-date-picker', 'el-time-picker', 'el-radio-group',
    'el-checkbox-group', 'el-switch']

export default {
    name: 'FormPage',
    props: {
        formData: {
            type: Object,
            required: true
        },
        pageData: {
            type: Object,
            required: true
        },
        dataList: {
            type: Array,
            required: false
        }
    },
    data() {
        let formConfig = _.cloneDeep(this.formData)
        // let formConfig = this.formData ? JSON.parse(JSON.stringify(this.formData)) : {}
        let searchForm = this.buildSearchForm(formConfig.fields)
        let advancedSearchForm = this.buildSearchForm(formConfig.fields)

        return {
            settingDialogVisible: false,
            // dataList: [],
            fullFields: [],
            searchFields: [],
            advancedSearchFields: [],
            tableColumns: [],
            searchChecked: [],
            advancedSearchChecked: [],
            tableColumnChecked: [],
            formConfig: formConfig,
            searchForm: searchForm,
            advancedSearchForm: advancedSearchForm,
            advancedSearchWrap: false,
            total: '',
            // 分页参数
            pageParams: {
                page: 1,
                limit: 10
            }
        }

    },
    created() {
        // let pageConfig = state.page
        let pageConfig = this.pageData
        // let formConfig = _.cloneDeep(this.formData)
        // filter form fields by pageConfig
        // let searchForm = this.buildSearchForm(formConfig.fields)
        // let advancedSearchForm = this.buildSearchForm(formConfig.fields)
        this.fullFields = this.filterFields(this.formConfig.fields)
        this.searchFields = this.filterFields(pageConfig.search).slice(0, 2)
        this.advancedSearchFields = this.filterFields(pageConfig.advancedSearch)
        this.tableColumns = this.filterFields(pageConfig.table)
        this.initSetting()
    },
    methods: {
        // 请求两个数据
        filterFields(collection) {
            return this.formConfig.fields.filter((item) => {
                return validFieldTag.includes(item.tag) && collection.some((currentValue) => {
                    return currentValue.vModel === item.vModel
                })
            })
        },
        buildSearchForm(formFields) {
            let obj = {}
            formFields.forEach((item) => {
                if (item.vModel) {
                    obj[item.vModel] = undefined
                }
            })
            return obj
        },
        /*     callSettingDialog() {
         this.settingDialogVisible = true
         },
         */
        initSetting() {
            this.searchChecked = this.searchFields.map((item) => item.vModel)
            this.advancedSearchChecked = this.advancedSearchFields.map((item) => item.vModel)
            this.tableColumnChecked = this.tableColumns.map((item) => item.vModel)
            console.log(this.tableColumns)
        },
        handlePageSetting() {
            this.searchFields = this.filterFields(this.searchChecked.map(item => {
                return { vModel: item }
            }))
            this.advancedSearchFields = this.filterFields(this.advancedSearchChecked.map(item => {
                return { vModel: item }
            }))
            this.tableColumns = this.filterFields(this.tableColumnChecked.map(item => {
                return { vModel: item }
            }))
            this.settingDialogVisible = false
        },
        deleteData(row) {
            this.$emit('delete', row)
        },
        edit(row) {
            this.$emit('edit', row)
        }
        // save() {
        //     mutations.setPage({
        //         search: this.searchFields,
        //         advancedSearch: this.advancedSearchFields,
        //         table: this.tableColumns
        //     })
        //     this.$emit('save', 'page')
        // }

    },
    computed: {
        baseInfo() {
            return state.baseInfo
        }
    }
}
</script>

<style lang="scss" scoped>

.page-setting-section {
    margin-bottom: 16px;
}

.page-setting-title {
    font-size: 14px;
    font-weight: 700;
    line-height: 40px;
}

.page-setting-title i {
    margin-right: 8px;
    color: rgba(0, 0, 0, .45);
}

.page-setting-title .meta {
    font-size: 12px;
    color: rgba(0, 0, 0, .25)
}

.el-dialog__body {
    padding: 10px 20px 20px 20px;
}

.el-checkbox-group {
    line-height: 1.75;
}
</style>

<style lang="scss">

.app-page-h {
    background-color: #f9f9f9;
    padding: 5px;

    .app-page-header__title {
        font-size: 14px !important;
    }
}
</style>
