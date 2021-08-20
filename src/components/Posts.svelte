<script>
  import { getAllBlogPosts } from "../../scripts/firebase";
  import { onDestroy, onMount } from "svelte";
  import PostsGrid from "./PostsGrid.svelte";

  export let loggedInUser;

  let Posts;
  let lastPost;

  onMount(async () => {
    const data = await getAllBlogPosts(false, true);
    Posts = data.posts;
    lastPost = data.lastPost;
  });

  onDestroy(() => {
    Posts = [];
    lastPost = null;
  })

  $: onSeeMore = () => {
    getAllBlogPosts({ next: true, trim: true }).then((data) => {
      Posts.push(...data.posts);
      Posts = Posts;
      lastPost = data.lastPost;
    });
  };
</script>

<PostsGrid {loggedInUser} {lastPost} {Posts} on:seeMore={()=> onSeeMore()}/>

