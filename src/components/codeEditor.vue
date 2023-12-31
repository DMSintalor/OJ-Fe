<script>
import {defineComponent} from 'vue'
import {cpp} from "@codemirror/lang-cpp";
import {python} from "@codemirror/lang-python";
import {javascript} from "@codemirror/lang-javascript";
import {php} from "@codemirror/lang-php";
import {oneDark} from "@codemirror/theme-one-dark";
import bus from "@/event";
import {basicLight} from "@/themes/basic-light";
import {basicDark} from "@/themes/basic-dark";

export default defineComponent({
    name: "codeEditor",
    expose: ['updateExtension', 'code'],
    props: {
        modelValue: {
            type: String,
            default: '',
        },
        placeholder: {
            type: String,
            default: '',
        },
        height: {
            type: String,
            default: '200px',
        },
        init_language: {
            type: String,
            default: ''
        },
        is_disabled: {
            type: Boolean,
            default: false,
        },
    },
    computed: {
        value_: {
            get() {
                return this.value
            },
            set(val) {
                this.$emit('update:modelValue', val)
                return true
            },
        },
    },
    data() {
        return {
            l2e: {
                cxx: cpp(),
                c: cpp(),
                py3: python(),
                py2: python(),
                js: javascript(),
                php: php()
            },
            extensions: [basicLight],
            language: '',
            code: '',
        }
    },
    created() {
        if (localStorage.getItem('is_dark') === 'true') this.extensions = [basicDark]
        this.code = this.modelValue
        if (this.init_language) this.updateExtension(this.init_language)
    },
    mounted() {
        bus.on('changeDark', (is_dark) => {
            if (this.language) this.extensions = [this.l2e[this.language]]
            else this.extensions = []
            if (is_dark) {
                this.extensions.push(basicDark)
            } else {
                this.extensions.push(basicLight)
            }
            this.$forceUpdate()
        })
    },
    methods: {
        updateExtension(language) {
            this.language = language
            this.extensions = [this.l2e[language]]
            if (localStorage.getItem('is_dark') === 'true') this.extensions.push(basicDark)
            else this.extensions.push(basicLight)
        }
    }
})
</script>
<script setup>
import {cpp} from "@codemirror/lang-cpp";
import {python} from "@codemirror/lang-python";
import {javascript} from "@codemirror/lang-javascript";
import {php} from "@codemirror/lang-php";
import {Codemirror} from "vue-codemirror";
</script>

<template>
    <codemirror
            v-model="code"
            :disabled="is_disabled"
            placeholder="Code goes here..."
            :style="{ height: '400px' }"
            :autofocus="true"
            :indent-with-tab="true"
            :tab-size="2"
            :extensions="extensions"
            @input="$emit('update:modelValue', code)"
    />
</template>

<style scoped lang="scss">

</style>
