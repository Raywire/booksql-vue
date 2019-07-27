<template>
  <div class="home">
    <!-- Apollo Query -->
    <ApolloQuery :query="categoriesQuery">
      <!-- The result will automatically updated -->
      <template slot-scope="{ result: { data, loading }, isLoading }">
        <!-- Some content -->
        <div v-if="isLoading">Loading...</div>
        <div v-else>
          <a href="#" class="link-margin" @click.prevent="selectCategory('all')"
            >All</a
          >
          <a
            href="#"
            class="link-margin"
            @click.prevent="selectCategory('featured')"
            >Featured</a
          >
          <a
            href="#"
            @click.prevent="selectCategory(category.id)"
            v-for="category of data.categories"
            :key="category.id"
            class="link-margin"
          >
            {{ category.id }}.
            {{ category.name }}
          </a>
        </div>
      </template>
    </ApolloQuery>
    <div v-if="selectedCategory === 'all'">
      <ApolloQuery
        :key="1"
        :query="query"
        :variables="{ count: count, page: page }"
      >
        <!-- The result will automatically updated -->
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <h1>All Books</h1>
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <div v-for="book of data.books.data" :key="book.id">
              <router-link :to="`/books/${book.id}`">
                {{ book.id }}.
                {{ book.title }}
              </router-link>
              <div>
                {{ book.author }}
              </div>
              <img :src="`${book.image}`" alt="cover image">
            </div>
            <div v-if="!data.books.paginatorInfo.hasMorePages">
              <button @click="loadPrevious()">Previous</button>
            </div>
            <div v-if="data.books.paginatorInfo.hasMorePages">
              <button @click="loadNext()">Next</button>
            </div>
          </div>
        </template>
      </ApolloQuery>
    </div>
    <div v-else-if="selectedCategory === 'featured'">
      <ApolloQuery :key="2" :query="query" :variables="{ featured: true }">
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <h1>Featured</h1>
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <div v-for="book of data.booksByFeatured" :key="book.id">
              <router-link :to="`/books/${book.id}`">
                {{ book.id }}.
                {{ book.title }}
              </router-link>
              <div>
                {{ book.author }}
              </div>
              <img :src="`${book.image}`" alt="cover image">
            </div>
          </div>
        </template>
      </ApolloQuery>
    </div>
    <div v-else>
      <ApolloQuery
        :key="3"
        :query="query"
        :variables="{ id: selectedCategory }"
      >
        <!-- The result will automatically updated -->
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <!-- Some content -->
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <h1>{{ data.category.name }} Books</h1>
            <div v-for="book of data.category.books" :key="book.id">
              <router-link :to="`/books/${book.id}`">
                {{ book.id }}.
                {{ book.title }}
              </router-link>
              <div>
                {{ book.author }}
              </div>
              <img :src="`${book.image}`" alt="cover image">
            </div>
          </div>
        </template>
      </ApolloQuery>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src 
import categoryQuery from "@/graphql/queries/Category.gql"
import categoriesQuery from "@/graphql/queries/Categories.gql"
import booksQuery from"@/graphql/queries/Books.gql"
import booksFeaturedQuery from "@/graphql/queries/BooksFeatured.gql"

export default { 
  name: "home",
  components:{},
  data() {
    return { categoryQuery, categoriesQuery, booksQuery,
  booksFeaturedQuery, selectedCategory: "all", query: booksQuery, categories: [],
  books: [], count: 20, page: 1 } 
  },
  methods: {
    selectCategory(category)
    {
      if(category === "all")
      { 
        this.query = booksQuery
      }
      else if (category === "featured")
      { this.query = booksFeaturedQuery }
      else { this.query = categoryQuery } this.selectedCategory = category
      },
    loadNext() { 
      this.page += 1
    },
    loadPrevious() { 
      this.page -= 1
    }
  } 
}
</script>
<style>
.link-margin {
  margin-right: 2em;
}
</style>
