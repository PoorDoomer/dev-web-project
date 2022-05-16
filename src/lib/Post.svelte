<script>
    import supabase from '$lib/supabase'
    import {readable, get} from 'svelte/store'
    export let id
    export let title
    export let content
    export let comments

    async function addComment({target}) {
      const formData = new FormData(target)
      const {data, error} = await supabase
        .from('comments')
        .insert({
          content: formData.get('comment'),
          post: id
        })
    }
      comments = readable(null, (set) => {
      supabase
        .from('comments')
        .select('*')
        .limit(2)
        .filter('post', 'eq', id)
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
  </script>
  
  <article style="padding: 2rem;">
    <div id = "progressbar" ></div>
<div id = "scrollPath"></div>
<div id = "progressbar1" ></div>
<div id = "scrollPath1"></div>
    <h3 ><a href="/{id}">{title}</a></h3>

    <p>{content}</p>
    <h4>First comments</h4>
    {#if $comments}
      {#each  $comments as {content, id}}
        <h5>Id user :{id}</h5>
        <p>{content}</p>
      {:else}
        No comments yet!
      {/each}
    {:else}
      Loading..
    {/if}
  
  
    <form on:submit|preventDefault={addComment}>
      <input placeholder=comment name=comment>
      <button>Add Comment</button>
    </form>
</article>

<style>
article{
  padding: 50px;
background: rgb(255, 255, 255)
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