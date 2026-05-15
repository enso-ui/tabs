<template>
    <div class="wrapper"
        :class="wrapperClass">
        <div :class="tabsClass">
            <ul class="tab-list">
                <core-tabs
                    v-bind="attrs">
                    <template #default="{ key, tabs, tabEvents }">
                        <li :class="{ 'is-active': tab.active }"
                            v-for="tab in tabs"
                            :key="key(tab.id)">
                            <a :disabled="tab.disabled || null"
                                v-on="tabEvents(tab)">
                                <slot name="label"
                                    :tab="tab.id">
                                    {{ tab.id }}
                                </slot>
                            </a>
                        </li>
                    </template>
                </core-tabs>
            </ul>
        </div>
        <slot/>
    </div>
</template>

<script>
import CoreTabs from '../renderless/CoreTabs.vue';

const tabModifiers = new Set([
    'is-left', 'is-centered', 'is-right',
    'is-small', 'is-normal', 'is-medium', 'is-large',
    'is-boxed', 'is-toggle', 'is-toggle-rounded', 'is-fullwidth',
]);

export default {
    name: 'Tabs',

    components: { CoreTabs },

    inheritAttrs: false,

    computed: {
        attrs() {
            const { class: _class, ...attrs } = this.$attrs;

            return attrs;
        },
        classGroups() {
            const groups = { tabs: [], wrapper: [] };

            this.classNames(this.$attrs.class)
                .forEach((className) => {
                    const group = tabModifiers.has(className)
                        ? 'tabs'
                        : 'wrapper';

                    groups[group].push(className);
                });

            return groups;
        },
        tabsClass() {
            return ['tabs', ...this.classGroups.tabs];
        },
        wrapperClass() {
            return this.classGroups.wrapper;
        },
    },

    methods: {
        classNames(value) {
            if (!value) {
                return [];
            }

            if (typeof value === 'string') {
                return value.split(/\s+/).filter(Boolean);
            }

            if (Array.isArray(value)) {
                return value.flatMap(classValue => this.classNames(classValue));
            }

            return Object.entries(value)
                .filter(([, enabled]) => enabled)
                .flatMap(([className]) => this.classNames(className));
        },
    },
};
</script>

<style lang="scss">
    a[disabled] {
        opacity: .5;
        cursor: not-allowed;
    }
</style>
