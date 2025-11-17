<script>
  export let name;
  export let description;
</script>

<div class="card">
  <h3>{name}</h3>
  <p>{description}</p>
</div>

<style>
  .card {
    background-color: var(--card-background);
    padding: 1.5rem;
    border-radius: 8px;
    transition: transform 0.3s, box-shadow 0.3s;
    border: 1px solid #333;
  }

  .card:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 20px rgba(0,0,0,0.2);
  }

  h3 {
    margin-top: 0;
    color: var(--primary-color);
  }
</style>
