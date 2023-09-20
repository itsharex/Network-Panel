<template>
	<el-container>

		<el-main>
			<MainUI :isVisible="isVisible" />
			<br>
			<IPinfo :isVisible="isVisible" />
		</el-main>
	</el-container>
</template>

<script lang="ts" setup>
	import MainUI from "./components/Main.vue"
	import IPinfo from "./components/IPinfo.vue"
	import header_icon from "./assets/logo.svg"
	import { ref } from 'vue'
	const isVisible = ref(true)
	document.addEventListener("visibilitychange", function () {
		var string = document.visibilityState
		if (string === 'hidden') isVisible.value = false
		else isVisible.value = true
	})
</script>

<style>

</style>
