<script>
  let sentence = "[Select Difficulty Above]"
  let typed = ""
  let received = ""
  let theyTyped = ""
  let theirProgress = 0

  const ws = new WebSocket( 'ws://127.0.0.1:3000' )

  // when connection is established...
  ws.onopen = () => {

    ws.onmessage = async msg => {
      received = await msg.data.text()

      if (received === "1") {
        sentence = "This is a short blurb to test your typing ability. You even get to race a friend. How fun!"
      } else if (received === "2") {
        sentence = "This is a more complex blurb. It will require more than just a few sentences, and it should take you a bit longer than the easy one. If you're looking for more of a challenge try hard."
      } else if (received === "3") {
        sentence = "This is the most complex blurb; it includes various punctuation as well as many words. Did you know that the sentence 'A quick brown fox jumped over the lazy dog.' uses all the letters in the alphabet? That is such a fascinating thing to think about. Wouldn't you agree?"
      } else if (received === "win") {
        alert("They Won. :(")
      }else if (received !== "1" && received !== "2" && received !== "3" && received !== "win") {
        theyTyped = received
        theirProgress = Math.min(100, Math.floor((received.length / sentence.length) * 100))
      }
    }
  }

  const send = function() {
    typed = document.querySelector('input').value
    ws.send( typed )
    win()
  }

  const easy = function() {
    sentence = "This is a short blurb to test your typing ability. You even get to race a friend. How fun!"
    ws.send( 1 )
  }

  const medium = function() {
    sentence = "This is a more complex blurb. It will require more than just a few sentences, and it should take you a bit longer than the easy one. If you're looking for more of a challenge try hard."
    ws.send( 2 )
  }

  const hard = function() {
    sentence = "This is the most complex blurb; it includes various punctuation as well as many words. Did you know that the sentence 'A quick brown fox jumped over the lazy dog.' uses all the letters in the alphabet? That is such a fascinating thing to think about. Wouldn't you agree?"
    ws.send( 3 )
  }

  const win = function() {
    if (Math.floor((typed.length / sentence.length) * 100) === 100) {
      ws.send("win")
      alert("You Won! :)")
    }
  }
</script>

<style>
    .progress {
        height: 10px;
        background-color: green;
        border-style: dotted;
        border-width: 1px;
    }
    .other {
        height: 10px;
        background-color: red;
        border-style: dotted;
        border-width: 1px;
    }
    .opponent {
        border-style: solid;
        border-width: 1px;
        padding: 5px;
        overflow-wrap: break-word;
    }
    .input {
        width: 100%;
    }
    button {
        margin-right: 10px;
    }
</style>

<h1>Race the Type</h1>
<h2>1. Select a Difficulty:</h2>
<button on:click={easy}>Easy</button> <button on:click={medium}>Medium</button> <button on:click={hard}>Hard</button>

<h2>2. Type the following:</h2>
<p>{sentence}</p>
<input class="input" type='text' bind:value={typed} on:input={send} placeholder="Begin Typing Here"/>

<p>Your Progress: {Math.min(100, Math.floor((typed.length / sentence.length) * 100))}%</p>
<div class="progress" style='width: {100, (typed.length / sentence.length) * 100}%'></div>

<p class="opponent">{theyTyped}</p>
<p>Their Progress: {theirProgress}%</p>
<div class="other" style="width: {theirProgress}%"></div>