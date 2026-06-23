<script setup lang="ts">
	import {onMounted, type Ref, ref} from 'vue';

	let carrouselElement : Ref<HTMLDivElement | null> = ref(null);

	const carrouselData : { title : string, body : string, imagePath : string, imageAlt : string, imageFitType : "cover" | "contain" } []
		=
		[
			{
				title: "Kerewandemba",
				body: 'Kerewandemba is a small town in The Gambia, about 180 kilometers (110 miles) from the Banjul Airport.'
					+ ' It is surrounded by beautiful wildlife, striking scenery, rich traditions, and ingenious people who provide'
					+ ' for themselves in a less industrial, more traditional way. At Kerewandemba ECO Tourism, it is our pleasure to help'
					+ ' visitors experience this beautiful nature and culture.',
				imagePath: '/images/paysage1.webp',
				imageAlt: 'A wide landscape view of Kerewandemba.',
				imageFitType: "cover"
			},
			{
				title: 'Cuisine',
				imagePath: '/images/food1.webp',
				imageAlt: 'Traditional food prepared for visitors.',
				body: 'The land of The Gambia provides its people with a rich variety of ingredients. After setting aside what'
					+ ' their religion may forbid, they combine those ingredients using techniques passed down through generations,'
					+ ' alongside others learned more recently. The result is a gastronomic experience that feels'
					+ ' exciting to newcomers. And who knows, maybe you will bring some of the techniques you learn here into your'
					+ ' own cooking.',
				imageFitType: 'cover',
			},
			{
				title: 'Scenery',
				imagePath: '/images/road_and_birds.webp',
				imageAlt: 'A road cutting through the landscape with birds nearby.',
				body: 'The area surrounding Kerewandemba is wonderfully diverse, from the lush forests around the Gambia River to'
					+ ' the marshlands and savanna. It is a place filled with mesmerizing baobab trees, along with countless'
					+ ' other natural wonders.',
				imageFitType: 'cover',
			},
			{
				title: 'Birding',
				imagePath: '/images/birdwatching2.webp',
				imageAlt: 'A bird seen near the river.',
				body: 'Many beautiful birds can be spotted in the area around Kerewandemba. From birdsong to brilliant colors,'
					+ ' birding in The Gambia can be a truly joyful experience.',
				imageFitType: 'cover',
			},
			{
				title: 'Wildlife',
				imagePath: '/images/lurking_chimp.webp',
				imageAlt: 'A chimpanzee partially hidden in the vegetation.',
				imageFitType: 'cover',
				body: 'The Gambia River is surrounded by remarkable wildlife that lives in the lush forest. Experiencing the'
					+ ' magical moment of spotting this wildlife is a privilege that stays with one for life.',
			},
			{
				title: 'Sustainable Materials',
				imagePath: '/images/cattail2.webp',
				imageAlt: 'Natural material gathered for traditional crafts.',
				body: 'All the tools and utensils that make life easier and more comfortable must be made from some kind of material.'
					+ ' Today, many non-natural materials are used in Kerewandemba, but the crafts of turning what nature provides into'
					+ ' useful materials are still practiced. Here, we are more than happy to introduce visitors to these important crafts.',
				imageFitType: 'cover',
			},
			{
				title: 'Traditional Building',
				imagePath: '/images/hut.webp',
				imageAlt: 'A traditional hut used for village life and demonstrations.',
				body: 'A little dust from the ground, a little water from the well, time, and wood for fuel, and before long'
					+ ' a place to live begins to appear.',
				imageFitType: 'cover',
			},
			{
				title: 'Charcoal Making',
				imagePath: '/images/charcoal_being_produced.webp',
				imageAlt: 'Traditional charcoal production in progress.',
				body: 'Charcoal has long been a common fuel across many human societies. It is made by processing wood under controlled'
					+ ' burning conditions, creating a lighter, more efficient, and easier-to-use fuel than wood itself. Even if the'
					+ ' traditional mud-covered kiln cannot always be scaled down to the size of the group, illustrative versions can be'
					+ ' made with metal covers. Learning about this traditional energy source can help people deepen their understanding'
					+ ' of the beautiful world we live in.',
				imageFitType: 'cover',
			},
			{
				title: 'Animal Husbandry',
				imagePath: '/images/donkeys.webp',
				imageAlt: 'Donkeys standing in an open field.',
				body: 'Animal husbandry is an important part of the economy of the peoples of The Gambia. In Kerewandemba, you can'
					+ ' learn about the herding practices of settled households and of nomadic pastoralists who at times move through'
					+ ' the area their rivers of cattle.',
				imageFitType: 'cover',
			},
			{
				title: 'Meet Your Host',
				imagePath: '/images/host.webp',
				imageAlt: 'Portrait of the local host.',
				body: 'Hello, my name is Beakery Leigh, and it will be my pleasure to host you here and introduce you to the'
					+ ' many wonders Kerewandemba has to offer. Besides hosting visitors, I also cultivate rice and grow tree'
					+ ' species that are not native to The Gambia in order to investigate their economic viability and whether they'
					+ ' can be used here without upsetting the fragile ecological balance.',
				imageFitType: 'contain',
			},
			{
				title: 'Venture On',
				imagePath: '/images/marshlands.webp',
				imageAlt: 'Open marshlands at the edge of the village.',
				body: 'Has any of this caught your attention? Contact me on WhatsApp at +220 702 6904. I do not always have'
					+ ' internet connectivity, so it may take me a few days to respond, but some good things take a little time.'
					+ ' Tell me which activities interest you most, when you intend to come, and how long you would like to stay.'
					+ ' Then I can let you know whether there is another group visiting around that time that you could merge with, and'
					+ ' whether the season is well suited to the activities you want to do. Keep in mind that the dry season in'
					+ ' The Gambia runs from November to May or June, and many people find that the most enjoyable time to visit.',
				imageFitType: 'cover',
			}
		];

	let activeSlide : Element | null = null;

	function createCarrousel() : void
	{
		for (let i : number = 0; i < carrouselData.length; i++)
		{
			const cardTitle : HTMLHeadingElement = document.createElement('h1');

			cardTitle.id = "slide" + (i + 1).toString() + "cardTitle";
			cardTitle.textContent = carrouselData[i]!.title;
			cardTitle.classList.add("cardTitle");

			const cardContent: HTMLParagraphElement = document.createElement('p');

			cardContent.id = "slide" + (i + 1).toString() + "cardContent";
			cardContent.textContent = carrouselData[i]!.body;
			cardContent.classList.add("cardContent");

			const card : HTMLDivElement = document.createElement('div');

			card.id = "slide" + (i + 1).toString() + "card";
			card.appendChild(cardTitle);
			card.appendChild(cardContent);
			card.classList.add("card");

			const BackgroundImage : HTMLImageElement = document.createElement('img');

			BackgroundImage.id = "slide" + (i + 1).toString() + "BackgroundImage";
			BackgroundImage.src = carrouselData[i]!.imagePath;
			BackgroundImage.alt = carrouselData[i]!.imageAlt;
			BackgroundImage.classList.add("BackgroundImage");
			BackgroundImage.classList.add(carrouselData[i]!.imageFitType);

			const BackgroundWash : HTMLDivElement = document.createElement('div');

			BackgroundWash.id =  "slide" + (i + 1).toString() + "BackgroundWash";
			BackgroundWash.classList.add("BackgroundWash");

			const image : HTMLImageElement = document.createElement('img');

			image.id = "slide" + (i + 1).toString() + "image";
			image.src = carrouselData[i]!.imagePath;
			image.alt = carrouselData[i]!.imageAlt;
			image.classList.add("image");
			image.classList.add(carrouselData[i]!.imageFitType);

			const newSlide : HTMLDivElement = document.createElement('div');

			newSlide.id = "slide" + (i + 1).toString();
			newSlide.appendChild(BackgroundImage);
			newSlide.appendChild(BackgroundWash);
			newSlide.appendChild(image);
			newSlide.appendChild(card);
			newSlide.classList.add("slide");

			carrouselElement.value!.appendChild(newSlide);
		}
		activeSlide = document.getElementById("slide1");
	}

	async function onCarrouselScrollEnd() : Promise<void>
	{
		let closestSlideIndex : number | null = null;
		let closestDistance : number | null = null;
		for (let i : number = 0; i < carrouselElement.value!.children.length; i++)
		{
			const slide : HTMLElement = carrouselElement.value!.children[i] as HTMLElement;
			const distance : number = Math.abs(slide.offsetTop - carrouselElement.value!.scrollTop);

			if (closestDistance == null || distance < closestDistance)
			{
				closestDistance = distance;
				closestSlideIndex = i;
			}
		}

		const closestSlide : HTMLElement = carrouselElement.value!.children[closestSlideIndex!]! as HTMLElement;

		if (closestSlide == activeSlide)
		{
			console.log("closestSlide offsetTop = " + closestSlide!.offsetTop);
			console.log("carrouselElement scrollTop = " + carrouselElement.value!.scrollTop);
			if (closestSlide!.offsetTop > Math.round(carrouselElement.value!.scrollTop) && closestSlideIndex != 0)
			{
				activeSlide = carrouselElement.value!.children[closestSlideIndex! - 1] as HTMLElement;
			}
			else if
				(
					closestSlide!.offsetTop < Math.round(carrouselElement.value!.scrollTop)
					&&
					closestSlideIndex! != carrouselElement.value!.children.length - 1
				)
			{
				activeSlide = carrouselElement.value!.children[closestSlideIndex! + 1] as HTMLElement;
			}
			else
			{
				return;
			}
		}
		else
		{
			activeSlide = closestSlide;
		}

		const scrollIntoViewOptions : ScrollIntoViewOptions = {behavior: "smooth"};

		activeSlide!.scrollIntoView(scrollIntoViewOptions);
	}

	function createAndSetUpCarrousel() : void
	{
		createCarrousel();
		carrouselElement.value!.addEventListener("scrollend", onCarrouselScrollEnd);
	}

	onMounted(createAndSetUpCarrousel);
</script>

<template>
	<div
		id="carrousel"
		ref="carrouselElement"
	/>
</template>

<style>
	html
	{
		/*todo understand these gradients*/
		background:
			radial-gradient(circle at top, rgba(53, 94, 59, 0.26), transparent 38%),
			linear-gradient(180deg, #03150d 0%, #062117 48%, #03110b 100%);
		height: 100dvh;
		width: 100dvw;
	}

	body
	{
		height: 100%;
		width: 100%;
		margin: 0;
	}

	#app
	{
		height: 100%;
		width: 100%;
	}

	#carrousel
	{
		width: 100%;
		height: 100%;
		overflow-y: auto;
		overflow-x: auto;
		display: flex;
		flex-direction: column;
	}

	.slide
	{
		height: 100%;
		width: 100%;
		position: relative;
		flex-shrink: 0;
	}

	.BackgroundImage
	{
		height: 100%;
		width: 100%;
		position: absolute;
		filter: blur(30px) brightness(.38) saturate(.92);
	}

	.BackgroundWash
	{
		height: 100%;
		width: 100%;
		position: absolute;
		background: radial-gradient(circle at center, #114c3024, #114c3038 30%, #03110bbd), linear-gradient(180deg, #03110b14, #03110b5c);
	}

	.image
	{
		--margin-bottom: .65rem;
		--margin-top: .65rem;
		--margin-horizontal: .65rem;
		margin: var(--margin-top) var(--margin-horizontal) var(--margin-bottom);
		height: calc(100% - var(--margin-bottom) - var(--margin-top));
		width: calc(100% - (var(--margin-horizontal) * 2));
		position: absolute;
	}

	.cover
	{
		object-fit: cover;
	}

	.contain
	{
		object-fit: contain;
	}

	.card
	{
		--margin: 1.5rem;
		--padding: 1rem;
		--bottom: 0rem;
		position: absolute;
		z-index: 1;
		bottom: var(--bottom);
		background: rgba(4, 18, 12, 0.34);
		margin: var(--margin);
		padding: var(--padding);
		background: linear-gradient(180deg, #04120c24, #04120c57);
		backdrop-filter: blur(10px);
		max-width: calc(100% - var(--margin) * 2 - var(--padding) * 2);
	}

	.cardTitle
	{
		color: #f5eddc;
		font-family: Iowan Old Style, Palatino;
		font-weight: 800;
		text-shadow: 0 .08em .9em rgba(0, 0, 0, .34);
		margin: 0;
	}

	.cardContent
	{
		--width: 30rem;
		font-size: 0.9rem;
		line-height: 1.5;
		font-weight: 650;
		color: #f5eddceb;
		font-family: Avenir Next, Segoe UI, sans-serif;
		text-shadow: 0 .08em .85em rgba(0, 0, 0, .3);
		max-width: 100%;
		width: var(--width);
		min-width: min-content;
		text-wrap: balance;
	}

	@media all and (max-width: 50rem)
	{
		.card
		{
			--margin: 0.75rem;
			--bottom: 3rem;
		}

		.contain.image
		{
			--margin-bottom: 8rem;
		}
		
		.cardContent
		{
			--width: 100%;
		}
	}
</style>

