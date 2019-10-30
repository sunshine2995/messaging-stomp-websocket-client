<template>
  <div class="about">
    <h1>This is an about page</h1>
    <button @click="sub1">sub1</button>
    <button @click="sub2">sub2</button>
    <button @click="click">click</button>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import SockJS from 'sockjs-client';
import Stomp from 'stompjs';

@Component
export default class About extends Vue {
  private client: Stomp.Client;

  public mounted() {
    const sock = new SockJS('http://192.168.96.134:8080/gs-guide-websocket');
    const that = this;
    const client = Stomp.over(sock);
    client.connect(
      {},
      (frame: any) => {
        client.subscribe('/topic/greetings', (greeting: any) => {
          console.log('!greeting: ', JSON.parse(greeting.body).content);
        });
      },
    );
    this.client = client;
  }

  public click() {
    this.client.send('/app/hello', {}, JSON.stringify({ name: 'ming' }));
  }

  public sub1() {
    this.client.subscribe('/topic/products/1', (greeting) => {
      console.log('!sub1: ', JSON.parse(greeting.body).content);
    });
  }

  public sub2() {
    this.client.subscribe('/topic/products/2', (greeting) => {
      console.log('!sub2: ', JSON.parse(greeting.body).content);
    });
  }
}
</script>
