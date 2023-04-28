<script lang="ts">
  export let title = 'Digital Postcards'
  export let description = 'Explore cities around the world'
  export let image = 'https://digital-postcards.samalander.repl.co/card.png';
</script>

<svelte:head>
	<title>{title}</title>
  <meta name="description" content={description}/>
    
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:site" content="www.samalander.dev" />
  <meta name="twitter:title" content={title} />
  <meta name="twitter:description" content={description} />
  <meta name="twitter:image" content={image} />

  <meta property="og:title" content={title} />
  <meta property="og:description" content={description} />
  <meta property="og:image" content={image} />

  <link rel="icon" href="https://digital-postcards.samalander.repl.co/favicon.ico" type="image/x-icon"/>
</svelte:head>