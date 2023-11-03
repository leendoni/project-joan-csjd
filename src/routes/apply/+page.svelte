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

<div class="flex flex-col w-screen h-screen items-center overflow-x-hidden">
	<div class="h-14 navbar navbar-sticky navbar-bordered navbar-no-boxShadow navbar-glass">
		<div class="navbar-start">
			<a href="/login">
				<button
					type="button"
					class="btn btn-outline">Back to Login Page</button>
			</a>
		</div>
		<div class="navbar-end">
			<p>Student Application</p>
		</div>
	</div>
	<div class="flex flex-col w-full lg:w-1/2 pt-24 pb-12 px-8 gap-8">
		<div class="flex flex-col">
			<h1 class="text-2xl font-semibold">Student Application</h1>
			<p class="text-sm">Fill out this application if you aspire to be a student of CSJD.</p>
		</div>
		<div class="divider my-0" />
		<div class="flex flex-col">
			<h1 class="text-lg font-semibold underline">Academic Information</h1>
		</div>
		<div class="form-group flex lg:grid lg:grid-cols-2">
			<div class="form-field w-full">
				<label
					for="acadAY"
					class="form-label">Academic Year</label>
				<input
					id="acadAY"
					class="input max-w-full"
					value="2023-2024"
					readonly />
			</div>
			<div class="form-field w-full">
				<label
					for="userDP"
					class="form-label">Department</label>
				<select
					id="userDP"
					class="select">
					<option>Elementary</option>
					<option>Junior High School</option>
					<option>Senior High School</option>
				</select>
			</div>
		</div>
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
					class="form-label">First Name</label>
				<input
					id="userMN"
					class="input max-w-full" />
			</div>
			<div class="form-field w-full">
				<label
					for="userSF"
					class="form-label">Suffix</label>
				<input
					id="userSF"
					class="input max-w-full" />
			</div>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field lg:w-1/2">
				<label
					for="userSX"
					class="form-label">Gender</label>
				<select class="select">
					<option>Male</option>
					<option>Female</option>
				</select>
			</div>
			<div class="form-field w-full">
				<label
					for="userAD"
					class="form-label">Address</label>
				<input
					id="userAD"
					class="input max-w-full" />
			</div>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field w-full">
				<label
					for="userCN"
					class="form-label">Contact Number</label>
				<input
					id="userCN"
					class="input max-w-full" />
			</div>
			<div class="form-field w-full">
				<label
					for="userEC"
					class="form-label">Emergency Contact Number</label>
				<input
					id="userEC"
					class="input max-w-full" />
			</div>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field w-full">
				<label
					for="userMA"
					class="form-label">Mother's Name</label>
				<input
					id="userMA"
					class="input max-w-full" />
			</div>
			<div class="form-field w-full">
				<label
					for="userFA"
					class="form-label">Father's Name</label>
				<input
					id="userFA"
					class="input max-w-full" />
			</div>
			<div class="form-field w-full">
				<label
					for="userGA"
					class="form-label">Guardian's Name</label>
				<input
					id="userGA"
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
				class="w-full lg:w-1/3 btn btn-outline-primary">
				Submit Application</button>
		</div>
	</div>
</div>
