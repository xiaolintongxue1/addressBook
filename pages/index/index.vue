<template>
	<view>
		<txl @ev="evFunc" :datas="datas" :index="false" name="employeeName"></txl>
	</view>
</template>

<script>
import txl from '@/components/yt-txl/index.vue';

export default {
	components:{
		txl
	},
	data() {
		let datas = [{
        "employeeId": "test1",
        "employeeName": "刘志远",
        "companyId": "1",
        "companyName": "test company",
        "departmentId": "testdeptid",
        "departmentName": "测试部",
        "postId": "testpostid",
        "postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "刘志远",
		"companyId": "1",
		"companyName": "test company",
		"departmentId": "testdeptid",
		"departmentName": "测试部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test3333",
		"employeeName": "周兴达",
		"companyId": "1",
		"companyName": "test company",
		"departmentId": "testdeptid",
		"departmentName": "运营部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "李霄",
		"companyId": "1",
		"companyName": "test company",
		"departmentId": "testdeptid",
		"departmentName": "设计部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "王广望",
		"companyId": "1",
		"companyName": "test company",
		"departmentId": "testdeptid",
		"departmentName": "策划部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "小冰冰",
		"companyId": "1",
		"companyName": "test company",
		"departmentId": "testdeptid",
		"departmentName": "编辑部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "小冰冰",
		"companyId": "1",
		"companyName": "test company",
		"departmentId": "testdeptid",
		"departmentName": "客服部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "小冰冰",
		"companyId": "1",
		"companyName": "test company",
		"departmentId": "testdeptid",
		"departmentName": "市场部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "小冰冰",
		"companyId": "0",
		"companyName": "test company2",
		"departmentId": "testdeptid",
		"departmentName": "市场部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "小冰冰",
		"companyId": "0",
		"companyName": "test company2",
		"departmentId": "testdeptid",
		"departmentName": "市场部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    },{
        "employeeId": "test1",
		"employeeName": "小冰冰",
		"companyId": "0",
		"companyName": "test company2",
		"departmentId": "testdeptid",
		"departmentName": "市场部",
		"postId": "testpostid",
		"postName": "testpost",
		"tel":"18720710529"
    }]
		return {
			datas: datas
		}
	},
	onLoad() {
		
	},
	methods: {

	}
}
</script>

<style>
	
</style>
