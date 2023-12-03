<script>
import axios from 'axios';

export default {
	data() {
		return {
			units: 'metric', // imperial
			unit_sign: '°C', // °F
			city: 'Montreal',
			error: '',
			info: null,
		}
	},
	computed: {
		cityName() {
			return '"' + this.city + '"';
		},
		showTemp() {
			return 'Temperature: ' + this.info.main.temp + ' ' + this.unit_sign;
		},
		showFeelsLike() {
			return 'Feels like: ' + this.info.main.feels_like + ' ' + this.unit_sign;
		},
		showMinTemp() {
			return 'Min temperature: ' + this.info.main.temp_min + ' ' + this.unit_sign;
		},
		showMaxTemp() {
			return 'Max temperature: ' + this.info.main.temp_max + ' ' + this.unit_sign;
		},
	},
	methods: {
		changeUnits(unit) {
			if (unit == 'C') {
				this.units = 'metric';
				this.unit_sign = '°C';
			}
			else {
				this.units = 'imperial';
				this.unit_sign = '°F';
			}

			if (this.city.length != 0) {
				this.getWeather();
			}
		},
		getWeather() {
			const appid = import.meta.env.VITE_OPENWEATHERMAP_APPID;
			const city_name = this.city.trim();

			if (city_name.length < 2) {
				this.error = 'Enter more than 1 character';
				return false;
			}

			this.error = '';

			axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${city_name}&units=${this.units}&appid=${appid}`)
				.then(res => {
					this.info = res.data;
				})
				.catch(error => {
					if (error.response && error.response.status === 404) {
						this.info = null;
						this.error = 'This city is not found';
					}
				});
		}
	}
}
</script>

<template>
	<div class="wrapper">
		<button class="unit_button" :class="this.units == 'metric' ? 'active' : 'inactive'" @click.prevent="changeUnits('C')">°C</button>
		<button class="unit_button" :class="this.units == 'imperial' ? 'active' : 'inactive'" @click.prevent="changeUnits('F')">°F</button>

		<h1>Meteo</h1>
		<p>Get meteo in {{ city.length != 0 ? cityName : "your city" }}</p>

		<input v-model="city" type="text" placeholder="Enter city name" value="" />

		<button v-if="city.length != 0" @click.prevent="getWeather()" class="button">Get meteo</button>
		<button v-else class="button" disabled>Get meteo</button>

		<p class="error">{{ error }}</p>

		<div class="info" v-if="info != null">
			<p>{{ showTemp }}</p>
			<p>{{ showFeelsLike }}</p>
			<p>{{ showMinTemp }}</p>
			<p>{{ showMaxTemp }}</p>
		</div>
	</div>
</template>

<style scoped>
.wrapper {
	width: 900px;
	height: 500px;
	border-radius: 50px;
	background: #1d101f;
	padding: 20px;
	text-align: center;
	color: #fff;
	font-family: Verdana;
}
.wrapper h1 {
	margin-top: 50px;
}
.wrapper p {
	margin-top: 20px;
}
.wrapper input {
	margin-top: 30px;
	background: transparent;
	border: 0;
	border-bottom: 2px solid #ff0000;
	font-size: 30px;
	color: #ffffff;
	padding: 8px;
	outline: none;
}
.wrapper input:focus {
	border-bottom-color: chartreuse;
}
.wrapper .button {
	background: #e3dc4b;
	color: #000000;
	border-radius: 10px;
	border: 1px solid #e3dc4b;
	padding: 10px;
	margin-left: 20px;
	cursor: pointer;
}
.wrapper .button:disabled {
	background: #545454;
	color: #ffffff;
	border-radius: 10px;
	border: 1px solid #545454;
	padding: 10px;
	margin-left: 20px;
}
.wrapper .active {
	background: #ff0000;
	color: #ffffff;
	border-radius: 3px;
	border: 1px solid #ff0000;
	padding: 10px;
	margin-left: 2px;
	cursor: pointer;
}
.wrapper .inactive {
	background: #545454;
	color: #000000;
	border-radius: 3px;
	border: 1px solid #545454;
	padding: 10px;
	margin-left: 2px;
	cursor: pointer;
}
.error {
	color: #ff0000;
}
</style>
