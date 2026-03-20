<script setup lang="ts">
	import { nextTick, onBeforeUnmount, onMounted, ref, type Ref, } from 'vue';

	type SlideFit = 'cover' | 'contain';

	type Slide = {
		title: string;
		image: string;
		alt: string;
		fit: SlideFit;
		description?: string;
	};

	type SlideElement = HTMLElement;

	const slides: readonly Slide[] = [
		{
			title: 'Kerewandemba',
			image: '/images/paysage1.jpg',
			alt: 'A wide landscape view of Kerewandemba.',
			description: 'Kerewandemba is a small town in The Gambia, about 180 kilometers (110 miles) from Banjul Airport.'
				+ ' It is surrounded by beautiful wildlife, striking scenery, rich traditions, and ingenious people who provide'
				+ ' for themselves in a less industrial, more traditional way. At KW ECO Tourism, it is our pleasure to help'
				+ ' visitors experience this beautiful nature and culture.',
			fit: 'cover',
		},
		{
			title: 'Cuisine',
			image: '/images/food1.jpg',
			alt: 'Traditional food prepared for visitors.',
			description: 'The land of The Gambia provides its people with a rich variety of ingredients. After setting aside what'
				+ ' their religion may forbid, they combine those ingredients using techniques passed down through generations,'
				+ ' alongside others learned more recently. The result is a gastronomic experience that feels'
				+ ' exciting to newcomers. And who knows, maybe you will bring some of the techniques you learn here into your'
				+ ' own cooking.',
			fit: 'cover',
		},
		{
			title: 'Scenery',
			image: '/images/road_and_birds.jpg',
			alt: 'A road cutting through the landscape with birds nearby.',
			description: 'The area surrounding Kerewandemba is wonderfully diverse, from the lush forests around the Gambia River to'
				+ ' the marshlands and savanna. It is a place filled with mesmerizing baobab trees, along with countless'
				+ ' other natural wonders.',
			fit: 'cover',
		},
		{
			title: 'Birding',
			image: '/images/birdwatching2.jpg',
			alt: 'A bird seen near the river.',
			description: 'Many beautiful birds can be spotted in the area around Kerewandemba. From birdsong to brilliant colors,'
				+ ' birding in The Gambia can be a truly joyful experience.',
			fit: 'cover',
		},
		{
			title: 'Wildlife',
			image: '/images/lurking_chimp.jpg',
			alt: 'A chimpanzee partially hidden in the vegetation.',
			fit: 'cover',
			description: 'The Gambia River is surrounded by remarkable wildlife that lives in the lush forest. Experiencing the'
				+ ' magical moment of spotting this wildlife is a privilege that stays with you for life.',
		},
		{
			title: 'Sustainable Materials',
			image: '/images/cattail2.jpg',
			alt: 'Natural material gathered for traditional crafts.',
			description: 'All the tools and utensils that make life easier and more comfortable must be made from some kind of material.'
				+ ' Today, many non-natural materials are used in Kerewandemba, but the crafts of turning what nature provides into'
				+ ' useful materials are still practiced. Here, we are more than happy to introduce visitors to these important crafts.',
			fit: 'cover',
		},
		{
			title: 'Traditional Building',
			image: '/images/hut.png',
			alt: 'A traditional hut used for village life and demonstrations.',
			description: 'A little dust from the ground, a little water from the well, some time, and wood for fuel, and before long'
				+ ' a place to live begins to appear.',
			fit: 'cover',
		},
		{
			title: 'Charcoal Making',
			image: '/images/charcoal_being_produced.webp',
			alt: 'Traditional charcoal production in progress.',
			description: 'Charcoal has long been a common fuel across many human societies. It is made by processing wood under controlled'
				+ ' burning conditions, creating a lighter, more efficient, and easier-to-use fuel than wood itself. Even if the'
				+ ' traditional mud-covered kiln cannot always be scaled down to the size of the group, illustrative versions can be'
				+ ' made with metal covers. Learning about this traditional energy source can help people deepen their understanding'
				+ ' of the beautiful world we live in.',
			fit: 'cover',
		},
		{
			title: 'Animal Husbandry',
			image: '/images/donkeys.jpg',
			alt: 'Donkeys standing in an open field.',
			description: 'Animal husbandry is an important part of the economy of the peoples of The Gambia. In Kerewandemba, you can'
				+ ' learn about the herding practices of settled households and of nomadic pastoralists who at times move through'
				+ ' the area in rivers of cattle.',
			fit: 'cover',
		},
		{
			title: 'Meet Your Host',
			image: '/images/host.jpg',
			alt: 'Portrait of the local host.',
			description: 'Hello, my name is Beakery Leigh, and it will be my pleasure to host you here and introduce you to the'
				+ ' many wonders Kerewandemba has to offer. Besides hosting visitors, I also cultivate rice and grow tree'
				+ ' species that are not native to The Gambia in order to investigate their economic viability and whether they'
				+ ' can be used here without upsetting the fragile ecological balance.',
			fit: 'contain',
		},
		{
			title: 'Venture On',
			image: '/images/marshlands.jpg',
			alt: 'Open marshlands at the edge of the village.',
			description: 'Has any of this caught your attention? Contact me on WhatsApp at +220 702 6904. I do not always have'
				+ ' internet connectivity, so it may take me a few days to respond, but some good things take a little time.'
				+ ' Tell me which activities interest you most, when you intend to come, and how long you would like to stay.'
				+ ' Then I can let you know whether there is another group visiting around that time that you could join, and'
				+ ' whether the season is well suited to the activities you want to do. Keep in mind that the dry season in'
				+ ' The Gambia runs from November to May or June, and many people find that the most enjoyable time to visit.',
			fit: 'cover',
		},
	];

	const carouselRef: Ref<HTMLElement | null> = ref<HTMLElement | null>(null);
	const slideRefs: Ref<SlideElement[]> = ref<SlideElement[]>([]);
	const activeIndex: Ref<number> = ref<number>(0);
	const isReady: Ref<boolean> = ref<boolean>(false);
	const isAutoScrolling: Ref<boolean> = ref<boolean>(false);

	let observer: IntersectionObserver | null = null;
	let unlockTimer: number = 0;

	function collectSlideRefs(): void
	{
		if (!carouselRef.value)
		{
			slideRefs.value = [];
			return;
		}

		slideRefs.value = Array.from(
			carouselRef.value.querySelectorAll<SlideElement>('.slide'),
		);
	}

	function clampIndex(index: number): number
	{
		return Math.max(0, Math.min(index, slides.length - 1));
	}

	function stopAutoScrolling(): void
	{
		isAutoScrolling.value = false;
	}

	function releaseAutoScrolling(): void
	{
		window.clearTimeout(unlockTimer);
		unlockTimer = window.setTimeout(
			stopAutoScrolling,
			850,
		);
	}

	function scrollToSlide(index: number): void
	{
		const target: SlideElement | undefined = slideRefs.value[clampIndex(index)];

		if (!target)
		{
			return;
		}

		isAutoScrolling.value = true;
		target.scrollIntoView({
			behavior: 'smooth',
			block: 'start',
		});
		releaseAutoScrolling();
	}

	function handleWheel(event: WheelEvent): void
	{
		if (event.ctrlKey || Math.abs(event.deltaY) < 10)
		{
			return;
		}

		if (!window.matchMedia('(pointer: fine)').matches)
		{
			return;
		}

		event.preventDefault();

		if (isAutoScrolling.value)
		{
			return;
		}

		const direction: number = event.deltaY > 0 ? 1 : -1;
		const nextIndex: number = clampIndex(activeIndex.value + direction);

		if (nextIndex === activeIndex.value)
		{
			return;
		}

		scrollToSlide(nextIndex);
	}

	function handleSlideIntersections(entries: IntersectionObserverEntry[]): void
	{
		let nextActiveIndex: number = activeIndex.value;
		let highestRatio: number = 0;

		for (const entry of entries)
		{
			if (!entry.isIntersecting)
			{
				continue;
			}

			const index: number = Number(entry.target.getAttribute('data-index'));

			if (Number.isNaN(index))
			{
				continue;
			}

			if (entry.intersectionRatio < highestRatio)
			{
				continue;
			}

			highestRatio = entry.intersectionRatio;
			nextActiveIndex = index;
		}

		activeIndex.value = nextActiveIndex;
	}

	function markReady(): void
	{
		isReady.value = true;
	}

	function queueReadyState(): void
	{
		requestAnimationFrame(markReady);
	}

	async function mountCarousel(): Promise<void>
	{
		await nextTick();
		collectSlideRefs();

		observer = new IntersectionObserver(
			handleSlideIntersections,
			{
				threshold: [0.45, 0.6, 0.8],
			},
		);

		for (const slide of slideRefs.value)
		{
			observer.observe(slide);
		}

		window.addEventListener('wheel', handleWheel, { passive: false });
		requestAnimationFrame(queueReadyState);
	}

	function unmountCarousel(): void
	{
		observer?.disconnect();
		window.removeEventListener('wheel', handleWheel);
		window.clearTimeout(unlockTimer);
	}

	onMounted(mountCarousel);
	onBeforeUnmount(unmountCarousel);
</script>

<template>
	<main
		ref="carouselRef"
		class="carousel-app"
		:class="{ 'carousel-app--ready': isReady }"
	>
		<div class="floating-brand">
			KWTurisim
		</div>

		<section
			class="carousel"
			aria-label="KWTurisim scroll carousel"
		>
			<article
				v-for="(slide, index) in slides"
				:key="slide.title"
				class="slide"
				:class="{ 'slide--active': index === activeIndex }"
				:data-index="index"
			>
				<img
					class="slide__background"
					:src="slide.image"
					alt=""
					aria-hidden="true"
					:loading="index === 0 ? 'eager' : 'lazy'"
					decoding="async"
				>

				<div class="slide__wash" />

				<img
					class="slide__image"
					:class="`slide__image--${slide.fit}`"
					:src="slide.image"
					:alt="slide.alt"
					:loading="index === 0 ? 'eager' : 'lazy'"
					decoding="async"
				>

				<div class="slide__copy">
					<h1
						v-if="index === 0"
						class="slide__title"
					>
						{{ slide.title }}
					</h1>
					<h2
						v-else
						class="slide__title"
					>
						{{ slide.title }}
					</h2>
					<p
						v-if="slide.description"
						class="slide__description"
					>
						{{ slide.description }}
					</p>
				</div>
			</article>
		</section>
	</main>
</template>

<style scoped>
:global(html) {
	background:
		radial-gradient(circle at top, rgba(53, 94, 59, 0.26), transparent 38%),
		linear-gradient(180deg, #03150d 0%, #062117 48%, #03110b 100%);
	scroll-behavior: smooth;
	scroll-snap-type: y mandatory;
}

:global(body) {
	margin: 0;
	min-height: 100vh;
	min-height: 100dvh;
	overflow-x: hidden;
	scroll-behavior: smooth;
	scroll-snap-type: y mandatory;
	color: #f5eddc;
	font-family: 'Avenir Next', 'Segoe UI', sans-serif;
}

:global(#app) {
	min-height: 100vh;
	min-height: 100dvh;
}

:global(*) {
	box-sizing: border-box;
}

.carousel-app {
	--brand: #114c30;
	--surface: rgba(4, 18, 12, 0.24);
	--text: #f5eddc;
	--muted: rgba(245, 237, 220, 0.78);
}

.carousel {
	min-height: 100vh;
}

.floating-brand {
	position: fixed;
	top: 1.75rem;
	left: 1.75rem;
	z-index: 5;
	padding: 0.65rem 1.15rem;
	border: 1px solid rgba(245, 237, 220, 0.18);
	border-radius: 999px;
	background: rgba(4, 18, 12, 0.34);
	color: var(--text);
	backdrop-filter: blur(10px);
	letter-spacing: 0.12em;
	text-transform: uppercase;
	font-size: 0.8rem;
	pointer-events: none;
}

.slide {
	position: relative;
	height: 100vh;
	height: 100dvh;
	overflow: clip;
	isolation: isolate;
	scroll-snap-align: start;
	scroll-snap-stop: always;
}

.slide__background,
.slide__wash,
.slide__image,
.slide__copy {
	position: absolute;
}

.slide__background {
	inset: 0;
	width: 100%;
	height: 100%;
	object-fit: cover;
	filter: blur(30px) brightness(0.38) saturate(0.92);
	transform: scale(1.18);
}

.slide__wash {
	inset: 0;
	width: 100%;
	height: 100%;
	background:
		radial-gradient(circle at center, rgba(17, 76, 48, 0.14) 0%, rgba(17, 76, 48, 0.22) 30%, rgba(3, 17, 11, 0.74) 100%),
		linear-gradient(180deg, rgba(3, 17, 11, 0.08) 0%, rgba(3, 17, 11, 0.36) 100%);
}

.slide__image {
	inset: 0;
	width: 100%;
	height: 100%;
	padding: clamp(0.8rem, 2vw, 1.7rem);
	transition:
		transform 820ms cubic-bezier(0.2, 0.9, 0.2, 1),
		filter 820ms cubic-bezier(0.2, 0.9, 0.2, 1),
		opacity 820ms cubic-bezier(0.2, 0.9, 0.2, 1);
}

.slide__image--cover {
	object-fit: cover;
}

.slide__image--contain {
	object-fit: contain;
}

.slide__copy {
	left: clamp(1rem, 4vw, 3rem);
	right: clamp(1rem, 4vw, 3rem);
	bottom: clamp(4.75rem, 11vh, 7.5rem);
	max-width: min(44rem, calc(100% - clamp(2rem, 8vw, 6rem)));
	padding: clamp(0.9rem, 2vw, 1.35rem);
	text-align: left;
	background:
		linear-gradient(180deg, rgba(4, 18, 12, 0.14) 0%, rgba(4, 18, 12, 0.34) 100%);
	backdrop-filter: blur(10px);
	-webkit-backdrop-filter: blur(10px);
	opacity: 0;
	transform: translateY(3.5rem);
	transition:
		transform 860ms cubic-bezier(0.18, 0.88, 0.2, 1),
		opacity 860ms cubic-bezier(0.18, 0.88, 0.2, 1);
	z-index: 2;
}

.slide__title {
	margin: 0;
	font-family: 'Iowan Old Style', 'Palatino Linotype', 'Book Antiqua', Georgia, serif;
	font-size: clamp(1.8rem, 4.2vw, 3.9rem);
	font-weight: 800;
	line-height: 0.94;
	text-wrap: balance;
	text-shadow: 0 0.08em 0.9em rgba(0, 0, 0, 0.34);
}

.slide__description {
	margin: 0.85rem 0 0;
	max-width: 34rem;
	font-size: clamp(0.82rem, 1vw, 0.95rem);
	font-weight: 650;
	line-height: 1.5;
	color: rgba(245, 237, 220, 0.92);
	text-shadow: 0 0.08em 0.85em rgba(0, 0, 0, 0.3);
}

.slide:not(.slide--active) .slide__image {
	transform: scale(1.03);
	filter: saturate(0.84) brightness(0.78);
	opacity: 0.92;
}

.carousel-app--ready .slide--active .slide__copy {
	opacity: 1;
	transform: translateY(0);
}

.slide--active .slide__image {
	transform: scale(1);
	filter: saturate(1) brightness(1);
	opacity: 1;
}

@media (max-width: 900px) {
	.slide__copy {
		left: 0.8rem;
		right: 0.8rem;
		bottom: 5.25rem;
		max-width: none;
		padding: 0.95rem 1rem 1.05rem;
	}

	.slide__title {
		font-size: clamp(2.05rem, 9.1vw, 3.6rem);
	}

	.slide__description {
		max-width: none;
		font-size: 0.9rem;
		line-height: 1.5;
	}

	.slide__image {
		padding: 0.65rem;
	}
}

@media (max-width: 900px) and (pointer: coarse) {
	.floating-brand {
		top: 1.2rem;
		left: 1.2rem;
		padding: 0.9rem 1.45rem;
		font-size: 1rem;
	}
}

@media (prefers-reduced-motion: reduce) {
	:global(html) {
		scroll-behavior: auto;
	}

	.slide__copy,
	.slide__image {
		transition: none;
		transform: none;
		opacity: 1;
		filter: none;
	}
}
</style>
