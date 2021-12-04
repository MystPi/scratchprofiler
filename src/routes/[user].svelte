<script context="module">
	export async function load({ page, fetch }) {
    let user = page.params.user;
    let data = await fetch('https://scratchdb.lefty.one/v3/user/info/' + user);

    if (!data.ok) {
      return {
        props: {
          exists: false,
          id: 0,
          user: 'User doesn\'t exist'
        }
      }
    }

    let forumData = await fetch('https://scratchdb.lefty.one/v3/forum/user/info/' + user);
    let posts = 0;

    if (forumData.ok) {
      forumData = await forumData.json();
      posts = forumData.counts.total.count;
    }

    data = await data.json();

    let follows = data.statistics ? data.statistics.followers : 0;

		return {
      props: {
        user: data.username,
        follows,
        data,
        exists: true,
        posts
      }
    }
  }
</script>

<script>
  import { fade } from 'svelte/transition';
  import { goto } from '$app/navigation';

  let open = false;
  let input;

  export let user;
  export let follows;
  export let posts;
  export let exists;
  export let data;

  function show() {
    open = true;
  }

  function hide() {
    open = false;
  }
</script>
<div class="min-h-screen overflow-auto bg-white sm:bg-gray-100 sm:flex sm:justify-center sm:items-center">
  <div class="bg-white sm:m-8 sm:w-[36rem] sm:rounded-2xl sm:shadow-xl sm:relative">
    <!-- Input dialog -->
    {#if open}
      <div transition:fade={{ duration: 100 }} id="overlay" class="absolute top-0 bottom-0 left-0 right-0 z-10 flex items-center justify-center sm:rounded-2xl">
        <div class="bg-white shadow-xl rounded-2xl">
          <input bind:this={input} placeholder="Username" class="px-4 py-1 m-4 font-medium text-indigo-900 placeholder-gray-400 bg-indigo-100 rounded-full w-44 focus:outline-none focus:ring ring-indigo-300 ring-opacity-90">
          <br>
          <div class="flex mx-4 mb-4 space-x-2">
            <button on:click={() => {hide(); goto(input.value)}} class="flex-1 inline btn">SAVE</button>
            <button on:click={hide} class="flex-1 font-bold text-indigo-700">CANCEL</button>
          </div>
        </div>
      </div>
    {/if}
    <!-- Header -->
    <div class="relative w-full overflow-auto bg-gradient-to-tr from-indigo-700 to-blue-400 sm:rounded-t-2xl">
      <button on:click={show} class="absolute flex items-center justify-center w-8 h-8 ml-auto transition bg-indigo-700 rounded-full right-4 top-4 hover:bg-indigo-800"><svg xmlns="http://www.w3.org/2000/svg" enable-background="new 0 0 24 24" height="24px" viewBox="0 0 24 24" width="24px" fill="white"><rect fill="none" height="24" width="24"/><path d="M19.5,12c0-0.23-0.01-0.45-0.03-0.68l1.86-1.41c0.4-0.3,0.51-0.86,0.26-1.3l-1.87-3.23c-0.25-0.44-0.79-0.62-1.25-0.42 l-2.15,0.91c-0.37-0.26-0.76-0.49-1.17-0.68l-0.29-2.31C14.8,2.38,14.37,2,13.87,2h-3.73C9.63,2,9.2,2.38,9.14,2.88L8.85,5.19 c-0.41,0.19-0.8,0.42-1.17,0.68L5.53,4.96c-0.46-0.2-1-0.02-1.25,0.42L2.41,8.62c-0.25,0.44-0.14,0.99,0.26,1.3l1.86,1.41 C4.51,11.55,4.5,11.77,4.5,12s0.01,0.45,0.03,0.68l-1.86,1.41c-0.4,0.3-0.51,0.86-0.26,1.3l1.87,3.23c0.25,0.44,0.79,0.62,1.25,0.42 l2.15-0.91c0.37,0.26,0.76,0.49,1.17,0.68l0.29,2.31C9.2,21.62,9.63,22,10.13,22h3.73c0.5,0,0.93-0.38,0.99-0.88l0.29-2.31 c0.41-0.19,0.8-0.42,1.17-0.68l2.15,0.91c0.46,0.2,1,0.02,1.25-0.42l1.87-3.23c0.25-0.44,0.14-0.99-0.26-1.3l-1.86-1.41 C19.49,12.45,19.5,12.23,19.5,12z M12.04,15.5c-1.93,0-3.5-1.57-3.5-3.5s1.57-3.5,3.5-3.5s3.5,1.57,3.5,3.5S13.97,15.5,12.04,15.5z"/></svg></button>
      <p class="absolute font-medium text-white cursor-default right-4 bottom-4">Scratch Profiler</p>
      <div class="flex items-center">
        <img src="https://cdn2.scratch.mit.edu/get_image/user/{data ? data.id : 0}_400x400.png" class="w-24 h-24 m-4 rounded-full shadow-md" alt="User's profile pic"/>
        <h1 class="text-4xl font-black text-white whitespace-nowrap">{user}</h1>
      </div>
    </div>
    {#if exists}
      <!-- Main content -->
      <div class="p-4">
        <!-- Buttons -->
        <div class="flex flex-wrap gap-2">
          <a href="https://scratch.mit.edu/users/{user}" class="btn">Scratch</a>
          <a href="https://ocular.jeffalo.net/user/{user}" class="btn">Ocular</a>
          <a href="https://scratchstats.com/{user}" class="btn">ScratchStats</a>
          <a href="https://magnifier.potatophant.net/users/{user}" class="btn">Magnifier</a>
          <a href="https://postpercent.rirurin.com/users/{user}" class="btn">Postpercent</a>
        </div>
        <!-- Stats -->
        <div class="flex flex-wrap gap-2 mt-8">
          <div class="stat"><span class="inline btn hover:bg-indigo-700">{follows}</span> followers</div>
          <div class="stat"><span class="inline btn hover:bg-indigo-700">{posts}</span> forum posts</div>
        </div>
        <!-- About/Work -->
        <div class="flex flex-col mt-8 sm:flex-row sm:space-x-1">
          <div class="flex-1 overflow-auto max-h-52">
            <h1 class="mb-1 text-2xl font-black text-gray-900">About</h1>
            <div class="text-gray-600">{@html data.bio}</div>
          </div>
          <div class="flex-1 mt-4 overflow-auto sm:mt-0 max-h-52">
            <h1 class="mb-1 text-2xl font-black text-gray-900">Work</h1>
            <div class="text-gray-600">{@html data.work}</div>
          </div>
        </div>
      </div>
    {:else}
      <div class="p-4">
        <h1 class="mb-1 text-2xl font-black text-center text-gray-900">No stats available</h1>
      </div>
    {/if}
  </div>
</div>
