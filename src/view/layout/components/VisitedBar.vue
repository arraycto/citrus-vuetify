<template>
    <v-row justify="space-around">
        <v-col>
            <v-sheet tile elevation="1">
                <v-chip-group mandatory active-class="chip-active" v-model="activeIndex">
                    <v-chip v-for="(item,index) in visitedItems"
                            :key="index"
                            :close="item.deletable"
                            class="ml-1 bar-chip"
                            label
                            small
                            outlined
                            close-icon="mdi-close"
                            @click="active(item,index)"
                            @click:close="close(item)"
                    >
                        {{item.name}}
                    </v-chip>
                </v-chip-group>
            </v-sheet>
        </v-col>
    </v-row>
</template>

<script>
    export default {
        name: "VisitedBar",
        data: () => ({
            //当前在用的ITEM下标
            activeIndex: 0,
            defaultItems: []
        }),
        computed: {
            visitedItems() {
                return this.$store.state.visited.visitedItems
            }
        },
        watch: {
            $route() {
                this.addItem();
            }
        },
        methods: {
            active(item, index) {
                this.activeIndex = index;
                if (item.path === this.$route.path) {
                    return;
                }
                this.$router.push({path: item.path})
            },
            addItem() {
                const route = this.$route;
                const currentRouteIndex = this.visitedItems.findIndex(i => i.path === route.path);
                if (currentRouteIndex !== -1) {
                    this.refresh(this.visitedItems[currentRouteIndex], currentRouteIndex);
                    return;
                }

                const originalLength = this.visitedItems.length;
                const visitedItem = {
                    name: route.meta.text || route.name,
                    path: route.path,
                    deletable: route.path !== '/'
                };
                this.$store.dispatch('visited/addItem', visitedItem);
                this.refresh(visitedItem, originalLength);

            },
            close(item) {
                const preIndex = this.visitedItems.indexOf(item) - 1;
                this.$store.dispatch('visited/removeItem', item);
                this.active(this.visitedItems[preIndex], preIndex)
            },
            refresh(item, index) {
                this.$nextTick(() => {
                    this.active(item, index)
                })
            }
        },
        mounted() {
            this.addItem();
        }
    }
</script>

<style scoped>
    .chip-active {
        /*border-bottom: #85b71b solid 3px !important;*/
        border-bottom: #80abfa solid 2px !important;
    }

    .bar-chip {
        border-radius: 0 !important;
        height: 28px !important;
        margin-top: 2px !important;
        margin-bottom: 2px !important;
    }

    .bar-chip:hover {
        border-bottom: #80abfa solid 2px !important;
    }
</style>
