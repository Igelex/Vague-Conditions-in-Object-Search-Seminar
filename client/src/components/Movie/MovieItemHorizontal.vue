<template>
    <div class="movie">
        <div class="movie-h-container elevation-5" :class="{expand: expanded}">
            <router-link class="to-details"
                         :recomm_id="recomm_id"
                         :to="{ name: 'movie', params: { id: movie.tmdb_id}, query: {recomm_id}}">

            </router-link>
            <v-img
                    class="movie-h-poster"
                    height="278"
                    :src="'https://image.tmdb.org/t/p/w185' + movie.poster_path"
            >
            </v-img>
            <span class="prise">
                {{price}}$
            </span>
            <div class="popularity">
                <vue-ellipse-progress :progress="popularityProgress()" :size="35"
                                      color="white"
                                      :thickeness="2"
                                      empty-color="black"
                                      empty-color-fill="rgba(0, 0, 0, 0.38)"
                                      :animation="{
                                        type: 'rs',
                                        duration: 500,
                                        delay: 500
                                      }"
                                      font-color="white"
                                      :legend-value="parseFloat(movie.vote_average.toFixed(1))"/>
            </div>
            <div class="bookmark">
                <v-btn icon flat color="error" @click="bookmark()">
                    <v-icon color="error">
                        {{favorite ? 'favorite' : 'favorite_border'}}
                    </v-icon>
                </v-btn>
            </div>
            <div class="details">
                <v-btn icon small color="white" @click="expandCard">
                    <v-icon color="blue">
                        {{expanded ? 'expand_less' : 'expand_more'}}
                    </v-icon>
                </v-btn>
            </div>
        </div>
        <div class="movie-info" :class="{'info-expanded': expanded}">
            <movie-info :expanded="expanded" :movie="movie" :price="price"/>
        </div>
    </div>
</template>

<script>
    import PopularityCircle from "../Base/PopularityCircle";
    import MovieInfo from "./MovieInfo";
    import {callInteraction} from "../../api/recommender";
    import logger from '@/interactions_logger'

    export default {
        name: "MovieItemHorizontal",
        components: {MovieInfo, PopularityCircle},
        props: ['movie', 'recomm_id'],
        data: () => ({
            expand: false,
            favorite: false,
            price: 0,
        }),
        methods: {
            expandCard() {
                if (this.$store.getters.expanded_card !== this.movie.tmdb_id) {
                    this.expand = true;
                } else {
                    this.expand = !this.expand;
                }
                if (this.expand) {
                    this.portionView();
                    this.$store.dispatch('setExpandedCard', this.movie.tmdb_id);
                }
            },
            popularityProgress() {
                return parseFloat((this.movie.vote_average * 100 / 10).toFixed(1));
            },
            generatePrise() {
                this.price = (Math.random() * (20 - 1) + 1).toFixed(2);
            },

            goToDetails() {
                this.$router.push({name: 'movie', params: {id: this.movie.tmdb_id}})
            },

            bookmark() {
                if (this.favorite) {
                    this.removeBookmark();
                } else {
                    callInteraction('bookmark', {
                        user_id: this.$store.getters.user.id,
                        item_id: this.movie.tmdb_id,
                        recomm_id: this.recomm_id
                    })
                        .then(res => logger('bookmark',res.data))
                        .catch(e => console.error(e));
                }
                this.favorite = !this.favorite;
            },
            removeBookmark() {
                callInteraction('removebookmark', {user_id: this.$store.getters.user.id, item_id: this.movie.tmdb_id})
                    .then(res => logger('removed bookmark',res.data))
                    .catch(e => console.log(e));
            },
            portionView() {
                callInteraction('portionview', {
                    user_id: this.$store.getters.user.id,
                    item_id: this.movie.tmdb_id,
                    recomm_id : this.recomm_id,
                })
                    .then(res => logger('portion view',res.data))
                    .catch(e => console.log(e));
            },
        },
        mounted() {
            this.generatePrise();
        },
        computed: {
            expanded() {
                return this.expand && this.$store.getters.expanded_card === this.movie.tmdb_id;
            }
        }
    }
</script>

<style scoped lang="scss">
    .movie {
        transition: 0.3s;
        display: inline-block;
        margin: 8px 12px;
        width: 185px;
        position: relative;
        perspective: 1000px;

        &:hover {
            z-index: 1;
        }
    }

    .movie-h-container {
        transition: 0.3s;
        display: inline-block;
        width: 185px;
        border-radius: 5px;
        overflow: hidden;
        animation: movie 1s ease-in-out;
        cursor: pointer;
        position: relative;

        &:hover {
            .movie-h-poster {
                transform: scale(1.1);
            }
        }

        &.expand {
            z-index: 5;
            transform: scale(1.1) rotateX(-30deg) translateY(-10px);
            perspective: 1000px;

            .movie-h-poster {
                filter: sepia(0.3) grayscale(0.2) brightness(0.9);
            }
        }

        .prise {
            display: inline-block;
            padding: 0 4px 0 15px;
            background: rgba(188, 121, 45, 0.71);
            color: white;
            font-weight: bold;
            position: absolute;
            top: 0;
            right: 0;
            clip-path: polygon(19% 0, 100% 0%, 100% 100%, 0% 100%);
        }

        .popularity {
            position: absolute;
            top: 10px;
            left: 10px;
            z-index: 2;
        }

        .bookmark {
            position: absolute;
            bottom: 0;
            left: 0;
            z-index: 2
        }

        .details {
            opacity: 0.5;
            position: absolute;
            bottom: 0;
            right: 0;
            z-index: 2
        }
    }

    .to-details {
        position: absolute;
        top: 0;
        left: 0;
        height: 278px;
        width: 185px;
        z-index: 1;
    }

    .movie-h-poster {
        transition: 0.5s;
        filter: sepia(0.5) grayscale(0.3) brightness(0.7);
    }

    .movie-info {
        transition: 0.5s;
        position: static;
        height: 0;
        width: 420px;

        &.info-expanded {
            margin: 16px 0 16px 0;
            z-index: 5;
            height: 278px;
            top: 0;
            left: 165px;
        }
    }

    @keyframes movie {
        0% {
            height: 0;
            opacity: 0;
            transform: scale(0);
        }
        0% {
            transform: scale(1);
            opacity: 1;
            height: 278px;
        }
    }

</style>