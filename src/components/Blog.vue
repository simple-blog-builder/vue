<template>
	<div>
		<h1>{{ blog.config.blogTitle }}</h1>
		<h2 v-if="blog.config.blogSubtitle !== 'No Description Provided'">{{ blog.config.blogSubtitle }}</h2>

		<div v-for="post in blog.entries" :key="post.name">
		</div>

	</div>
</template>

<script lang="ts">
import { ref } from "vue";
import { SBBCore } from "../../../core/src/index";

export default {
	name: "Blog",
	props: {
		title: String,
		desctiption: String,
	},
	data() {
		return {
			blog: ref(
				SBBCore.createBlog({
					blogTitle: this.title || "No Title Provided",
					blogSubtitle: this.desctiption || "No Description Provided",
				})
			),
			posts: [],
		};
	},
	created() {
		this.fetchPosts().then((posts: File[]) => {
			this.blog.loadEntries(posts);
		});
	},
	methods: {
		async fetchPosts() {
			const fetchDomain =
				"https://raw.githubusercontent.com/simple-blog-builder/core/main/example/";
			const postList = ["06-03-1997_blog-example.md"];

			const posts = await Promise.all(
				postList.map(async (post) => {
					const response = await fetch(fetchDomain + post);
					const data = await response.text();
					return new File([data], post, { type: "text/markdown" });
				})
			);

			return posts;
		},
	},
};
</script>

<style lang="scss">
h1 {
	color: red;
}
</style>
