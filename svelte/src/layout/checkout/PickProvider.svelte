<script lang="ts">
import { put_user } from "lib/PixeldrainAPI";
import { payment_providers, type CheckoutState, type PaymentProvider } from "./CheckoutLib";

export let state: CheckoutState

const set_provider = async (p: PaymentProvider) => {
	try {
		await put_user({checkout_provider: p.name})
	} catch(err) {
		alert("Failed to update user:"+ err)
	}

	state.provider = p
}
</script>

<span>Please select a payment provider</span>

<div class="highlight_blue">
	<p>
		Unfortunately, PayPal has stopped providing their checkout services to
		pixeldrain. This leaves us with only cryptocurrencies. I am looking for
		a new payment provider, preferably one which is not run by dickheads,
		but those seem to be rare. If you have a suggestion, feel free to
		<a href="https://docs.pixeldrain.com/community/">let me know</a>. If you
		are unable to pay with cryptocurrency, you should switch to <a
		href="https://www.patreon.com/pixeldrain/join">a Patreon
		subscription</a>
		instead.
	</p>
</div>

<div class="providers">
	{#each payment_providers as p (p.name)}
		{#if p.country_filter === undefined || p.country_filter.includes(state.country.alpha2)}
			<button on:click={() => set_provider(p)}>
				<img src="/res/img/payment_providers/{p.icon}.svg" alt={p.label} title={p.label}/>
				{p.label}
			</button>
		{/if}
	{/each}
</div>

<style>
.providers {
	display: grid;
	grid-template-columns: repeat(auto-fit, minmax(8em, 1fr));
}
.providers > button {
	flex-direction: column;
	justify-content: space-around;
}
.providers > button > img {
	max-width: 3em;
	max-height: 3em;
}
</style>
