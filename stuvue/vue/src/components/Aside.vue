<template>
    <div>
        <el-menu
            :default-active="$route.path"
            router
            style="width: 200px; min-height: calc(100vh - 50px)"
        >
            <div v-for="m in user.permissions" :key="m.id">
                <el-menu-item v-if="m.name !== 'Person' && m.name !== 'Password' " :index="m.path">
                    <i :class="m.icon"></i> {{ m.comment }}
                </el-menu-item>
            </div>
        </el-menu>
    </div>
</template>

<script>

export default {
    name: "Aside",
    data() {
        return {
            user: {}
        }
    },
    created() {
        let userStr = sessionStorage.getItem("user") || "{}"
        this.user = JSON.parse(userStr)

        // 请求服务端，确认当前登录用户的 合法信息
        // request.get("/user/" + this.user.id).then(res => {
        //   if (res.code === '0') {
        //     this.user = res.data
        //   }
        // })
    }
}
</script>

<style scoped>

</style>
