<template>
    <div style="width: 95%;">
        <div v-if="key == ''" style="text-align: center;">
            <q-icon name="warning" color="warning" size="4rem" />
            Please setup the ApiKey by setting
        </div>
        <div style="width: 100%; " v-else>
            <!-- <q-chat-message
                label="Sunday, 19th"
            /> -->
            <q-chat-message
                v-for="item in chatList"
                :name="item.type"
                :avatar="item.type == 'robot' ? avatarRobot : avatarMe"
                :text="[item.text]"
                :sent="item.type == 'robot' ? false : true"
            />

        </div>
        <div style="background-color: #f6f6f6;padding: 5px;border-top-left-radius: 6px;border-top-right-radius: 6px;" class="shadow-up-7 fixed-bottom" v-if="key != ''">
            <q-input bottom-slots v-model="text" label="" :dense="dense">
                <template v-slot:before>
                    <q-avatar>
                        <img src="https://cdn.quasar.dev/img/avatar5.jpg">
                    </q-avatar>
                </template>

                <template v-slot:append>
                    <q-icon v-if="text !== ''" name="close" @click="text = ''" class="cursor-pointer" />
                </template>

                <template v-slot:after>
                    <q-btn round dense flat icon="send" @click="send" :loading="waiting"/>
                </template>
            </q-input>
        </div>
    </div>
</template>

<script lang="ts">
import {
    defineComponent
} from 'vue';
import { inject } from 'vue'
import { Configuration, OpenAIApi } from "openai";
let configuration:any = null;
let openai:any = null;
export default defineComponent({
    name: 'Chat',
    data() {
        return {
            key: '',
            text: '',
            waiting: false,
            avatarRobot: 'https://cdn.quasar.dev/img/avatar3.jpg',
            avatarMe: 'https://cdn.quasar.dev/img/avatar4.jpg',
            chatList: [{
                type: 'me',
                stamp: '5 minutes ago',
                text: 'Are you there?'
            }, {
                type: 'robot',
                stamp: '4 minutes ago',
                text: 'Yes i am here,can i help you, hahaha you are so smart'
            },]
        }
    },
    created() {
        this.key = localStorage.getItem('key') || '';
        const bus:any = inject('bus')
        bus.on('keychange', (key: any) => {
            this.key = key;
        })
        this.reConnection();
    },
    methods: {
        send() {
            if(this.waiting) {
                this.$q.notify({
                    color: '',
                    message: 'Waiting for result',
                    position: 'top',
                    icon: ''
                });
                return;
            }
            this.chatList.push({
                type: 'me',
                stamp: 'now',
                text: this.text
            });
            this.text = '';
            this.waiting = true;
        },
        reConnection() {
            if(this.key) {
                configuration = new Configuration({
                    apiKey: this.key+'0',
                });
                openai = new OpenAIApi(configuration);
                const response = openai.createCompletion({
                    model: "text-davinci-003",  // 对话模型的名称
                    prompt: this.text,
                    temperature: 0.9,  // 值在[0,1]之间，越大表示回复越具有不确定性
                    max_tokens: 3600,  // 回复最大的字符数
                    top_p: 1,
                    frequency_penalty: 0.06,  // [-2,2]之间，该值越大则更倾向于产生不同的内容
                    presence_penalty: 0.05,  // [-2,2]之间，该值越大则更倾向于产生不同的内容
                    stop: ["#"]
                }).then((rs: any) => {
                    if(rs.status == 200) {
                        console.log(rs)
                    } else {

                    }
                }).catch((err: any) => {
                    configuration = null;
                    openai = null;
                     this.$q.dialog({
                        title: 'Error',
                        message: err.message + ', Please check your ApiKey'
                    })
                    console.log(err)
                });
            }
        }
    },
    setup() {
    }
})
</script>

<style lang="less">
</style>