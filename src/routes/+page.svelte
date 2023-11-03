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

	let isResetting = false,
		isUpdating = false,
		isUpdated = false;

	let hasIncorrectUN = false,
		hasIncorrectPW = false;

	let loclID = '',
		loclLN = '',
		loclFN = '',
		loclMN = '',
		loclCL = '';
</script>
