<script lang="ts">
import { createEventDispatcher } from "svelte";
import { copy_text } from "util/Util.svelte";
import FileStats from "./FileStats.svelte";
import type { FSNavigator } from "./FSNavigator";
import EditWindow from "./edit_window/EditWindow.svelte";
import FilePreview from "./viewers/FilePreview.svelte";
import { fs_share_url } from "./FilesystemAPI";
import ShareDialog from "./ShareDialog.svelte";

let dispatch = createEventDispatcher()

export let nav: FSNavigator
export let details_visible = false
export let edit_window: EditWindow
export let edit_visible = false
export let file_viewer: HTMLDivElement
export let file_preview: FilePreview
let share_dialog: ShareDialog

$: share_url = fs_share_url($nav.path)
let link_copied = false
export const copy_link = () => {
	if (share_url === "") {
		edit_window.edit(nav.base, true, "share")
		return
	}

	copy_text(share_url)
	link_copied = true
	setTimeout(() => {link_copied = false}, 60000)
}

let fullscreen = false
export const toggle_fullscreen = () => {
	if (document.fullscreenElement !== null) {
		try {
			document.exitFullscreen()
		} catch (err) {
			console.debug("Failed to exit fullscreen", err)
		}
		fullscreen = false
	} else {
		if (!file_preview.toggle_fullscreen()) {
			file_viewer.requestFullscreen()
		}
		fullscreen = true
	}
}

let expanded = true
let expand = (e: Event) => {
	e.preventDefault()
	e.stopPropagation()
	expanded = !expanded
}
</script>

<div class="toolbar" class:expanded>
	<div class="stats_container" on:click={expand} on:keypress={expand} role="button" tabindex="0">
		<button class="button_expand hidden_vertical" on:click={expand}>
			{#if expanded}
				<i class="icon">expand_more</i>
			{:else}
				<i class="icon">expand_less</i>
			{/if}
		</button>
		<FileStats nav={nav}/>
	</div>

	<div class="separator"></div>
	<div class="grid">

		<div class="button_row">
			<button on:click={() => {nav.open_sibling(-1)}}>
				<i class="icon">skip_previous</i>
			</button>
			<button on:click={() => {nav.shuffle = !nav.shuffle}} class:button_highlight={nav.shuffle}>
				<i class="icon">shuffle</i>
			</button>
			<button on:click={() => {nav.open_sibling(1)}}>
				<i class="icon">skip_next</i>
			</button>
		</div>

		<div class="separator hidden_horizontal"></div>

		<button on:click={() => dispatch("download")}>
			<i class="icon">save</i>
			<span>Download</span>
		</button>

		{#if share_url !== ""}
			<button on:click={copy_link} class:button_highlight={link_copied}>
				<i class="icon">content_copy</i>
				<span><u>C</u>opy link</span>
			</button>
		{/if}

		<!-- Share button is enabled when: The browser has a sharing API, or the user can edit the file (to enable sharing)-->
		{#if $nav.base.id !== "me" && (navigator.share !== undefined || $nav.permissions.write === true)}
			<button on:click={(e) => share_dialog.open(e, nav.path)}>
				<i class="icon">share</i>
				<span>Share</span>
			</button>
		{/if}

		<button
			class="toolbar_button"
			on:click={toggle_fullscreen}
			class:button_highlight={fullscreen}
			title="Open page in full screen mode">
			{#if fullscreen}
				<i class="icon">fullscreen_exit</i>
			{:else}
				<i class="icon">fullscreen</i>
			{/if}
			<span>Fullscreen</span>
		</button>

		<div class="separator hidden_horizontal"></div>

		<button on:click={() => details_visible = !details_visible} class:button_highlight={details_visible}>
			<i class="icon">help</i>
			<span>Deta<u>i</u>ls</span>
		</button>

		{#if $nav.base.id !== "me" && $nav.permissions.write === true}
			<button on:click={() => edit_window.edit(nav.base, true, "file")} class:button_highlight={edit_visible}>
				<i class="icon">edit</i>
				<span><u>E</u>dit</span>
			</button>
		{/if}
	</div>
</div>

<ShareDialog nav={nav} bind:this={share_dialog}/>

<style>
.toolbar {
	flex: 0 0 auto;
	overflow-x: hidden;
	overflow-y: scroll;
	transition: max-height 0.3s;
	background-color: var(--shaded_background);
	backdrop-filter: blur(4px);
}
.grid {
	display: grid;
	grid-template-columns: repeat(auto-fit, minmax(7.5em, 1fr));
}
.separator {
	height: 1px;
	margin: 2px 0;
	width: 100%;
	background-color: var(--separator);
}

.button_row {
	display: flex;
	flex-direction: row;
}
.button_row > * {
	flex: 1 1 auto;
	justify-content: center;
}

.stats_container {
	display: flex;
	flex-direction: column;
}
.button_expand {
	line-height: 1em;
}

.hidden_vertical {
	display: none;
}
.hidden_horizontal {
	display: block;
}

/* This max-width needs to be synced with the .viewer_area max-width in
Toolbar.svelte and the .label max-width in FileStats.svelte */
@media (max-width: 1000px) {
	.toolbar {
		overflow-y: hidden;
		max-height: 2.1em;
	}
	.toolbar.expanded {
		overflow-y: scroll;
		max-height: 25vh;
	}
	.stats_container {
		flex-direction: row;
	}
	.separator {
		margin: 0;
	}

	.hidden_vertical {
		display: block;
	}
	.hidden_horizontal {
		display: none;
	}
}

</style>
