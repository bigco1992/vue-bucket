<template>
	<!-- <div class="contents">
		<div class="form-wrapper form-wrapper-sm">
			<PageHeader>Login</PageHeader>
			<form @submit.prevent="submitForm" class="form">
				<div>
					<label for="username">ID</label>
					<input type="text" id="username" v-model="username" />
				</div>
				<div>
					<label for="password">PW</label>
					<input type="password" id="password" v-model="password" />
				</div>
				<button class="btn">login</button>
			</form>
			<p class="log">
				{{ logMessage }}
			</p>
		</div>
	</div> -->
	<div class="userInfoHeader">
		<div class="userInfoBody">
			<div class="container">
				<div class="blueBg">
					<div class="box signin">
						<h2>이미 회원이신가요?</h2>
						<button @click="signinBtn" id="signinBtn" class="signinBtn">
							로그인
						</button>
					</div>
					<div class="box signup">
						<h2>회원이 아니신가요?</h2>
						<button @click="signupBtn" class="signupBtn">회원가입</button>
					</div>
				</div>
				<div class="formBx">
					<div class="form signinForm">
						<form @submit.prevent="submitForm">
							<h3>Sign In</h3>
							<input
								type="text"
								id="signinUsername"
								v-model="signinUsername"
								placeholder="Email"
							/>
							<input
								type="password"
								id="password"
								v-model="signinPassword"
								placeholder="Password"
							/>
							<button class="loginBtn">LOGIN</button>
							<!-- <a href="#" class="forgot">Forgot Password</a> -->
						</form>
					</div>
					<div class="form signupForm">
						<form @submit.prevent="registerUser">
							<h3>Sign Up</h3>
							<div>
								<input
									type="text"
									id="signupUsername"
									v-model="signupUsername"
									:class="usernameValidClass"
									placeholder="Email Address"
								/>
								<p class="validation-text warning" v-if="!isUsernameValid">
									이메일 형식이 잘못되었습니다.
								</p>
							</div>
							<div>
								<input
									type="password"
									id="signupPassword"
									v-model="signupPassword"
									:class="passwordValidClass"
									placeholder="Password"
								/>
								<p class="validation-text">
									<span class="warning" v-if="!isPasswordValid">
										Password must be over 8 letters
									</span>
								</p>
							</div>
							<div>
								<input
									type="text"
									id="signupNickname"
									v-model="signupNickname"
									:class="nicknameValidClass"
									placeholder="Nickname"
								/>
							</div>
							<button
								type="submit"
								class="loginBtn"
								:class="isButtonDisabled"
								:disabled="isButtonDisabled"
							>
								Sign Up
							</button>
						</form>
						<p class="log">
							{{ signupLogMessage }}
						</p>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import bus from '@/utils/bus.js';
import { signupUser } from '@/api/auth';
import { validateEmail, validatePassword } from '@/utils/validation';
// import PageHeader from './common/PageHeader.vue';

export default {
	// components: {
	// 	PageHeader,
	// },
	data() {
		return {
			signinUsername: '',
			signinPassword: '',
			signinLogMessage: '',
			signupUsername: '',
			signupPassword: '',
			signupNickname: '',
			signupLogMessage: '',
		};
	},
	computed: {
		usernameValidClass() {
			if (!this.signupUsername) {
				return;
			}
			return validateEmail(this.signupUsername) ? 'valid' : 'invalid';
		},
		isUsernameValid() {
			if (!this.signupUsername) {
				return true;
			}
			return validateEmail(this.signupUsername);
		},
		passwordValidClass() {
			if (!this.signupPassword) {
				return;
			}
			return validatePassword(this.signupPassword) ? 'valid' : 'invalid';
		},
		isPasswordValid() {
			if (!this.signupPassword) {
				return true;
			}
			return validatePassword(this.signupPassword);
		},
		nicknameValidClass() {
			return this.signupNickname ? 'valid' : null;
		},
		isButtonDisabled() {
			return !this.signupUsername ||
				!this.signupPassword ||
				!this.signupNickname ||
				!validateEmail(this.signupUsername) ||
				!validatePassword(this.signupPassword)
				? 'disabled'
				: null;
		},
	},
	methods: {
		async submitForm() {
			if (!this.signinUsername || !this.signinPassword) {
				alert('Fill in the account information');
				return;
			}
			try {
				const response = await this.$store.dispatch('LOGIN', {
					username: this.signinUsername,
					password: this.signinPassword,
				});
				bus.$emit('show:toast', response.data.message);
				this.$router.push('/main');
				this.initForm();
			} catch (error) {
				this.signinLogMessage = error.response.data;
				bus.$emit('show:toast', this.signinLogMessage);
			}
		},
		async registerUser() {
			try {
				await signupUser({
					username: this.signupUsername,
					password: this.signupPassword,
					nickname: this.signupNickname,
				});
				bus.$emit('show:toast', '회원가입이 완료되었습니다.');
				document.getElementById('signinBtn').click();
				this.initSignupForm();
			} catch (error) {
				if (error.response.status === 409) {
					this.signupLogMessage = `${this.signupUsername} already exists`;
				}
			}
		},
		initForm() {
			this.signinUsername = '';
			this.signinPassword = '';
		},
		initSignupForm() {
			this.signupUsername = '';
			this.signupPassword = '';
			this.signupNickname = '';
		},
		signupBtn() {
			const formBx = document.querySelector('.formBx');
			const body = document.querySelector('.userInfoBody');
			const header = document.querySelector('header');
			const footer = document.querySelector('footer');

			formBx.classList.add('active');
			body.classList.add('active');
			header.classList.add('active');
			footer.classList.add('active');
		},
		signinBtn() {
			const formBx = document.querySelector('.formBx');
			const body = document.querySelector('.userInfoBody');
			const header = document.querySelector('header');
			const footer = document.querySelector('footer');

			body.classList.remove('active');
			formBx.classList.remove('active');
			header.classList.remove('active');
			footer.classList.remove('active');
			this.initSignupForm();
		},
	},
};
</script>

<style scoped>
.userInfoHeader {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

.userInfoBody {
	position: relative;
	display: flex;
	justify-content: center;
	align-items: center;
	min-height: 100vh;
	background: #03a9f4;
	transition: 0.5s;
}

.userInfoBody.active {
	background: #f43648;
}

.container {
	position: relative;
	width: 800px;
	height: 500px;
	margin: 20px;
}
.blueBg {
	position: absolute;
	top: 40px;
	width: 100%;
	height: 420px;
	display: flex;
	justify-content: center;
	align-items: center;
	background: rgba(255, 255, 255, 0.2);
	box-shadow: 0 5px 45px rgba(0, 0, 0, 0.15);
}
.blueBg .box {
	position: relative;
	width: 50%;
	height: 100%;
	display: flex;
	justify-content: center;
	align-items: center;
	flex-direction: column;
}
.blueBg .box h2 {
	color: #fff;
	font-size: 14px;
	font-weight: 500;
	margin-bottom: 10px;
}
.blueBg .box button {
	cursor: pointer;
	padding: 10px 20px;
	background: #fff;
	color: #333;
	font-size: 16px;
	font-weight: 500;
	border: none;
}
.formBx {
	position: absolute;
	top: 0;
	left: 0;
	width: 50%;
	height: 100%;
	background: #fff;
	z-index: 1000;
	display: flex;
	justify-content: center;
	align-items: center;
	box-shadow: 0 5px 45px rgba(0, 0, 0, 0.25);
	transition: 0.5s ease-in-out;
	overflow: hidden;
}
.formBx.active {
	left: 50%;
}

.formBx .form {
	position: absolute;
	left: 0;
	width: 100%;
	padding: 50px;
	transition: 0.5s;
}

.formBx .signinForm {
	transition-delay: 0.25s;
}

.formBx.active .signinForm {
	left: -100%;
	transition-delay: 0s;
}

.formBx .signupForm {
	left: 100%;
	transition-delay: 0s;
}

.formBx.active .signupForm {
	left: 0;
	transition-delay: 0.25s;
}

.formBx .form form {
	width: 100%;
	display: flex;
	flex-direction: column;
}

.formBx .form form h3 {
	font-size: 1.5em;
	color: #333;
	margin-bottom: 20px;
	font-weight: 500;
}

.formBx .form form input {
	width: 100%;
	margin-top: 20px;
	padding: 10px;
	outline: none;
	font-size: 16px;
}

.formBx .form form button {
	width: 100%;
	margin-bottom: 20px;
	padding: 10px;
	outline: none;
	font-size: 16px;
	border: 1px solid #333;
}

.formBx .form form button {
	background: #03a9f4;
	border: none;
	color: #fff;
	cursor: pointer;
}

.formBx.active .signupForm button {
	background: #f43648;
}

.formBx .form form .forgot {
	color: #333;
	text-align: center;
	font-size: 16px;
}

@media (max-width: 991px) {
	.container {
		max-width: 400px;
		height: 650px;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.container .blueBg {
		top: 0;
		height: 100%;
	}
	.formBx {
		width: 100%;
		height: 500px;
		top: 0;
		box-shadow: none;
	}
	.blueBg .box {
		position: absolute;
		width: 100%;
		height: 150px;
		bottom: 0;
	}
	.box.signin {
		top: 0;
	}
	.formBx.active {
		left: 0;
		top: 150px;
	}
}

.form .validation-text {
	font-size: 12px;
	text-align: center;
	margin-top: 3px;
}

.loginBtn {
	margin-top: 20px;
}
</style>
