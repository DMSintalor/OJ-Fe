<script>
import {defineComponent} from 'vue'
import {Delete, Edit, Hide, View} from "@element-plus/icons-vue";
import {encryptData} from '@/AES'

export default defineComponent({
    name: "adminProblemPage",
    computed: {
        Hide() {
            return Hide
        },
        View() {
            return View
        }
    },
    components: {Delete, Edit, View},
    data() {
        return {
            items: [],
            page_size: 20,
            page_num: 1,
            total: 1,
            pages: 1,
            tableLoading: false,
        }
    },
    mounted() {
        this.page_num = this.$route.params.pid ? this.$route.params.pid : this.page_num
        this.getPage()
    },
    methods: {
        getPage() {
            this.items = []
            this.tableLoading = true
            this.$axios.get('/admin/problem/all', {params: {size: this.page_size, page: this.page_num}}).then(res => {
                this.items = res.data.items
                this.tableLoading = false
                this.page_size = res.data.size
                this.page_num = res.data.page
                this.total = res.data.total
                this.pages = res.data.pages
            })
        },
        createProblem() {
            this.$router.push({
                name: 'adminProblemAdd',
            })
        },
        changePage(page) {
            this.$router.push({
                name: 'adminProblems',
                params: {
                    pid: page
                }
            })
            this.page_num = page
            this.getPage()
        },
        editProblem(r) {
            this.$router.push({
                name: 'adminProblemEdit',
                params: {
                    pid: encryptData(r.id)
                }
            })
        },
        deleteProblem(r) {
            this.$axios.delete('/admin/problem', {
                params: {pid: encryptData(r.id)}
            }).then(res => {
                if (res.data) {
                    this.$notice.success('Delete successfully!')
                    this.getPage()
                }
            })
        },
        changeVisible(row) {
            this.$axios.get('/admin/problem/visible', {
                params: {pid: encryptData(row.id)}
            }).then(res => {

            })
        }
    }
})
</script>
<template>
    <keep-alive>
        <div style="width: 60%;margin:0 auto;">
            <el-button type="primary"
                       style="margin-bottom: 10px;"
                       @click="createProblem"
            >Create Problem
            </el-button>
            <el-table
                    v-loading="tableLoading"
                    :data="items">
                <el-table-column label="ID" width="100" prop="id"></el-table-column>
                <el-table-column label="Title" prop="title"></el-table-column>
                <el-table-column label="Author" prop="author" width="150"></el-table-column>
                <el-table-column label="Create Time" prop="" width="150">
                    <template #default="slot">
                        {{ $formatDate(slot.row.create_time) }}
                    </template>
                </el-table-column>
                <el-table-column label="Visible" prop="" width="150" align="center">
                    <template #default="slot">
                        <el-tooltip :content="slot.row.status?'Hide this problem':'Display this problem'">
                            <el-switch v-model="slot.row.status"
                                       @change="changeVisible(slot.row)"
                                       active-color="rgb(0, 97, 174)"
                            ></el-switch>
                        </el-tooltip>
                    </template>
                </el-table-column>
                <el-table-column label="Operation" width="150" align="center">
                    <template #default="slot">
                        <el-tooltip content="Edit problem">
                            <el-button plain
                                       @click="editProblem(slot.row)"
                                       size="small">
                                <el-icon>
                                    <Edit/>
                                </el-icon>
                            </el-button>
                        </el-tooltip>
                        <el-tooltip content="Delete problem">
                            <el-button plain
                                       @click="deleteProblem(slot.row)"
                                       size="small">
                                <el-icon>
                                    <Delete/>
                                </el-icon>
                            </el-button>
                        </el-tooltip>
                    </template>
                </el-table-column>
            </el-table>
            <el-pagination
                    style="margin-top: 20px;"
                    v-model:current-page="page_num"
                    :hide-on-single-page="false"
                    :page-size="page_size"
                    layout="prev, pager, next"
                    :page-count="pages"
                    @current-change="changePage"
            ></el-pagination>
        </div>
    </keep-alive>
</template>

<style scoped>

</style>
