<script lang="ts">
    import { onMount } from 'svelte';
    import { page } from '$app/stores';
    import { get } from 'svelte/store';
    import { goto } from '$app/navigation';
  
    interface Character {
      id: number;
      name: string;
      status: string;
      species: string;
      origin: { name: string };
      image: string;
    }
  
    let character: Character | null = null;
    let loading = true;
    let error: string | null = null;
  
    async function fetchCharacter(id: string) {
      loading = true;
      error = null;
      try {
        const res = await fetch(`https://rickandmortyapi.com/api/character/${id}`);
        if (!res.ok) {
          throw new Error('Erro ao carregar personagem');
        }
        character = await res.json();
      } catch (e) {
        error = e.message;
      } finally {
        loading = false;
      }
    }
  
    // Pega o id da URL via store `page`
    const id = get(page).params.id;
  
    onMount(() => {
      fetchCharacter(id);
    });
  
    function goBack() {
      goto('/'); // volta para a lista
    }
  </script>
  
  <div class="container">
    {#if loading}
      <p>Carregando personagem...</p>
    {:else if error}
      <p style="color: #ff5555;">{error}</p>
      <button on:click={goBack}>Voltar para lista</button>
    {:else if character}
      <h2>{character.name}</h2>
      <img src={character.image} alt={character.name} />
      <p><strong>Status:</strong> {character.status}</p>
      <p><strong>Espécie:</strong> {character.species}</p>
      <p><strong>Origem:</strong> {character.origin.name}</p>
  
      <button on:click={goBack} aria-label="Voltar para a lista">← Voltar</button>
    {/if}
  </div>
  
  <style>
    .container {
      max-width: 480px;
      width: 90vw;
      margin: 2rem auto;
      padding: 1.5rem;
      background: #1f1f2e;
      border-radius: 12px;
      text-align: center;
      color: #00ff91;
      border: 2px solid #00ff91;
      font-family: 'Arial', sans-serif;
  
      display: flex;
      flex-direction: column;
      align-items: center;
      box-sizing: border-box;
    }
  
    img {
      border-radius: 50%;
      width: 80vw;
      max-width: 200px;
      height: 80vw;
      max-height: 200px;
      border: 4px solid #00ff91;
      margin-bottom: 1rem;
      object-fit: cover;
    }
  
    button {
      margin-top: 2rem;
      background: #00ff91;
      color: #1f1f2e;
      border: none;
      padding: 0.7rem 1.5rem;
      font-weight: bold;
      border-radius: 10px;
      cursor: pointer;
      font-size: 1.1rem;
      transition: background-color 0.3s ease;
    }
  
    button:hover {
      background: #00ffa2;
    }
  </style>
  