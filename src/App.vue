<template>
	<v-app>
		<div id="bg">
			<router-view/>
		</div>
	</v-app>
</template>

<style>
	#bg{
		background: black;
		color: white;
		height: 100%;
	}
	body, html {
		background: #000;
	}
</style>