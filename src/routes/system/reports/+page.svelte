<script>
	import { onMount } from 'svelte';

	import { initializeApp } from 'firebase/app';
	import { collection, doc, getDoc, getDocs, getFirestore, setDoc } from 'firebase/firestore';

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

	let data = [];
	let limitedData = [];

	async function getActionLogs() {
		try {
			const q = collection(db, 'csjd-main', 'data', 'logs');
			const snapshot = await getDocs(q);
			data = snapshot.docs.map((doc) => {
				const rawData = doc.data();
				const formattedDate = new Date(rawData.axonDT.seconds * 1000).toLocaleString();
				return { ...rawData, axonDT: formattedDate };
			});

			data.sort((a, b) => new Date(b.axonDT) - new Date(a.axonDT));

			limitedData = data.slice(currentPage * 6, (currentPage + 1) * 6);
		} catch (error) {
			console.error('Error fetching action logs from Firestore:', error);
		}
	}

	let selectedFilter = '';

	function applyFilter(filter) {
		selectedFilter = filter;
		updateLimitedData();
	}

	function updateLimitedData() {
		switch (selectedFilter) {
			case 'Successful':
				limitedData = data
					.filter((item) => item.axonST.toLowerCase() === 'successful')
					.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			case 'Unsuccessful':
				limitedData = data
					.filter((item) => item.axonST.toLowerCase() === 'unsuccessful')
					.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			case 'Recent First':
				data.sort((a, b) => new Date(b.axonDT) - new Date(a.axonDT));
				limitedData = data.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			case 'Oldest First':
				data.sort((a, b) => new Date(a.axonDT) - new Date(b.axonDT));
				limitedData = data.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			case 'Clear Filter':
				limitedData = data.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			default:
				limitedData = data.slice(currentPage * 6, (currentPage + 1) * 6);
		}
	}

	let currentPage = 0;

	function changePage(pageNum) {
		currentPage = pageNum;
		limitedData = data.slice(currentPage * 6, (currentPage + 1) * 6);
	}

	let searchQuery = '';

	function handleSearch(event) {
		searchQuery = event.target.value.toLowerCase();
		limitedData = data
			.filter(
				(item) =>
					item.axonON.toLowerCase().includes(searchQuery) ||
					item.axonDC.toLowerCase().includes(searchQuery) ||
					item.axonBY.toLowerCase().includes(searchQuery)
			)
			.slice(0, 6);
	}

	let selectedRowData = null;

	function handleRowClick(data) {
		selectedRowData = data;
	}

	import ExcelJS from 'exceljs';

	async function handleExport() {
		const templateUrl = '/Reports.xlsx';
		const templateResponse = await fetch(templateUrl);
		const templateBuffer = await templateResponse.arrayBuffer();

		const workbook = new ExcelJS.Workbook();
		await workbook.xlsx.load(templateBuffer);

		workbook.eachSheet((sheet) => {
			if (sheet.name !== 'ActionReport') {
				workbook.removeWorksheet(sheet.id);
			}
		});

		const worksheet = workbook.getWorksheet('ActionReport');

		worksheet.getCell('I9').value = selectedRowData.axonID;
		worksheet.getCell('I10').value = selectedRowData.axonON;
		worksheet.getCell('I11').value = selectedRowData.axonDC;
		worksheet.getCell('I12').value = selectedRowData.axonBY;
		worksheet.getCell('I13').value = selectedRowData.axonST;
		worksheet.getCell('I14').value = selectedRowData.axonDT;

		worksheet.protect('joan-csjd-main', {
			selectLockedCells: false
		});

		const buffer = await workbook.xlsx.writeBuffer();

		const blob = new Blob([buffer], {
			type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
		});

		const url = URL.createObjectURL(blob);

		const a = document.createElement('a');
		a.href = url;
		a.download = `SAR-${selectedRowData.axonID}.xlsx`;
		a.click();

		URL.revokeObjectURL(url);
	}

	// functions below must exist on all documents

	const modlID = 'X02';
	const modlNM = 'System Reports';

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
		loclID = localStorage.getItem('loclID');
		loclLN = localStorage.getItem('loclLN');
		loclFN = localStorage.getItem('loclFN');
		loclMN = localStorage.getItem('loclMN');
		loclCL = localStorage.getItem('loclCL');

		await getActionLogs();
		checkModuleAccess();
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
											class="menu-item menu-active ml-6">System Reports</a>
										<a
											href="/system/defaults"
											class="menu-item ml-6">System Defaults</a>
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
									id="menu-5"
									class="menu-toggle"
									on:change={toggleMenu} />
								<label
									class="menu-item justify-between"
									for="menu-5">
									<div class="flex gap-2">
										<span>Archiving Modules</span>
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
											href="/archives/student"
											class="menu-item ml-6">Student Archives</a>
										<a
											href="/archives/employee"
											class="menu-item ml-6">Employee Archives</a>
										<a
											href="/archives/system"
											class="menu-item ml-6">System Archives</a>
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

	{#if modlST}
		<div
			class="flex flex-col h-auto w-full lg:w-5/6 pt-20 pb-8 pl-6 lg:pl-80 pr-6 gap-8 bg-white dark:bg-backgroundPrimary">
			<div class="flex flex-col">
				<h1 class="text-xl font-semibold">System Analytics</h1>
				<p class="text-xs">
					Below are the current totals of certain values in the system (as of today).
				</p>
			</div>
			<div class="divider my-0" />
			<div class="flex flex-col">
				<h1 class="text-xl font-semibold">System Reports</h1>
				<p class="text-xs">Below is a masterlog of all system activities.</p>
			</div>
			<div class="flex flex-row justify-between">
				<div class="flex flex-row gap-2">
					<input
						class="input"
						placeholder="Search..."
						on:input={handleSearch} />
					<div class="dropdown dropdown-hover">
						<button
							class="btn btn-outline-secondary"
							tabindex="0">Filter</button>
						<div class="dropdown-menu dropdown-menu-right-bottom ml-2">
							<p
								class="dropdown-item text-sm"
								on:click={() => applyFilter('Successful')}>
								Successful
							</p>
							<p
								class="dropdown-item text-sm"
								on:click={() => applyFilter('Unsuccessful')}>
								Unsuccessful
							</p>
							<div class="divider my-0" />
							<p
								class="dropdown-item text-sm"
								on:click={() => applyFilter('Recent First')}>
								Recent First
							</p>
							<p
								class="dropdown-item text-sm"
								on:click={() => applyFilter('Oldest First')}>
								Oldest First
							</p>
							<div class="divider my-0" />
							<p
								class="dropdown-item text-sm"
								on:click={() => applyFilter('Clear Filter')}>
								Clear Filter
							</p>
						</div>
					</div>
				</div>
				<div class="flex flex-row gap-2">
					<button
						class="btn btn-outline-success"
						on:click={() => getActionLogs()}>Refresh</button>
					<div class="pagination">
						<button
							class="btn"
							on:click={() => changePage(currentPage - 1)}
							disabled={currentPage === 0}>
							<svg
								width="18"
								height="18"
								viewBox="0 0 20 20"
								fill="none"
								xmlns="http://www.w3.org/2000/svg">
								<path
									fill-rule="evenodd"
									clip-rule="evenodd"
									d="M12.2574 5.59165C11.9324 5.26665 11.4074 5.26665 11.0824 5.59165L7.25742 9.41665C6.93242 9.74165 6.93242 10.2667 7.25742 10.5917L11.0824 14.4167C11.4074 14.7417 11.9324 14.7417 12.2574 14.4167C12.5824 14.0917 12.5824 13.5667 12.2574 13.2417L9.02409 9.99998L12.2574 6.76665C12.5824 6.44165 12.5741 5.90832 12.2574 5.59165Z"
									fill="#969696" />
							</svg>
						</button>
						<button
							class="btn btn-outline-primary"
							on:click={() => changePage(currentPage + 1)}
							disabled={currentPage === Math.floor(data.length / 6)}>
							<svg
								width="18"
								height="18"
								viewBox="0 0 20 20"
								fill="none"
								xmlns="http://www.w3.org/2000/svg">
								<path
									fill-rule="evenodd"
									clip-rule="evenodd"
									d="M7.74375 5.2448C7.41875 5.5698 7.41875 6.0948 7.74375 6.4198L10.9771 9.65314L7.74375 12.8865C7.41875 13.2115 7.41875 13.7365 7.74375 14.0615C8.06875 14.3865 8.59375 14.3865 8.91875 14.0615L12.7437 10.2365C13.0687 9.91147 13.0687 9.38647 12.7437 9.06147L8.91875 5.23647C8.60208 4.9198 8.06875 4.9198 7.74375 5.2448Z"
									fill="#969696" />
							</svg>
						</button>
					</div>
				</div>
			</div>
			<div class="flex w-full overflow-x-auto">
				<table class="table table-hover table-compact">
					<thead>
						<tr>
							<th>Date & Time</th>
							<th>Description</th>
							<th>Performed By</th>
							<!-- <th>Status</th> -->
						</tr>
					</thead>
					<tbody>
						{#each limitedData as item (item.axonID)}
							<tr on:click={() => handleRowClick(item)}>
								<td>{item.axonDT}</td>
								<td>{item.axonDC}</td>
								<td>{item.axonBY}</td>
								<!-- <td>{item.axonST}</td> -->
							</tr>
						{/each}
					</tbody>
				</table>
			</div>
			<div class="divider my-0" />
			<div class="flex flex-col">
				<h1 class="text-xl font-semibold">Specific Action Report</h1>
				<p class="text-xs">Select a report log item from the table to load its contents.</p>
			</div>
			{#if selectedRowData}
				<div id="pdf-content">
					<p>Action ID: {selectedRowData.axonID}</p>
					<p>Module: {selectedRowData.axonON}</p>
					<p>Description: {selectedRowData.axonDC}</p>
					<p>Performed By: {selectedRowData.axonBY}</p>
					<p>Status: {selectedRowData.axonST}</p>
					<p>Date & Time: {selectedRowData.axonDT}</p>
				</div>
				<button
					class="btn btn-outline-primary"
					on:click={handleExport}>Export</button>
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
</div>
