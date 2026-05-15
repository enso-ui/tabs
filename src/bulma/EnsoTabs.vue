<template>
    <div class="enso-tabs"
        :class="wrapperClass">
        <core-tabs ref="tabs"
            v-bind="attrs">
            <template #default="{ key, tabs, tabEvents }">
                <div :class="tabsClass">
                    <ul class="tab-list enso-tabs-surface">
                        <li :class="{ 'is-active': tab.active }"
                            v-for="tab in tabs"
                            :key="key(tab.id)">
                            <a :class="{ 'enso-tab-active': tab.active }"
                                :disabled="tab.disabled || null"
                                v-on="tabEvents(tab)">
                                <slot name="label"
                                    :tab="tab.id">
                                    {{ tab.id }}
                                </slot>
                            </a>
                        </li>
                    </ul>
                </div>
                <slot/>
            </template>
        </core-tabs>
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
    name: 'EnsoTabs',

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
            return [
                'tabs', 'is-toggle', 'is-fullwidth', 'no-scrollbars',
                ...this.classGroups.tabs,
            ];
        },
        tabs() {
            return this.$refs.tabs.tabs;
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
    .enso-tabs  {
        position: relative;

        .tabs.is-fullwidth.is-toggle > .tab-list {
            background: var(--bulma-card-header-background-color);
            border: 1px solid var(--enso-surface-border);
            border-radius: 6px;

            li {
                padding: 0.3em;
            }

            .is-active > a {
                opacity: 1;
                font-weight: 600;
            }

            a {
                color: var(--bulma-text-light);
                transition: background-color 0.2s ease, box-shadow 0.2s ease, color 0.2s ease;
                border: none;
                border-radius: 6px;
                background: transparent;
                height: 1.75rem;

                &[disabled] {
                    color: light-dark(
                        color-mix(in srgb, var(--bulma-text) 68%, var(--bulma-text-light)),
                        color-mix(in srgb, var(--bulma-text-light) 84%, var(--bulma-text-strong))
                    );
                    opacity: .8;
                    cursor: not-allowed;
                }
            }

            .is-active > a,
            a.enso-tab-active {
                background: var(--enso-surface);
                border: none;
                box-shadow:
                    0 1px 2px color-mix(in srgb, var(--bulma-text) 6%, transparent),
                    0 8px 20px color-mix(in srgb, var(--bulma-text) 10%, transparent);
                color: var(--bulma-text-strong);
            }
        }
    }
</style>
