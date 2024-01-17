<script>
    import { onMount, onDestroy } from 'svelte';
  
    let data = null;
  
    const fetchData = async () => {
      try {
        const response = await fetch('https://nine-lives.vercel.app/api/team');
        const result = await response.json();
        data = result;
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    };
  
    onMount(() => {
      fetchData();
      const interval = setInterval(fetchData, 60000);
      onDestroy(() => clearInterval(interval));
    });
  </script>
  
  <main>
    <h1>Hello Svelte!</h1>
  
    <h2>API Data:</h2>
    
    {#if data}
      <pre>{JSON.stringify(data, null, 2)}</pre>
    {:else}
      <p>Loading data...</p>
    {/if}
  </main>  