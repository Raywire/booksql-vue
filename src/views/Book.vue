
<template>
  <div class="book">
    <ApolloQuery :key="2" :query="require('@/graphql/queries/Book.gql')" :variables="{ id: $route.params.id }">
      <template slot-scope="{ result: { data, loading }, isLoading }">
        <h1>Featured</h1>
        <div v-if="isLoading">Loading...</div>
        <div v-else>
          <div>{{ data.book.title }}</div>
          <div>{{ data.book.author }}</div>
          <img :src="`${data.book.image}`" alt="Book Cover">
        </div>
      </template>
    </ApolloQuery>
  </div>
</template>
