<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	import { initializeApp } from 'firebase/app';
	import { doc, getDoc, getFirestore, setDoc } from 'firebase/firestore';

	const firebaseConfig = {
		apiKey: 'AIzaSyCJXvnm6dIMnD8AQtNUw-OSZV8yq1HMDXI',
		authDomain: 'joan-csjd.firebaseapp.com',
		projectId: 'joan-csjd',
		storageBucket: 'joan-csjd.appspot.com',
		messagingSenderId: '747539872930',
		appId: '1:747539872930:web:c2983b9785371391d26bfb'
	};

	const app = initializeApp(firebaseConfig);
	const db = getFirestore(app);

	import bcrypt from 'bcryptjs';

	let isChecked = false;
	let hasApplied = false;
	let userID = '';

	async function handleCreate() {
		let userST,
			userCL,
			userOL,
			userFP,
			userUN,
			userPW,
			userDP,
			userYR,
			userSC,
			userLR,
			userLN,
			userFN,
			userMN,
			userSF,
			userSX,
			userAD,
			userCN,
			userEC,
			userMA,
			userFA,
			userGA,
			userCP,
			userCR,
			userPR,
			userOF,
			userP1,
			userP2,
			userP3,
			userP4,
			userP5,
			userP6,
			userR1,
			userR2,
			userR3,
			userR4,
			userR5,
			userR6,
			userR7,
			userR8;

		userST = 'Pre-Registered';
		userCL = 'Student';
		userID = makeID();

		userDP = document.getElementById('userDP').value;
		userLR = document.getElementById('userLR').value;

		userLN = document.getElementById('userLN').value;
		userFN = document.getElementById('userFN').value;
		userMN = document.getElementById('userMN').value;
		userSF = document.getElementById('userSF').value;
		userSX = document.getElementById('userSX').value;
		userAD = document.getElementById('userAD').value;
		userCN = document.getElementById('userCN').value;
		userEC = document.getElementById('userEC').value;

		userMA = document.getElementById('userMA').value;
		userFA = document.getElementById('userFA').value;
		userGA = document.getElementById('userGA').value;

		const motherRadio = document.getElementById('motherRadio');
		const fatherRadio = document.getElementById('fatherRadio');
		const guardianRadio = document.getElementById('guardianRadio');

		if (motherRadio.checked) {
			userCP = userMA;
			userCR = 'Mother';
		}

		if (fatherRadio.checked) {
			userCP = userFA;
			userCR = 'Father';
		}

		if (guardianRadio.checked) {
			userCP = userGA;
			userCR = 'Guardian';
		}

		userP1 = document.getElementById('userP1').value;
		userP2 = document.getElementById('userP2').value;
		userP3 = document.getElementById('userP3').value;
		userP4 = document.getElementById('userP4').value;
		userP5 = document.getElementById('userP5').value;
		userP6 = document.getElementById('userP6').value;

		userUN = `${userLN.toLowerCase()}.${userID}@csjd.joan.cloud`;

		const defaultPassword = `${userLN.toLowerCase()}.${userID}`;
		const hashedPassword = await bcrypt.hash(defaultPassword, 10);

		const data = {
			userST,
			userCL,
			userID,
			userOL: false,
			userFP: false,
			userUN,
			userPW: hashedPassword,
			userDP,
			userYR: '',
			userSC: '',
			userLR,
			userLN,
			userFN,
			userMN,
			userSF,
			userSX,
			userAD,
			userCN,
			userEC,
			userMA,
			userFA,
			userGA,
			userCP,
			userCR,
			userPR: '',
			userOF: 0,
			userP1,
			userP2,
			userP3,
			userP4,
			userP5,
			userP6,
			userR1: false,
			userR2: false,
			userR3: false,
			userR4: false,
			userR5: false,
			userR6: false,
			userR7: false,
			userR8: false
		};

		try {
			const docRef = doc(db, 'csjd-main', 'data', 'users', userUN);
			await setDoc(docRef, data);

			console.log('Document written with ID: ', userUN);

			// handleAction('Successful', `Added user ${userUN}`, `${loclLN}, ${loclFN} ${loclMN}`);
			hasApplied = true;

			document.getElementById('userLR').value = '';
			document.getElementById('userLN').value = '';
			document.getElementById('userFN').value = '';
			document.getElementById('userMN').value = '';
			document.getElementById('userSF').value = '';
			document.getElementById('userSX').value = '';
			document.getElementById('userAD').value = '';
			document.getElementById('userCN').value = '';
			document.getElementById('userEC').value = '';
			document.getElementById('userMA').value = '';
			document.getElementById('userFA').value = '';
			document.getElementById('userGA').value = '';
			document.getElementById('userP1').value = '';
			document.getElementById('userP2').value = '';
			document.getElementById('userP3').value = '';
			document.getElementById('userP4').value = '';
			document.getElementById('userP5').value = '';
			document.getElementById('userP6').value = '';
		} catch (error) {
			console.error('Error adding document: ', error);
		}
	}

	// functions below must exist on all documents

	const modlID = 'A02';
	const modlNM = 'Student Application';

	let modlST = true;

	// local values
	let loclID = '',
		loclLN = '',
		loclFN = '',
		loclMN = '',
		loclCL = '';

	let activeMenu = null;

	// generate ID
	function makeID() {
		const charset = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
		let randomString = '';

		for (let i = 0; i < 6; i++) {
			const randomIndex = Math.floor(Math.random() * charset.length);
			randomString += charset.charAt(randomIndex);
		}

		return randomString;
	}

	// action logs
	function handleAction(axonST, axonDC, axonBY) {
		let axonID = makeID();

		const logRef = doc(db, 'csjd-main', 'data', 'logs', axonID);
		const logData = {
			axonID,
			axonST,
			axonDC,
			axonBY,
			axonON: modlNM,
			axonDT: new Date()
		};

		setDoc(logRef, logData)
			.then(() => {
				console.log('Action logged successfully.');
			})
			.catch((error) => {
				console.error('Error logging action:', error);
			});
	}

	// check module status
	async function checkModuleAccess() {
		try {
			const moduleDocRef = doc(db, 'csjd-main', 'defaults', 'module', `${modlNM}`);
			const moduleDocSnapshot = await getDoc(moduleDocRef);

			if (moduleDocSnapshot.exists()) {
				const moduleData = moduleDocSnapshot.data();
				modlST = moduleData.enabled;
			} else {
				console.log('Module document not found.');
			}
		} catch (error) {
			console.error('Error fetching module data:', error);
		}
	}

	onMount(async () => {
		checkModuleAccess();
	});
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
	{#if modlST}
		<div class="flex flex-col w-full lg:w-1/2 pt-24 pb-12 px-8 gap-8">
			<div class="flex flex-col">
				<h1 class="text-2xl font-semibold">Student Application</h1>
				<p class="text-sm">Fill out this application if you aspire to be a student of CSJD.</p>
			</div>
			<div class="flex flex-col">
				<h1 class="text-sm font-semibold underline">Academic Information</h1>
			</div>
			<div class="form-group flex flex-col lg:flex-row w-full">
				<div class="form-field w-full">
					<label
						for="userLR"
						class="form-label">Learner's Ref. No.</label>
					<input
						id="userLR"
						class="input max-w-full" />
				</div>
				<div class="form-field w-full">
					<label
						for="userDP"
						class="form-label">Current Department</label>
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
				<h1 class="text-sm font-semibold underline">Personal Information</h1>
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
						class="form-label">First Name</label>
					<input
						id="userFN"
						class="input max-w-full" />
				</div>
				<div class="form-field w-full">
					<label
						for="userMN"
						class="form-label">Middle Name</label>
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
					<select
						id="userSX"
						class="select">
						<option>Male</option>
						<option>Female</option>
					</select>
				</div>
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
						for="userMA"
						class="form-label">Mother's Name</label>
					<input
						id="userMA"
						class="input max-w-full" />
					<div class="flex">
						<div class="flex h-auto items-center gap-2">
							<input
								type="radio"
								class="radio"
								name="contactPerson"
								id="motherRadio"
								value="Mother" />
							<p class="text-sm">set as Contact Person</p>
						</div>
					</div>
				</div>
				<div class="form-field w-full">
					<label
						for="userFA"
						class="form-label">Father's Name</label>
					<input
						id="userFA"
						class="input max-w-full" />
					<div class="flex">
						<div class="flex h-auto items-center gap-2">
							<input
								type="radio"
								class="radio"
								name="contactPerson"
								id="fatherRadio"
								value="Father" />
							<p class="text-sm">set as Contact Person</p>
						</div>
					</div>
				</div>
				<div class="form-field w-full">
					<label
						for="userGA"
						class="form-label">Guardian's Name</label>
					<input
						id="userGA"
						class="input max-w-full" />
					<div class="flex">
						<div class="flex h-auto items-center gap-2">
							<input
								type="radio"
								class="radio"
								name="contactPerson"
								id="guardianRadio"
								value="Guardian" />
							<p class="text-sm">set as Contact Person</p>
						</div>
					</div>
				</div>
			</div>
			<div class="flex flex-col">
				<h1 class="text-sm font-semibold underline">Health Information</h1>
			</div>
			<div class="form-group flex lg:flex-row">
				<div class="form-field w-full">
					<label
						for="userP1"
						class="form-label">Health Problem 1</label>
					<input
						id="userP1"
						class="input max-w-full" />
				</div>
				<div class="form-field w-full">
					<label
						for="userP2"
						class="form-label">Health Problem 2</label>
					<input
						id="userP2"
						class="input max-w-full" />
				</div>
				<div class="form-field w-full">
					<label
						for="userP3"
						class="form-label">Health Problem 3</label>
					<input
						id="userP3"
						class="input max-w-full" />
				</div>
			</div>
			<div class="form-group flex lg:flex-row">
				<div class="form-field w-full">
					<label
						for="userP4"
						class="form-label">Health Problem 4</label>
					<input
						id="userP4"
						class="input max-w-full" />
				</div>
				<div class="form-field w-full">
					<label
						for="userP5"
						class="form-label">Health Problem 5</label>
					<input
						id="userP5"
						class="input max-w-full" />
				</div>
				<div class="form-field w-full">
					<label
						for="userP6"
						class="form-label">Health Problem 6</label>
					<input
						id="userP6"
						class="input max-w-full" />
				</div>
			</div>
			<div class="divider my-0" />
			<div class="flex flex-col lg:flex-row items-center justify-between">
				<div class="flex flex-row items-center gap-2">
					<input
						type="checkbox"
						class="checkbox"
						bind:checked={isChecked} />
					<p>I certify that the above information is correct.</p>
				</div>
				<br />
				<button
					type="button"
					class="w-full lg:w-1/3 btn btn-outline-primary"
					disabled={!isChecked}
					on:click={handleCreate}>
					Submit Application</button>
			</div>
			<input
				class="modal-state"
				id="modal-1"
				type="checkbox"
				bind:checked={hasApplied} />
			<div class="modal">
				<label
					class="modal-overlay"
					for="modal-1" />
				<div class="modal-content flex flex-col gap-5">
					<label
						for="modal-1"
						class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2">✕</label>
					<h1 class="text-2xl font-semibold">Application Received!</h1>
					<span
						>Your student application has been received. Proceed to the school registrar if you wish
						to pursue with enrolling.</span>
					<span>Your application reference number is {userID}</span>
				</div>
			</div>
		</div>
	{:else if !modlST}
		<div
			class="flex flex-col h-screen w-screen justify-center pt-20 pb-8 pl-6 lg:pl-80 pr-6 gap-8 bg-white dark:bg-backgroundPrimary">
			<div class="flex flex-col">
				<h1 class="text-6xl font-bold">This module<br /> is disabled.</h1>
				<br />
				<p class="text-sm">
					This module is currently disabled by your system administrator. <br />
					Check back for availability later.
				</p>
			</div>
		</div>
	{/if}
	<!-- <div class="flex flex-col w-full lg:w-1/2 pt-24 pb-12 px-8 gap-8">
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
	</div> -->
</div>
