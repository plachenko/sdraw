<script>
    import { onMount } from 'svelte';
  
    let canvas;
    let ctx;

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
      ctx = canvas.getContext('2d');
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
  
    function pUp(){
      clearInterval(drawInt);
      drawInt = null;

      pdn = false;
      strokeArr = [];

      console.log('send');
    }
  
    function pMv(e){
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
  
  
