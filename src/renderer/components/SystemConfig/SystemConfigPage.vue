<template>
	<div class="content-page">
		<div class="content-nav">
			<el-breadcrumb class="breadcrumb" separator="/">
				<el-breadcrumb-item :to="{ path: '/dashboard' }">首页</el-breadcrumb-item>
				<el-breadcrumb-item>配置管理</el-breadcrumb-item>
				<el-breadcrumb-item>系统配置</el-breadcrumb-item>
			</el-breadcrumb>
			<!-- <div class="operation-nav">
				<router-link to="/dashboard/systemConfig/add">
					<el-button type="primary" icon="plus">添加配置</el-button>
				</router-link>
			</div> -->
		</div>
		<div class="content-main">
			<div class="form-table-box">
				<el-table :data="tableData" style="width: 100%" border stripe>
					<el-table-column prop="id" label="ID" width="100">
					</el-table-column>
					<el-table-column prop="key" label=键>
					</el-table-column>
					<el-table-column prop="value" label="值">
					</el-table-column>
					<el-table-column prop="description" label="描述">
					</el-table-column>
					
					<el-table-column label="操作" width="140">
						<template scope="scope">
							<el-button size="small" @click="handleRowEdit(scope.$index, scope.row)">编辑</el-button>
							<el-button size="small" v-if="isShowDelete" type="danger" @click="handleRowDelete(scope.$index, scope.row)">删除</el-button>
						</template>
					</el-table-column>
				</el-table>
			</div>
			<div class="page-box">
				<el-pagination @current-change="handlePageChange" :current-page="page" :page-size="10" layout="total, prev, pager, next, jumper" :total="total">
				</el-pagination>
			</div>
		</div>
	</div>
</template>

<script>


import DateUtil from "@/js/DateUtil";
import api from "@/config/api";
export default {
	data() {
		return {
			isShowDelete : false,
			page: 1,
			total: 0,
			filterForm: {
				name: '',
				nickname:'',
			},
			tableData: []
		}
	},
	methods: {
		handlePageChange(val) {
			this.page = val;
			this.getList()
		},
		handleRowEdit(index, row) {
			this.$router.push({ name: 'systemConfig_add', query: { id: row.id } })
		},
		handleRowDelete(index, row) {

			this.$confirm('确定要删除?', '提示', {
				confirmButtonText: '确定',
				cancelButtonText: '取消',
				type: 'warning'
			}).then(() => {

				this.axios.post('systemConfig/destory', { id: row.id }).then((response) => {
					console.log(response.data)
					if (response.data.errno === 0) {
						this.$message({
							type: 'success',
							message: '删除成功!'
						});

						this.getList();
					}
				})


			});
		},
		onSubmitFilter() {
			this.page = 1
			this.getList()
		},
		getList() {
			this.axios.get('systemConfig', {
				params: {
					page: this.page
				}
			}).then((response) => {
                this.tableData = response.data.data.data
                this.page = response.data.data.currentPage
                this.total = response.data.data.count
			})
		}
	},
	components: {

	},
	mounted() {
		 this.isShowDelete = api.isShowDelete;
		this.getList();
	},

	filters: {
        formatDate(time) {
			var date = new Date(time*1000);
            return DateUtil.formatDate(date, "yyyy-MM-dd hh:mm:ss");
        }
    }

}

</script>

<style>
.image-show {
  width: 60px;
  height: 60px;
  display: block;
}

</style>
