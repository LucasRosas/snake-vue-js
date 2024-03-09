<template>
	<div class="grid" :class="{ gameOver: gameOver }">
		<div v-if="gameOver" class="message" @click="reload()">Game Over</div>
		<div class="linha" v-for="(linha, i) in grid" :key="i">
			<div
				class="cell"
				v-for="(celula, j) in linha"
				:key="j"
				:class="{
					cobra: cobra.includes(i + '-' + j),
					green: green.includes(i + '-' + j),
				}"
			></div>
		</div>
	</div>
	score: {{ cobra.length - 3 }}
</template>

<script>
	export default {
		name: "App",
		data() {
			return {
				height: 20,
				width: 20,
				cobra: ["3-3", "3-4", "3-5"],
				green: ["5-6", "10-4"],
				direction: "Right",
				gameOver: false,
			};
		},
		computed: {
			grid() {
				return [...Array(20).keys()].map(() => [...Array(20).keys()]);
			},
		},
		methods: {
			reload() {
				location.reload();
			},
			nextTick() {
				window.addEventListener("keydown", this.handleKeyDown);
				if (this.gameOver) {
					return;
				}
				let head = this.cobra[this.cobra.length - 1].split("-").map((i) => +i);
				let i, j;
				switch (this.direction) {
					case "Left":
						i = head[0];
						j = head[1] - 1;

						break;
					case "Up":
						head = this.cobra[this.cobra.length - 1].split("-").map((i) => +i);
						i = head[0] - 1;
						j = head[1];

						break;
					case "Down":
						head = this.cobra[this.cobra.length - 1].split("-").map((i) => +i);
						i = head[0] + 1;
						j = head[1];
						break;
					case "Right":
						head = this.cobra[this.cobra.length - 1].split("-").map((i) => +i);
						i = head[0];
						j = head[1] + 1;
						break;
					default:
						break;
				}
				if (i > 19 || j > 19 || i < 0 || j < 0 || this.cobra.includes(i + "-" + j)) {
					this.gameOver = true;
					return;
				}
				this.cobra.push(i + "-" + j);

				head = this.cobra[this.cobra.length - 1];

				if (this.green.includes(head)) {
					this.green = this.green.filter((green) => {
						return green !== head;
					});
					let newGreen = Math.ceil(Math.random() * 19) + "-" + Math.ceil(Math.random() * 19);
					while (this.cobra.includes(newGreen)) {
						newGreen = Math.ceil(Math.random() * 19) + "-" + Math.ceil(Math.random() * 19);
					}
					this.green.push(newGreen);
					return;
				}
				this.cobra.shift();
			},

			handleKeyDown(event) {
				if (!event.key.includes("Arrow")) return;
				switch (event.key) {
					case "ArrowUp":
						if (this.direction == "Down") return;
						break;
					case "ArrowLeft":
						if (this.direction == "Right") return;
						break;
					case "ArrowRight":
						if (this.direction == "Left") return;
						break;
					case "ArrowDown":
						if (this.direction == "Up") return;
						break;
					default:
						break;
				}
				this.direction = event.key.split("Arrow")[1];
				window.removeEventListener("keydown", this.handleKeyDown);
			},
		},
		mounted() {
			window.setInterval(this.nextTick, 300);
		},
	};
</script>

<style scoped>
	.grid {
		border: 1px solid white;
		position: relative;
	}
	.message {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		font-size: 50px;
		color: salmon;
		font-weight: 800;
	}
	.message::after {
		content: "Reiniciar";
		font-size: 20px;
		color: white;
		font-weight: 800;
		position: absolute;
		bottom: -20px;
		left: 50%;
		transform: translate(-50%, 0);
		cursor: pointer;
	}
	.gameOver {
		border: 1px solid red;
		animation: blink 0.5s ease-in-out;
		opacity: 1;
	}
	@keyframes blink {
		from {
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}
	.linha {
		display: flex;
	}
	.cell {
		border: 1px solid #181818;
		width: 40px;
		height: 40px;
	}
	.cobra {
		background-color: greenyellow;
		border-radius: 5px;
	}
	.gameOver .cobra {
		background-color: red;
	}
	.green {
		background-color: green;
		border-radius: 50%;
		outline: 5px solid #181818;
		outline-offset: -5px;
	}
</style>
