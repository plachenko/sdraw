<script>
  import { onMount } from 'svelte';

  export let imgBlob;

  let canvas;
  let tmpCanvas;
  let tmpCtx;
  let ctx;
  let curCol = '#000';

  onMount(() => {
    /* tmpCanvas.width = 512; */
    /* tmpCanvas.height = 512; */

    canvas.width = 512;
    canvas.height = 512;

    /* tmpCtx = tmpCanvas.getContext('2d'); */
    ctx = canvas.getContext('2d');
    // ctx.fillStyle = "#FF0";
    // ctx.fillRect(0, 0, canvas.width, canvas.height);
  });

  let count = 0
  let tCont;

  let strokePos = [];

  let pdn = false;

  const increment = () => {
    count += 1
  }

  function pDn(e){
    pdn = true;
    ctx.strokeStyle = curCol;
    /* tmpCtx.beginPath(); */
    ctx.moveTo(e.offsetX, e.offsetY)
  }

  function pUp(){
    /* ctx.closePath(); */
    /*
    tmpCtx.clearRect(0,0,canvas.width, canvas.height)
    strokePos.forEach((el)=>{
      tmpCtx.lineTo(el[0], el[1]);
    });

    tmpCtx.fill();

    strkoePos = [];
    pdn = false;
    */
    /* ctx.closePath(); */
    /* ctx.stroke(); */
    pdn = false;
    /* toBlob(); */
  }

  export const toBlob = function(el){
    let  url = '';
    canvas.toBlob((e)=>{
      
      let reader = new FileReader();
      reader.readAsDataURL(e);
      reader.onloadend = function(){
        let base64 = reader.result;
        // console.log(base64);
        imgBlob = base64;
      }

    })
  }

  function pMv(e){
    if(pdn){
      /* ctx.clearRect(0,0,canvas.width, canvas.height) */
      /* strokePos.push([e.offsetX, e.offsetY]); */

      /* strokePos.forEach((el)=>{ */
      /*   ctx.lineTo(el[0], el[1]); */
      /* }); */

      ctx.lineTo(e.offsetX, e.offsetY);

      ctx.stroke();

      /*
      tmpCtx.closePath();
      tmpCtx.fill();
      */
    }
  }

  function lineWidth(e){
    ctx.lineWidth = e.srcElement.value;
  }

  function colorValue(e){
    ctx.fillStyle = ""+e.srcElement.value;
  }
  
</script>

<div id="SDCanvasContainer">
  <div 
    id="touchContainer"
    on:pointerdown={pDn}
    on:pointerup={pUp}
    on:pointermove={pMv}
    bind:this={tCont}>
  </div>
  {#if imgBlob}
    <img style="position: absolute; left: 0px; top:0px;" src={imgBlob} />
  {/if}
  <!--
  <canvas bind:this={tmpCanvas}></canvas>
  -->
  <canvas bind:this={canvas}></canvas>

  <div id="controlsDiv">
    Size: 
    <input type="range" on:input={lineWidth} />
    Color: 
    <input type="color" bind:value={curCol} on:input={colorValue} />

  </div>
</div>

<style>
  /* canvas{position: absolute; width: 100%; height:100%;top:0px;left:0px;} */
#controlsDiv{
  background-color:#CCC;
  min-height: 10px;
  width: 100%;
  position: absolute;
}

#SDCanvasContainer{
  background-color:#FFF;
  border: 1px solid;
  box-sizing: border-box;
  position: relative;
  height: 512px;
  place-self: center;
  cursor: crosshair;
  }
#touchContainer{
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0px;
  top: 0px;
  z-index:9999;
}

</style>

