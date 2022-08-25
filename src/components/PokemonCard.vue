<template>
 <div class="poke-id">#{{ pokemon.id }}</div>
 <img :src="imageUrl + pokemon.id + '.png'" width="96" height="96" alt="">
 <h3>{{ pokemon.name }}</h3>
</template>

<script>
export default {
 props: [
  'imageUrl',
  'pokemon'
 ],
}
</script>

<style>
h3 {
 margin: 0;
}

.poke-id {
 position: absolute;
 top: 0;
 left: 0;
 font-size: .7rem;
 padding: 3px 5px;
 width: auto;
 height: auto;
 border-radius: 5px 0 5px 0;
 background: #1f9625;
 color: #efefef
}
</style>