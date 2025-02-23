<script>
import { onMount } from "svelte";
import Euro from "../util/Euro.svelte";
import Modal from "../util/Modal.svelte";
import OtherPlans from "./OtherPlans.svelte";

let file_expiry
let direct_linking

onMount(() => {
	if (window.location.hash === "#direct_linking" || window.location.hash === "#hotlinking") {
		direct_linking.toggle()
	}
})
</script>

<section>
	<p>
		Pixeldrain features two different payment modes. We offer a monthly
		subscription which is managed by Patreon, and a prepaid service which
		supports a dozen different payment providers. For low usage Prepaid is
		usually better as there's no monthly fee.
	</p>
</section>

<div class="vertical_scroll">
	<div class="grid">
		<div></div>
		<div class="top_row free_feat">
			<span class="bold">Free</span>
		</div>
		<div class="top_row pro_feat">
			<span class="bold">Pro</span>
		</div>
		<div class="top_row prepaid_feat">
			<span class="bold">Prepaid</span>
		</div>

		<div class="left_col">
			Price
		</div>
		<div class="feature_cell free_feat">
			<span class="bold">Free</span>
		</div>
		<div class="feature_cell pro_feat">
			<span class="bold">€4 / month</span> or
			<span class="bold">€40 / year</span><br/>
			Charged through Patreon
		</div>
		<div class="feature_cell prepaid_feat">
			<span class="bold">€1 / month minimum</span><br/>
			Only charged when total usage is below €1
		</div>

		<div class="left_col">
			Downloading
		</div>
		<div class="feature_cell free_feat">
			<span class="bold">6 GB per day</span><br/>

			Download speed is reduced to 1 MiB/s when exceeded. Max 5 concurrent
			downloads
		</div>
		<div class="feature_cell pro_feat">
			<span class="bold">4 TB per 30 days</span><br/>

			Transfer limit is used for downloading, sharing and hotlinking. No
			connection limit
		</div>
		<div class="feature_cell prepaid_feat">
			<span class="bold">€1 per TB transferred</span><br/>

			Used for downloading, sharing and hotlinking. You only pay for what
			you use. No connection limit
		</div>

		<div class="left_col">
			<button class="round" on:click={direct_linking.toggle}>
				<i class="icon">info</i>
				Hotlinking
			</button>
		</div>
		<div class="feature_cell free_feat">
			<span class="bold">Hotlinking not supported</span><br/>
			Hotlinked files get blocked
		</div>
		<div class="feature_cell span2 right pro_feat">
			<span class="bold">Hotlinking supported</span><br/>
			Hotlinking uses your transfer limit
		</div>

		<div class="left_col">
			Storage
		</div>
		<div class="feature_cell span2 left pro_feat">
			<span class="bold">No limit</span><br/>
			Files expire when they are not downloaded
		</div>
		<div class="feature_cell prepaid_feat">
			<span class="bold">€4 / TB / month</span><br/>
			No limit, you only pay for what you use
		</div>

		<div class="left_col">
			<button class="round" on:click={file_expiry.toggle}>
				<i class="icon">info</i>
				File expiry
			</button>
		</div>
		<div class="feature_cell free_feat">
			<span class="bold">120 days</span> (4 months)
		</div>
		<div class="feature_cell pro_feat">
			<span class="bold">240 days</span> (8 months)<br/>
			Plans without expiry are available
		</div>
		<div class="feature_cell prepaid_feat">
			<span class="bold">Files do not expire</span><br/>
			While prepaid plan is active
		</div>

		<div class="left_col">
			Max file size
		</div>
		<div class="feature_cell free_feat">
			<span class="bold">20 GB</span> per file
		</div>
		<div class="feature_cell span2 right pro_feat">
			<span class="bold">100 GB</span> per file
		</div>

		<div class="left_col">
			Payment processors
		</div>
		<div class="feature_cell free_feat">
			<span class="bold">None</span>
		</div>
		<div class="feature_cell pro_feat">
			<span class="bold">PayPal</span>, <span class="bold">Credit card</span>
		</div>
		<div class="feature_cell prepaid_feat">
			<span class="bold">PayPal</span>, <span class="bold">SEPA</span>,
			<span class="bold">Credit card</span><br/>
			And many more
		</div>

		<div></div>
		<div class="bottom_row free_feat">
			Free
		</div>
		<div class="bottom_row pro_feat">
			{#if window.user.subscription.id === "patreon_1"}
				You have this plan<br/>
				Thank you for supporting pixeldrain!
			{:else}
				<a href="https://www.patreon.com/join/pixeldrain/checkout?rid=5736701&cadence=1" class="button button_highlight round">
					€ 4 per month
				</a>
				or
				<a href="https://www.patreon.com/join/pixeldrain/checkout?rid=5736701&cadence=12" class="button button_highlight round">
					€ 40 per year!
				</a>
				<br/>
				(Excluding tax)
				<br/>
				Subscription managed by Patreon
			{/if}
		</div>
		<div class="bottom_row prepaid_feat">
			{#if window.user.username === ""}
				<!-- User is not logged in -->
				Account required<br/>
				<a href="/login?redirect=checkout" class="button button_highlight round">
					<i class="icon">login</i>
					Log in
				</a>
				or
				<a href="/register?redirect=checkout" class="button button_highlight round">
					<i class="icon">how_to_reg</i>
					Register
				</a>
				<br/>
				Payments processed by Mollie<br/>
				No recurring payments
			{:else}
				<!-- User is logged in -->
				{#if window.user.subscription.type === ""}
					<a href="/user/prepaid/deposit#deposit" class="button button_highlight round">
						Deposit credit
					</a>
					to activate Prepaid
				{:else if window.user.subscription.type === "patreon"}
					Patreon subscription active. Prepaid cannot be activated
				{:else if window.user.subscription.type === "prepaid"}
					Prepaid plan is active.<br/>
					Current balance <Euro amount={window.user.balance_micro_eur}/>
					<br/>
					<a href="/user/prepaid/deposit#deposit" class="button button_highlight round">
						Top up my credit
					</a>
				{/if}
			{/if}
		</div>
	</div>
</div>

<section>
	<h2>Other plans available on Patreon</h2>
	<OtherPlans/>
</section>

<Modal bind:this={file_expiry} title="File Expiry Postponing" padding>
	<p>
		Files on pixeldrain have to expire eventually. If we didn't do this the
		website would keep growing forever and we would run out of money pretty
		quickly.
	</p>
	<p>
		Pixeldrain uses a postponing system for expiring files. When a file is
		freshly uploaded it gets 120 days by default (240 days if you have the
		pro plan). After these 120 days we will check when the file was last
		viewed. Files which are regularly viewed could still bring new users to
		the platform, it would be rude to show these people a File Not Found
		page. So if the file was viewed in the last 120 days we will simply
		postpone the next check a month. If the file was not viewed however, it
		will be deleted.
	</p>
	<p>
		Views are only counted when someone visits the download page in a web
		browser. This makes sure that users can see that the file comes from
		pixeldrain.
	</p>
	<p>
		This way we can minimize dead links, and you won't have to tell your
		friends to 'hurry and download this before it expires'.
	</p>
</Modal>

<Modal bind:this={direct_linking} title="Hotlinking Bandwidth" padding>
	<p>
		Paying for bandwidth is the most expensive part of running pixeldrain.
		Because of this we have to limit what can be downloaded and by who.
	</p>
	<p>
		Normally when you view a file it's on pixeldrain's file viewer. The file
		viewer is the page with the download button, the name of the file and a
		frame where you can view the file if it's an image, video, audio, PDF or
		text file.
	</p>
	<h3>Rate limiting</h3>
	<p>
		It's also possible to link directly to a file instead of the download
		page. This circumvents our advertisers and branding and thus we lose
		money when people do this. That's why I added 'hotlink protection mode'
		to files. This mode is enabled when a file has been downloaded five
		times more than it has been viewed through the file viewer. When hotlink
		protection mode is activated a file cannot be downloaded through the
		API, the request needs to come from the file viewer page. On the file
		viewer you will see a CAPTCHA to fill in when you click the download
		button.
	</p>
	<p>
		More information about <a
		href="https://en.wikipedia.org/wiki/Inline_linking" target="_blank"
		rel="noreferrer">Hotlinking on Wikipedia</a>.
	</p>
	<h3>Hotlinking with a Pro subscription</h3>
	<p>
		When you have a Pro subscription you will get a monthly data transfer
		limit for all the files on your account combined. Files you download
		from pixeldrain are subtracted from the data cap. If you have <a
		href="/user/subscription">hotlinking</a>
		enabled your data cap is also used when other people download
		your files.
	</p>
	<p>
		In principle there is always someone who pays for the bandwidth usage
		when a file is being downloaded:
	</p>
	<ol>
		<li>
			If the person downloading the file has a Pro subscription their data
			cap is used.
		</li>
		<li>
			If the person who uploaded the file has a Pro subscription and
			hotlinking is enabled on their account, then the uploader's data cap
			is used.
		</li>
		<li>
			If neither the uploader nor the downloader has a Pro subscription
			the download will be supported by advertisements on the download
			page.
		</li>
	</ol>
	<p>
		The bandwidth cap on your account is a 30 day rolling window. This means
		that bandwidth usage will expire 30 days after it was used. Your counter
		will not reset at the start of the next month.
	</p>
	<p>
		When a list of files is downloaded with the 'DL all files' button each
		file in the resulting zip file will be counted separately.
	</p>
</Modal>

<style>
.bold {
	font-weight: bold;
}

.vertical_scroll {
	overflow-x: scroll;
	overflow-y: hidden;
	width: 100%;
}

.grid {
	display: inline-grid;
	grid-auto-flow: row;
	grid-template-columns: 9em 1fr 1fr 1fr;
	min-width: 40em;
	max-width: 70em;
	gap: 5px;
	margin: 10px;
}
.grid > div {
	justify-content: center;
	align-items: center;
	text-align: center;
	padding: 0.5em;
	min-height: 3em;
}

.left_col {
	border-top-left-radius: 6px;
	border-bottom-left-radius: 6px;
	border: 1px solid var(--separator);
}
.top_row {
	border-top-left-radius: 6px;
	border-top-right-radius: 6px;
}
.bottom_row {
	border-bottom-left-radius: 6px;
	border-bottom-right-radius: 6px;
	border: 1px solid var(--separator);
	font-weight: bold;
}
.feature_cell {
	background-color: var(--card_color);
	border: 1px solid var(--background_color);
}
.pro_feat {
	border: 1px solid var(--highlight_color);
}
.free_feat {
	border: 1px solid #ebcb8b;
}
.prepaid_feat {
	border: 1px solid #ec2cfa;
}
.span2 {
	grid-column: span 2;
}
.span2.left {
	border-image: linear-gradient(to right, #ebcb8b 0%, var(--highlight_color) 100%) 1;
}
.span2.right {
	border-image: linear-gradient(to right, var(--highlight_color) 0%, #ec2cfa 100%) 1;
}
</style>
