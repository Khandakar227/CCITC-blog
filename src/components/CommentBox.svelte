<script>
  import viewport from "../../scripts/viewport";
  import { getComments, CreatedAt, writeComment } from "../../scripts/firebase";
  import LoadingIcon from "../icons/loadingIcon.svelte";

  let initiateGetComments = false;
  export let postId;
  export let loggedInUser;

  let mycomment = {
    commenter: loggedInUser?.user?.displayName,
    content: "",
    user_id: loggedInUser?.user?.uid,
  };

  export const postAComment = async() => {
    if(mycomment?.content.trim()) {
      await writeComment(mycomment)
      window.location.reload();
    }
  }
</script>

{#if loggedInUser?.user}
  <div class="comment_box pb-2">
    <textarea
      bind:value={mycomment.content}
      placeholder="Leave a comment"
      use:viewport
      on:enterViewport={() => (initiateGetComments = true)}
    />
    <button class="button" on:click={() => postAComment()}>Post a comment</button>
  </div>
{:else}
  <p
    class="need_sign_up"
    use:viewport
    on:enterViewport={() => (initiateGetComments = true)}
  >
    YOU NEED TO SIGN UP TO COMMENT
  </p>
{/if}

<container class="comment_section">
  {#if initiateGetComments}
    {#await getComments(postId)}
      <LoadingIcon className="icon" />
    {:then comments}
      {#if comments?.comments[0]}
        {#each comments?.comments as comment}
          <div class="p-1 my-1 comment_wrapper">
            <div style="display: flex;align-items: baseline;">
              <h3 class="pb-1">{comment?.commenter}</h3>
              &nbsp;<small style="padding-left: 5px;"
                >{CreatedAt(comment?.created_at.seconds)}</small
              >
            </div>
            <pre>{comment?.content}</pre>
          </div>
        {/each}
      {/if}
    {:catch error}
      <p style="color: red">{error.message}</p>
    {/await}
  {/if}
</container>

<style>
  textarea {
    width: 100%;
    /* min-height: 100px; */
    font-size: 1rem;
    margin: 8px 0;
    display: inline-block;
    border: 1px solid #ccc;
    box-shadow: inset 0 1px 3px #ddd;
    border-radius: 4px;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
    padding-left: 20px;
    padding-right: 20px;
    padding-top: 12px;
    padding-bottom: 12px;
    resize: none;
  }
  .need_sign_up {
    padding: 2rem 10px;
    border: 1px solid #a9a9a952;
    background: #9c9c9c38;
    margin: 0 0 2rem 0;
    border-radius: 10px;
  }
  .button {
    color: white;
  }
  pre {
    word-break: break-word;
    font-family: inherit;
    white-space: break-spaces;
  }
  .comment_wrapper {
    border-radius: 10px;
    background: #00000019;
  }
</style>
