<script>
import axios from 'axios';

export default {
    data() {
        return {
            movies: [],
            currentPage: 1,
            totalPages: 1,
            loading: false,
            searchQuery: '',
        };
    },
    mounted() {
        this.fetchMovies();
    },
    methods: {
        fetchMovies(page = 1) {
            if (this.loading || page > this.totalPages) return;

            this.loading = true;
            const options = {
                method: 'GET',
                url: 'https://api.themoviedb.org/3/search/movie',
                params: { query: this.searchQuery, include_adult: 'false', language: 'it-IT', page: page, api_key: '57880bab5f08b4351e407906454d0666' },
                headers: {
                    accept: 'application/json',
                }
            };

            axios.request(options)
                .then((response) => {
                    if (page === 1) {
                        this.movies = response.data.results;
                    } else {
                        this.movies = [...this.movies, ...response.data.results];
                    }
                    this.totalPages = response.data.total_pages;
                    this.currentPage = page;
                    this.loading = false;
                })
                .catch((error) => {
                    console.error(error);
                    this.loading = false;
                });
        },
        searchMovies() {
            this.currentPage = 1;
            this.totalPages = 1;
            this.movies = [];
            this.fetchMovies();
        },
        loadMore() {
            if (this.currentPage < this.totalPages) {
                this.fetchMovies(this.currentPage + 1);
            }
        },
        getImageUrl(path) {
            return path ? `https://image.tmdb.org/t/p/w342${path}` : 'path/to/default/image.jpg';
        }
    }
};

</script>

<template>
    <div>
        <div>
            <h1>Film</h1>
            <div class="movie-sc"><input v-model="searchQuery" placeholder="Cerca un film..." />
                <button @click="searchMovies">Cerca</button>
            </div>
            <ul>
                <li v-for="movie in movies" :key="movie.id">
                    <div class="card">
                        <img :src="getImageUrl(movie.poster_path)" />
                        <h2>{{ movie.title }}</h2>
                        <p><strong>Nome Originale:</strong> {{ movie.original_name }}</p>
                        <p><strong>Voto Medio:</strong> {{ movie.vote_average }}</p>
                        <p><strong>Descrizione:</strong> {{ movie.overview }}</p>
                    </div>
                </li>
            </ul>
            <div class="up-more">
                <button v-if="currentPage < totalPages" @click="loadMore">Carica più</button>
            </div>

        </div>
    </div>





</template>

<style scoped>
.card {
    justify-content: center;
    align-items: center;
    text-align: center;
    width: 20rem;
    border-radius: 0;

}

ul {
    justify-content: center;
    gap: 2rem;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    list-style-type: none;
    padding: 0;
}

li {
    margin: 20px 0;
}

img {

    width: 20rem;
    display: block;

}

.movie-sc {
    padding: 2rem;
    justify-content: space-between;
}

.up-more {
    padding: 2rem;
}

.card p {
    color: black;
}
</style>
