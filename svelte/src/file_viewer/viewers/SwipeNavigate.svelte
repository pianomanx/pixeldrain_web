<script context="module">
// Dead zone before the swipe action gets detected
const swipe_inital_offset = 25
// Amount of pixels after which the navigation triggers
const swipe_trigger_offset = 75

export const swipe_nav = (node, swipe_enabled) => {
	let start_x = 0
	let start_y = 0
	let render_offset = 0
	let enabled = swipe_enabled

	const touchstart = e => {
		start_x = e.touches[0].clientX
		start_y = e.touches[0].clientY
		render_offset = 0
	}

	const touchmove = e => {
		if (!enabled) {
			return
		}

		const offset_x = e.touches[0].clientX - start_x
		const abs_x = Math.abs(offset_x)
		const abs_y = Math.abs(e.touches[0].clientY - start_y)
		const neg = offset_x < 0 ? -1 : 1

		// The cursor must have moved at least 50 pixels and three times as much
		// on the x axis than the y axis for it to count as a swipe
		if (abs_x > swipe_inital_offset && abs_y < abs_x/3) {
			set_offset((abs_x-swipe_inital_offset)*neg, false)
		} else {
			set_offset(0, true)
		}
	}

	const touchend = e => {
		if (!enabled) {
			return
		}

		if (render_offset > swipe_trigger_offset) {
			set_offset(1000, true)
			node.dispatchEvent(new CustomEvent("prev"))
		} else if (render_offset < -swipe_trigger_offset) {
			set_offset(-1000, true)
			node.dispatchEvent(new CustomEvent("next"))
		} else {
			set_offset(0, true)
		}
	}

	const set_offset = (off, animate) => {
		render_offset = off

		let detail = "transform: translateX("+off+"px);"
		if (animate) {
			detail += "transition: transform 400ms;"
		}

		// Clear the transformation if the offset is zero
		if (off === 0) {
			detail = ""
		}

		node.dispatchEvent(new CustomEvent("style", {detail: detail}))
	}

	node.addEventListener("touchstart", touchstart)
	node.addEventListener("touchmove", touchmove)
	node.addEventListener("touchend", touchend)

	return {
		update(swipe_enabled) {
			enabled = swipe_enabled
			if (!enabled) {
				render_offset = 0
			}
		},
		destroy() {
			node.removeEventListener("touchstart", touchstart)
			node.removeEventListener("touchmove", touchmove)
			node.removeEventListener("touchend", touchend)
		}
	}
}
</script>
