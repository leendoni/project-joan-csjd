<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	import { initializeApp } from 'firebase/app';

	const firebaseConfig = {
		apiKey: 'AIzaSyCJXvnm6dIMnD8AQtNUw-OSZV8yq1HMDXI',
		authDomain: 'joan-csjd.firebaseapp.com',
		projectId: 'joan-csjd',
		storageBucket: 'joan-csjd.appspot.com',
		messagingSenderId: '747539872930',
		appId: '1:747539872930:web:c2983b9785371391d26bfb'
	};

	const app = initializeApp(firebaseConfig);

	let isChecked = false;

	let loclID = '',
		loclLN = '',
		loclFN = '',
		loclMN = '',
		loclCL = '';
</script>

<div class="flex flex-col w-screen h-screen items-center">
	<div class="h-14 navbar navbar-sticky navbar-bordered navbar-no-boxShadow navbar-glass">
		<div class="navbar-start">
			<a href="/login">
				<button
					type="button"
					class="btn btn-outline">Back to Login Page</button>
			</a>
		</div>
		<div class="navbar-end">
			<p>Parent Registration</p>
		</div>
	</div>
	<div class="flex flex-col w-full lg:w-3/5 pt-24 pb-12 px-8 gap-8">
		<div class="flex flex-col">
			<h1 class="text-2xl font-semibold">Parent Registration</h1>
			<p class="text-sm">Fill out this application if your child is a student of CSJD.</p>
			<br />
			<p class="text-sm">
				Wrong form? You maybe looking for the <a
					href="/apply"
					class="underline">Student Application</a> form.
			</p>
		</div>
		<div class="divider my-0" />
		<div class="flex flex-col">
			<h1 class="text-lg font-semibold underline">Personal Information</h1>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field w-full">
				<label
					for="userLN"
					class="form-label">Last Name</label>
				<input
					id="userLN"
					class="input max-w-full" />
			</div>
			<div class="form-field w-full">
				<label
					for="userFN"
					class="form-label">Middle Name</label>
				<input
					id="userFN"
					class="input max-w-full" />
			</div>
			<div class="form-field w-full">
				<label
					for="userMN"
					class="form-label">Last Name</label>
				<input
					id="userMN"
					class="input max-w-full" />
			</div>
		</div>
		<div class="divider my-0" />
		<div class="flex flex-col lg:flex-row items-center justify-between">
			<div class="flex flex-row items-center gap-2">
				<input
					type="checkbox"
					class="checkbox" />
				<p>I certify that the above information is correct.</p>
			</div>
			<br />
			<button
				type="button"
				class="w-full lg:w-1/3 btn btn-outline-primary">Register</button>
		</div>
	</div>
</div>
