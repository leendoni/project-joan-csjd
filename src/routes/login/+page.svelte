<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	import { initializeApp } from 'firebase/app';
	import {
		collection,
		doc,
		getDoc,
		getDocs,
		getFirestore,
		query,
		setDoc,
		updateDoc,
		where
	} from 'firebase/firestore';

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

	let isResetting = false,
		isUpdating = false,
		isUpdated = false;

	let hasIncorrectUN = false,
		hasIncorrectPW = false;

	import bcrypt from 'bcryptjs';

	async function handleLogin() {
		if (
			document.getElementById('userUN').value != '' &&
			document.getElementById('userPW').value != ''
		) {
			try {
				const q = query(
					collection(db, 'csjd-main', 'data', 'users'),
					where('userUN', '==', document.getElementById('userUN').value)
				);
				const querySnapshot = await getDocs(q);

				if (!querySnapshot.empty) {
					const userData = querySnapshot.docs[0].data();
					const hashedPassword = userData.userPW;

					const isPasswordValid = await bcrypt.compare(
						document.getElementById('userPW').value,
						hashedPassword
					);

					if (isPasswordValid) {
						const updatedData = {
							userOL: true
						};

						const docRef = doc(db, 'csjd-main', 'data', 'users', userData.userUN);

						try {
							await updateDoc(docRef, updatedData);
						} catch (error) {
							console.error('Error updating document:', error);
						}

						localStorage.setItem('loclID', userData.userID);
						localStorage.setItem('loclLN', userData.userLN);
						localStorage.setItem('loclFN', userData.userFN);
						localStorage.setItem('loclMN', userData.userMN);
						localStorage.setItem('loclCL', userData.userCL);

						handleAction(
							'Successful',
							`User ${userData.userUN} logged in.`,
							`${userData.userLN}, ${userData.userFN} ${userData.userMN}`
						);

						goto('/dashboard');
					} else {
						hasIncorrectPW = true;
					}
				} else {
					hasIncorrectUN = true;
				}
			} catch (error) {
				console.error('Error while attempting to sign in:', error);
			}
		} else {
			hasIncorrectUN = true;
			hasIncorrectPW = true;
		}
	}

	// functions below must exist on all documents

	const modlID = 'A01';
	const modlNM = 'Login';

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

{#if modlST}
	<div class="flex flex-col w-screen h-screen items-center bg-white dark:bg-backgroundPrimary">
		{#if !isResetting}
			<div class="flex flex-col w-full h-full items-center justify-center p-8">
				<!-- sm logo -->
				<svg
					class="flex lg:hidden"
					viewBox="0 0 1100 240"
					width="240">
					<style type="text/css">
						.st0 {
							fill: none;
							stroke: currentColor;
							stroke-width: 40;
							stroke-linecap: square;
							stroke-miterlimit: 10;
						}
						.st1 {
							fill: none;
							stroke: currentColor;
							stroke-width: 40;
							stroke-miterlimit: 10;
						}
						.st2 {
							fill: currentColor;
						}
					</style>
					<g id="Layer_1">
						<rect
							x="140"
							y="100"
							width="40"
							height="40"
							class="st2 svg-elem-1" />
						<path
							class="st0 svg-elem-2"
							d="M40,120v80h200c0,0,40,0,40-40s0-120,0-120H160" />
						<rect
							x="440"
							y="100"
							width="40"
							height="40"
							class="st2 svg-elem-3" />
						<path
							class="st1 svg-elem-4"
							d="M480,200H340V80c0,0,0-40,40-40s200,0,200,0v80" />
						<rect
							x="920"
							y="100"
							width="40"
							height="40"
							class="st2 svg-elem-5" />
						<path
							class="st1 svg-elem-6"
							d="M620,40h140c0,0,0,80,0,120s-40,40-40,40H520v-80h80" />
						<rect
							x="620"
							y="100"
							width="40"
							height="40"
							class="st2 svg-elem-8" />
						<path
							class="st0 svg-elem-7"
							d="M820,200V80c0,0,0-40,40-40s200,0,200,0v160" />
					</g>
				</svg>
				<!-- xl logo -->
				<svg
					class="hidden lg:flex"
					viewBox="0 0 1100 240"
					width="360">
					<style type="text/css">
						.st0 {
							fill: none;
							stroke: currentColor;
							stroke-width: 40;
							stroke-linecap: square;
							stroke-miterlimit: 10;
						}
						.st1 {
							fill: none;
							stroke: currentColor;
							stroke-width: 40;
							stroke-miterlimit: 10;
						}
						.st2 {
							fill: currentColor;
						}
					</style>
					<g id="Layer_1">
						<rect
							x="140"
							y="100"
							width="40"
							height="40"
							class="st2 svg-elem-1" />
						<path
							class="st0 svg-elem-2"
							d="M40,120v80h200c0,0,40,0,40-40s0-120,0-120H160" />
						<rect
							x="440"
							y="100"
							width="40"
							height="40"
							class="st2 svg-elem-3" />
						<path
							class="st1 svg-elem-4"
							d="M480,200H340V80c0,0,0-40,40-40s200,0,200,0v80" />
						<rect
							x="920"
							y="100"
							width="40"
							height="40"
							class="st2 svg-elem-5" />
						<path
							class="st1 svg-elem-6"
							d="M620,40h140c0,0,0,80,0,120s-40,40-40,40H520v-80h80" />
						<rect
							x="620"
							y="100"
							width="40"
							height="40"
							class="st2 svg-elem-8" />
						<path
							class="st0 svg-elem-7"
							d="M820,200V80c0,0,0-40,40-40s200,0,200,0v160" />
					</g>
				</svg>
				<br />
				<div class="flex flex-col w-full lg:w-1/3">
					<div class="form-group">
						<div class="form-field">
							<label
								for="userUN"
								class="form-label">Username</label>
							<input
								id="userUN"
								class="input max-w-full" />
						</div>
						<div class="form-field">
							<label
								for="userPW"
								class="form-label">Password</label>
							<input
								id="userPW"
								placeholder="Type here"
								type="password"
								class="input max-w-full" />
						</div>
						<div class="form-field">
							<div class="form-control justify-between">
								<div class="flex gap-2">
									<input
										type="checkbox"
										class="checkbox" />
									<p>Remember me</p>
								</div>
							</div>
						</div>
						<div class="form-field">
							<button
								type="button"
								class="btn btn-outline-primary w-full"
								on:click={handleLogin}>
								Sign In
							</button>
							<button
								on:click={() => (isResetting = true)}
								type="button"
								class="btn btn-outline w-full">Forgot Password</button>
						</div>
					</div>
				</div>
			</div>
		{/if}
		{#if isResetting}
			<div class="flex flex-col w-full h-full items-center justify-center p-8">
				<div class="flex flex-col w-full lg:w-1/3">
					<div class="flex flex-col">
						<h1 class="text-2xl font-semibold">Find your account</h1>
						<p class="text-sm">Provide your account details to reset your password.</p>
					</div>
					<br />
					<div class="form-group">
						<div class="form-field">
							<label
								for="userUN"
								class="form-label">Username</label>
							<input
								id="userUN"
								class="input max-w-full" />
						</div>
						{#if !isUpdating && !isUpdated}
							<div class="form-field pt-5">
								<button
									type="button"
									class="btn btn-outline-primary w-full">Send Reset Request</button>
								<button
									type="button"
									class="btn btn-outline w-full"
									on:click={() => (isResetting = false)}>Cancel</button>
							</div>
						{:else if isUpdating}
							<div class="form-field pt-5">
								<div class="form-control justify-between">
									<button
										type="button"
										class="btn btn-loading btn-outline-primary w-full">Sending Request</button>
								</div>
							</div>
						{/if}
						{#if isUpdated}
							<div class="form-field">
								<div class="form-control flex flex-col gap-4">
									<p class="">
										Your password reset request has been received. Wait atleast one (1) day for your
										system administrator to reset your password before logging in again.
									</p>
									<p class="text-sm">
										Use the format <strong>lastname.accountID</strong> as your password the next time
										you log in.
									</p>
									<p class="text-xs italic">Sample password: delacruz.fg23e1</p>
								</div>
							</div>
							<div class="form-control justify-between">
								<button
									type="button"
									class="btn btn-outline-primary w-full"
									on:click={() => (isResetting = false)}>Return to Login</button>
							</div>
						{/if}
					</div>
				</div>
			</div>
		{/if}
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
