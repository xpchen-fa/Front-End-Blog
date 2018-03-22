<template>
    <div class="edit-div needsclick"
          style="-webkit-user-select: text"
         v-text="innerText"
         placeholder="请输入..."
         :contenteditable="canEdit"
         ref="editdiv"
         @keyup="limit($event)"
         @focus="isLocked = true"
         @blur="isLocked = false"
         @input="changeText">
</div>
</template>
<script type="text/ecmascript-6">
    export default {
        name: "editDiv",
        props: {
            value: {
                type: String,
                default: ""
            },
            canEdit: {
                type: Boolean,
                default: true
            },
            limitnum: {
                type: Number,
                default: 999
            }
        },
        data() {
            return {
                innerText: this.value,
                isLocked: false
            };
        },
        watch: {
            "value" () {
                if (!this.isLocked && !this.innerText) {
                    this.innerText = this.value;
                }
            }
        },
        methods: {
            changeText() {
                this.$emit("input", this.$el.innerText);
            },
            limit(event) {
                if (this.$el.innerText.length >= this.limitnum) {
                    this.$el.innerText = this.$el.innerText.substring(0, this.limitnum);
                    event.preventDefault();
                }
            }
        },
        mounted() {
            this.$refs.editdiv.focus();
        }
    };
</script>
<style lang="less" rel="stylesheet/less">
    .edit-div {
        -webkit-user-select: auto;
        user-modify: read-write-plaintext-only;
        width: 100%;
        min-height: 0.6rem;
        max-height: 10rem;
        line-height: 0.6rem;
        font-size: 0.3rem;
        overflow: auto;
        word-break: break-all;
        outline: none;
        user-select: text;
        white-space: pre-wrap;
        color: #333333;
        &[contenteditable=true] {
            &:empty:before {
                font-size: 0.28rem;
                line-height: 0.6rem;
                content: attr(placeholder);
                display: block;
                color: #ccc;
            }
        }
    }
</style>
