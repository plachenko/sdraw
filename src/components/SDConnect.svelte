<script>
  import axios from 'axios';
  import { createEventDispatcher } from 'svelte';
    import SdCanvas from './SDCanvas.svelte';
  const genRanHex = size => [...Array(size)].map(() => Math.floor(Math.random() * 16).toString(16)).join('');
  
  let blobImg;
  export const send = function(blob){
    if(!blob) return;
    console.log('got blob:', blob);
    blobImg = blob; 
    setTimeout((e)=>{
      sendEvt();
    },1000);
  }

  let prompt = 'test';
  let connecting = false;
  let netArr = [
    {name: 'default', ip: '127.0.0.1', port: '7860'}
  ];

  const dispatch = createEventDispatcher();
  

  let curNetwork = netArr[0];

  let dataArr = [
    {name: 'prompt', type:'input', value: ''},
    {name: 'sampler', type:'dropdown', value: ['k_euler_a']},
    {name: 'samples', type:'range', value: ''},
    {name: 'cfg', type:'range', value: ''},
    {name: 'blurAmt', type:'range', value: ''},
  ];

  const img = '';
  const img2 = '';
  const mask = '';

  const SDProps = {
    sessionHash: genRanHex(10),
    prompt: prompt,
    blurAmt: 6,
    style: [
      'None',
      'None'
    ],
    imgs: [
      '',
      '',
      ''
    ],
    hints: [
      '',
      '',
      ''
    ]
  };

  /*
  const networkObj = {
    "fn_index": 28,
    "data": [
        0,
        SDProps.prompt,
        SDProps.negPrompt,
        SDProps.style[0],
        SDProps.style[1],
        SDProps.imgs[0],
        {
          "image": SDProps.imgs[1],
          "mask": SDProps.imgs[2]
        },
        null,
        null,
        "Draw mask",
        20,
        "Euler a",
        13,
        "latent noise",
        false,
        false,
        1,
        1,
        23.5,
        SDProps.blurAmt,
        -1,
        -1,
        0.33,
        0,
        0,
        false,
        512,
        512,
        "Just resize",
        false,
        32,
        "Inpaint masked",
        "",
        "",
        "None",
        "",
        "",
        1,
        50,
        0,
        false,
        12,
        0.95,
        SDProps.hints[0], 
        128,
        8,
        [
            "left",
            "right",
            "up",
            "down"
        ],
        1,
        0.05,
        128,
        4,
        "fill",
        [
            "left",
            "right",
            "up",
            "down"
        ],
        false,
        null,
        "",
        false,
        SDProps.hints[1],
        64,
        "None",
        "Seed",
        "",
        "Steps",
        "",
        true,
        false,
        null,
        "",
        SDProps.hints[2],
    ],
    "session_hash": SDProps.sessionHash
  };
  */
  const networkObj = {
    "fn_index": 28,
    "data": [
        0,
        prompt,
        "",
        "None",
        "None",
        blobImg,
        null,
        null,
        null,
        "Draw mask",
        20,
        "Euler a",
        4,
        "fill",
        false,
        false,
        1,
        1,
        7,
        0.75,
        -1,
        -1,
        0,
        0,
        0,
        false,
        512,
        512,
        "Just resize",
        false,
        32,
        "Inpaint masked",
        "",
        "",
        "None",
        "",
        "",
        1,
        50,
        0,
        false,
        4,
        1,
        "<p style=\"margin-bottom:0.75em\">Recommended settings: Sampling Steps: 80-100, Sampler: Euler a, Denoising strength: 0.8</p>",
        128,
        8,
        [
            "left",
            "right",
            "up",
            "down"
        ],
        1,
        0.05,
        128,
        4,
        "fill",
        [
            "left",
            "right",
            "up",
            "down"
        ],
        false,
        null,
        "",
        false,
        "<p style=\"margin-bottom:0.75em\">Will upscale the image to twice the dimensions; use width and height sliders to set tile size</p>",
        64,
        "None",
        "Seed",
        "",
        "Steps",
        "",
        true,
        false,
        null,
        "",
        ""
    ],
    "session_hash": "cvg4wti8xwh"
};

  let curStatus = 0;
  const statusArr = [
    {name: "sending...", color: '#CCC'},
    {name: "Connected!", color: '#0F0'},
    {name: "Can't Connect...", color: '#F00'},
  ];

  let imRet;

  function sendEvt(){
    let ip = curNetwork.ip || '127.0.0.1';
    let port = curNetwork.port || '7860';
    connecting = true;

    // console.log(networkObj);s
    console.log(blobImg)

    axios.post(`http://${ip}:${port}/api/predict`, {
    "fn_index": 28,
    "data": [
        0,
        prompt,
        "",
        "None",
        "None",
        blobImg,
        null,
        null,
        null,
        "Draw mask",
        20,
        "Euler a",
        4,
        "fill",
        false,
        false,
        1,
        1,
        16,
        0.3,
        -1,
        -1,
        0,
        0,
        0,
        false,
        512,
        512,
        "Just resize",
        false,
        32,
        "Inpaint masked",
        "",
        "",
        "None",
        "",
        "",
        1,
        50,
        0,
        false,
        4,
        1,
        "<p style=\"margin-bottom:0.75em\">Recommended settings: Sampling Steps: 80-100, Sampler: Euler a, Denoising strength: 0.8</p>",
        128,
        8,
        [
            "left",
            "right",
            "up",
            "down"
        ],
        1,
        0.05,
        128,
        4,
        "fill",
        [
            "left",
            "right",
            "up",
            "down"
        ],
        false,
        null,
        "",
        false,
        "<p style=\"margin-bottom:0.75em\">Will upscale the image to twice the dimensions; use width and height sliders to set tile size</p>",
        64,
        "None",
        "Seed",
        "",
        "Steps",
        "",
        true,
        false,
        null,
        "",
        ""
    ],
    "session_hash": "cvg4wti8xwh"
}
    
    ).then((e)=>{
      imRet = e.data.data[0][0];
      dispatch('imgRet', imRet);
      connecting = false;
    }).catch((e)=>{
      curStatus = 2;
      statusArr[2].name = "Can't Connect: "+ e.message;
      setTimeout((e)=>{
        connecting = false;
      }, 2000);
    });
  }

  function clickEvt(e){
    e.preventDefault();
    dispatch('connecting');
    console.log('test');
  }

  function inputChangeEvt(e){
    e.preventDefault();
    console.log(e);
  }

  function netChangeEvt(e){
    e.preventDefault();
    if(!curNetwork.port){
      curNetwork.port = '7860';
    }
  }

</script>

<div id="SDConnectContainer">
  <form id="SDConnectInput">

    <select bind:value={curNetwork} on:change={netChangeEvt}>
      {#each netArr as network}
        <option value={network}>{network.name}</option>
      {/each}
    </select>
    
      <label for="ip">IP</label>
      <input 
        id="ip" 
        name="ip" 
        bind:value={curNetwork.ip} 
        type="input" 
        placeholder="ip" />
  
      <label for="port">Port</label>
      <input 
        bind:value={curNetwork.port} 
        id="port" 
        name="port"
        placeholder="port" 
        type="input" />

        <br />
      <input bind:value={prompt} />
      <button on:click={clickEvt}>Send</button>

    <!-- --
      <hr />

      {#each dataArr as data}
        <input type={data.type} on:input={inputChangeEvt} />
      {/each}
    
    <label for="port" style="float:left;">Prompt</label>
    <input 
      id="port" 
      name="port" 
      bind:value={port} 
      placeholder="port" 
      type="input" />

    <hr />
    <label for="port" style="float:left;">Prompt</label>
    <input 
      id="port" 
      name="port" 
      bind:value={port} 
      placeholder="port" 
      type="input" />
 -->
  </form>
  {#if connecting}
    <div id="SDConnectStatus" style="background-color: {statusArr[curStatus].color}">{statusArr[curStatus].name}</div>
  {/if}
</div>

<style>
  form{
    margin: 0px;
    padding: 0px;
  }
  input{
    border: none;
    border-bottom: 1px solid;
    background-color: #FFF;
    color:#000;
    }

  #SDConnectContainer{
    color: #000;
    /* position: absolute; */
    /* top: 0px; */
    }
  #SDConnectStatus{
    background-color:#F00;
    }

    #SDConnectInput{
      padding: 10px;
    }
</style>
