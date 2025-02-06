<script>
	import { onMount } from 'svelte';

	let isNearMouse = false;
	let position = { x: 0, y: 0 };
	let timeout;
	let drumRotation = 0;
	let drumClickable = false;

	// Track drum rotation
	function updateDrumRotation() {
		drumRotation = (drumRotation + 2) % 360; // Increment by 2 degrees each frame
		drumClickable = isNearMouse && drumRotation >= 90 && drumRotation <= 180;
		requestAnimationFrame(updateDrumRotation);
	}

	onMount(() => {
		requestAnimationFrame(updateDrumRotation);
	});

	const getRandomPosition = () => {
		const viewportWidth = window.innerWidth;
		const viewportHeight = window.innerHeight;
		const maxX = viewportWidth * 0.1;
		const maxY = viewportHeight * 0.1;

		return {
			x: position.x + (Math.random() - 0.5) * maxX * 2,
			y: position.y + (Math.random() - 0.5) * maxY * 2
		};
	};

	const move = () => {
		position = getRandomPosition();
		timeout = setTimeout(move, 5000);
	};

	onMount(() => {
		timeout = setTimeout(move, 5000);
		return () => clearTimeout(timeout);
	});

	const handleMouseEvent = () => {
		clearTimeout(timeout);
		move();
	};

	const handleButtonClick = () => {
		if (drumClickable) {
			alert('Button clicked at the right moment!');
			// Add your button click logic here
		}
	};
</script>

<div
	class="fixed"
	style="transform: translate({position.x}px, {position.y}px); transition: transform 1s ease-in-out"
	on:mouseenter={() => {
    isNearMouse = true;
    handleMouseEvent();
  }}
	on:mouseleave={() => {
    isNearMouse = false;
    handleMouseEvent();
  }}
	role="presentation"
>
	<div class="relative w-64 h-64 p-4">
		<svg
			xmlns="http://www.w3.org/2000/svg"
			viewBox="0 0 200 200"
			width="200"
			height="200"
			class="w-full h-full transition-transform duration-700 ease-out {!isNearMouse ? 'animate-spin-slow' : 'reset-position'}"
		>
			<!-- Outer Frame -->
			<g id="frame">
				<!-- Main body -->
				<rect x="40" y="40" width="120" height="120" fill="#4a5568" />

				<!-- 8-bit corner details -->
				<rect x="40" y="40" width="10" height="10" fill="#718096" />
				<rect x="150" y="40" width="10" height="10" fill="#2d3748" />
				<rect x="40" y="150" width="10" height="10" fill="#2d3748" />
				<rect x="150" y="150" width="10" height="10" fill="#1a202c" />

				<!-- Control panel -->
				<rect x="50" y="50" width="100" height="20" fill="#2d3748" />
				<circle cx="70" cy="60" r="5" fill="#a0aec0" />
				<!-- Middle button - now clickable -->
				<rect
					x="85"
					y="55"
					width="15"
					height="10"
					fill={drumClickable ? "#10B981" : "#a0aec0"}
					style="cursor: {drumClickable ? 'pointer' : 'default'};"
					on:click={handleButtonClick}
				/>
				<rect x="110" y="55" width="15" height="10" fill="#a0aec0" />

				<!-- Door frame -->
				<circle cx="100" cy="110" r="40" fill="#a0aec0" />
			</g>

			<!-- Inner Drum -->
			<g id="drum" class="drum-spin">
				<!-- Door window -->
				<circle cx="100" cy="110" r="35" fill="#e2e8f0" />

				<!-- 8-bit drum details -->
				<rect x="85" y="85" width="30" height="5" fill="#cbd5e0" />
				<rect x="85" y="130" width="30" height="5" fill="#cbd5e0" />

				<!-- Clothes pattern -->
				<path
					d="M 80,100 L 120,120 M 75,110 L 125,110 M 120,100 L 80,120"
					stroke="#4a5568"
					stroke-width="4"
					stroke-linecap="square"
				/>
			</g>
		</svg>
	</div>
</div>

<style>
    @keyframes spin-slow {
        from {
            transform: rotate(0deg);
        }
        to {
            transform: rotate(360deg);
        }
    }

    @keyframes drum-spin {
        from {
            transform: rotate(0deg);
        }
        to {
            transform: rotate(360deg);
        }
    }

    :global(.animate-spin-slow) {
        animation: spin-slow 3s linear infinite;
    }

    :global(.reset-position) {
        transform: rotate(0deg) !important;
        transition: all 0.7s cubic-bezier(0.4, 0, 0.2, 1) !important;
    }

    :global(.drum-spin) {
        animation: drum-spin 1.5s linear infinite;
        transform-origin: 100px 110px;
    }

    :global(.origin-center) {
        transform-origin: center;
    }
</style>