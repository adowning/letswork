<template>

    <div class="editComic">

        <template v-if="loadingStatus">
            <loading></loading>
        </template>

        <template v-else>
            <h1>Edit Comic:</h1>
            <form>
                <input v-model="title" placeholder="Title" required>
                <br>
                <input v-model="series" placeholder="Series" required>
                <br>
                <input v-model="issueNumber" placeholder="Issue Number" required>
                <br>
                <input v-model="artUrl" placeholder="Cover Art Url" required>
                <br>
                <textarea v-model="description" placeholder="Description" required></textarea>
                <br>
                <input v-model="pubYear" placeholder="Year Published" required>
                <br>
                <input v-model="publisher" placeholder="Publisher" required>
                <br>
                <input v-model="writer" placeholder="Written By" required>
                <br>
                <input v-model="artist" placeholder="Art By" required>
                <br>
                <span type="submit" class="button is-outlined is-danger" @click="updateComic()">Submit</span>
            </form>

        </template>

    </div>

</template>

<script>
import gql from 'graphql-tag'
import Loading from '../../components/Loading.vue'
    
const comicQuery = gql`
  query ($id: ID!) {
    Comic(id: $id) {
        title,
        series,
        issueNumber,
        artUrl,
        description,
        pubYear,
        publisher,
        writer,
        artist,

      collections(orderBy: name_ASC ) {
            name
            id
        }
    }
  }
`

const updateComic = gql`
  mutation ($id: ID!, $title: String!, $series: String!, $issueNumber: String!, $artUrl: String!, $description: String!, $pubYear: String!, $publisher: String!, $writer: String!, $artist: String!){
    updateComic(id: $id, title: $title, series: $series, issueNumber: $issueNumber, artUrl: $artUrl, description: $description, pubYear: $pubYear, publisher: $publisher, writer: $writer, artist: $artist) {
     id
    }
  }
`
export default {

    name: 'EditComic',

    data: () => ({
        title: '',
        series: '',
        issueNumber: '',
        artUrl: '',
        description: '',
        pubYear: '',
        publisher: '',
        writer: '',
        artist: '',
        loadingStatus: false,
    }),

    props: ['comic'],

    components: {
        'loading': Loading,
    },

    methods: {

        comicQuery(){

            this.loadingStatus = true
            let id = this.comic

            this.$apollo.query({
                query: comicQuery,
                variables: {
                    id
                }
            }).then((response) => {
                this.title = response.data.Comic.title
                this.artUrl = response.data.Comic.artUrl
                this.artist = response.data.Comic.artist
                this.description = response.data.Comic.description
                this.issueNumber = response.data.Comic.issueNumber
                this.pubYear = response.data.Comic.pubYear
                this.publisher = response.data.Comic.publisher
                this.series = response.data.Comic.series
                this.writer = response.data.Comic.writer
                this.loadingStatus = false
            }).catch((error) => {
                // Error
                console.error(error)
                alert(error)
                this.loadingStatus = false
            })

        },

        updateComic(){
            this.loadingStatus = true

            let id = this.comic 
            let title = this.title
            let artUrl = this.artUrl
            let artist = this.artist 
            let description = this.description
            let issueNumber = this.issueNumber 
            let pubYear = this.pubYear 
            let publisher = this.publisher 
            let series = this.series 
            let writer = this.writer 

            // Mutation
            this.$apollo.mutate({
                mutation: updateComic,
                variables: {
                    id,
                    title,
                    artUrl,
                    artist,
                    description,
                    issueNumber,
                    pubYear,
                    publisher,
                    series,
                    writer
                },
            }).then((data) => {
                window.localStorage.setItem("Snackbar", true)
                window.localStorage.setItem("snackMessage", "Comic updated")
                this.$router.push({ path: '/' })
                location.reload()
            }).catch((error) => {
                // Error
                console.error(error)
                alert(error)
                this.loadingStatus = false
            })
        },

    },

    created() {
        this.comicQuery()
    }

}

</script>

<style scoped>

.editComic {
    display: flex;
    justify-content: flex-start;
    flex-direction: column;
    width: 67%;
}

input {
    margin-bottom: 25px;
    height: 50px;
    font-size: 18px;
}

textarea {
    height: 150px;
    width: 100%;
    font-size: 18px;
    border: solid 1px #bbb;
    margin-bottom: 25px;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", "Helvetica", "Arial", sans-serif;
    padding: 9px;
}

h1 {
    font-size: 24px;
    margin-bottom: 50px;
    margin-top: 10px;
}

li {
    float: left;
    margin-right: 10px;
}

ul {
    overflow: hidden;
}
.button:hover, .button.is-hovered {
    background-color: #fe0000;
    color: whitesmoke;
}

.button.is-danger.is-outlined:hover, .button.is-danger.is-outlined:focus{
    background-color: #fe0000;
}

.button.is-danger.is-outlined{
    border-color: #fe0000;
    color: #fe0000;
}

.button {
    width: 100%;
    height: 50px;
    font-size: 20px;
    margin-top: 25px;
    margin-bottom: 50px;
}

input {
    width: 100%;
}

</style>
<style src="bulma/css/bulma.css"></style>
