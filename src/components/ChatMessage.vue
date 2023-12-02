<template>
    <div ref="msgComp" class="message">
        <div class="message__outer">
            <div v-if="isOptions" class="message__inner__options">
                <div 
                    v-for="(item, key) in options" :key="key" 
                    ref="messageContainer" 
                    class="message__options__bubble"
                    @click.stop="optionCb(item)"
                >{{ item.message }}</div>
            </div>
            <div v-else ref="messageBlock" class="message__inner">
                <div ref="messageContainer" class="message__bubble">. . .</div>
                <div class="message__spacer"></div>
            </div>
        </div>
    </div>
</template>

<script>
import { resolveComponent } from 'vue';


    export default {
        name: 'ChatMessage',
        props: {
            chatMsg: String,
            isBot: Boolean,
            isOptions: Boolean,
            options: Array,
            addMsgFn: Function,
            pauseFn: Function,
            scrollFn: Function
        },
        mounted () {
            this.scrollFn();
            if (this.isBot && !this.isOptions) {
                this.$refs.messageContainer.classList.add('message__bot');
            } else if (!this.isOptions) {
                this.$refs.messageContainer.classList.add('message__user');
                this.$refs.messageBlock.classList.add('message__inner__user');
            }
            this.$refs.msgComp.setAttribute("style",`display: block;`);
            this.pauseFn(2000)
            .then(e => {
                this.$refs.messageContainer.innerText = this.chatMsg;
            })
        },
        methods: {
            /**
             * when user has chosen one of the options 
             * the callback adds a new message to the chat
             * @param {Object} item 
             */
            optionCb (item) {
                const data = {
                    message: item.message,
                    nextStep: item.nextStep
                }
                this.addMsgFn(data, false);
            }
        }
    }

</script>

<style scoped>
    .message {
        width: 95%;
        margin: 0 auto;
        padding-top: 1vh;
        padding-bottom: 1vh;
        display: none;
    }
    .message__outer {
        display: flex;
    }

    .message__inner {
        flex: 1;
        display: flex;
        max-width: 100%;
    }

    .message__inner__user {
        flex-direction: row-reverse;
    }

    .message__spacer {
        flex: 1;
    }

    .message__bubble {
        min-width: 40px;
        max-width: 70%;
        overflow-wrap: break-word;
        background-color: rgb(98 52 173);
        padding: 10px;
        font-size: 16px;
        border-radius: 20px;
    }

    .message__bot {
        border-bottom-left-radius: 1px;
    }

    .message__user {
        background-color: rgb(51 115 229);
        border-bottom-right-radius: 1px;
        margin-top: 12px;
        margin-bottom: 12px;
    }

    .message__inner__options {
        display: grid;
        grid-gap: 10px;
        grid-template-columns: repeat(3, minmax(50px, 1fr));
        text-align: center;
    }

    .message__options__bubble {
        overflow-wrap: break-word;
        background-color: rgb(123 75 202);
        padding: 7px;
        font-size: 14px;
        border-radius: 20px;
        cursor: pointer;
    }

    .message__options__bubble:hover {
        background-color: rgb(137, 93, 208);
    }

</style>