<script>
  import { HtmlTag } from "svelte/internal";
  import { get } from "svelte/store";

  import Tailwindcss from "./Tailwindcss.svelte";
  String.prototype.replaceAt = function (index, replacement) {
    return (
      this.substr(0, index) +
      replacement +
      this.substr(index + replacement.length)
    );
  };
   let k = 3;
   let f = 0;
   async function checkRow(x) {
    let yy = await krz;
    let nzyw = [...yy];
    let temp = nzyw.map((a) => a.word);
    let word2 = temp[x.x];
    let index = nzyw[temp.indexOf(word2)].indexpass;
    let s = true;
    for (let i = 0; i < word2.length; i++) {
      if (
        document.getElementById(x.x + "x" + i).value != word2[i] &&
        document.getElementById(x.x + "x" + i).value != " " + word2[i] &&
        document.getElementById(x.x + "x" + i).value != word2[i] + " "
      ) {
        s = false;
      }
    }
    if (s) {
      for (let i = 0; i < word2.length; i++) {
        document.getElementById(x.x + "x" + i).style.backgroundColor =
          "rgb(133, 255, 129)";
        document.getElementById(x.x + "x" + i).style.color = "black";
        document.getElementById(x.x + "x" + i).disabled = true;
      }
      let pass = await getPass();
      document.getElementById(`${index}pass`).value = pass[index]
      f++;
      if (
        f==pass.length
      ) {
        win();
      }
    }
  }
  
  const krz = getPasses();
  async function getPass() {
    let resp = await fetch("./krzyzowka.json");
    let respJSON = await resp.json();
    let pass = respJSON[0].password;
    return pass;
  }
  var time = 1;
  var interval = setInterval(function () {
    var m = Math.floor(time / 60);
    var s = time - m * 60;
    var fTime =
      xxx(m, "0", 2) + ":" + xxx(s, "0", 2);
    document.getElementById("gameTimer").innerHTML = "Czas:  " + fTime;
    time++;
  }, 1000);
  async function getPasses() {
    let resp = await fetch("./krzyzowka.json");
    let respJSON = await resp.json();
    let JJ = respJSON[0].words;
    let shuffledArray = JJ.sort((a, b) => 0.5 - Math.random());
    return shuffledArray;
  }
 
  async function removedFrom() {
    let x = await krz;
    let nzyw = [...x];
    let temp = nzyw.map((a) => a.word);
    for (let i = 0; i < temp.length; i++) {
      for (
        let k = 0;
        k < temp[i].length - Math.round(temp[i].length / 3);
        k++
      ) {
        let temprandom = Math.floor(Math.random() * temp[i].length);
        if (temp[i][temprandom] != " ") {
          temp[i] = temp[i].replaceAt(temprandom, " ");
        } else {
          k--;
        }
      }
    }
    return temp;
  }
  async function win() {
    clearInterval(interval);
    document.body.style.background =
      "yellowgreen";
  }
 
  async function dInp() {
    const delay = (ms) => new Promise((res) => setTimeout(res, ms));
    await delay(300);
    let x = await krz;
    let nzyw = [...x];
    let temp = nzyw.map((a) => a.word);
    for (let i = 0; i < temp.length; i++) {
      for (let j = 0; j < temp[i].length; j++) {
        let input = document.getElementById(i + "x" + j);
        if (input.value != " ") {
          input.disabled = true;
          input.style.color = "black";
          input.style.backgroundColor = "#ababab";
        }
      }
    }
  }
  
  
  async function letterCheck() {
    let x = await krz;
    let nzyw = [...x];
    let temp = nzyw.map((a) => a.word);
    let litera = document.getElementById("letter").value;
    for (let i = 0; i < temp.length; i++) {
      for (let j = 0; j < temp[i].length; j++) {
        if (temp[i][j] == litera) {
          document.getElementById(i + "x" + j).value = temp[i][j];
          document.getElementById(i + "x" + j).disabled = true;
          document.getElementById(i + "x" + j).style.color = "black";
          document.getElementById(i + "x" + j).style.backgroundColor =
            "#01796f";
          document.getElementById(i + "x" + j).style.border =
            "2px solid #004a08";
        }
      }
    }
    k--;
    document.getElementById("hm").innerHTML =
      "Pozostało: " + k + " prob";
    if (k == 0) {
      document.getElementById("b1").disabled = true;
    }
  }
  function xxx(string, pad, length) {
    return (new Array(length + 1).join(pad) + string).slice(-length);
  }
</script>

<Tailwindcss />
<container id="main">
  {#await removedFrom() then res}
    {#each res as item, x}
      <div id={x}>
        {#each item as j, y}
          <input id="{x}x{y}" type="text" value={j} on:input={(e) => checkRow({ x })}/>
        {/each}
      </div>
    {/each}
  {/await}
  {#await dInp()}
  <!-- hmm -->
  {/await}
  {#await getPass() then res}
      <div class="password">
        {#each res as item, x}
        <input value=" " id="{x}pass" disabled style="color:black;">
        {/each}
      </div> 
  {/await}
</container>
<p id="gameTimer">Czas: 00:00</p>
<div id="letterToShow">
  <input type="text" name="letter" id="letter" />
  <button on:click={letterCheck} id="b1">Sprawdź!</button>
  <p id="hm">Pozostało: 3 prob</p>
</div>

<style>
 * {
    padding: 0px;
    margin: 0px;
  }
  .password{
    border:1px solid black;
    margin-top:30px;
    text-align:center;
  }
  input {
    border: 1px solid #074f08;
  }
  input {
    width: 50px;
    height: 50px;
    text-align: center;
  }
 
  input {
    margin: 1px;
  }
  div {
    margin-bottom: 2px;
  }
  :global(body) {
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background: wheat;
  }
  #hm{
	  color: rgb(24, 143, 24);
	  font-size: larger;
  }
 
  #b1 {
    background-color: rgb(24, 143, 24);
	color:whitesmoke;
    padding: 5px;
    border-radius: 20px;
    font-weight: bold;
  }


  #gameTimer {
    position: absolute;
	color:lightcoral;
    font-size: 6em;
    top: 10px;
    left: 18%;
  }
  #letterToShow {
    position: absolute;
    top: 60px;
    left: 61%;
  }
  #main{
    position:absolute;
    top:200px;
  }
</style>
