<script lang="ts">
	import { onMount } from "svelte";

	import "@ui5/webcomponents-icons/dist/add.js";
	import "@ui5/webcomponents-icons/dist/decline.js";
	import "@ui5/webcomponents-icons/dist/delete.js";

	import "@ui5/webcomponents/dist/Button.js";
	import "@ui5/webcomponents-fiori/dist/Bar.js";
	import "@ui5/webcomponents/dist/List.js";
	import "@ui5/webcomponents/dist/StandardListItem.js"
	import "@ui5/webcomponents-fiori/dist/FlexibleColumnLayout.js";
	import "@ui5/webcomponents/dist/Card.js";
	import "@ui5/webcomponents/dist/CardHeader.js";

	interface Supervisee {
		email: string;
		gender: string;
		firstname: string;
		lastname: string;
	}

	interface Supervisor {
		email: string;
		gender: string;
		firstname: string;
		lastname: string;
		supervisees: Array<Supervisee>
	}

	let supervisors: Array<Supervisor> = [];
	let supervisor: Supervisor;

	// view
	let headerTitle: string = "Supervisors (0)";
	let fcLayout: string = "OneColumn";

	onMount(async () => {
		// getting superficial data for testing purposes
		let res = await fetch("https://randomuser.me/api/?results=5");
		let body = await res.json();
		const { results: supervisorResults } = body;
		
		res = await fetch("https://randomuser.me/api/?results=3");
		body = await res.json();
		const { results: superviseeResults } = body;
		let supervisees: Array<Supervisee> = [];

		superviseeResults.map(({email, gender, name: {first: firstname, last: lastname} }) => {
			let supervisee: Supervisee = {email, gender, firstname, lastname};
			supervisees = [...supervisees, supervisee];
		});

		supervisorResults.map(({email, gender, name: {first: firstname, last: lastname} }) => {
			const supervisor: Supervisor = {email, gender, firstname, lastname, supervisees};
			supervisors = [...supervisors, supervisor];
		});
		
		headerTitle = `Users (${supervisors.length})`;
	});

	const onItemClick = (index) => {
		supervisor = supervisors[index];

		fcLayout = "TwoColumnsMidExpanded"; 
	};

	const onCloseSecondColumn = () => {
		fcLayout = "OneColumn";
	};

</script>

<main>
	<div class="wrapper">
		<ui5-flexible-column-layout layout={fcLayout} class="fcLayout">
			<div slot="startColumn">
				<ui5-bar design="Header">
					<ui5-label slot="startContent">{headerTitle}</ui5-label>
					<ui5-button design="Transparent" icon="add" slot="endContent"></ui5-button>
				</ui5-bar>
				<ui5-list class="full-width">
					{#each supervisors as supervisor, i}
						<ui5-li on:click={() => onItemClick(i)} description={supervisor.email} additional-text={supervisor.gender} additional-text-state={supervisor.gender === "male" ? "Information" : "Error"}>{supervisor.firstname} {supervisor.lastname}</ui5-li>
					{/each}
				</ui5-list>
			</div>
			<div slot="midColumn">
				<ui5-bar design="Header">
					<ui5-button on:click={onCloseSecondColumn} icon="decline" design="Transparent" slot="startContent"></ui5-button>
					<ui5-button design="Emphasized" slot="endContent">Edit</ui5-button>
					<ui5-button design="Transparent" slot="endContent" icon="delete"></ui5-button>
				</ui5-bar>
				<!-- content -->
				<div>
					{#if supervisor}
						{#each supervisor.supervisees as supervisee, i}
							<ui5-card class="small">
								<ui5-card-header slot="header" title-text={`${supervisee.firstname} ${supervisee.lastname}`} subtitle-text={supervisee.gender}>
									<img alt="White dude" src="https://images.unsplash.com/photo-1568990545613-aa37e9353eb6?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=764&q=80" slot="avatar" />
								</ui5-card-header>

								<div class="content content-padding">
									<ui5-title level="H5" class="card-title">Contact details</ui5-title>
									<div class="content-group">
										<ui5-label>Address</ui5-label>
										<ui5-title level="H6">481 West Street, Anytown 45066, USA</ui5-title>
									</div>
									<div class="content-group">
										<ui5-label>Email</ui5-label>
										<ui5-link target="_blank">{supervisee.email}</ui5-link>
									</div>
								</div>
							</ui5-card>
						{/each}
					{/if}
				</div>

				<ui5-bar design="Footer" class="footer">
					<ui5-button design="Positive" slot="endContent">Save</ui5-button>
					<ui5-button design="Transparent" slot="endContent">Cancel</ui5-button>
				</ui5-bar>
			</div>
		</ui5-flexible-column-layout>
	</div>
</main>

<style>
	main {
		height:100%;
        margin:0;
        padding:0;
		background-color: red;
	}

	.footer {
	}

	.card-title {
		padding-bottom: 1rem;
	}

	.content,
	.content-group {
		display: flex;
		flex-direction: column;
		padding: 0.5rem;
	}
</style>