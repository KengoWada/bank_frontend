<template>
  <div class="about">

    <b-container fluid>
      <b-row>
        <b-col cols="4" class="leftSection"></b-col>
        <b-col cols="4">
          <b-spinner v-if="isLoading && !client"></b-spinner>
          <template v-else>
            <b-card
              v-if="client"
              :title="client.name"
              tag="article"
              style="max-width: 20rem;"
              class="mb-2"
            >
              <b-card-text>
                <p>Customer Requirements</p>
                <b-list-group>
                  <b-list-group-item
                  v-for="(action, index) in client.bankActions"
                  :key="index"
                  >{{ action }}</b-list-group-item>
                </b-list-group>
              </b-card-text>

              <b-button
                @click="fetchNextClient"
                variant="primary"
                :disabled="isLoading"
              >
                <b-spinner v-if="isLoading"></b-spinner>
                <template v-else>Next Client</template>
              </b-button>
            </b-card>
            <b-card v-else>
              <b-card-text>
                No clients in the queue.
              </b-card-text>
            </b-card>
          </template>
        </b-col>
      </b-row>
    </b-container>

    
    
  </div>
</template>


<script>
import axios from 'axios';

export default {
  name: 'Tellers',
  data() {
    return {
      client: null,
      isLoading: false,
    }
  },
  async created() {
    await this.fetchNextClient();
  },
  methods: {
    async fetchNextClient() {
      this.isLoading = true;

      const response = await axios.get('http://localhost:3000/');
      if (response.data.message !== 'Done') {
        this.client = null;
        this.isLoading = false;
        return;
      }

      setTimeout(async () => {
        this.client = response.data.client;
        this.isLoading = false;
      }, 3000);
    }
  },
}
</script>
