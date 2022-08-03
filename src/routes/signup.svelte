<script>
  import ErrorMessage from "$lib/components/error-message.svelte";
  import {fetchPost} from "$lib/fetch";

  let email;
  let password;
  let err;
  let success = false;

  async function onSignup() {
    const { error } = await fetchPost("/api/user/signup", {
        email,
        password
      });
    if (error) {
      err = error;
      //console.log(error);
      return;
    }
    success = true;
  }
</script>
  
<svelte:head>
  <title>Inscription - Groupomania</title>    
</svelte:head>

<ErrorMessage error={err}/>
{#if success}
  <div class="alert alert-success">
    Inscription réussie.
    <a href="/">Merci de vous connecter</a>
  </div>
{/if}



<form on:submit|preventDefault={onSignup}>
  <div>INSCRIPTION</div> <br/>
  <div class="mb-3">
    <label for="email" class="form-label">Email</label>
    <input bind:value={email} type="email" class="form-control" id="email" aria-describedby="emailHelp" />
    <div id="emailHelp" class="form-text">Les informations renseignées ne seront communiquées à aucun tiers.</div>
  </div>
  <div class="mb-3">
    <label for="password" class="form-label">Mot de passe</label>
    <input
      bind:value={password}
      type="password"
      class="form-control"
      id="password"
      minlength="8"
      />
  </div>
  <button type="submit" class="btn btn-primary">Inscription</button>
</form>