<script>
  import { createEventDispatcher } from "svelte";
  import { userStore } from "$lib/store";
  import { fetchPut } from "$lib/fetch";
  import MessageForm from "$lib/components/message-form.svelte";
  import ErrorMessage from "$lib/components/error-message.svelte";

  export let messageData;

  let message = messageData.message;
  let imagePreview = messageData.imageUrl;
  let err = "";
  let success = "";
  let user = null;

  const dispatch = createEventDispatcher();

  userStore.subscribe(async userData => (user = userData));

  async function onSubmit({ detail }) {
    err = "";
    if (!user) {
      err = "Echec de la connexion";
      return;
    }
    if (user.userId !== messageData.userId && !user.admin) {
      err = "Utilisateur non autorisé";
      return;
    }
    const updatedMessage = messageData;
    updatedMessage.message = message;
    if (detail?.length) updatedMessage.imageUrl = detail;
    const { error } = await fetchPut(`/api/message/${messageData._id}`, {
      message: updatedMessage,
      user
    });
    if (error) {
      err = error;
      return;
    }
    success = "Votre message a été modifié. Retournez à l'Accueil pour afficher votre nouveau message.";
    dispatch("edit");
  }
</script>

<!-- verification que l'utilisateur connecté est l'auteur du message -->
{#if user && user.userId.length && (user.userId === messageData.userId || user.admin)}
  <MessageForm
    bind:message={message}
    bind:imagePreview={imagePreview}
    on:submit={onSubmit}
    error={err}
    {success}
  />
{:else}
  <ErrorMessage error="Utilisateur non autorisé" />
{/if}