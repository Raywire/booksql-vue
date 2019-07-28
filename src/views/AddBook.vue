<template>
  <div class="container">
    <h1>Create Book</h1>
    <form action="#" method="POST" @submit.prevent="addBook()">
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
        <button class="btn btn-primary" type="submit">Add Book</button>
      </div>
    </form>
  </div>
</template>
<script>
import addBook from '@/graphql/mutations/AddBook.gql'
export default {
  data() {
    return {
      title: '',
      author: '',
      image: '',
      description: '',
      link: '',
      featured: false,
      category_id: 1
    }
  },
  methods: {
    addBook() {
      this.$apollo.mutate({
        mutation: addBook,
        variables: {
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
        this.$router.push("/")
      }).catch((error) => {
        console.error(error)
      })
    }
  }
}
</script>

