<template>
	<div>
		<div class="main list-container contents">
			<PageHeader></PageHeader>
			<ul v-if="postItems">
				<li v-for="item in postItems" :key="item._id">
					<div class="post-title">
						<router-link :to="`/post/${item._id}`">{{
							item.title
						}}</router-link>
					</div>
					<div class="post-contents">
						{{ item.contents }}
					</div>
					<div class="post-time">
						{{ item.createdAt | formatDate }}
						<i class="icon ion-md-create" @click="editPost(item._id)"></i>
						<i class="icon ion-md-trash" @click="removePost(item._id)"></i>
					</div>
				</li>
			</ul>
			<LoadingSpinner v-else></LoadingSpinner>
		</div>
		<CreateButton></CreateButton>
	</div>
	<!-- <div class="swiperHeader">
		<section>
			<Swiper :options="swiperOptions" class="swiper-container">
				<PageHeader></PageHeader>
				<div v-if="postItems" class="swiper-wrapper">
					<SwiperSlide
						v-for="item in postItems"
						:key="item._id"
						class="swiper-slide"
					>
						<div class="testimonialBox">
							<img src="quote.png" class="quote" />
							<div class="post-title">
								<router-link :to="`/post/${item._id}`">{{
									item.title
								}}</router-link>
							</div>
							<div class="content">
								<p>
									{{ item.contents }}
								</p>
								<div class="details">
									<div class="imgBx">
										<img src="user1.jpg" />
									</div>
									<h3>Someone Famous<br /><span>Creative Designer</span></h3>
									<div class="post-time">
										{{ item.createdAt | formatDate }}
										<i
											class="icon ion-md-create"
											@click="editPost(item._id)"
										></i>
										<i
											class="icon ion-md-trash"
											@click="removePost(item._id)"
										></i>
									</div>
								</div>
							</div>
						</div>
					</SwiperSlide>
				</div>
				<LoadingSpinner v-else></LoadingSpinner>
			</Swiper>
			<CreateButton></CreateButton>
		</section>
	</div> -->
</template>

<script>
import PageHeader from '@/components/common/PageHeader.vue';
import LoadingSpinner from '@/components/common/LoadingSpinner.vue';
import CreateButton from '@/components/common/CreateButton.vue';
import { fetchPosts, deletePostById } from '@/api/posts.js';
import bus from '@/utils/bus.js';
// import { Swiper, SwiperSlide } from 'vue-awesome-swiper';
// import 'swiper/css/swiper.css';

// var swiper = new Swiper('.swiper-container', {
//       effect: 'coverflow',
//       grabCursor: true,
//       centeredSlides: true,
//       slidesPerView: 'auto',
//       coverflowEffect: {
//         rotate: 0,
//         stretch: 0,
//         depth: 100,
//         modifier: 2,
//         slideShadows: true,
//       },
//       loop: false,
//     });
export default {
	components: {
		CreateButton,
		PageHeader,
		LoadingSpinner,
		// Swiper,
		// SwiperSlide,
	},
	data() {
		return {
			postItems: null,
			// swiperOptions: {
			// 	effect: 'coverflow',
			// 	grabCursor: true,
			// 	centeredSlides: true,
			// 	slidesPerView: 'auto',
			// 	coverflowEffect: {
			// 		rotate: 50,
			// 		stretch: 0,
			// 		depth: 100,
			// 		modifier: 1,
			// 		slideShadows: true,
			// 	},
			// 	pagination: {
			// 		el: '.swiper-pagination',
			// 	},
			// },
		};
	},
	methods: {
		async fetchData() {
			try {
				const {
					data: { posts: postItems },
				} = await fetchPosts();
				this.postItems = postItems;
				return;
			} catch (error) {
				bus.$emit('show:toast', `error`);
			}
		},
		editPost(id) {
			this.$router.push(`/post/${id}`);
		},
		async removePost(id) {
			try {
				if (confirm('Delete it?')) {
					const response = await deletePostById(id);
					await this.fetchData();
					bus.$emit('show:toast', `${response.data.title} was deleted`);
				}
			} catch (error) {
				bus.$emit('show:toast', `error`);
			}
		},
	},
	created() {
		this.fetchData();
	},
};
</script>

<style scoped>
.list-container {
	padding-top: 13px;
}
.list-container.sticky {
	margin-top: 76px;
}
ul {
	display: flex;
	flex-wrap: wrap;
}
ul > li {
	position: relative;
	flex-grow: 1;
	width: 320px;
	height: 250px;
	margin: 7px;
	padding: 10px 20px;
	background: white;
	box-shadow: 0 20px 20px rgba(0, 0, 0, 0.08);
	border-radius: 3px;
}
.post-title {
	font-size: 24px;
	font-weight: 600;
	margin-bottom: 0.5rem;
}
.post-contents {
	height: 160px;
	overflow-y: auto;
	font-size: 18px;
}
.post-time {
	position: absolute;
	bottom: 4px;
	right: 5px;
	font-size: 14px;
	color: #9e9e9e;
}
.icon {
	font-size: 1.3rem;
	cursor: pointer;
	color: #364f6b;
	padding-right: 0.4rem;
}
.icon:hover {
	color: #3fc1c9;
}
.icon:active {
	color: #fc5185;
}
.ion-md-create {
	padding-left: 0.1rem;
}
/* .swiperHeader {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

section {
	position: relative;
	width: 100%;
	min-height: 100vh;
	display: flex;
	justify-content: center;
	align-items: center;
	background: #2196f3;
	overflow: hidden;
}

.swiper-container {
	width: 100%;
	padding-top: 50px;
	padding-bottom: 50px;
}

.swiper-slide {
	background-position: center;
	background-size: cover;
	width: 320px;
	box-shadow: 0 15px 50px rgba(0, 0, 0, 0.2);
	filter: blur(4px);
	background: #d1ebff;
	border-radius: 10px;
}

.swiper-slide-active {
	filter: blur(0px);
	background: #fff;
}

.testimonialBox {
	position: relative;
	width: 100%;
	padding: 40px;
	padding-top: 90px;
	color: #999;
}

.testimonialBox .quote {
	position: absolute;
	top: 20px;
	right: 30px;
	opacity: 0.2;
}

.testimonialBox .details {
	display: flex;
	align-items: center;
	margin-top: 20px;
}

.testimonialBox .details .imgBx {
	position: relative;
	width: 60px;
	height: 60px;
	border-radius: 50%;
	overflow: hidden;
	margin-right: 10px;
}

.testimonialBox .details .imgBx img {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	object-fit: cover;
}

.testimonialBox .details h3 {
	font-size: 16px;
	font-weight: 400;
	letter-spacing: 1px;
	color: #2196f3;
	line-height: 1.1em;
}

.testimonialBox .details h3 span {
	font-size: 12px;
	color: #666;
}

.swiper-container-3d .swiper-slide-shadow-left,
.swiper-container-3d .swiper-slide-shadow-right {
	background-image: none;
} */
</style>
