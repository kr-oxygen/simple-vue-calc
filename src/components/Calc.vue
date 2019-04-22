<template>
	<div class="calculator">
		<div id="expressions" class="expressions display">
			<div v-for="(part, index) in splitedExpression" :key="index">
				{{part}}
			</div>
		</div>
		<div class="result display">{{calculatedResutl || '0'}}</div>
		<div @click="allClear" class="btn double-column">AC</div>
		<div @click="backSpace" class="btn">⌫</div>
		<div @click="append('/')" class="btn operator">÷</div>
		<div @click="append('7')" class="btn">7</div>
		<div @click="append('8')" class="btn">8</div>
		<div @click="append('9')" class="btn">9</div>
		<div @click="append('*')" class="btn operator">*</div>
		<div @click="append('4')" class="btn">4</div>
		<div @click="append('5')" class="btn">5</div>
		<div @click="append('6')" class="btn">6</div>
		<div @click="append('-')" class="btn operator">-</div>
		<div @click="append('1')" class="btn">1</div>
		<div @click="append('2')" class="btn">2</div>
		<div @click="append('3')" class="btn">3</div>
		<div @click="append('+')" class="btn operator">+</div>
		<div @click="append('0')" class="btn zero-column">0</div>
		<div @click="result" class="btn operator">=</div>
	</div>
</template>

<script>
const operators = new Set(['/', '*', '-', '+'])

function getFormattedNumber(str) {
	let sign = ''
	
	if (str.startsWith('-')) {
		str = str.substr(1);
		sign = '-'
	}

	const numberOfChunks = Math.ceil(str.length / 3)
	const chanks = new Array(numberOfChunks)

	for (let i = numberOfChunks - 1, o = str.length - 3; i >= 0; --i, o -= 3) {
		let length = 3

		if (o < 0) {
			length = length + o
			o = 0
		}

		chanks[i] = str.substr(o, length)
	}

	return `${sign}${chanks.join(',')}`
}

export default {
	data() {
		return {
			current: ''
		}
	},
	methods: {
		append(value) {
			const lastIndex = this.current.length - 1

			if (!this.current && value === '0') {
				return
			}

			if (operators.has(value)) {
				if (!this.current) {
					return
				}

				if (operators.has(this.current[lastIndex])) {
					this.current = `${this.current.substr(0, lastIndex)}${value}`

					return
				}
			}
			
			this.current = `${this.current}${value}`
		},

		allClear() {
			this.current = ''
		},

		backSpace() {
			this.current = this.current.substr(0, this.current.length - 1)
		},

		result() {
			this.current = `${this.calculatedResutl}`.replace(/,/g, '')
		}
	},

	computed: {
		calculatedResutl() {
			const lastIndex = this.current.length - 1
			let expression = operators.has(this.current[lastIndex])
				? this.current.substr(0, lastIndex)
				: this.current

			const result = Math.ceil(eval(expression))
			
			return isNaN(result)
				? ''
				: getFormattedNumber(new String(result))
		},

		splitedExpression() {
			const parts = this.current
				.split(/(\/|\*|-|\+)/)
				.map(part => {
					if (part.length < 4) {
						return part
					} else {
						return getFormattedNumber(part)
					}
				})

			return parts
		}
	},

	watch: {
		splitedExpression: function () {
			const expressions = document.getElementById('expressions')

			expressions.scrollTop = expressions.scrollHeight
		}
	}
}
</script>

<style scoped>
.calculator {
	width: 400px;
	margin: 0 auto;
	font-size: 40px;
	display: grid;
	grid-template-columns: repeat(4, 1fr);
	grid-auto-rows: minmax(50px, auto);
}

.display {
	grid-column: 1/5;
	background-color: #333;
	color: white;
	padding: 2px 10px 2px 10px;
	text-overflow: ellipsis;
	overflow: hidden;
	white-space: nowrap;
}

.double-column {
	grid-column: 1/3;
}

.zero-column {
	grid-column: 1/4;
}

.btn {
	background-color: #f2f2f2;
	border: 1px solid #999;
}

.operator {
	background-color: orange;
	color: white;
}

.expressions {
	display: flex;
	flex-flow: row wrap;
	justify-content: flex-end;
	overflow-y: auto;
	height: 60px;
	font-size: 20px;
}

.result {
	display: flex;
	flex-flow: row wrap;
	justify-content: flex-end;
}
</style>
