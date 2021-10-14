<script lang="ts">
	import { onMount } from "svelte";

	import "@ui5/webcomponents-icons/dist/add.js";
	import "@ui5/webcomponents-icons/dist/decline.js";

	import "@ui5/webcomponents/dist/Button.js";
	import "@ui5/webcomponents-fiori/dist/ShellBar.js";
	import "@ui5/webcomponents/dist/List.js";
	import "@ui5/webcomponents/dist/StandardListItem.js"
	import "@ui5/webcomponents-fiori/dist/FlexibleColumnLayout.js";

	let users: Array<any> = [];
	let headerTitle: string = "Users (0)";
	let fcLayout: string = "OneColumn";

	onMount(async () => {
		const res = await fetch("https://randomuser.me/api/?results=20");
		const body = await res.json();
		
		users = body.results;
		headerTitle = `Users (${users.length})`;
	})

	const onItemClick = (e) => {
		console.log(e.target);
		fcLayout = "TwoColumnsMidExpanded";
	};

	const onCloseSecondColumn = () => {
		fcLayout = "OneColumn";
	}

</script>

<main>
	<ui5-flexible-column-layout layout={fcLayout}>
		<div slot="startColumn">
			<ui5-list class="full-width" header-text={headerTitle}>
				{#each users as user}
					<ui5-li on:click={onItemClick} description={user.email} additional-text={user.gender} additional-text-state={user.gender === "male" ? "Information" : "Error"}>{user.name.first} {user.name.last}</ui5-li>
				{/each}
			</ui5-list>
		</div>
		<div slot="midColumn">
			<div class="colHeader">
				<ui5-button design="Emphasized">Edit</ui5-button>
				<ui5-button design="Transparent" icon="add"></ui5-button>
				<ui5-button on:click={onCloseSecondColumn} icon="decline" design="Transparent"></ui5-button>
			</div>
		</div>
	</ui5-flexible-column-layout>
</main>

<style>
</style>