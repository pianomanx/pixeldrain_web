<script>
import Footer from "layout/Footer.svelte";
import Speedtest from "./Speedtest.svelte";

</script>

<header>
	<h1>Pixeldrain Speedtest</h1>
</header>

<div class="page_content">
	<Speedtest/>

	<section>
		<h2>How does this work?</h2>
		<p>
			The speedtest measures the maximum download speed from pixeldrain's
			servers to your computer. This speed is not affected by the daily
			download limit for free users.
		</p>
		<p>
			The speed measured is the maximum speed reached for two continuous
			seconds during the test. The normal test has a time limit of 12
			seconds and the long test 30 seconds. When the maximum speed has not
			changed for a third of the test duration (4 or 10 seconds) the test
			will end and the result will be final. The long test will use much
			more bandwidth, so if you're on a cap it's recommended to use the
			normal test.
		</p>
		<p>
			The speed gauge uses a logarithmic scale, which means that each
			datapoint is 10 times more than the last one.
		</p>

		<h2>What do the numbers mean?</h2>
		<p>
			The first number is the download speed in bytes per second. This is
			useful for estimating how long a file download will take.
		</p>
		<ul>
			<li>1 kB = 1000 bytes</li>
			<li>1 MB = 1000 kB</li>
			<li>1 GB = 1000 MB</li>
			<li>etc..</li>
		</ul>
		<p>
			The second number shows the download speed in bits. This is usually
			the speed your ISP advertises to you. This number is equal to the
			first number multiplied by eight, because there are eight bits in a
			byte.
		</p>
		<p>
			The third number shows the latency to the pixeldrain servers. This
			is dependent on how far you are removed from the closest pixeldrain
			server. The lower the latency the faster your downloads will be,
			generally. The number shows request latency and not ping latency.
			HTTP requests have some overhead which means this latency number
			shows multiple round trips instead of one.
		</p>
		<p>
			The last number shows how much data the speedtest was able to
			transfer in the duration of the test.
		</p>

		<h2>Why is the speed different from other speed tests?</h2>
		<p>
			Most speed tests have servers in datacenters which are located very
			close to your home. They are also connected directly to your ISP
			which means that the ISP does not have to pay for the bandwidth
			because it stays within their network.
		</p>
		<p>
			Pixeldrain does not have this luxury. Because our budget is very
			small we are only able to afford the cheapest bandwidth available.
			This means that the data has to travel further and is more likely to
			be throttled.
		</p>
		<p>
			VPN services also add overhead to your connections, which lowers
			effective download speed. VPNs using OpenVPN lower your download
			speed by approximately 17.2%, and VPNs using the more modern
			WireGuard protocol lower your download speed by approximately 4.5%.
			To get most accurate results make sure you have any VPN services
			turned off.
		</p>
		<h2>How do I read these results?</h2>
		<p>
			If the speed is a lot slower than your usual downloads it can mean
			two things.
		</p>
		<ol>
			<li>
				Your ISP is limiting the connection speed to pixeldrain's
				servers
			</li>
			<li>
				The pixeldrain servers are overloaded
			</li>
		</ol>
		<p>
			Number two is usually not the case, when that happens I will
			<a href="https://twitter.com/Fornax96">tweet about it</a>.
			Unfortunately there is nothing I can do about the first scenario. I
			might be able to get more servers and better bandwidth if the site
			gets many more subscribers, but for now that's out of reach.
		</p>
	</section>
</div>

<Footer/>

<style>
.page_content {
	margin-bottom: 16px;
}
</style>
