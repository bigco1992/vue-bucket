<template>
	<footer>
		<a href="https://github.com/pepperKIM/vue-bucket" target="_blank"
			>View GitHub source</a
		>
		<div>© 2021 BigCO</div>
	</footer>
</template>

<script>
export default {};
</script>

<style scoped>
footer {
	position: fixed;
	bottom: 0;
	width: 100%;
	height: 52px;
	padding: 0 20px;
	margin-top: 50px;
	font-size: 14px;
	display: flex;
	justify-content: space-between;
	align-items: center;
	background: #03a9f4;
	transition: 0.5s;
	color: white;
	z-index: 1001;
}

footer.active {
	background: #f43648;
}

footer a {
	color: white;
}
</style>
