<script>
  import { onMount } from 'svelte';
  import SDConnect from './components/SDConnect.svelte';
  import SDCanvas from './components/SDCanvas.svelte';
  import SDLayer from './components/SDLayer.svelte';
  import svelteLogo from './assets/svelte.svg'
  import Counter from './lib/Counter.svelte'

  import SDNetwork from './components/SDNetwork.svelte';

  export let imgBlob;
  let SDCan;
  let SDCon;

  let blob='';

  onMount(()=>{
    console.log(imgBlob);
  });

  function imgRet(e){
    console.log(e.detail);
    blob = e.detail;
  }

  function connectEvt(conEl){
    console.log('connect.');
    SDCan.toBlob();
    console.log(imgBlob,'t');
    /* SDCon.send(imgBlob); */
  }
</script>

<main>
  <div id="header">
    <!--
    <SDNetwork on:imgRet={imgRet} bind:this={SDCon} on:connecting={connectEvt} />
    -->
    <!--<SDConnect on:imgRet={imgRet} bind:this={SDCon} on:connecting={connectEvt} />-->
  </div>
  <div id="mainCont">
    <!--
    <SDCanvas bind:imgBlob={imgBlob} bind:this={SDCan} />
    -->
    <SDLayer />
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
