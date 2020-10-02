<script lang="ts">
  import { msg } from "@/stores";
  import { onMount } from "svelte";

  interface IFace extends HTMLElement {
    align: string;
    speak: (text: string) => Promise<SpeechSynthesisUtterance>;
  }

  let face: IFace;

  function speak(text: string): Promise<SpeechSynthesisUtterance> {
    const mouth: HTMLElement = this.querySelector(".mouth");
    const utterance: SpeechSynthesisUtterance = new SpeechSynthesisUtterance(
      text
    );
    let mouthAnimation: Animation;

    utterance.onstart = () => {
      mouthAnimation = mouth.animate(
        [
          { transform: "scale(1)" },
          { transform: "scale(1.2)" },
          { transform: "scale(1)" },
        ],
        {
          duration: 100,
          iterations: Infinity,
        }
      );
    };

    speechSynthesis.speak(utterance);

    return new Promise((res) => {
      utterance.onend = () => {
        mouthAnimation.cancel();
        res(utterance);
      };
    });
  }

  onMount(() => {
    face.speak = speak;

    face.addEventListener("click", () => {
      msg.update((msg) =>
        msg === "Hello World!" ? "Goodbye Moon!" : "Hello World!"
      );
      face.speak(`Hello! What is your name?`).then(() => {
        const speechRecognition: webkitSpeechRecognition = new webkitSpeechRecognition();

        speechRecognition.onresult = ({ results }) => {
          const transcript: string = results[0][0].transcript;
          const userName: string = transcript.replace(
            /hello|hi|my|name|is/g,
            ""
          );

          face.speak(`I love you, ${userName}`);
        };

        speechRecognition.start();
      });
    });
  });
</script>

<style lang="scss">
  .face {
    $green: lime;

    @keyframes float {
      50% {
        transform: translateY(-64px);
      }
    }

    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin: auto;
    margin-top: 66%;
    width: 33vw;
    height: 33vw;
    animation: float 8000ms ease infinite;
    cursor: pointer;

    .eye-row {
      display: flex;
      justify-content: space-around;
      width: 100%;

      .eye {
        width: 10vw;
        height: 10vw;
        background: radial-gradient(white, $green);
        border-radius: 8px;
        box-shadow: 0 0 32px rgba($green, 0.5);
      }
    }

    .mouth-row {
      display: flex;
      justify-content: center;

      .mouth {
        width: 30vw;
        height: 10vw;
        background: radial-gradient(white, $green);
        border-radius: 8px;
        box-shadow: 0 -64px 512px rgba($green, 0.5);
      }
    }
  }
</style>

<div class="face" bind:this={face}>
  <div class="eye-row">
    <div id="left" class="eye" />
    <div id="right" class="eye" />
  </div>
  <div class="mouth-row">
    <div class="mouth" />
  </div>
</div>
