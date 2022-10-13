<script>
  import axios from 'axios';
  import { onMount, createEventDispatcher } from 'svelte';
  import SdCanvas from './SDCanvas.svelte';

  const genRanHex = size => [...Array(size)].map(() => Math.floor(Math.random() * 16).toString(16)).join('');
  
  let blobImg;
  export const send = function(blob){
    console.log(blob);
    if(!blob) return;
    console.log('got blob:', blob);
    blobImg = blob; 
    setTimeout((e)=>{
      sendEvt();
    },1000);
  }

  let name = 'default';
  let ip = '127.0.0.1';
  let port = '7860';

  let prompt = 'test';
  let connecting = false;
  let netArr = [];

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

  onMount((e)=>{
    axios.post(`http://${ip}:${port}/api/predict`, {
      data: [],
      fn_index: 163,
      session_hash: genRanHex(10)
    });
    localStorage.removeItem('ipStore');
    netArr = localStorage.getItem('ipStore') || [{name: name, ip: ip, port: port}];
    console.log(netArr);
  });

  const SDObj = {
    prompt: prompt,
    neg: '',
    blob: null,
    sampleSteps: 20,
    sampleMethod: 'Euler a',
    CFGScale: 7,
    denoise: .6,
    dim: {
      w: 512,
      h: 512
    }
  };

  let curStatus = 0;
  const statusArr = [
    {name: "sending...", color: '#CCC'},
    {name: "Connected!", color: '#0F0'},
    {name: "Can't Connect...", color: '#F00'},
  ];

  let imRet;

  function sendEvt(){
    // let ip = curNetwork.ip || '127.0.0.1';
    // let port = curNetwork.port || '7860';
    connecting = true;

    SDObj.prompt = prompt
    SDObj.blob = blobImg;

    axios.post(`http://${ip}:${port}/api/predict`, {
    "fn_index": 31,
    "data": [
        0,
        SDObj.prompt,
        SDObj.neg,
        "None",
        "None",
        SDObj.blob, 
        null,
        null,
        null,
        "Draw mask",
        SDObj.sampleSteps,
        SDObj.sampleMethod,
        4,
        "fill",
        false,
        false,
        1,
        1,
        SDObj.CFGScale,
        SDObj.denoise,
        -1,
        -1,
        0,
        0,
        0,
        false,
        SDObj.dim.w,
        SDObj.dim.h,
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
        false,
        null,
        "",
        "", //html hint
        64,
        "None",
        "Seed",
        "",
        "Nothing",
        "",
        true,
        false,
        null,
        "", //html hint
        ""  //html hint
    ],
    "session_hash": "smr4vp09g4"
  }).then((e)=>{
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

  function handleSubmit(e){
    dispatch('connecting');

    // console.log(imgBlob);
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

  function saveIP(){
    netArr.push({
      name: name,
      ip: ip,
      port: port
    });

    console.log(netArr);

    /* localStorage.setItem('ipStore', netArr) */
  }

</script>

<div id="SDConnectContainer">
  <form id="SDConnectInput" on:submit|preventDefault={handleSubmit}>
      <div class="row">
        <select bind:value={curNetwork} on:change={netChangeEvt}>
          {#each netArr as network}
            <option value={network}>{network.name}</option>
          {/each}
        </select>
      
        <label for="ip">IP</label>
        <input 
          id="ip" 
          name="ip" 
          bind:value={ip} 
          type="input" 
          placeholder="ip" />
    
        <label for="port">Port</label>
        <input 
          bind:value={port} 
          id="port" 
          name="port"
          placeholder="port" 
          type="input" />

        <div style="background-color:#F00;" on:click={saveIP}>Save</div>
      </div>

      <div class="row">
        <input bind:value={prompt} />
        <button>Make Art</button>
      </div>
  </form>

  {#if connecting}
    <div id="SDConnectStatus" style="background-color: {statusArr[curStatus].color}">{statusArr[curStatus].name}</div>
  {/if}
  
</div>

<style>
  form{
    margin: 0px;
    padding: 0px;
    display: flex;
    flex: 1;
    width:100%;
    flex-wrap: wrap;
    }

  input{
    flex: 1 1 160px;
    margin: 0px 5px;
    border: none;
    border-bottom: 1px solid;
    background-color: #FFF;
    color:#000;
    }

  #SDConnectContainer{
    color: #000;
    /* position: absolute; */
    /* top: 0px; */
    flex: 1;
    display: flex;
    width:100%;
    }
  #SDConnectContainer .item{
    flex: 1 1 160px;
  }
  #SDConnectContainer .row{
    display: flex;
    flex-direction: row;
  }
  #SDContainer .row > div{
    flex: 1;
    /* width: 100px; */
    width: 100%;
  }
  #SDConnectStatus{
    background-color:#F00;
    }

    #SDConnectInput{
      padding: 10px;
    }

    /*
    .row{
      flex: 1;
    }
    */
</style>
