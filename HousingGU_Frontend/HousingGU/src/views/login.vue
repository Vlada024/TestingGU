<script setup>
	import axios from "axios";
	import { ref, onMounted } from "vue";
	import { toast } from "vue3-toastify";
	import "vue3-toastify/dist/index.css";
	import { useRouter, useRoute } from "vue-router";
	import { userStore } from "../stores/userStore";
	const userInfo = userStore();
	const route = useRoute();
	const router = useRouter();
	const formUserName = ref("");
	const formPassword = ref("");

	const formVisible = ref(false);
	onMounted(() => {
		formVisible.value = true;
	});
	async function login() {
		let loginCred = {
			userName: formUserName.value,
			password: formPassword.value,
		};
		await axios
			.post("/login", loginCred, {
				withCredentials: true,
			})
			.then(function (response) {
				const { data } = response;
				if (data.success) {
					userInfo.setUserInfo(data.user.username, data.user.admin, data.user.email, data.user.phoneNumber, data.token, data.user.profilePicture, data.user.id, data.user.PreferenceFilled);
					toast.success("Sign in successfully", {
						position: toast.POSITION.BOTTOM_RIGHT,
						theme: "colored",
					});
					axios.defaults.headers.common["Authorization"] = `Bearer ${data.token}`; //enable JWT Token
					setTimeout(() => {
						if (data.user.PreferenceFilled == false) {
							router.push("/roomatePreference");
						} else {
							router.push("/profile");
						}
					}, 1500);
				}
			})
			.catch(function (error) {
				//console.log(error + "Error heeeeeeeeeeeeeeeeeeeeeeeere");
				if ((error.name = "AxiosError")) {
					toast.error(error.response.data.message, {
						position: toast.POSITION.BOTTOM_RIGHT,
						theme: "colored",
					});
				} else {
					toast.error(error.response.data.message, {
						position: toast.POSITION.BOTTOM_RIGHT,
						theme: "colored",
					});
				}

				return;
			});
	}
</script>

<template>
	<div class="login-page">
		<div class="login-container">
			<div class="login-form">
				<form :class="{ 'slide-in': formVisible }">
					<img src="../assets/img/logo.png" alt="Logo" class="LogoImg" />
					<h2>Login</h2>
					<div class="mb-3">
						<label for="Username" class="form-label">Username</label>
						<input type="text" class="form-control" id="Username" placeholder="Enter your username" v-model="formUserName" />
					</div>
					<div class="mb-3">
						<label for="password" class="form-label">Password</label>
						<input type="password" class="form-control" id="password" placeholder="Enter your password" v-model="formPassword" />
					</div>
					<button type="button" @click="login()" class="btn btn-primary">Login</button>
					<div class="text-center mt-3">
						<button type="button" class="btn btn-secondary btn-sm" @click="router.push({ path: '/' })">Forgot Password?</button>
					</div>
					<div class="text-center mt-3">
						<button type="button" class="btn btn-primary btn-sm" @click="router.push({ path: '/register' })">Don't have an account?</button>
					</div>
				</form>
			</div>
			<div class="dashboard">
				<img src="../assets/img/login.jpg" alt="Dashboard Image" class="sideImg" />
			</div>
		</div>
	</div>
</template>

<style scoped>
	.LogoImg {
		width: 20vw;
	}
	.login-container {
		display: flex;
		height: 95vh;
		overflow-x: hidden;
	}
	.login-container .login-form {
		flex: 0 0 50%;
		background: #f8f9fa;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 50px;
	}

	.login-container .login-form form {
		max-width: 400px;
		width: 100%;
	}

	.login-container .login-form h2 {
		text-align: center;
		margin-bottom: 30px;
	}

	.login-container .dashboard {
		background: #f8f9fa;
		flex: 0 0 50%;
		display: flex;
		justify-content: center;
		align-items: center;
		color: #fff;
	}
	.login-container .dashboard img {
		width: 100%;
		height: 100%;
		clip-path: polygon(21% 0, 100% 0, 79% 100%, 0% 100%);
	}

	.login-container .dashboard a {
		color: #fff;
		text-decoration: underline;
	}
	.slide-in {
		animation: slideInFromLeft 0.6s ease-in-out forwards;
	}

	@keyframes slideInFromLeft {
		from {
			transform: translateX(-140%);
		}
		to {
			transform: translateX(0);
		}
	}
</style>
