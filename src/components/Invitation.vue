<template>
    <div class="container">
        <el-form ref="form" class="form" label-position="top">
            <h1 class="form-header">加入Github组织</h1> <!-- 标题直接放置在el-form中，便于居中 -->
            <el-form-item>
                <el-input v-model="invitees" placeholder="邮箱或用户名"></el-input>
            </el-form-item>
            <div class="button-group">
                <el-button type="primary" @click="apply">申请加入</el-button>
                <el-button style="background-color: #1f9bcf; color: white;" @click="accept">接受邀请</el-button>
            </div>
            <div class="form-footer">
                未经过长期测试，可能存在封号风险
                <p style="font-size: 16px;font-weight: bold;">禁止使用大号加入</p>
            </div>
        </el-form>
    </div>
</template>

<script>
import axios from 'axios'
import { ref } from 'vue'
import { ElMessage } from 'element-plus';

export default {
    name: 'InvitationComponent',
    mounted() {
        document.title = "加入Github组织";
    },
    setup: function () {
        const invitees = ref('');
        const url = ref('');

        const apply = async () => {
            // 判断invitees是否为空
            if (invitees.value.trim() === '') {
                // 如果为空，则弹出提示消息
                ElMessage({
                    message: '请输入邮箱或用户名',
                    type: 'error',
                    duration: 3000 // 持续时间（毫秒）
                });
            } else {
                try {
                    const response = await axios.get(`https://github-organization-invitation.lemonzuo.org/api/inviteUser?invitees=${encodeURIComponent(invitees.value)}`);
                    let res = response.data;
                    console.log(res);
                    if (res.code === -1) {
                        // 失败
                        ElMessage({
                            message: res.message,
                            type: 'error',
                            duration: 3000 // 持续时间（毫秒）
                        });
                    } else {
                        // 成功
                        ElMessage({
                            message: res.message,
                            type: 'success',
                            duration: 3000 // 持续时间（毫秒）
                        });
                        // 取出服务端返回的URL作为跳转
                        url.value = res.url;
                        // 清空invitees
                        invitees.value = '';
                    }
                } catch (error) {
                    console.error(error);
                    ElMessage({
                        message: '请求服务端失败',
                        type: 'error',
                        duration: 3000 // 持续时间（毫秒）
                    });
                }
            }
        };

        const accept = () => {
            if (url.value) {
                window.open(url.value, '_blank').focus()
            } else {
                ElMessage({
                    message: '未申请加入或申请加入失败，无法跳转接受邀请',
                    type: 'error',
                    duration: 3000 // 持续时间（毫秒）
                });
            }
            // 跳转完清空
            url.value = '';
        }

        return {
            invitees,
            apply,
            accept
        }
    }
}
</script>

<style scoped>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    background-color: #f5f5f5;
}

.form-header {
    text-align: center;
    margin-bottom: 20px;
}

.form {
    width: 100%;
    max-width: 500px;
    padding: 25px;
    box-shadow: 0 2px 12px 0 rgba(0,0,0,0.1);
    border-radius: 10px;
    background: white;
    position: relative; /* 为了正确地定位form-header */
}

.el-form-item {
    margin-bottom: 24px;
}

.button-group {
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px; /* 增加了按钮下方的空间 */
}

.el-button {
    width: 48%;
    margin: 0;
}

.el-button:first-of-type {
    margin-right: 4%;
}

.form-footer {
    font-size: 14px;
    color: #ff4949;
    text-align: center;
}

@media (max-width: 600px) {
    .form {
        margin: 20px;
        width: auto;
    }
}
</style>
