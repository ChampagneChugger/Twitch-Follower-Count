<script lang="ts">
	import { onMount } from "svelte"

	let total: number

	async function getFollowers(userid: string, key: string) {
		const request = await fetch(
			`https://api.twitch.tv/helix/channels/followers?broadcaster_id=${userid}`,
			{
				method: "GET",
				headers: {
					Authorization: `Bearer ${key}`,
					"Client-Id": "8i91xo6kbowyzxsrufswgbwdbs0a16"
				}
			}
		)

		const data = await request.json()

		total = data.total
	}

	onMount(async () => {
		if (document.location.hash) {
			const hash: string = document.location.hash
			const hashArray: string[] = hash.split("=")

			const key: string = hashArray[1].split("&")[0]

			const request = await fetch(`https://api.twitch.tv/helix/users?login=rhythxo`, {
				headers: {
					Authorization: `Bearer ${key}`,
					"Client-Id": "8i91xo6kbowyzxsrufswgbwdbs0a16"
				}
			})

			const { data } = await request.json()

			getFollowers(data[0].id, key)

			setInterval(() => {
				getFollowers(data[0].id, key)
			}, 10000)
		}
	})
</script>

{#if !total}
	<a
		href="https://id.twitch.tv/oauth2/authorize?client_id=8i91xo6kbowyzxsrufswgbwdbs0a16&redirect_uri=http://localhost:5173/&response_type=token&scope=moderator:read:followers"
		>Connect with Twitch</a
	>
{:else}
	<h1>{total}</h1>
{/if}
