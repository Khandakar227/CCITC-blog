<script>
  import { CreatedAt, getUser } from "../../scripts/firebase";
  import { fade } from 'svelte/transition';
  import { darkmode } from "../../scripts/store";
  import LikeIcon from "../icons/likeIcon.svelte";
  import CommentIcon from "../icons/commentIcon.svelte";
  import Loading from "../icons/loadingIcon.svelte";
  import { createEventDispatcher } from 'svelte';

export let Posts;
export let loggedInUser;
export let lastPost;

const dispatch = createEventDispatcher();

function onSeeMore() {
    dispatch('seeMore');
}

$: getUserFromId = async (user_id) => {
    const userData = await getUser(user_id);
    return { ...userData };
  };

</script>

<section class="post_grid">
    {#if Posts}
      <container class="posts grid-center-all">
        {#each Posts as post,i }
          <article class="post" in:fade="{{delay: 250*i, duration: 300}}">
            {#if post.URL}
              <img class="__img" src={post.URL} alt={post.title} />
            {/if}
            <div class="creator_post_title">
              {#await getUserFromId(post?.user_id)}
                <small>Loading...</small>
                <h2>{post.title}</h2>
              {:then user}
                <img
                  class="avatar"
                  src={user?.image}
                  on:error={(e) => (e.target.src = "https://octodex.github.com/images/megacat.jpg")}
                  alt={user.name}
                />
                <div>
                  <h2>{post.title}</h2>
                  <p class="username">{user?.name}</p>
                  <small>{CreatedAt(post?.created_at?.seconds)}</small>
                </div>
              {/await}
            </div>
            {#if post?.description}
              <div class="description">
                {post?.description.slice(0, 120) + "..."}
              </div>
            {/if}
            <p class="buttons">
              <span class="interactions">
                <button class="transparent--button events">
                  <LikeIcon
                    className="icon"
                    style={`fill:${
                      post?.vote.includes(loggedInUser?.user?.uid)
                        ? "red"
                        : $darkmode?"white":"initial"
                    }`}
                  />
                </button>
                {post?.vote?.length}
              </span>
              <span class="interactions">
                <button class="transparent--button events"
                  ><CommentIcon className="icon" style={`fill:${$darkmode?"white":"initial"}`} /></button
                >
                {post?.comment_no}
              </span>
            </p>
            <a class="no_link read" href={`/post/${post.id}`}>Read</a>
          </article>
        {/each}
      </container>
      <div class="py-2" style="display: flex;justify-content: center;">
        {#if lastPost}
          <button on:click={onSeeMore} class="see_more transparent--button"
            >SEE MORE</button
          >
        {/if}
      </div>
    {:else}
      <div style="position: fixed;width: 100%;justify-content: center;display: grid;left: 0;">
        <Loading />
      </div>
    {/if}
  </section>

  <style>
    .read {
      text-align: center;
      width: 100%;
      display: block;
      padding: 5px 0;
      border-radius: 5px;
      background: #9696964f;
      font-weight: bolder;
      box-shadow: 2px 2px 2px 2px #8800005e;
    }
    .read:focus {
      box-shadow: inset 2px 2px 2px 2px #8800005e;
    }
    span.interactions {
      display: flex;
      font-weight: bold;
      align-items: center;
      border: 1px solid #ff00006b;
      background: #ff00001c;
      padding: 0 4px;
    }
    .events {
      padding: 2px;
      margin: 3px;
      border-radius: 2pc;
    }
    .events:hover {
      background-color: rgba(0, 0, 0, 0.116);
    }
    .post .__img {
      max-width: 350px;
      width: 100%;
      margin: 0 auto;
    }
    .post {
      display: grid;
      justify-content: center;
      align-items: center;
      padding: 5px;
      background-color: hsl(0deg 0% 61% / 22%);
      box-shadow: 0px 1px 2px 0px #0000005e;
    }
    .posts {
      position: relative;
      gap: 10px;
      grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
    }
    .description {
      font-size: clamp(1rem, 5vw, 1.2rem);
      word-break: break-all;
      padding: 10px 0;
    }
    img.avatar {
      max-width: 55px;
      border-radius: 2pc;
    }
    .creator_post_title {
      display: grid;
      justify-content: center;
      align-items: center;
      grid-template-columns: auto auto;
      gap: 1vw;
      padding: 5px;
    }
    .username {
      margin: 5px 0px;
    }
    .buttons {
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 1vw;
      padding-bottom: 5px;
    }
    .see_more {
      background: #989caf5b;
      padding: 8px 5px;
      border-radius: 5px;
      border: 1px solid lightsteelblue;
      width: 90%;
    }
    .see_more:hover {
      filter: drop-shadow(0px 0px 1px grey) brightness(0.7);
    }
    .post_grid {
      min-height: calc(100vh - 385px);margin: 0 auto; max-width: 1200px;
    }
  </style>
  