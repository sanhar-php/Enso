<template>

    <div class="navbar-item search">
        <typeahead is-rounded
            source="core.search.index"
            :placeholder="__('Search')"
            :searching="__('Searching') + '..'"
            :no-results="__('Nothing found')"
            v-model="query"
            @update="redirect">
            <template slot="option"
                slot-scope="{ highlight, item }">
                <span>
                    <span class="tag is-bold is-info is-uppercase">
                        {{ __(item['group']) }}
                    </span>
                    <span v-html="highlight(item['label'])"/>
                    <span class="route-controls"
                        v-if="item.routes.length">
                        <span class="icon is-small route-control"
                            @mousedown.stop="redirect(item, route.name)"
                            v-for="(route, index) in item.routes"
                            :key="index">
                            <fa :icon="route.icon" size="sm"/>
                        </span>
                    </span>
                </span>
            </template>
        </typeahead>
    </div>

</template>

<script>

import { library } from '@fortawesome/fontawesome-svg-core';
import { faEye, faPencilAlt, faListUl } from '@fortawesome/free-solid-svg-icons';
import Typeahead from '../../../components/enso/bulma/Typeahead.vue';

library.add(faEye, faPencilAlt, faListUl);

export default {
    name: 'Search',

    components: { Typeahead },

    data: () => ({
        query: '',
    }),

    methods: {
        route(search, { routes }) {
            return routes.find(route =>
                route.indexOf(search) >= 0);
        },
        redirect(item, to = null) {
            if (!to && !item.routes.length) {
                return;
            }

            this.$router.push({
                name: to || item.routes[0].name,
                params: item.param,
            });

            this.$nextTick(() => (this.query = ''));
        },
    },
};
</script>

<style lang="scss" scoped>
    .navbar-item.search {
        .tag {
            padding: 0.5em;
            height: 1.6em;
            opacity: .7;
            -webkit-box-shadow: 0 1px 1px rgba(10, 10, 10, 0.2);
            box-shadow: 0 1px 1px rgba(10, 10, 10, 0.2);
        }
        .route-controls {
            position: absolute;
            right: 1em;
            margin-top: .15em;

            .route-control {
                z-index: 4;
                opacity: 0.8;
                cursor: pointer;
                margin-right: 0.2em;

                &:hover {
                    opacity: 1;
                }
            }
        }

        position: absolute;

        @media screen and (min-width: 1024px) {
            width: 34em;
            left: calc(50% - 17em);
        }

        @media screen and (min-width: 768px) and (max-width: 1023px) {
            width: 26em;
            left: calc(50% - 13em);
        }

        @media screen and (max-width: 767px) {
            width: 22em;
            left: calc(50% - 11em);
        }
    }
</style>
