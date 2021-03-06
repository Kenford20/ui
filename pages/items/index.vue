<template>
    <div class="columns">
        <main class="column is-12">
            <section class="section-container">
                <LoadingMessage :is-loading="isLoading" />
                <div v-if="!isLoading && list && list.length" class="section-box">
                    Filters on next release; More items to be added soon
                    <hr />
                    <div class="columns is-mobile resource-item-list item-page">
                        <ItemPopover
                            v-for="item in itemList"
                            :id="item._id"
                            :key="item._id"
                            :resource="item"
                            :is-list="true"
                        />
                    </div>
                </div>
            </section>
        </main>
    </div>
</template>

<script>
import { mapGetters } from "vuex";
import ItemPopover from "~/components/items/ItemPopover";
import { mountedPageView } from "~/util/vueMixins";
import LoadingMessage from "~/components/general/LoadingMessage";
import { headMetaTags } from "~/util/Utils";

export default {
    components: {
        LoadingMessage,
        ItemPopover,
    },
    mixins: [mountedPageView],
    inject: ["assetsUrl"],
    data() {
        return {
            isLoading: false,
        };
    },
    // use this.list
    computed: {
        ...mapGetters("item", ["list"]),
        itemList() {
            const newList = [];
            this.list.forEach(item => {
                newList.push({
                    item: item._id,
                    qty: item.name,
                });
            });
            return newList;
        },
    },
    asyncData({ store }) {
        return store.dispatch("item/getList");
    },

    mounted() {
        if (!this.list || (this.list && !this.list.length)) {
            this.isLoading = true;
            this.$store.dispatch("item/getList").then(() => {
                this.isLoading = false;
            });
        }
    },

    head() {
        return headMetaTags(
            {
                title: "Items",
                description:
                    "See information about all Items, Catalysts, Enhancement Ingredients and Crafting Materials in EpicSeven game, including Farming Locations, AP Shops and more!",
                image: `${this.assetsUrl}/website/box_items.png`,
            },
            this
        );
    },
};
</script>
