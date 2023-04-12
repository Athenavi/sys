<template>
    <div style="padding: 10px">
        <el-row :gutter="20">
            <el-col :span="12">
                <el-card>
                    <div style="padding: 10px 0; font-size: 18px">关于</div>
                    <div class="demo-image">
                        <div v-for="fit in fits" :key="fit" class="block">
                            <span class="demonstration">{{ fit }}</span>
                            <el-image
                                :fit="fit"
                                :src="url"
                                style="width: 100px; height: 100px"></el-image>
                        </div>
                    </div>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script>
export default {
    data() {
        return {
            fits: ['fill', 'contain', 'cover', 'none', 'scale-down'],
            url: 'http://www.tangzy.xyz/usr/uploads/2022/06/2730781865.jpg'
        }
    }
}
</script>

<style scoped>

</style>
