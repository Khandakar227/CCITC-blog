<script>
  import Heading from "./components/Heading.svelte";
  import BlogsTitle from "./components/blogsTitle.svelte";
  import Posts from "./components/Posts.svelte";

export let loggedInUser;

</script>


<svelte:head>
  <meta
    name="description"
    content="CCITC's official blog site. Sign in with your Google account. Create a blog of your own. Show it to your friends."
  />
  <meta property="og:title" content="CCITC -Blog" />
  <meta
    property="og:description"
    content="CCITC's official blog site. Sign in with your Google account. Create a blog of your own. Show it to your friends."
  />
  <meta
    property="og:image"
    content="https://ccitclub.github.io/database/img/blog-ccitc.jpg"
  />
</svelte:head>

<Heading {loggedInUser}/>
<section class="blog_container">
    <BlogsTitle />
    <Posts {loggedInUser}/>
</section>

<style>
    .blog_container {
    display: grid;
    justify-content: center;
    align-items: flex-start;
    grid-template-columns: auto auto;
    gap: 1vw;
  }
  @media (max-width: 730px) {
    .blog_container {
      grid-template-columns: auto;
    }
  }
</style>