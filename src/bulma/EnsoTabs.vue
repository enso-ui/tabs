<template>
    <div class="enso-tabs"
        :class="$attrs.class">
        <core-tabs ref="tabs"
            v-bind="$attrs">
            <template #default="{ key, tabs, tabEvents }">
                <div class="tabs is-toggle is-fullwidth no-scrollbars"
                    :class="`is-${size}`">
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

export default {
    name: 'EnsoTabs',

    components: { CoreTabs },

    inheritAttrs: false,

    props: {
        size: {
            type: String,
            default: 'normal',
            validator: (value) => ['normal', 'small', 'medium', 'large']
                .includes(value),
        },
    },

    computed: {
        tabs() {
            return this.$refs.tabs.tabs;
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
                transition: background-color 0.2s ease, border-color 0.2s ease, box-shadow 0.2s ease, color 0.2s ease;
                border: 1px solid transparent;
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
                border-color: var(--enso-surface-border);
                box-shadow: 0 1px 2px color-mix(in srgb, var(--bulma-text) 8%, transparent);
                color: var(--bulma-text-strong);
            }
        }
    }
</style>
