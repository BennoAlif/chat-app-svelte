<script>
    export let name;
    export let toggle = true;
    let feedback = '';

    function login() {
        if (name) {
            toggle = !toggle
        }else{
            feedback ='Masukin nama dulu'
        }
    };
</script>

<div class="center-align">
    {#if toggle}
        <h2 class="teal-text">Welcome</h2>
        <label for="name" class="red-text">{feedback}</label>
        <input id="name" class="center-align" type="text" bind:value={name} on:keydown.Enter={login} placeholder="Enter Your Name...">
        <button class="btn teal" on:click={login}>Masuk</button>
    {/if}

    {#if name}
        <h2>Hi! {name}</h2>
    {/if}
</div>