<template>
    <div style="width: 95%;">
        <div v-if="key == ''">
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
        <div style="background-color: #f6f6f6;padding: 5px;border-top-left-radius: 10px;border-top-right-radius: 10px;" class="shadow-up-7 fixed-bottom">
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
        }
    },
    setup() {
    }
})
</script>

<style lang="less">
</style>