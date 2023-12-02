<template>
    <div class="container">
        <header>
            <div class="title">Chat window</div>
        </header>
        <div ref="contentBox" class="content">
            <div class="content__inner">
                <ChatMessage 
                    v-for="(item, key) in allMessages" :key="key"
                    :isBot="item.isBot" 
                    :chatMsg="item.message"
                    :isOptions = "item.isOptions"
                    :options = "item.options"
                    :addMsgFn = "addNewMessage"
                    :pauseFn = "pause"
                    :scrollFn = 'scrollToBottom'
                />
            </div>
        </div>
        <div class="panel__input">
            <input ref="userInput" class="panel__input__box" @keyup.enter="sendMessage()" />
            <div class="panel__input__btn__cont">
                <div class="panel__input__btn" @click.stop="sendMessage()">Send</div>
            </div>
        </div>
    </div>
</template>

<script>

    import ChatMessage from './ChatMessage.vue';

    export default {
        name: 'ChatWindow',
        props: {
            botMessages: Array
        },
        components: {
            ChatMessage
        },
        data() {
            return {
                allMessages: [],
            }
        },
        mounted () {
            this.init();
        },
        methods: {
            /**
             * add the first message of the bot to the chat
             */
            init() {
                if (!this.botMessages) return
                this.addNewMessage(this.botMessages[0], true);
            },
            /**
             * create new message from given parameters
             * and add it to the chat
             * @param {Object} msgData 
             * @param {Boolean} isBot 
             */
            async addNewMessage(msgData, isBot) {
                let msg;
                // add message to the message list
                if (msgData.message) {
                    msg = msgData.message;
                    const item = {
                        message: msgData.message,
                        isBot
                    }
                    this.allMessages.push(item);
                }
                if (isBot) await this.pause(2000);
                // process message with options
                if (msgData.options) {
                    const item = {
                        options: msgData.options,
                        isOptions: true,
                    }
                    this.allMessages.push(item);
                }
                // process next message if there are options
                let nextStep;
                if (msgData.nextStep)
                    nextStep = this.botMessages.find(e => e.id === msgData.nextStep);

                if (nextStep && nextStep.options) {
                    await this.addNewMessage(nextStep, isBot);
                }
                // process the next bot message after user message
                // chosen from given options
                if (nextStep && !isBot) {
                    await this.pause(3000);
                    await this.addNewMessage(nextStep, true);
                }
                // process bot message after user message entered by input
                if (!nextStep && !isBot) {
                    const item = this.findAnswer(msgData.message);
                    await this.pause(3000);
                    this.addNewMessage(item, true);
                }
            },
            /**
             * pause program for given time
             * @param {Number} time ms
             */
            pause (time) {
                return new Promise(resolve => setTimeout(resolve, time));
            },
            /**
             * send message inetered by user in input field
             */
            sendMessage () {
                const message = this.$refs.userInput.value;
                if (!message) return
                const item = {
                    message,
                    freeMsg: true
                }
                this.addNewMessage(item, false);
                this.$refs.userInput.value = "";
            },
            /**
             * get bot answer for user message entered in input field
             * @param {String} msg 
             */
            findAnswer (msg) {
                let item;
                const readyAnswer = this.botMessages.find(e => e.options && e.options.findIndex(opt => opt.message.toLowerCase() === msg.toLowerCase()) !== -1);
                if (readyAnswer) {
                    const option = readyAnswer.options.find(e => e.message.toLowerCase() === msg.toLowerCase());
                    item = this.botMessages.find(e => e.id === option.nextStep);
                }

                if (!item) {
                    item = this.botMessages.find(e => e.defOpt);
                }
                return item;
            },
            /**
             * scroll element to the bottom
             */
            scrollToBottom() {
                this.$refs.contentBox.scrollTop = this.$refs.contentBox.scrollHeight - this.$refs.contentBox.clientHeight;
            }
        }
    }

</script>

<style scoped>
    .container {
        width: 90vw;
        max-width: 500px;
        height: 95vh;
        max-height: 900px;
        border: 1px solid #000;
        border-radius: 20px;
        display: flex;
        flex-direction: column;
    }

    header {
        width: 100%;
        height: 50px;
        background-color: rgb(76, 18, 168);
        border-top-right-radius: 20px;
        border-top-left-radius: 20px;
    }

    .title {
        font-size: 30px;
        padding-top: 10px;
        padding-left: 10px;
    }

    .content {
        position: relative;
        width: 100%;
        height: calc(100% - 100px);
        background-color: rgb(198, 176, 235);
        overflow-y: scroll;
        scrollbar-width: 5px;
        scrollbar-color: rgb(140, 89, 222) rgba(198, 176, 235, 0);
    }

    .content::-webkit-scrollbar {
        width: 5px;
    }
    .content::-webkit-scrollbar-track {
        background: rgba(198, 176, 235, 0);
        border-radius: 0;
    }
    .content::-webkit-scrollbar-thumb {
        background-color: rgb(140, 89, 222);
        border-radius: 0;
        border: none;
    }

    .content__inner {
        /* width: calc(100% - 10px); */
    }

    .panel__input {
        display: flex;
        flex-direction: row;
        flex: 1;
        position: relative;
        width: 100%;
        height: 50px;
        border-bottom-left-radius: 20px;
        border-bottom-right-radius: 20px;
        background-color: rgb(223, 213, 241);
    }

    .panel__input__box {
        flex: 1;
        height: 50px;
        border-bottom-left-radius: 20px;
        background-color: rgb(223, 213, 241);
        color: #141313;
        font-size: 16px;
        border: none;
        outline: none;
        padding-left: 20px;
        overflow-wrap: break-word;
    }

    .panel__input__btn__cont {
        height: 50px;
        width: 65px;
        border-bottom-right-radius: 20px;
        display: flex;
        justify-content: center;
    }

    .panel__input__btn {
        height: 40px;
        width: 40px;
        border-radius: 20px;
        background-color: rgb(143, 87, 239);
        color: #cdcdcd;
        font-size: 14px;
        margin: auto 0;
        text-align: center;
        padding-top: 12px;
        cursor: pointer;
    }

    .panel__input__btn:hover {
        background-color: rgb(156, 110, 235);
    }

</style>