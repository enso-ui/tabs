<script>
export default {
    name: 'CoreTabs',

    inheritAttrs: false,

    emits: ['activated', 'registered', 'removed', 'selected', 'update:modelValue'],

    props: {
        modelValue: {
            type: [String, Object, Number, null],
            default: null,
        },
    },

    data: () => ({
        tabs: [],
    }),

    computed: {
        controlled() {
            return this.modelValue !== null && this.modelValue !== undefined;
        },
    },

    provide() {
        return {
            tabState: {
                tabs: this.tabs,
                register: this.register,
                activate: this.activate,
                remove: this.remove,
            },
        };
    },

    methods: {
        activate(activeTab) {
            if (this.controlled && !this.matches(activeTab.id, this.modelValue)) {
                return;
            }

            this.setActive(activeTab);
        },
        activateById(id) {
            const tab = this.tabs.find(({ id: tabId }) => this.matches(tabId, id));

            if (tab) {
                this.setActive(tab);
            }
        },
        matches(first, second) {
            return this.key(first) === this.key(second);
        },
        setActive(activeTab) {
            this.tabs
                .forEach(tab => (tab.active = activeTab._.uid === tab._.uid));

            this.$nextTick(() => this.$emit('activated', activeTab.id));
        },
        key(id) {
            return typeof id === 'object'
                ? JSON.stringify(id)
                : id;
        },
        register(tab) {
            this.tabs.push(tab);
            this.$emit('registered', tab.id);

            if (this.controlled && this.matches(tab.id, this.modelValue)) {
                this.setActive(tab);
            } else if (!this.controlled && this.tabs.length === 1) {
                this.activate(tab);
            }
        },
        remove(tab) {
            const index = this.tabIndex(tab);
            this.tabs.splice(index, 1);
            this.$emit('removed', tab.id);
        },
        select(tab) {
            if (!tab.disabled) {
                this.$emit('selected', tab.id);
                this.$emit('update:modelValue', tab.id);
                this.setActive(tab);
            }
        },
        tabIndex(tab) {
            return this.tabs.findIndex(({ _ }) => _.uid === tab._.uid);
        },
    },

    watch: {
        modelValue(value) {
            this.activateById(value);
        },
    },

    render() {
        return this.$slots.default({
            key: this.key,
            tabs: this.tabs,
            tabEvents: tab => ({
                click: () => this.select(tab),
            }),
        });
    },
};
</script>
