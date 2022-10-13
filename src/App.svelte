<script>
  import { onMount } from 'svelte';
  import SDConnect from './components/SDConnect.svelte';
  import SDCanvas from './components/SDCanvas.svelte';
  import SDLayer from './components/SDLayer.svelte';

  let SDCan;
  let SDCon;
  let result;

  let blob='';

  let img = '';

  let drawObjArr = [];
  let curObjIdx = 0;

  let toolIdx = 0;
  const toolArr = [
    'pen',
    'fill'
  ];

  onMount(()=>{
    // console.log(imgBlob);
    let toggle = true;
    document.addEventListener('keydown', (e) => {
      if(e.which == 32){
        toggle = !toggle;
        result.style.display = (toggle ? "none" : "block");
      }
      // curObjIdx++;
    })
  });

  function imgRet(e){
    console.log(e.detail);
    blob = e.detail;
  }

  function connectEvt(conEl){
    SDCan.toBlob();
    console.log(imgBlob);
    setTimeout(() => {

      SDCon.send(imgBlob);
    }, 500);
  }

  function blobEvt(e){
    // console.log(e.detail);
      SDCon.send(e.detail);
  }

  function drawEvt(e){
    const curObj = drawObjArr[curObjIdx];

    if(curObj){
      drawObjArr[curObjIdx] = [...curObj, ...e.detail];
    } else {
      drawObjArr[curObjIdx] = e.detail;
    }

    // console.log(drawObjArr);
  }
</script>

<main>
  <div id="header">
    <!--
    <SDNetwork on:imgRet={imgRet} bind:this={SDCon} on:connecting={connectEvt} />
    -->
    <SDConnect on:imgRet={imgRet} bind:this={SDCon} on:connecting={connectEvt} />
  </div>
  <div id="mainCont">
    <!--
    <SDCanvas bind:imgBlob={imgBlob} bind:this={SDCan} />
    -->
    <div style="position: relative; place-content: center; margin: auto">
      <img bind:this={result} style="position: absolute; top: 0px; left: 0px; z-index: 9999" src={blob} />
      <SDLayer on:blob={blobEvt} bind:imgBlob={img} bind:this={SDCan} bind:curTool={toolArr[toolIdx]} on:drawEvt={drawEvt} />
    </div>
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
