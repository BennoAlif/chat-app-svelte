<script>
	import { db } from './firebase'
	import { onMount, beforeUpdate, afterUpdate } from 'svelte'
	import { collectionData } from 'rxfire/firestore';
	import { startWith } from 'rxjs/operators';
	import { fade, fly } from 'svelte/transition';

	import moment from 'moment'

	import Name from './Name.svelte'

	let toggle;
	let name;
	let newMessage;
	let div;
	let autoscroll;
	let feedback = '';


	const query = db.collection('messages').orderBy('timestamp');
	const chats = collectionData(query, 'id').pipe(startWith([]));

	function add() {
		if (content) {
			db.collection('messages').add({ name, content: newMessage, timestamp: Date.now() })
			.then(() => {
				newMessage=''
				feedback=''
			})
		} else {
			feedback = "Harus diisi dulu"
		}
	}

	afterUpdate(() => {
		if (autoscroll) div.scrollTo(0, div.scrollHeight);
	});

</script>

<style>
	.chat{
		height: 25vh;
		overflow: auto;
		scroll-behavior: smooth;
	}
	.card{
		width: 480px;
		margin: 10% auto;
	}
	.card li{
		padding: 2%;
	}
	.chat span{
		font-size: 1em;
		display: block;
	}
	.chat .time{
		font-size: 0.8em;
	}
	
	.chat::-webkit-scrollbar {
		width: 3px;
	}
	
	.chat::-webkit-scrollbar-track {
		background: #ddd;
	}
	
	.chat::-webkit-scrollbar-thumb {
		background: #aaa; 
	}
</style>

<!-- Materialize CSS -->

<svelte:head>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0-beta/css/materialize.min.css">
</svelte:head>

<!-- HTML -->

<div class="container">
    <div class="card z-depth-4">
        <div class="card-content">

			<Name bind:toggle={toggle} bind:name={name}/>

			{#if !toggle}
				<div in:fade>
					<ul class="chat" bind:this={div}>

						{#each $chats as chat}
							<li class="{chat.name === name ? 'right-align' : 'left-align'}">
								<span class="{chat.name === name ? 'teal-text' : 'blue-text'}">{chat.name}</span>
								<span class="grey-text text-darken-3">{chat.content}</span>
								<span class="grey-text time">{moment(chat.timestamp).format('lll')}</span>
							</li>
						{/each}

					</ul>
					<div class="row">
						<div class="col s10">
							<label class="red-text" for="content">{feedback}</label>
							<input id="content" bind:value={newMessage} placeholder="Type a message...">
						</div>
						<div class="col s2">
							<button class="btn-floating teal" on:click={add}><i class="material-icons right">send</i></button>
						</div>
					</div>
					
				</div>
			{/if}

		</div>
	</div>
</div>