<script lang="ts">
  import { msg } from "./stores";
  import { Router, Link, Route } from "svelte-routing";
  import Home from "@/routes/Home.svelte";
  import Bot from "@/routes/Bot.svelte";

  let msgStore: string;

  const unsubMsgStore = msg.subscribe((val) => (msgStore = val));
</script>

<style lang="scss">
  #app {
    width: 100%;
    height: 100%;
    background-color: black;

    // * {
    //   border: 1px solid white;
    // }

    .background {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      background: url("assets/images/gears.gif") center / 25vh;
      filter: hue-rotate(180deg);
      opacity: 0.25;
    }

    nav {
      display: flex;
      justify-content: space-around;
      color: white;
    }
  }
</style>

<div id="app">
  <div class="background" />
  <Router>
    <nav>
      {msgStore}
      <Link to="/">Home</Link>
      <Link to="bot">Bot</Link>
      {msgStore}
    </nav>
    <div>
      <Route path="/" component="{Home}" />
      <Route path="bot" component="{Bot}" />
    </div>
  </Router>
</div>
