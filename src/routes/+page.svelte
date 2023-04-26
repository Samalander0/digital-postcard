<script lang="ts">
  // Library Imports
  import '$lib/styles/main.scss';
  import { onMount } from 'svelte';
  import { page } from '$app/stores';

  // Other Imports
  import signature from '$lib/images/signature-white.png';
  import share from '$lib/images/share.svg';
  import info from '$lib/images/info.svg';
  import { places } from '$lib/places.js';
  import { layouts } from '$lib/layouts.js';

  // App Code
  function getRandomInt(max) {
    return Math.floor(Math.random() * max);
  }

  let loading = true;

  const placeNum = getRandomInt(places.length);
  let place = places[placeNum];
  //place = places[7];
  const layoutNum = getRandomInt(layouts.length);
  let imgLayout = layouts[layoutNum];
  //imgLayout = layouts[3];
  const pretitles = ["Visit", "Explore", "Enjoy", "Discover", "Experience"];
  let pretitleText = pretitles[getRandomInt(5)];
  let buttonText = pretitleText;

  let showInfo = false;
  const toggleInfo = () => {
    if (showInfo) {
      showInfo = false;
      smoother.paused(false);
    } else {
      showInfo = true;
      smoother.paused(true)
    }
  }
  
  // GSAP
  import { gsap } from "gsap/dist/gsap";
  import { ScrollTrigger } from "gsap/dist/ScrollTrigger";
  import { ScrollSmoother } from "$lib/gsap/ScrollSmoother";
  gsap.registerPlugin(ScrollTrigger, ScrollSmoother);
  
  let smoother;

  onMount(() => {
    if ($page.url.searchParams.get('city')) {
      place = places[$page.url.searchParams.get('city')]
    }
    if ($page.url.searchParams.get('layout')) {
      imgLayout = layouts[$page.url.searchParams.get('layout')]
    }
    
    smoother = ScrollSmoother.create({
    	wrapper: "#smooth-wrapper",
    	content: "#smooth-content",
    	smooth: 2,
      smoothTouch: 0.1,
    	effects: true,
    });
    smoother.effects("img", { speed: "auto" });

    gsap.to("#visit", {
      opacity: 0.2,
      scrollTrigger: {
        scrub: true,
        start: "top top",
        end: "+=500",
      }
    })
    gsap.to("#scroll", {
      opacity: 0,
      scrollTrigger: {
        scrub: true,
        start: "top top",
        end: "+=600",
      }
    })

    loading = false;
  })
</script>

<svelte:head>
  {#if loading}
    <title>Digital Postcards | Loading...</title>
  {:else}
	 <title>Digital Postcards | {place.name}</title>
  {/if}
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<!--
  Made By Sam Cheng
-->

<nav id="nav" class={loading ? "" : "shown"}>
  <button on:click={() => {navigator.share({
    title: "Digital Postcard",
    text: `${ pretitleText } ${place.name}!`,
    url: `https:\/\/visit.samalander.repl.co/?city=${placeNum}&layout=${layoutNum}`,
  })}}>
    <img src={share} alt="share"/>
  </button>
  <button on:click={toggleInfo}>
    <img src={info} alt="info"/>
  </button>
</nav>

<div id="loader" class={loading ? "" : "loaded"}>
  <h2>Loading...</h2>
</div>

<div id="info" class={showInfo ? "" : "hidden"} aria-hidden={!showInfo} style={`--color: ${place.background};`}>
  <div id="content">
    <h2>What's this?</h2>
    <p>
      First of all, thanks for visiting! Digital Postcards is a passion project created by <a href="//www.samalander.dev" target="_blank">Sam Cheng</a> with a goal of showcasing interesting cities from around the world. It uses GSAP's <a href="https://greensock.com/scrolltrigger/" target="_blank">Scroll Trigger</a> & <a href="https://greensock.com/scrollsmoother/" target="_blank">Scroll Smoother</a> for the scroll effects, and images are from <a href="https://unsplash.com/" target="_blank">Unsplash</a>.<br/> There are currently {places.length} cities that you can visit (That's {places.length * 7} images), and each are curated manually. You also have a chance of getting one of {layouts.length} image layouts, each highlighting different images. In total (including the text above the city name), there are {places.length * layouts.length * pretitles.length} different combinations.
    </p>
    <small>Click outside to close</small>
  </div>
  <button id="click-to-close" on:click={toggleInfo}></button>
</div>

<div id="app" style={`background: ${place.background}`} class={loading ? "" : "loaded"}>
  <h2 class="text" id="visit">{pretitleText}</h2>
  <h1 class="text">{place.name}</h1>
  <h1 class="text" aria-hidden="true" style={`color: ${place.color}`}>{place.name}</h1>
  <h1 class="text" aria-hidden="true">{place.name}</h1>
  <h3 class="text" id="scroll">{place.emoji}👇</h3>
  
  <div id="smooth-wrapper">
    <div id="smooth-content">
      <main id="images">
        {#each place.images as image, i}
          <div style={`width: ${imgLayout[i].width}vw; margin-left: ${imgLayout[i].position}%; margin-top: ${imgLayout[i].top}vw; overflow: hidden; height: ${imgLayout[i].height}vw;`} data-speed={imgLayout[i].speed}>
            <img src={`https://images.unsplash.com/photo-${image}?w=${imgLayout[i].width * 16}`} />
          </div>
        {/each}
      </main>
      <div id="new">
        <button on:click={() => {window.location.reload()}} style={`--background: ${place.color}; --hover-color: ${place.background};`} title={`Currently ${places.length} cities (That's ${places.length * 7} images!) & ${layouts.length} image layouts`} on:mouseover={() => {buttonText = pretitles[getRandomInt(5)]}}>{buttonText} a new city?</button>
        <p id="mobile">Psst... This experience was built for desktop, so try it on a larger screen for best results!</p>
      </div>
      <footer>
        <div id="city-info">
          <p>
            {place.longName} <br/>
            <a href={place.link} target="_blank">{place.cordinates}</a>
          </p>
        </div>
        <div class="footer-made-by">
          <p>made by</p>
          <a href="https://www.samalander.dev" target="_blank" rel="noreferrer">
            <img src={signature} alt="Sam Cheng Signature"/>
          </a>
        </div>
      </footer>
    </div>
  </div>
</div>