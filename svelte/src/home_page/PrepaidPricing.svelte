
<section>
	<div class="prices">
		<div>
			<div>Storage pricing</div>
			<div>€ 4 / TB / month</div>
		</div>
		<div>
			<div>Egress pricing</div>
			<div>€ 1 / TB</div>
		</div>
		<div>
			<div>Minimum fee *</div>
			<div>€0.01 / day</div>
		</div>
	</div>
	<p style="text-align: center;">
		* The minimum fee is only charged when daily usage is less than €0.01
	</p>

	<h2>What you get</h2>
	<ul>
		<li>
			<span class="bold">Unlimited</span> storage space
		</li>
		<li>
			Cloud storage with <a
			href="https://docs.pixeldrain.com/filesystem/#rclone">rclone</a>
			and <a
			href="https://docs.pixeldrain.com/filesystem/#ftps">FTPS</a>
			support
		</li>
		<li>
			<span class="bold">Customizable</span> download pages (custom
			branding colours, header and background images)
		</li>
		<li>
			<span class="bold">Fault tolerance</span> for up to four storage
			server failures
		</li>
		<li>
			<span class="bold">1.6 Terabits per second</span> of total
			bandwidth capacity
		</li>
		<li>
			Twenty-three high-performance caching servers optimized for data
			transfer over long distances
		</li>
		<li>
			File caching in
			<span class="bold">Portland</span>,
			<span class="bold">Querétaro</span>,
			<span class="bold">New York</span>,
			<span class="bold">São Paulo</span>,
			<span class="bold">Frankfurt</span> and
			<span class="bold">Singapore</span>
		</li>
	</ul>
	<h2>What you don't get</h2>
	<ul>
		<li>
			Data backups - Please keep a local copy of everything you store
			in case of emergency
		</li>
		<li>
			Commercial support - If you have a question, please check the <a
			href="https://docs.pixeldrain.com/questions_and_answers/">Q&A
			page</a> first. If that doesn't answer it, try the <a
			href="https://discord.gg/UDjaBGwr4p" target="_blank">support
			channel</a> on Discord.
		</li>
	</ul>
</section>

<style>
.bold {
	font-weight: bold;
	color: var(--highlight_color);
	text-shadow: 1px 1px 0px var(--shadow_color);
}

.prices {
	display: flex;
	flex-direction: row;
	flex-wrap: wrap;
	justify-content: center;
	gap: 10px;
}
.prices > div {
	flex: 1 0 200px;
	min-width: 200px;
	display: flex;
	flex-direction: column;
	text-align: center;
	border-radius: 6px;
	overflow: hidden;
	border: 2px solid var(--card_color);
}
.prices > div > div {
	padding: 4px;
}
.prices > div > div:nth-child(2) {
	background: var(--card_color);
	font-size: 1.3em;
}
</style>
