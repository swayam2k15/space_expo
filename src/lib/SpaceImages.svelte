<script>
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';

  const apiKey = import.meta.env.API_KEY;

  const apiUrl = `https://api.nasa.gov/planetary/apod?api_key=apiKey&count=10`;
  let resultsArray = [];
  let favorites = writable({});

  // Fetch pictures from NASA API
  async function getNasaPictures() {
    try {
      const response = await fetch(apiUrl);
      resultsArray = await response.json();
    } catch (error) {
      console.error('Error fetching NASA pictures:', error);
    }
  }

  // Save an item to favorites
  function saveFavorite(itemUrl) {
    favorites.update(currentFavorites => {
      if (!currentFavorites[itemUrl]) {
        const itemToAdd = resultsArray.find(item => item.url === itemUrl);
        if (itemToAdd) {
          const newFavorites = { ...currentFavorites, [itemUrl]: itemToAdd };
          localStorage.setItem('nasaFavorites', JSON.stringify(newFavorites));
          return newFavorites;
        }
      }
      return currentFavorites;
    });
  }

  // Remove an item from favorites
  function removeFavorite(itemUrl) {
    favorites.update(currentFavorites => {
      if (currentFavorites[itemUrl]) {
        const { [itemUrl]: _, ...newFavorites } = currentFavorites;
        localStorage.setItem('nasaFavorites', JSON.stringify(newFavorites));
        return newFavorites;
      }
      return currentFavorites;
    });
  }

  // On component mount
  onMount(() => {
    getNasaPictures();
    const localFavorites = JSON.parse(localStorage.getItem('nasaFavorites'));
    if (localFavorites) {
      favorites.set(localFavorites);
    }
  });
</script>

<style>
  .container {
    display: flex;
    flex-direction: column;
    gap: 20px;
  }
  .title, .subtitle {
    color: #ff3e00;
  }
  .clickable {
  cursor: pointer; /* Ensures it shows a pointer cursor which indicates clickability */
  pointer-events: auto; /* Overrides any inherited pointer-events property */
}
  .card {
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 20px;
    background: #f9f9f9;
  }
  img {
    width: 100%;
    height: auto;
    border-radius: 4px;
  }
</style>

<div class="container">
  {#each resultsArray as result (result.url)}
    <div class="card">
      <a href="{result.hdurl}" title="View Full Image" target="_blank">
        <img src="{result.url}" alt="NASA Picture of the Day" class="card-img-top">
      </a>
      <div>
        <h5>{result.title}</h5>
        <button class="clickable" on:click={() => saveFavorite(result.url)}>Add To Favorites</button>
        <p>{result.explanation}</p>
        <small>{result.date}</small>
      </div>
    </div>
  {/each}

  <h2 class="subtitle">Favorites:</h2>
  {#each Object.values($favorites) as favorite (favorite.url)}
    <div class="card">
      <a href="{favorite.hdurl}" title="View Full Image" target="_blank">
        <img src="{favorite.url}" alt="Favorite NASA Picture">
      </a>
      <div>
        <h5 class="subtitle">{favorite.title}</h5>
        <button class="clickable" on:click={() => removeFavorite(favorite.url)}>Remove Favorite</button>
        <p>{favorite.explanation}</p>
        <small>{favorite.date}</small>
      </div>
    </div>
  {/each}
</div>
