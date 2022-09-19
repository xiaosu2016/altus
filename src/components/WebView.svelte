<script lang="ts">
  import { afterUpdate, onMount } from "svelte";
  import type { TabType } from "../types";
  import { themes } from "../store";
  export let partition;
  export let tab: TabType;

  const userAgent = window.navigator.userAgent.replace(
    /(Altus|Electron)([^\s]+\s)/g,
    ""
  );

  // const userAgent = window.navigator.userAgent

  let webviewElement;
  let hasStoppedLoading = false;

  const sendWebviewConfig = () => {
      alert(tab.id)
      console.log(tab.id);
      
    webviewElement.send("set-id", tab.id);
    let currentTheme = $themes.find((theme) => theme.id === tab.config.theme);
    if (currentTheme) {
      webviewElement.send("set-theme", {
        name: tab.config.theme,
        ...(currentTheme.css
          ? {
              css: currentTheme.css,
            }
          : {}),
      });
    } else {
      webviewElement.send("set-theme", {
        name: tab.config.theme,
        css: "",
      });
    }
    webviewElement.send("toggle-notifications", tab.config.notifications);
    webviewElement.send("set-utility-bar", tab.config.utilityBar);
    webviewElement.setAudioMuted(!tab.config.sound);
  };
  let isInMain = ()=>{
    // alert(77777777)
    return new Promise((resolve,reject)=>{
      webviewElement.addEventListener('dom-ready',() => {
        webviewElement.executeJavaScript(`
`)

      })
      
      
    })
  }
  onMount(() => {
    isInMain().then(()=>{
          alert(1111)
        })
  })
  afterUpdate(() => {
    if (!hasStoppedLoading) {
      webviewElement.addEventListener("did-stop-loading", () => {
        hasStoppedLoading = true;

        sendWebviewConfig();
        // webviewElement.openDevTools()
       
      });
    } else {
      sendWebviewConfig();
    }
  });
  onload = function(){

    // var webview = document.getElementById(`webview-${tab.id}`)
    // webview.addEventListener('did-finish-load ', function() {
    //   webview.openDevTools()
    //   alert()
    // // webview.executeJavaScript(``)

    // })
  }

</script>
<!-- 
  useragent={userAgent}

 -->
<webview
  id={`webview-${tab.id}`}
  src="https://web.whatsapp.com"
  preload="../src/preload.js"
  {partition}
  bind:this={webviewElement}
  useragent={userAgent}
  webpreferences={`spellcheck=${tab.config.spellChecker ? 1 : 0}`}
/>
