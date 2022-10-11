<script>
    import { onMount, createEventDispatcher } from 'svelte';
  
    export let curCol = '#000';
    /* export let lineWidth = '1'; */

    const dispatch = createEventDispatcher();


    let canvas;
    let ctx;

    let strokeArr = [];
    let pdn = false;
    let pout = false;
    let drawInt = null;

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
  
    onMount(() => {
      canvas.width = 512;
      canvas.height = 512;
      ctx = canvas.getContext('2d');

      document.addEventListener('pointerup', (e) => {
        if(!pout) return;
        pUp();
        pout = false;
      });
    });

    function draw(){
      drawInt = setInterval((e) => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        pen();
      }, 10);
    }

    function pen(){
      strokeArr.forEach((e, idx) => {
        if(idx < 1) return;
        ctx.moveTo(strokeArr[idx-1][0],  strokeArr[idx-1][1]);
        ctx.lineTo(e[0], e[1]);
      });
      ctx.stroke();
    }

    function fill(){
      ctx.beginPath();
      strokeArr.forEach((e, idx) => {
        if(idx < 1) return;
        ctx.lineTo(e[0], e[1]);
      });
      ctx.closePath();
      ctx.fill();
    }
  
    function pDn(e){
      pdn = true;
      ctx.strokeStyle = curCol;
      strokeArr.push([e.offsetX, e.offsetY]);
      ctx.moveTo(e.offsetX, e.offsetY)
      draw();
    }
  
    function pOut(){
      if(pdn){
        pout = true;
      }
    }

    function pIn(){
      if(pdn){
        pout = false;
      }
    }

    function pUp(){
      clearInterval(drawInt);
      drawInt = null;

      pdn = false;

      strokeArr = [];
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      dispatch('drawEvt');
    }
  
    function pMv(e){
      if(pout) return;
      if(pdn){
        strokeArr.push([e.offsetX, e.offsetY]);
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
      on:pointerout={pOut}
      on:pointerenter={pIn}
      >
    </div>
  
    <canvas bind:this={canvas}></canvas>
  </div>
  
  <style>
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
  
  

