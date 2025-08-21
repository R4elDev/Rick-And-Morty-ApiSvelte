<script lang="ts">
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';
  
    interface Character {
      id: number;
      name: string;
      image: string;
      status: string;
      species: string;
      origin: { name: string };
    }
  
    let characters: Character[] = [];
    let filteredCharacters: Character[] = [];
    let search = '';
    let page = 1;
    let totalPages = 1;
    const pageSize = 20;
  
    async function fetchCharacters(pageNumber: number) {
      const res = await fetch(`https://rickandmortyapi.com/api/character?page=${pageNumber}`);
      const data = await res.json();
      characters = data.results;
      filteredCharacters = characters;
      totalPages = data.info.pages;
    }
  
    onMount(() => {
      fetchCharacters(page);
    });
  
    function handleSearch() {
      if (search.trim() === '') {
        filteredCharacters = characters;
        return;
      }
      filteredCharacters = characters.filter(c =>
        c.name.toLowerCase().includes(search.toLowerCase())
      );
    }
  
    function nextPage() {
      if (page < totalPages) {
        page++;
        fetchCharacters(page);
      }
    }
  
    function prevPage() {
      if (page > 1) {
        page--;
        fetchCharacters(page);
      }
    }
  
    function openCharacter(id: number) {
      goto(`/character/${id}`);
    }
  </script>
  
  <style>
    .container {
      max-width: 960px;
      margin: 2rem auto;
      padding: 1rem;
      background: #0f111b;
      color: #00ff91;
      font-family: 'Arial', sans-serif;
    }
    input {
      width: 100%;
      padding: 0.5rem;
      border-radius: 8px;
      border: 1px solid #00ff91;
      margin-bottom: 1rem;
      background: #1f1f2e;
      color: #00ff91;
      font-weight: bold;
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fill,minmax(150px,1fr));
      gap: 1rem;
    }
    .card {
      background: #1f1f2e;
      border-radius: 12px;
      padding: 1rem;
      cursor: pointer;
      text-align: center;
      transition: transform 0.15s ease-in-out;
      border: 2px solid transparent;
    }
    .card:hover {
      transform: scale(1.05);
      border-color: #00ff91;
    }
    img {
      border-radius: 50%;
      width: 120px;
      height: 120px;
      object-fit: cover;
      margin-bottom: 0.5rem;
    }
    button {
      background: #00ff91;
      border: none;
      border-radius: 8px;
      color: #0f111b;
      font-weight: bold;
      padding: 0.5rem 1rem;
      margin: 0 0.25rem;
      cursor: pointer;
    }
    button:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  </style>
  
  <div class="container">
    <h1>Rick and Morty Characters</h1>
  
    <input
      type="text"
      placeholder="Buscar por nome..."
      bind:value={search}
      on:input={handleSearch}
    />
  
    <div class="grid">
      {#each filteredCharacters as character}
        <div class="card" on:click={() => openCharacter(character.id)}>
          <img src={character.image} alt={character.name} />
          <p>{character.name}</p>
        </div>
      {/each}
    </div>
  
    <div style="margin-top:1rem; text-align:center;">
      <button on:click={prevPage} disabled={page === 1}>Anterior</button>
      <span>Página {page} de {totalPages}</span>
      <button on:click={nextPage} disabled={page === totalPages}>Próxima</button>
    </div>
  </div>
  