<script>
    import supabase from '$lib/supabase'
    import {readable, get} from 'svelte/store'
    import Post from '$lib/Post.svelte'
    export let posts
    
    async function addPost(e) {
    const formData = new FormData(e.target)
    const {data, error} = await supabase
      .from('posts')
      .insert({
        title: formData.get('title'),
        content: formData.get('content')
        //nom: formData.get('nom')

      })
  }
// creation d'une constante qui va retourner la table post
   posts = readable(null, (set) => {
    supabase
      .from('posts')
      .select('*')
      .then(({error, data}) => set(data))
        // add subscription to supabase logic
        const subscription = supabase
      .from('posts')
      .on('*', (payload) => {
        // payload.evenType
        // payload.new
        // payload.old
        if (payload.eventType == 'INSERT'){
            set([...get(posts), payload.new]) 
        }
        
      })
      .subscribe()




      return () => supabase.removeSubscription(subscription)

  })



  

</script>


<!-- HTML CODEEE -->

<div id = "progressbar" ></div>
<div id = "scrollPath"></div>
<div id = "progressbar1" ></div>
<div id = "scrollPath1"></div>
<main style="display: flex; flex-direction: column; align-items:center;">
        <h1>Forum</h1> 
        <p>EIGSI</p>
        <h2>Creer un Post</h2>
        <form on:submit|preventDefault={addPost}>
            <input placeholder=title name=title> <br>
         <!--   <input placeholder=nom name=nom> <br> -->
            <textarea name=content></textarea> <br>
            <button>Creer Post</button>
          </form>
        <h2>Les posts</h2>
        {#if $posts}
            {#each [...$posts].reverse() as {title, content, id} }
            <Post {title} {content} {id} />
            {/each}
        {:else}
          Chargement ...
        {/if}

</main>


<!-- CSS CODEEEEE-->
<style>
  *
  {
margin: 0;
padding: 0;
font-family: consolas;
}
 main
{
padding: 50px;
background: #000
}
main h1
{
font-size: 2.5em;
color: #fff;
}
main p
{
font-size: 2.5em;
color: rgb(15, 161, 22);
}
main h2
{
  font-size: 1.6em;
color: #fff;
}
::-webkit-scrollbar
{
  width:0;
}
#scrollPath
{
position: fixed;
top: 0;
right: 0;
width: 10px;
height: 100%;
background: rgba(255,255,255,0.05);
}
#progressbar
{
position: fixed;
top: 0;
right: 0;
width: 10px;
height: 100%;
background: linear-gradient(to top, #008aff,#00ffe7);
animation: animate 5s linear infinite;
}
#scrollPath1
{
position: fixed;
top: 0;
left: 0;
width: 10px;
height: 100%;
background: rgba(255,255,255,0.05);
}
#progressbar1
{
position: fixed;
top: 0;
left: 0;
width: 10px;
height: 100%;
background: linear-gradient(to top, #008aff,#00ffe7);
animation: animate 5s linear infinite;
}
@keyframes animate
{
  from
  {
  filter: hue-rotate(0deg);
  }
  to
  {
  filter: hue-rotate(360deg);
  }
}

</style>