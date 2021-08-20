<script>
  import { getPost, getUser, CreatedAt } from "../scripts/firebase";
  import { darkmode } from "../scripts/store";
  import { onMount } from "svelte";
  import CommentBox from "./components/CommentBox.svelte";
  import LoadingIcon from "./icons/loadingIcon.svelte";
  import LikeIcon from "./icons/likeIcon.svelte";
  import CommentIcon from "./icons/commentIcon.svelte";
  import SignedInAvatar from "./components/SignedInAvatar.svelte";

  export let loggedInUser;
  export let params;

  $: getUserFromId = async (user_id) => {
    const userData = await getUser(user_id);
    return { ...userData };
  };

  onMount(async () => {
    const { title } = await getPost(params.id);
    document.title = title;
  });
</script>

<div
  class="pb-1"
  style="max-width:1500px;display: flex; border-bottom: 1px solid #bbbbbb52;
   justify-content: flex-end; margin: 0 10px"
>
  <SignedInAvatar {loggedInUser} />
</div>
<section class="post_wrapper grid-center">
  {#await getPost(params?.id)}
    <LoadingIcon />
  {:then Post}
    {#if Post}
      {#if Post?.error}
        <error>{Post?.error}</error>
      {/if}
      <div style="margin: 0 auto; max-width: 1100px;">
        <h1 class="title text-center pb-1 border_bottom">
          {Post?.title || ""}
        </h1>
        {#if Post?.URL}
          <figure class="text-center py-2">
            <img
              class="post_img"
              src={Post?.URL}
              alt={Post?.title}
              on:error={(e) => (e.target.src = "/assets/img/404.webp")}
            />
          </figure>
        {/if}
        <div class="author pb-1">
          {#await getUserFromId(Post?.user_id)}
            <LoadingIcon />
          {:then user}
            <img
              class="avatar"
              src={user?.image}
              on:error={(e) => (e.target.src = "/assets/img/megacat.webp")}
              alt={user.name}
            />
            <div>
              <h3 class="username py-1">{user?.name}</h3>
              <small>{CreatedAt(Post?.created_at?.seconds)}</small>
            </div>
          {/await}
        </div>
        <pre
          class="description py-1 pb-2">
                {Post?.description}
            </pre>
        {#if Post?.codes}
          <div class="code_wrapper">
            <pre
              class={$darkmode
                ? "code code_dark scrollbar_custom"
                : "scrollbar_custom code code_light"}>
                    {Post.codes}
                </pre>
          </div>
        {/if}
        <div class="my-2 react_section">
          <div>
            <button class="transparent--button"
              ><LikeIcon
                className="icon"
                style={`fill:${
                  Post?.vote.includes(loggedInUser?.user?.uid)
                    ? "red"
                    : $darkmode
                    ? "white"
                    : "initial"
                }`}
              /></button
            >
            <span>{Post?.vote.length}</span>
          </div>
          <div>
            <button class="transparent--button"
              ><CommentIcon
                className="icon"
                style={`fill: ${$darkmode ? "white" : "initial"}`}
              /></button
            >
            <span>{Post?.comment_no}</span>
          </div>
        </div>
        <CommentBox postId={Post?.id} />
      </div>
    {:else}
      <h3 class="text-center">No data</h3>
    {/if}
  {/await}
</section>

<style>
  error {
    color: red;
    background-color: white;
    padding: 5px;
    border-radius: 10px;
  }
  .border_bottom {
    border: 1px solid #e5e5e5;
    border-left-width: 0px;
    border-right-width: 0px;
    border-top-width: 0px;
  }
  .post_wrapper {
    padding-top: clamp(2.2rem, calc(12px + 3.85vw), 30px);
    margin: 0 5px;
  }
  figure {
    border: 1px solid #e5e5e5;
    background-color: antiquewhite;
  }
  h1.title {
    font-size: clamp(2.2rem, calc(12px + 3.85vw), 3.025rem);
    padding: 1rem 6px;
    line-height: 1.1;
  }
  .post_img {
    width: 100%;
    max-width: 800px;
    margin: 0 auto;
  }
  .author {
    display: flex;
    justify-content: flex-start;
    flex: auto;
    align-items: normal;
    gap: 2vw;
  }
  .author img {
    max-width: 18%;
    border-radius: 3pc;
    margin: 10px 0;
  }
  pre.description {
    word-break: break-word;
    font-family: inherit;
    white-space: break-spaces;
    font-size: clamp(1rem, 5vw, 1.2rem);
  }
  .code {
    word-break: break-word;
    white-space: break-spaces;
    overflow: auto;
    max-height: 600px;
    padding: 5px;
    width: 100%;
  }
  .code_wrapper {
    margin: 0 auto;
    overflow: hidden;
    max-height: 600px;
    width: 98%;
  }
  .code_light {
    background: #f1f3f4;
    color: #37474f;
  }
  .code_dark {
    background-color: #283142;
    color: #eceff1;
  }
  .react_section {
    width: 100%;
    gap: 2vw;
    display: flex;
    flex: auto;
    justify-content: space-evenly;
    align-items: center;
    padding: 5px;
    background: #90919240;
    border-radius: 1pc;
    border: 1px solid #6161615c;
  }
  .react_section div {
    padding: 5px 8px;
    background: #a9a9a95c;
    border-radius: 10px;
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 1vw;
    flex: auto;
  }
</style>
