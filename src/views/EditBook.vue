<template>
  <div class="container">
    <h1>Edit Book</h1>
    <form action="#" method="POST" @submit.prevent="editBook">
      <div class="form-group">
        <label for="title">Title</label>
        <input class="form-control" type="text" name="title" id="title" v-model="title">
      </div>
      <div class="form-group">
        <label for="author">Author</label>
        <input class="form-control" type="text" name="author" id="author" v-model="author">
      </div>
      <div class="form-group">
        <label for="image">Image</label>
        <input class="form-control" type="text" name="image" id="image" v-model="image">
      </div>
      <div class="form-group">
        <label for="description">Description</label>
        <textarea class="form-control" name="description" id="description" cols="30" rows="10" v-model="description"></textarea>
      </div>
      <div class="form-group">
        <label for="link">Link</label>
        <input class="form-control" type="text" name="link" id="link" v-model="link">
      </div>
      <div class="form-check">
        <label for="featured"><input type="checkbox" class="form-check-input" name="featured" v-model="featured">Featured</label>
      </div>
      <div class="form-group">
        <ApolloQuery :query="require('@/graphql/queries/Categories.gql')">
          <template slot-scope="{ result: { data, loading }, isLoading }">
            <div v-if="isLoading">Loading...</div>
            <select v-else class="form-control" v-model="category_id">
              <option
                v-for="category of data.categories"
                :key="category.id"
                class="link-margin"
                :value="category.id"
              >
                {{ category.name }}
              </option>
            </select>
          </template>
        </ApolloQuery>
      </div>
      <div class="form-group">
        <button class="btn btn-primary" type="submit">Update Book</button>
      </div>
    </form>
  </div>
</template>
<script>
import updateBook from '@/graphql/mutations/UpdateBook.gql'
import book from '@/graphql/queries/Book.gql'
export default {
  data() {
    return {
      title: '',
      author: '',
      image: '',
      description: '',
      link: '',
      featured: false,
      category_id: 1,
      book: null
    }
  },
  apollo: {
    // Advanced query with parameters
    // The 'variables' method is watched by vue
    book: {
      query: book,
      // Reactive parameters
      variables () {
        // Use vue reactive properties here
        if (this.$route && this.$route.params) {
          return {
            id: this.$route.params.id,
          }
        }
      },
      // Optional result hook
      result ({ data:{ book }, loading, networkStatus }) {
        console.log('We got some result!')
        this.title = book.title
        this.author = book.author
        this.image = book.image
        this.description = book.description
        this.link = book.link
        this.featured = book.featured
        this.category_id = book.category.id
      },
    },
  },
  methods: {
    editBook() {
      this.$apollo.mutate({
        mutation: updateBook,
        variables: {
          id: this.$route.params.id,
          title: this.title,
          author: this.author,
          image: this.image,
          link: this.link,
          description: this.description,
          featured: this.featured,
          category_id: parseInt(this.category_id, 10)
        }
      }).then((data) => {
        console.log(data)
        this.$router.push(`/books/${this.$route.params.id}`)
      }).catch((error) => {
        console.error(error)
      })
    }
  }
}
</script>

