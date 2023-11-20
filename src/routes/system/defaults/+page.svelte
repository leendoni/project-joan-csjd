<script>
	import { onMount } from 'svelte';

	import { initializeApp } from 'firebase/app';
	import { doc, getFirestore, onSnapshot, setDoc } from 'firebase/firestore';

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

	const modlID = 'X03';
	const modlNM = 'System Defaults';

	const systemSettings = [
		{ id: 'systID', path: 'systemID' },
		{ id: 'systST', path: 'systemStatus' }
	];

	const academicDefaults = [
		{ id: 'acadST', path: 'currentStatus' },
		{ id: 'acadAY', path: 'currentYear' },
		{ id: 'acadSM', path: 'currentSemester' },
		{ id: 'acadQT', path: 'currentQuarter' },
		{ id: 'acadSD', path: 'currentStartDate' },
		{ id: 'acadED', path: 'currentEndDate' },
		{ id: 'nrolSD', path: 'registStartDate' },
		{ id: 'nrolED', path: 'enrollEndDate' }
	];

	let userClasses = [
		// 'Developer',
		// 'Administrator',
		'Registrar',
		'Cashier',
		'Admission',
		'Faculty',
		'Guidance',
		'Librarian',
		'Student',
		'Parent'
	];

	let modules = [
		// {
		// 	section: 'Maintenance Modules',
		// 	items: ['System Reports', 'System Defaults', 'System Guide']
		// },
		{
			section: 'Management Modules',
			items: [
				'User Management',
				'Subject Management',
				'Section Management',
				'Schedule Management',
				'Bulletin Management'
			]
		},
		{
			section: 'Academic Modules',
			items: [
				'Admissions',
				'Enrollments',
				'Curriculum',
				'Sections',
				'Gradebook',
				'Violations & Sanctions',
				'Guidance Defaults'
			]
		},
		{
			section: 'Financial Modules',
			items: ['Transactions', 'Transaction Defaults']
		},
		// {
		// 	section: 'Archiving Modules',
		// 	items: ['Student Archives', 'Employee Archives', 'System Archives']
		// },
		{
			section: 'General Modules',
			items: ['Student Application', 'Parent Registration']
		}
	];

	// for start and end dates
	const validateEndDate = () => {
		const startDate = new Date(document.getElementById('acadSD').value);
		const endDate = new Date(document.getElementById('acadED').value);

		if (startDate > endDate) {
			alert('End date must be later than the start date.');
			document.getElementById('acadED').value = '';
		}
	};

	const validateEndDate2 = () => {
		const startDate = new Date(document.getElementById('nrolSD').value);
		const endDate = new Date(document.getElementById('nrolED').value);

		if (startDate > endDate) {
			alert('End date must be later than the start date.');
			document.getElementById('nrolED').value = '';
		}
	};

	// functions below must exist on all documents

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

	// accordion menu
	function toggleMenu(event) {
		const checkbox = event.target;

		if (checkbox.checked) {
			if (activeMenu && activeMenu !== checkbox) {
				activeMenu.checked = false;
			}

			activeMenu = checkbox;

			setTimeout(() => {
				checkbox.checked = false;
				activeMenu = null;
			}, 6500);
		} else {
			activeMenu = null;
		}
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

	// onMount(async () => {
	// 	loclID = localStorage.getItem('loclID');
	// 	loclLN = localStorage.getItem('loclLN');
	// 	loclFN = localStorage.getItem('loclFN');
	// 	loclMN = localStorage.getItem('loclMN');
	// 	loclCL = localStorage.getItem('loclCL');

	// 	// for system settings
	// 	systemSettings.forEach((setting) => {
	// 		const settingRef = doc(db, 'csjd-main', 'defaults', 'system', setting.path);
	// 		const element = document.getElementById(setting.id);
	// 		if (element) {
	// 			onSnapshot(settingRef, (doc) => {
	// 				if (doc.exists()) {
	// 					element.value = doc.data().value;
	// 				}
	// 			});

	// 			// Add an event listener to detect changes and update Firestore when the value is changed
	// 			element.addEventListener('change', () => {
	// 				setDoc(settingRef, { value: element.value });
	// 				handleAction(
	// 					'Successful',
	// 					`Made changes to ${setting.path}`,
	// 					`${loclLN}, ${loclFN} ${loclMN}`
	// 				);
	// 			});
	// 		}
	// 	});

	// 	// for academic defaults
	// 	academicDefaults.forEach((setting) => {
	// 		const settingRef = doc(db, 'csjd-main', 'defaults', 'academic', setting.path);
	// 		const element = document.getElementById(setting.id);
	// 		if (element) {
	// 			onSnapshot(settingRef, (doc) => {
	// 				if (doc.exists()) {
	// 					element.value = doc.data().value;
	// 				}
	// 			});

	// 			// Add an event listener to detect changes and update Firestore when the value is changed
	// 			element.addEventListener('change', () => {
	// 				setDoc(settingRef, { value: element.value });
	// 				handleAction(
	// 					'Successful',
	// 					`Made changes to ${setting.path}`,
	// 					`${loclLN}, ${loclFN} ${loclMN}`
	// 				);
	// 			});
	// 		}
	// 	});

	// 	// for classes
	// 	userClasses.forEach((userClass) => {
	// 		const userClassRef = doc(db, 'csjd-main', 'defaults', 'user', userClass);
	// 		onSnapshot(userClassRef, (doc) => {
	// 			if (doc.exists()) {
	// 				// Update the checkbox based on the value in Firestore
	// 				const isChecked = doc.data().enabled;
	// 				const checkbox = document.getElementById(userClass.replace(/ /g, ''));
	// 				if (checkbox) {
	// 					checkbox.checked = isChecked;
	// 				}
	// 			}
	// 		});

	// 		const checkbox = document.getElementById(userClass.replace(/ /g, ''));
	// 		if (checkbox) {
	// 			checkbox.addEventListener('change', () => {
	// 				setDoc(userClassRef, { enabled: checkbox.checked });
	// 				handleAction(
	// 					'Successful',
	// 					`Made changes to ${userClass}`,
	// 					`${loclLN}, ${loclFN} ${loclMN}`
	// 				);
	// 			});
	// 		}
	// 	});

	// 	// for modules
	// 	modules.forEach((module) => {
	// 		module.items.forEach((item) => {
	// 			const moduleRef = doc(db, 'csjd-main', 'defaults', 'module', item);
	// 			onSnapshot(moduleRef, (doc) => {
	// 				if (doc.exists()) {
	// 					const isChecked = doc.data().enabled;
	// 					const checkbox = document.getElementById(item.replace(/ /g, ''));
	// 					if (checkbox) {
	// 						checkbox.checked = isChecked;
	// 					}
	// 				}
	// 			});

	// 			const checkbox = document.getElementById(item.replace(/ /g, ''));
	// 			if (checkbox) {
	// 				checkbox.addEventListener('change', () => {
	// 					setDoc(moduleRef, { enabled: checkbox.checked });
	// 					handleAction('Successful', `Made changes to ${item}`, `${loclLN}, ${loclFN} ${loclMN}`);
	// 				});
	// 			}
	// 		});
	// 	});
	// });

	onMount(async () => {
		loclID = localStorage.getItem('loclID');
		loclLN = localStorage.getItem('loclLN');
		loclFN = localStorage.getItem('loclFN');
		loclMN = localStorage.getItem('loclMN');
		loclCL = localStorage.getItem('loclCL');

		// Consolidate snapshot listeners for system settings and academic defaults
		const allSettingsRefs = [];
		systemSettings.forEach((setting) => {
			const settingRef = doc(db, 'csjd-main', 'defaults', 'system', setting.path);
			const element = document.getElementById(setting.id);
			if (element) {
				allSettingsRefs.push({ ref: settingRef, element });
			}
		});

		academicDefaults.forEach((setting) => {
			const settingRef = doc(db, 'csjd-main', 'defaults', 'academic', setting.path);
			const element = document.getElementById(setting.id);
			if (element) {
				allSettingsRefs.push({ ref: settingRef, element });
			}
		});

		allSettingsRefs.forEach(({ ref, element }) => {
			onSnapshot(ref, (doc) => {
				if (doc.exists()) {
					element.value = doc.data().value;
				}
			});

			element.addEventListener('change', () => {
				setDoc(ref, { value: element.value });
				handleAction('Successful', `Made changes to ${ref.path}`, `${loclLN}, ${loclFN} ${loclMN}`);
			});
		});

		// Snapshot listeners for classes and modules
		const classesAndModulesRefs = [];
		userClasses.forEach((userClass) => {
			const userClassRef = doc(db, 'csjd-main', 'defaults', 'user', userClass);
			const checkbox = document.getElementById(userClass.replace(/ /g, ''));
			if (checkbox) {
				classesAndModulesRefs.push({ ref: userClassRef, checkbox });
			}
		});

		modules.forEach((module) => {
			module.items.forEach((item) => {
				const moduleRef = doc(db, 'csjd-main', 'defaults', 'module', item);
				const checkbox = document.getElementById(item.replace(/ /g, ''));
				if (checkbox) {
					classesAndModulesRefs.push({ ref: moduleRef, checkbox });
				}
			});
		});

		classesAndModulesRefs.forEach(({ ref, checkbox }) => {
			onSnapshot(ref, (doc) => {
				if (doc.exists()) {
					checkbox.checked = doc.data().enabled;
				}
			});

			checkbox.addEventListener('change', () => {
				setDoc(ref, { enabled: checkbox.checked });
				handleAction('Successful', `Made changes to ${ref.path}`, `${loclLN}, ${loclFN} ${loclMN}`);
			});
		});
	});
</script>

<div class="flex flex-col w-screen h-screen overflow-x-hidden bg-border">
	<div class="h-14 navbar navbar-sticky navbar-no-boxShadow navbar-bordered navbar-glass">
		<div class="navbar-start">
			<label
				for="sidebar-mobile-fixed"
				class="sm:hidden">
				<!-- sm logo -->
				<svg
					class="flex lg:hidden"
					viewBox="0 0 1100 240"
					width="120">
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
			</label>
		</div>
		<div class="navbar-end">
			<p>{modlNM}</p>
		</div>
	</div>

	<div class="sm:w-full sm:max-w-[18rem]">
		<input
			type="checkbox"
			id="sidebar-mobile-fixed"
			class="sidebar-state" />
		<label
			for="sidebar-mobile-fixed"
			class="sidebar-overlay" />
		<aside
			class="sidebar sidebar-fixed-left border-r-border border-r-2 sidebar-mobile h-full justify-start max-sm:fixed max-sm:-translate-x-full">
			<section class="sidebar-title flex flex-col text-center items-center pt-6">
				<div class="flex flex-col place-items-center">
					<!-- sm logo -->
					<svg
						class="flex"
						viewBox="0 0 1100 240"
						width="180">
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
					<span class="text-xs font-normal text-content2">Colegio de San Juan de Dios, Inc.</span>
					<span class="text-xs font-normal text-content2">systID : csjd-main</span>
				</div>
			</section>
			<section class="sidebar-content min-h-[20rem]">
				<nav class="menu rounded-md">
					<section class="menu-section px-4">
						<a
							href="/dashboard"
							class="menu-item">Dashboard</a>
						<div class="divider my-0" />
						<ul class="menu-items">
							<li>
								<input
									checked
									type="checkbox"
									id="menu-1"
									class="menu-toggle"
									on:change={toggleMenu} />
								<label
									class="menu-item justify-between"
									for="menu-1">
									<div class="flex gap-2">
										<span>Maintenance Modules</span>
									</div>
									<span class="menu-icon">
										<svg
											xmlns="http://www.w3.org/2000/svg"
											class="h-5 w-5"
											viewBox="0 0 20 20"
											fill="currentColor">
											<path
												fill-rule="evenodd"
												d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
												clip-rule="evenodd" />
										</svg>
									</span>
								</label>
								<div class="menu-item-collapse">
									<div class="min-h-0">
										<a
											href="/system"
											class="menu-item ml-6">System Information</a>
										<a
											href="/system/reports"
											class="menu-item ml-6">System Reports</a>
										<a
											href="/system/defaults"
											class="menu-item menu-active ml-6">System Defaults</a>
									</div>
								</div>
							</li>
							<li>
								<input
									type="checkbox"
									id="menu-2"
									class="menu-toggle"
									on:change={toggleMenu} />
								<label
									class="menu-item justify-between"
									for="menu-2">
									<div class="flex gap-2">
										<span>Management Modules</span>
									</div>
									<span class="menu-icon">
										<svg
											xmlns="http://www.w3.org/2000/svg"
											class="h-5 w-5"
											viewBox="0 0 20 20"
											fill="currentColor">
											<path
												fill-rule="evenodd"
												d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
												clip-rule="evenodd" />
										</svg>
									</span>
								</label>
								<div class="menu-item-collapse">
									<div class="min-h-0">
										<a
											href="/manage/users"
											class="menu-item ml-6">User Management</a>
										<a
											href="/manage/subjects"
											class="menu-item ml-6">Subject Management</a>
										<a
											href="/manage/sections"
											class="menu-item ml-6">Section Management</a>
										<a
											href="/manage/schedules"
											class="menu-item ml-6">Schedule Management</a>
										<div class="divider my-0" />
										<a
											href="/manage/bulletin"
											class="menu-item ml-6">Bulletin Management</a>
									</div>
								</div>
							</li>
							<li>
								<input
									type="checkbox"
									id="menu-3"
									class="menu-toggle"
									on:change={toggleMenu} />
								<label
									class="menu-item justify-between"
									for="menu-3">
									<div class="flex gap-2">
										<span>Academic Modules</span>
									</div>
									<span class="menu-icon">
										<svg
											xmlns="http://www.w3.org/2000/svg"
											class="h-5 w-5"
											viewBox="0 0 20 20"
											fill="currentColor">
											<path
												fill-rule="evenodd"
												d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
												clip-rule="evenodd" />
										</svg>
									</span>
								</label>
								<div class="menu-item-collapse">
									<div class="min-h-0">
										<a
											href="/academic/admissions"
											class="menu-item ml-6">Admissions</a>
										<a
											href="/academic/enrollments"
											class="menu-item ml-6">Enrollments</a>

										<a
											href="/academic/sections"
											class="menu-item ml-6">Sections</a>
										<a
											href="/academic/gradebook"
											class="menu-item ml-6">Gradebook</a>
										<div class="divider my-0" />
										<a
											href="/guidance"
											class="menu-item ml-6">Violations & Sanctions</a>
									</div>
								</div>
							</li>
							<li>
								<input
									type="checkbox"
									id="menu-4"
									class="menu-toggle"
									on:change={toggleMenu} />
								<label
									class="menu-item justify-between"
									for="menu-4">
									<div class="flex gap-2">
										<span>Financial Modules</span>
									</div>
									<span class="menu-icon">
										<svg
											xmlns="http://www.w3.org/2000/svg"
											class="h-5 w-5"
											viewBox="0 0 20 20"
											fill="currentColor">
											<path
												fill-rule="evenodd"
												d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
												clip-rule="evenodd" />
										</svg>
									</span>
								</label>
								<div class="menu-item-collapse">
									<div class="min-h-0">
										<a
											href="/transact"
											class="menu-item ml-6">Transactions</a>
									</div>
								</div>
							</li>

							<li>
								<input
									type="checkbox"
									id="menu-6"
									class="menu-toggle"
									on:change={toggleMenu} />
								<label
									class="menu-item justify-between"
									for="menu-6">
									<div class="flex gap-2">
										<span>Miscellaneous Modules</span>
									</div>
									<span class="menu-icon">
										<svg
											xmlns="http://www.w3.org/2000/svg"
											class="h-5 w-5"
											viewBox="0 0 20 20"
											fill="currentColor">
											<path
												fill-rule="evenodd"
												d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
												clip-rule="evenodd" />
										</svg>
									</span>
								</label>
								<div class="menu-item-collapse">
									<div class="min-h-0">
										<a
											href="/library"
											class="menu-item ml-6">Library Management</a>
										<a
											href="/clinic"
											class="menu-item ml-6">Clinic Management</a>
									</div>
								</div>
							</li>
						</ul>
						<div class="divider my-0" />
						<a
							href="/system/guide"
							class="menu-item">System Guide</a>
					</section>
				</nav>
			</section>
			<section class="sidebar-footer bg-gray-2 pt-2">
				<div class="divider my-0" />
				<div class="dropdown z-50 flex h-fit w-full cursor-pointer hover:bg-gray-4">
					<label
						for=""
						class="whites mx-2 flex h-fit w-full cursor-pointer p-0 hover:bg-gray-4"
						tabindex="-1">
						<div class="flex flex-row gap-4 p-4 items-center">
							<div class="avatar avatar-md items-center justify-center">
								<svg
									id="icon"
									fill="currentColor"
									xmlns="http://www.w3.org/2000/svg"
									width="32"
									viewBox="0 0 32 32">
									<defs>
										<style>
											.cls-1 {
												fill: none;
											}
										</style>
									</defs>
									<path
										id="_inner-path_"
										data-name="&lt;inner-path&gt;"
										class="cls-1"
										d="M8.0071,24.93A4.9958,4.9958,0,0,1,13,20h6a4.9959,4.9959,0,0,1,4.9929,4.93,11.94,11.94,0,0,1-15.9858,0ZM20.5,12.5A4.5,4.5,0,1,1,16,8,4.5,4.5,0,0,1,20.5,12.5Z" />
									<path
										d="M26.7489,24.93A13.9893,13.9893,0,1,0,2,16a13.899,13.899,0,0,0,3.2511,8.93l-.02.0166c.07.0845.15.1567.2222.2392.09.1036.1864.2.28.3008.28.3033.5674.5952.87.87.0915.0831.1864.1612.28.2417.32.2759.6484.5372.99.7813.0441.0312.0832.0693.1276.1006v-.0127a13.9011,13.9011,0,0,0,16,0V27.48c.0444-.0313.0835-.0694.1276-.1006.3412-.2441.67-.5054.99-.7813.0936-.08.1885-.1586.28-.2417.3025-.2749.59-.5668.87-.87.0933-.1006.1894-.1972.28-.3008.0719-.0825.1522-.1547.2222-.2392ZM16,8a4.5,4.5,0,1,1-4.5,4.5A4.5,4.5,0,0,1,16,8ZM8.0071,24.93A4.9957,4.9957,0,0,1,13,20h6a4.9958,4.9958,0,0,1,4.9929,4.93,11.94,11.94,0,0,1-15.9858,0Z" />
									<rect
										id="_Transparent_Rectangle_"
										data-name="&lt;Transparent Rectangle&gt;"
										class="cls-1"
										width="32"
										height="32" />
								</svg>
							</div>
							<div class="flex flex-col">
								<span>{loclFN}</span>
								<span class="text-xs font-normal text-content2">{loclCL} - {loclID}</span>
							</div>
						</div>
					</label>
					<div class="dropdown-menu-top-left dropdown-menu ml-2">
						<div class="flex flex-row gap-4 p-4 items-center">
							<div class="flex flex-col">
								<span class="text-xs font-normal text-content2">{loclCL}</span>
								<span>{loclFN}</span>
								<span class="text-xs font-normal text-content2">{loclMN} {loclLN}</span>
								<span class="text-xs font-normal text-content2">{loclID}</span>
							</div>
						</div>
						<p class="dropdown-item text-sm">Account Settings</p>
						<div class="divider my-0" />
						<a
							href="/login"
							class="dropdown-item text-sm">Logout</a>
					</div>
				</div>
			</section>
		</aside>
	</div>

	<div
		class="flex flex-col h-auto w-full lg:w-4/6 pt-20 pb-8 pl-6 lg:pl-80 pr-6 gap-8 bg-white dark:bg-backgroundPrimary">
		<div class="flex flex-col">
			<h1 class="text-xl font-semibold">System Settings</h1>
			<p class="text-xs">Set the current status of the system.</p>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field w-full">
				<label
					for="systID"
					class="form-label">System ID</label>
				<input
					id="systID"
					value="csjd-main"
					class="input max-w-full"
					readonly />
			</div>
			<div class="form-field w-full">
				<label
					for="systST"
					class="form-label">Status</label>
				<select
					id="systST"
					class="select">
					<option>Online</option>
					<option>Offline</option>
					<option>Maintenance</option>
				</select>
			</div>
		</div>
		<div class="divider my-0" />
		<div class="flex flex-col">
			<h1 class="text-xl font-semibold">Academic Defaults</h1>
			<p class="text-xs">Set the current academic values.</p>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field w-full">
				<label
					for="acadST"
					class="form-label">Current Status</label>
				<select
					id="acadST"
					class="select">
					<option>Pre-Enrollment</option>
					<option>Enrollment</option>
					<option>Ongoing</option>
					<option>Break</option>
				</select>
			</div>
			<div class="form-field w-full">
				<label
					for="acadAY"
					class="form-label">Current Year</label>
				<select
					id="acadAY"
					class="select">
					<option>2023-2024</option>
					<option>2024-2025</option>
					<option>2025-2026</option>
					<option>2026-2027</option>
					<option>2027-2028</option>
					<option>2028-2029</option>
					<option>2029-2030</option>
					<option>2030-2031</option>
					<option>2031-2032</option>
					<option>2032-2033</option>
				</select>
			</div>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field w-full">
				<label
					for="acadSM"
					class="form-label">Current Semester</label>
				<select
					id="acadSM"
					class="select">
					<option>First</option>
					<option>Second</option>
					<option>Summer</option>
				</select>
			</div>
			<div class="form-field w-full">
				<label
					for="acadQT"
					class="form-label">Current Quarter</label>
				<select
					id="acadQT"
					class="select">
					<option>First</option>
					<option>Second</option>
					<option>Third</option>
					<option>Fourth</option>
				</select>
			</div>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field w-full">
				<label
					for="acadSD"
					class="form-label">Start Date</label>
				<input
					id="acadSD"
					type="date"
					class="input max-w-full" />
			</div>
			<div class="form-field w-full">
				<label
					for="acadED"
					class="form-label">End Date</label>
				<input
					id="acadED"
					type="date"
					class="input max-w-full"
					on:change={validateEndDate} />
			</div>
		</div>
		<div class="form-group flex lg:flex-row">
			<div class="form-field w-full">
				<label
					for="nrolSD"
					class="form-label">Registration Start Date</label>
				<input
					id="nrolSD"
					type="date"
					class="input max-w-full" />
			</div>
			<div class="form-field w-full">
				<label
					for="nrolED"
					class="form-label">Enrollment End Date</label>
				<input
					id="nrolED"
					type="date"
					class="input max-w-full"
					on:change={validateEndDate2} />
			</div>
		</div>
		<div class="divider my-0" />
		<div class="flex flex-col">
			<h1 class="text-xl font-semibold">Account Control</h1>
			<p class="text-xs">Enable/disable account classes.</p>
		</div>
		<div class="form-group flex">
			{#each userClasses as userClass (userClass)}
				<div class="flex items-center w-full gap-2">
					<input
						type="checkbox"
						class="switch switch-md"
						id={userClass.replace(/ /g, '')} />
					<p>{userClass}</p>
				</div>
			{/each}
		</div>
		<div class="divider my-0" />
		<div class="flex flex-col">
			<h1 class="text-xl font-semibold">Module Control</h1>
			<p class="text-xs">Enable/disable modules.</p>
		</div>
		<div class="flex flex-col gap-8">
			{#each modules as module (module.section)}
				<div class="form-group flex">
					<h1 class="text-md font-semibold underline">{module.section}</h1>
					{#each module.items as item (item)}
						<div class="flex items-center w-full gap-2">
							<input
								type="checkbox"
								class="switch switch-md"
								id={item.replace(/ /g, '')} />
							<p>{item}</p>
						</div>
					{/each}
				</div>
			{/each}
		</div>
	</div>
</div>
