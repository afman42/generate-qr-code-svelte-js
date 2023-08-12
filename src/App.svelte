<script>
import QRCode from "qrcode";
import { tick} from "svelte"

let inputEl;
let qrCode = {
  name: "www.google.com",
  options: {
    //L,M,Q,H
    errorCorrectionLevel: "H",
    //0, 1, 2, 3, 4, 5, 6, 7.
    maskPattern: "0",
  },
  color: {
    dark: "#00050d",
    light: "#FFFFFF",
  },
  canvas: null,
  width: 200,
  margin: 4,
};


async function qr_code(qrCode) {
  await tick();
  QRCode.toCanvas(
    qrCode.name,
    {
      errorCorrectionLevel: qrCode.options.errorCorrectionLevel,
      maskPattern: parseInt(qrCode.options.maskPattern),
      color: qrCode.color,
      width: qrCode.width,
      margin: qrCode.margin,
    },
    function (err, canvas) {
      if (err) throw err;
      if(inputEl){
        inputEl.appendChild(canvas)      
        qrCode.canvas = canvas
      }
    }
  );
}

function download(){
  var link = document.createElement("a");
  link.download = qrCode.name + ".png";
  link.href = qrCode.canvas.toDataURL();
  link.click();
};


qr_code(qrCode)

function generate(e){
  e.preventDefault();
  if(qrCode.name != "" && qrCode.width > 0 && qrCode.margin > 0) {
    qr_code(qrCode)
    inputEl.children[0].remove()
  }else{
    alert("Missing Input")
  }
}
</script>



<div class="container">
  <div bind:this={inputEl} class="qrcode"></div>
  <div class="form-input">
    <form on:submit={generate}>
      <div>
        <label for="name">Name :</label>
        <input bind:value={qrCode.name} class="input" placeholder="Fill Name" />
      </div>
      <div>
        <label for="mp">Mask Pattern :</label>
        <select class="input" 
            bind:value={qrCode.options.maskPattern} 
            on:change={(event) => qrCode.options.maskPattern = event.currentTarget.value}
        >
          <option value="0">0</option>
          <option value="1">1</option>
          <option value="2">2</option>
          <option value="3">3</option>
          <option value="4">4</option>
          <option value="5">5</option>
          <option value="6">6</option>
          <option value="7">7</option>
        </select>
      </div>
      <div>
        <label for="ecl">Error Correction Level :</label>
        <input type="radio" value="L" 
          on:change={(event) => qrCode.options.errorCorrectionLevel = event.currentTarget.value} 
          checked={qrCode.options.errorCorrectionLevel == "L"}
        /> L
        <input type="radio" value="M"
          on:change={(event) => qrCode.options.errorCorrectionLevel = event.currentTarget.value} 
          checked={qrCode.options.errorCorrectionLevel == "M"}
        /> M
        <input type="radio" value="Q" 
          on:change={(event) => qrCode.options.errorCorrectionLevel = event.currentTarget.value} 
          checked={qrCode.options.errorCorrectionLevel == "Q"}
        /> Q
        <input type="radio" value="H" 
          on:change={(event) => qrCode.options.errorCorrectionLevel = event.currentTarget.value} 
          checked={qrCode.options.errorCorrectionLevel == "H"}
        /> H
      </div>
      <div style="display:flex;flex-direction:row;">
        <div style="margin-right:5px;">
          <label for="dark">Dark</label>
          <input type="color" bind:value={qrCode.color.dark} />
        </div>
        <div>
          <label for="light">Light</label>
          <input type="color" bind:value={qrCode.color.light} />
        </div>
      </div>
      <div>
        <label for="width">Width</label>
        <input type="number" class="input" min="0" bind:value={qrCode.width} id="width"/>
      </div>
      <div>
        <label for="margin">margin</label>
        <input type="number" class="input" min="0" bind:value={qrCode.margin} id="margin"/>
      </div>
      <div>
        <button type="button" on:click={download}>download</button>
        <button type="submit">generate</button>
      </div>
  </div>
</div>


