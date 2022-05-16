

<script context='module'>
      export async function load({ fetch, params, url }) {
        const {slug} = params
        
        return{
              props : {slug}
        }
      
      }
   

</script>


<script>

      import supabase from '$lib/supabase'
      import {readable, get} from 'svelte/store'
      import Post from '$lib/Post.svelte'
      export let slug;


      //Creation variable de posts
     export let posts = readable(null, (set) => {
    supabase
      .from('posts')
      .select('*')
      .filter('id', 'eq', slug)
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
  //creation de variable de comments
 export let comments = readable(null, (set) => {
      supabase
        .from('comments')
        .select('*')
        .filter('post', 'eq', slug)
        .then(({error, data}) => set(data))
      const subscription = supabase
        .from('comments:post=eq.' + id)
        .on('*', (payload) => {
          if (payload.eventType === 'INSERT') {
            set([...get(comments), payload.new])
          }
        })
        
        .subscribe()
      return () => supabase.removeSubscription(subscription)
    })

    // fonction pour ajout de comment
    async function addComment({target}) {
      const formData = new FormData(target)
      const {data, error} = await supabase
        .from('comments')
        .insert({
          content: formData.get('comment'),
          post: slug
        })
    }
    



</script>


<main style="display: flex; flex-direction: column; align-items:center;">
      <h1>Post numero {slug}</h1>
      {#if $posts}
      {#each [...$posts].reverse() as {title, content, id} }
      <Post {title} {content} {id} />
      {/each}
  {:else}
    Chargement ...
  {/if}


</main>
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

</style>