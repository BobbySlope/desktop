<template>
    <div id="field" class="field">
        <span v-if="title" class="title" v-html="title"></span>
        <span v-else class="title" v-html="description || format(field)"></span>
        <span v-if="title && description && description !== ''" class="description" v-html="description"></span>
        <select :id="id || uuid" :ref="uuid" :name="name" :value="value" v-on:input="update" v-on:change="change" v-bind:required="required">
            <option v-for="option in options" v-bind:value="option.value" :key="option.value">{{ option.text }}</option>
        </select>
    </div>
</template>

<script>
    import { decamel } from "../../services/schema";

    const INPUT_FOCUS_DELAY = 10;

    export default {
        name: "select-field",

        props: {
            id: {
                type: String,
                default: undefined,
            },
            name: String,
            field: [String, Number],
            title: String,
            description: String,
            value: [String, Number, Boolean, Object, Date],
            default: [String, Number, Boolean, Object, Date],
            type: String,
            options: Array,
            required: {
                type: Boolean,
                default: false,
            },
        },

        data() {
            return {
                uuid: "",
            };
        },

        mounted() {
            if (this.id === undefined || typeof String) {
                this.uuid = `text_field_${Math.random().toString(36).substring(2, 10)}`;
            } else {
                this.uuid = this.id;
            }

            if (this.value === undefined && this.default !== undefined) {
                this.$emit("change", this.default, this.value);

                setTimeout(() => {
                    if (this.$refs[this.uuid]) this.$refs[this.uuid].value = this.default;
                }, INPUT_FOCUS_DELAY);
            }

            if (this.autofocus) {
                setTimeout(() => {
                    if (this.$refs[this.uuid]) this.$refs[this.uuid].focus();
                }, INPUT_FOCUS_DELAY);
            }
        },

        methods: {
            format(value) {
                if (!Number.isNaN(parseInt(value, 10))) return "";
                if (!value || value === "") return value;

                return decamel(value);
            },

            update() {
                let value = null;

                switch ((this.type || "string").toLowerCase()) {
                    case "bool":
                    case "boolean":
                        this.$emit("input", (this.$refs[this.uuid].value || "").toLowerCase() === "true");
                        break;

                    case "float":
                    case "double":
                    case "decimal":
                    case "number":
                        value = parseFloat(this.$refs[this.uuid].value);

                        if (Number.isNaN(value)) {
                            value = null;
                        }

                        this.$emit("input", value);
                        break;

                    case "int":
                    case "integer":
                        value = parseInt(this.$refs[this.uuid].value, 10);

                        if (Number.isNaN(value)) {
                            value = null;
                        }

                        this.$emit("input", value);
                        break;

                    default:
                        this.$emit("input", this.$refs[this.uuid].value);
                        break;
                }
            },

            change() {
                let value = null;

                switch ((this.type || "string").toLowerCase()) {
                    case "bool":
                    case "boolean":
                        this.$emit("change", (this.$refs[this.uuid].value || "").toLowerCase() === "true");
                        break;

                    case "float":
                    case "double":
                    case "decimal":
                    case "number":
                        value = parseFloat(this.$refs[this.uuid].value);

                        if (Number.isNaN(value)) {
                            value = null;
                        }

                        this.$emit("change", value);
                        break;

                    case "int":
                    case "integer":
                        value = parseInt(this.$refs[this.uuid].value, 10);

                        if (Number.isNaN(value)) {
                            value = null;
                        }

                        this.$emit("change", value);
                        break;

                    default:
                        this.$emit("change", this.$refs[this.uuid].value);
                        break;
                }
            },
        },
    };
</script>

<style lang="scss" scoped>
    #field {
        display: flex;
        flex-direction: column;
        padding: 0 10px 20px 0;

        .title {
            font-size: 14px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            margin: 0 0 7px 0;
            user-select: none;

            &:empty {
                display: none;
            }
        }

        .description {
            font-size: 12px;
            margin: -7px 0 7px 0;
            user-select: none;

            &:empty {
                display: none;
            }
        }

        select {
            flex: 1;
            padding: 7px;
            font-size: 14px;

            &:focus {
                outline: 0 none;
            }
        }
    }
</style>
