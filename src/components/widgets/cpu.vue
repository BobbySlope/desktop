<template>
    <div id="widget">
        <div class="title">{{ $t("cpu") }}</div>
        <div class="value">{{ (cpu || {}).used || 0 }}%</div>
    </div>
</template>

<script>
    export default {
        name: "cpu-widget",

        computed: {
            cpu() {
                return this.$store.state.cpu;
            },
        },
    };
</script>

<style lang="scss" scoped>
    #widget {
        flex: 1;
        height: 100%;
        box-sizing: border-box;
        display: flex;
        flex-direction: column;
        align-items: center;
        align-content: center;
        padding: 14px;
        position: relative;
        cursor: default;
        user-select: none;

        .title {
            font-size: 15px;
        }

        .value {
            flex: 1;
            display: flex;
            align-items: center;
            align-content: center;
            color: var(--widget-highlight);
            text-align: center;
            font-size: 22px;
        }
    }
</style>
