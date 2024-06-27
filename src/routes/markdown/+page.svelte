<script>
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';

  let content = '';
  let query = '';
  let occurrences = null;

  // Carregar o conteúdo do arquivo Markdown
  onMount(async () => {
    const res = await fetch('/src/content/markdown.md');
    content = await res.text();

    const urlParams = new URLSearchParams(window.location.search);
    query = urlParams.get('q') || '';
    if (query) {
      occurrences = (content.match(new RegExp(query, 'g')) || []).length;
    }
  });

  // Atualizar contagem de ocorrências quando query mudar
  $: if (query) {
    occurrences = (content.match(new RegExp(query, 'g')) || []).length;
  }

  function updateURL() {
    const url = new URL(window.location);
    if (query) {
      url.searchParams.set('q', query);
    } else {
      url.searchParams.delete('q');
    }
    window.history.pushState({}, '', url);
  }

  function goToMainPage() {
    goto('/');
  }
</script>

<div class="container">
  <button class="back-button" on:click={goToMainPage}>Voltar à Página Principal</button>

  <h1>Markdown</h1> <!-- Título da página Markdown -->

  <div class="search-container">
    <input type="text" bind:value={query} placeholder="Buscar..." on:input={updateURL} class="search-input" />
    {#if query}
      <p class="occurrences">{occurrences} ocorrências</p>
    {/if}
  </div>

  <pre class="markdown-content">{content}</pre>
</div>

<style>
  .container {
    max-width: 800px;
    margin: 20px auto;
    padding: 20px;
    background-color: #fff;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    border-radius: 5px;
  }

  .back-button {
    background-color: #007bff;
    color: #fff;
    border: none;
    padding: 10px 20px;
    font-size: 16px;
    border-radius: 5px;
    cursor: pointer;
    margin-bottom: 10px;
  }

  .back-button:hover {
    background-color: #0056b3;
  }

  .search-container {
    margin-bottom: 10px;
    display: flex;
    align-items: center;
  }

  .search-input {
    flex: 1;
    padding: 10px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }

  .occurrences {
    font-size: 14px;
    margin-left: 10px;
    color: #6c757d;
  }

  .markdown-content {
    font-family: 'Courier New', Courier, monospace;
    white-space: pre-wrap;
    font-size: 16px;
    line-height: 1.5;
    overflow-x: auto;
    background-color: #f8f9fa;
    padding: 15px;
    border-radius: 5px;
    border: 1px solid #ccc;
  }
</style>
