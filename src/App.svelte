<script>
  import { onMount } from 'svelte';
  import SDConnect from './components/SDConnect.svelte';
  import SDCanvas from './components/SDCanvas.svelte';
  import svelteLogo from './assets/svelte.svg'
  import Counter from './lib/Counter.svelte'

  let SDCan;
  let SDCon;

  let blob='';

  function imgRet(e){
    console.log(e.detail);
    blob = e.detail;
  }

  function connectEvt(conEl){
    SDCan.toBlob();
    SDCon.send(blob);
  }
</script>

<main>
  <div id="header">
    <SDConnect on:imgRet={imgRet} bind:this={SDCon} on:connecting={connectEvt} />
  </div>
  <div id="mainCont">
    <SDCanvas bind:imgBlob={blob} bind:this={SDCan} />
  </div>

</main>

<style>
  main{
    width:100%;
    height:100%;
    user-select: none;
    }
    #mainCont{
      display: flex;
      height: 100%;
      flex: 1;
      place-content: center;
    }
  #header{
    width: 100%;
    min-height: 30px;
    background-color:#DDD;
    border-bottom: 1px solid;
    }

</style>
