<script>
	import { onMount } from 'svelte';

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

	import bcrypt from 'bcryptjs';

	async function handleCreate() {
		let userST,
			userCL,
			userID,
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

		userST = document.getElementById('userST').value;
		userCL = document.getElementById('userCL').value;
		userID = makeID();

		if (userST === 'Student') {
			userDP = document.getElementById('userDP').value;
			userYR = document.getElementById('userYR').value;
			userSC = document.getElementById('userSC').value;
			userLR = document.getElementById('userLR').value;
		} else {
			userDP = '';
			userYR = '';
			userSC = '';
			userLR = '';
		}

		userLN = document.getElementById('userLN').value;
		userFN = document.getElementById('userFN').value;
		userMN = document.getElementById('userMN').value;
		userSF = document.getElementById('userSF').value;
		userSX = document.getElementById('userSX').value;
		userAD = document.getElementById('userAD').value;
		userCN = document.getElementById('userCN').value;
		userEC = document.getElementById('userEC').value;

		if (userST === 'Student') {
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
		} else {
			userMA = '';
			userFA = '';
			userGA = '';

			userCP = document.getElementById('userCP').value;
			userCR = document.getElementById('userCR').value;
		}

		userP1 = document.getElementById('userP1').value;
		userP2 = document.getElementById('userP2').value;
		userP3 = document.getElementById('userP3').value;
		userP4 = document.getElementById('userP4').value;
		userP5 = document.getElementById('userP5').value;
		userP6 = document.getElementById('userP6').value;

		if (userST === 'Student') {
			userR1 = document.getElementById('userR1').checked;
			userR2 = document.getElementById('userR2').checked;
			userR3 = document.getElementById('userR3').checked;
			userR4 = document.getElementById('userR4').checked;
			userR5 = document.getElementById('userR5').checked;
			userR6 = document.getElementById('userR6').checked;
			userR7 = document.getElementById('userR7').checked;
			userR8 = document.getElementById('userR8').checked;
		} else {
			userR1 = '';
			userR2 = '';
			userR3 = '';
			userR4 = '';
			userR5 = '';
			userR6 = '';
			userR7 = '';
			userR8 = '';
		}

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
			userPR: '',
			userOF: 0,
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
			userR8
		};

		try {
			const docRef = doc(db, 'csjd-main', 'data', 'users', userUN);
			await setDoc(docRef, data);

			console.log('Document written with ID: ', userUN);

			handleAction('Successful', `Added user ${userUN}`, `${loclLN}, ${loclFN} ${loclMN}`);

			document.getElementById('userST').value = '';
			document.getElementById('userCL').value = '';
			document.getElementById('userDP').value = '';
			document.getElementById('userYR').value = '';
			document.getElementById('userSC').value = '';
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
			document.getElementById('userR1').checked = false;
			document.getElementById('userR2').checked = false;
			document.getElementById('userR3').checked = false;
			document.getElementById('userR4').checked = false;
			document.getElementById('userR5').checked = false;
			document.getElementById('userR6').checked = false;
			document.getElementById('userR7').checked = false;
			document.getElementById('userR8').checked = false;
		} catch (error) {
			console.error('Error adding document: ', error);
		}
	}

	async function handleUpdate() {
		if (selectedUserData.userCL === 'Student') {
			const motherRadio = document.getElementById('motherRadio');
			const fatherRadio = document.getElementById('fatherRadio');
			const guardianRadio = document.getElementById('guardianRadio');

			if (motherRadio.checked) {
				selectedUserData.userCP = selectedUserData.userMA;
				selectedUserData.userCR = 'Mother';
			}

			if (fatherRadio.checked) {
				selectedUserData.userCP = selectedUserData.userFA;
				selectedUserData.userCR = 'Father';
			}

			if (guardianRadio.checked) {
				selectedUserData.userCP = selectedUserData.userGA;
				selectedUserData.userCR = 'Guardian';
			}
		}

		const updatedData = {
			userST: selectedUserData.userST,
			userCL: selectedUserData.userCL,
			userDP: selectedUserData.userDP,
			userYR: selectedUserData.userYR,
			userSC: selectedUserData.userSC,
			userLR: selectedUserData.userLR,
			userLN: selectedUserData.userLN,
			userFN: selectedUserData.userFN,
			userMN: selectedUserData.userMN,
			userSF: selectedUserData.userSF,
			userSX: selectedUserData.userSX,
			userAD: selectedUserData.userAD,
			userCN: selectedUserData.userCN,
			userEC: selectedUserData.userEC,
			userMA: selectedUserData.userMA,
			userFA: selectedUserData.userFA,
			userGA: selectedUserData.userGA,
			userCP: selectedUserData.userCP,
			userCR: selectedUserData.userCR,
			userP1: selectedUserData.userP1,
			userP2: selectedUserData.userP2,
			userP3: selectedUserData.userP3,
			userP4: selectedUserData.userP4,
			userP5: selectedUserData.userP5,
			userP6: selectedUserData.userP6,
			userR1: selectedUserData.userR1,
			userR2: selectedUserData.userR2,
			userR3: selectedUserData.userR3,
			userR4: selectedUserData.userR4,
			userR5: selectedUserData.userR5,
			userR6: selectedUserData.userR6,
			userR7: selectedUserData.userR7,
			userR8: selectedUserData.userR8
		};

		const q = query(
			collection(db, 'csjd-main', 'data', 'users'),
			where('userUN', '==', selectedUserData.userUN.toString())
		);

		const snapshot = await getDocs(q);

		if (snapshot.empty) {
			console.error('No records found for the selected reference code.');
			return;
		}

		const docId = snapshot.docs[0].id;
		const docRef = doc(db, 'csjd-main', 'data', 'users', docId);

		try {
			await updateDoc(docRef, updatedData);
			handleAction(
				'Successful',
				`Updated user ${selectedUserData.userUN}`,
				`${loclLN}, ${loclFN} ${loclMN}`
			);
			fetchUsers();
			isEditing = false;
			isSelecting = true;
		} catch (error) {
			isSelecting = true;
			isEditing = false;
			console.error('Error updating document:', error);
		}
	}

	let users = [];

	async function fetchUsers() {
		try {
			const db = getFirestore(app);
			const usersCollection = collection(db, 'csjd-main', 'data', 'users');
			const querySnapshot = await getDocs(
				query(usersCollection, where('userST', 'in', ['Registered', 'Enrolled']))
			);

			users = querySnapshot.docs.map((doc) => doc.data());
			users = users.filter((user) => user.userCL === 'Student');

			switch (selectedFilter) {
				case 'Pre-Registered':
					limitedUsers = users
						.filter((user) => user.userST.toLowerCase() === 'pre-registered')
						.slice(currentPage * 6, (currentPage + 1) * 6);
					break;
				case 'Registered':
					limitedUsers = users
						.filter((user) => user.userST.toLowerCase() === 'registered')
						.slice(currentPage * 6, (currentPage + 1) * 6);
					break;
				case 'Enrolled':
					limitedUsers = users
						.filter((user) => user.userST.toLowerCase() === 'enrolled')
						.slice(currentPage * 6, (currentPage + 1) * 6);
					break;
				case 'Archived':
					limitedUsers = users
						.filter((user) => user.userST.toLowerCase() === 'archived')
						.slice(currentPage * 6, (currentPage + 1) * 6);
					break;
				case 'Clear Filter':
					limitedUsers = users.slice(currentPage * 6, (currentPage + 1) * 6);
					break;
				default:
					limitedUsers = users.slice(currentPage * 6, (currentPage + 1) * 6);
			}
		} catch (error) {
			console.error('Error fetching user data:', error);
		}
	}

	let limitedUsers = [];

	function updateLimitedUsers() {
		switch (selectedFilter) {
			case 'Pre-Registered':
				limitedUsers = users
					.filter((user) => user.userST.toLowerCase() === 'pre-registered')
					.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			case 'Registered':
				limitedUsers = users
					.filter((user) => user.userST.toLowerCase() === 'registered')
					.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			case 'Enrolled':
				limitedUsers = users
					.filter((user) => user.userST.toLowerCase() === 'enrolled')
					.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			case 'Archived':
				limitedUsers = users
					.filter((user) => user.userST.toLowerCase() === 'archived')
					.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			case 'Clear Filter':
				limitedUsers = users.slice(currentPage * 6, (currentPage + 1) * 6);
				break;
			default:
				limitedUsers = users.slice(currentPage * 6, (currentPage + 1) * 6);
		}
	}

	let currentPage = 0;

	function changePage(pageNum) {
		currentPage = pageNum;
		updateLimitedUsers();
	}

	let searchQuery = '';

	function handleSearch(event) {
		searchQuery = event.target.value.toLowerCase();
		limitedUsers = users
			.filter(
				(user) =>
					user.userLN.toLowerCase().includes(searchQuery) ||
					user.userFN.toLowerCase().includes(searchQuery) ||
					user.userUN.toLowerCase().includes(searchQuery) ||
					user.userID.toLowerCase().includes(searchQuery)
			)
			.slice(0, 6);
	}

	let selectedFilter = '';

	function filterUsers(filter) {
		selectedFilter = filter;
		fetchUsers();
	}

	let isCreating = false;
	let isSelecting = false;
	let isEditing = false;

	import ExcelJS from 'exceljs';

	async function handleExport() {
		const templateUrl = '/Reports.xlsx';
		const templateResponse = await fetch(templateUrl);
		const templateBuffer = await templateResponse.arrayBuffer();

		const workbook = new ExcelJS.Workbook();
		await workbook.xlsx.load(templateBuffer);

		if (selectedUserData.userCL === 'Student') {
			workbook.eachSheet((sheet) => {
				if (sheet.name !== 'UserInformationA') {
					workbook.removeWorksheet(sheet.id);
				}
			});

			const worksheet = workbook.getWorksheet('UserInformationA');

			worksheet.getCell('I9').value = selectedUserData.userST;
			worksheet.getCell('I10').value = selectedUserData.userCL;

			worksheet.getCell('I12').value = selectedUserData.userUN;
			worksheet.getCell('I13').value = selectedUserData.userUN.replace('@csjd.joan.cloud', '');

			worksheet.getCell('I16').value = selectedUserData.userDP;

			worksheet.getCell('I18').value = selectedUserData.userLR;
			worksheet.getCell('I19').value = selectedUserData.userYR;
			worksheet.getCell('I20').value = selectedUserData.userSC;

			worksheet.getCell('I22').value = selectedUserData.userLN;
			worksheet.getCell('I23').value = selectedUserData.userFN;
			worksheet.getCell('I24').value = selectedUserData.userMN;
			worksheet.getCell('I25').value = selectedUserData.userSF;
			worksheet.getCell('I26').value = selectedUserData.userSX;

			worksheet.getCell('I28').value = selectedUserData.userAD;
			worksheet.getCell('I29').value = selectedUserData.userCN;
			worksheet.getCell('I30').value = selectedUserData.userEC;

			worksheet.getCell('I32').value = selectedUserData.userMA;
			worksheet.getCell('I33').value = selectedUserData.userFA;
			worksheet.getCell('I34').value = selectedUserData.userGA;
			worksheet.getCell('I35').value = selectedUserData.userCP;

			worksheet.getCell('I40').value = selectedUserData.userP1;
			worksheet.getCell('I41').value = selectedUserData.userP2;
			worksheet.getCell('I42').value = selectedUserData.userP3;
			worksheet.getCell('R40').value = selectedUserData.userP4;
			worksheet.getCell('R41').value = selectedUserData.userP5;
			worksheet.getCell('R42').value = selectedUserData.userP6;

			worksheet.getCell('J45').value = selectedUserData.userR1 ? 'X' : '';
			worksheet.getCell('J47').value = selectedUserData.userR2 ? 'X' : '';
			worksheet.getCell('J49').value = selectedUserData.userR3 ? 'X' : '';
			worksheet.getCell('J51').value = selectedUserData.userR4 ? 'X' : '';
			worksheet.getCell('X45').value = selectedUserData.userR5 ? 'X' : '';
			worksheet.getCell('X47').value = selectedUserData.userR6 ? 'X' : '';
			worksheet.getCell('X49').value = selectedUserData.userR7 ? 'X' : '';
			worksheet.getCell('X51').value = selectedUserData.userR8 ? 'X' : '';

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
			a.download = `UIS-${selectedUserData.userUN}.xlsx`;
			a.click();

			handleAction(
				'Successful',
				`Exported UIS of user ${selectedUserData.userUN}`,
				`${loclLN}, ${loclFN} ${loclMN}`
			);

			URL.revokeObjectURL(url);
		} else {
			workbook.eachSheet((sheet) => {
				if (sheet.name !== 'UserInformationB') {
					workbook.removeWorksheet(sheet.id);
				}
			});

			const worksheet = workbook.getWorksheet('UserInformationB');

			worksheet.getCell('I9').value = selectedUserData.userST;
			worksheet.getCell('I10').value = selectedUserData.userCL;

			worksheet.getCell('I12').value = selectedUserData.userUN;
			worksheet.getCell('I13').value = selectedUserData.userUN.replace('@csjd.joan.cloud', '');

			worksheet.getCell('I16').value = selectedUserData.userFN;
			worksheet.getCell('I17').value = selectedUserData.userLN;
			worksheet.getCell('I18').value = selectedUserData.userMN;
			worksheet.getCell('I19').value = selectedUserData.userSF;
			worksheet.getCell('I20').value = selectedUserData.userSX;

			worksheet.getCell('I22').value = selectedUserData.userAD;
			worksheet.getCell('I23').value = selectedUserData.userCN;
			worksheet.getCell('I24').value = selectedUserData.userEC;

			worksheet.getCell('I26').value = selectedUserData.userCP;

			worksheet.getCell('I31').value = selectedUserData.userP1;
			worksheet.getCell('I32').value = selectedUserData.userP2;
			worksheet.getCell('I33').value = selectedUserData.userP3;
			worksheet.getCell('R31').value = selectedUserData.userP4;
			worksheet.getCell('R32').value = selectedUserData.userP5;
			worksheet.getCell('R33').value = selectedUserData.userP6;

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
			a.download = `UIS-${selectedUserData.userUN}.xlsx`;
			a.click();

			handleAction(
				'Successful',
				`Exported UIS of user ${selectedUserData.userUN}`,
				`${loclLN}, ${loclFN} ${loclMN}`
			);

			URL.revokeObjectURL(url);
		}
	}

	const departmentOptions = [
		'Pre-Elementary',
		'Elementary',
		'Junior High School',
		'Senior High School'
	];

	const yearLevelOptions = {
		'Pre-Elementary': ['Kinder 1', 'Kinder 2'],
		Elementary: ['Grade 01', 'Grade 02', 'Grade 03', 'Grade 04', 'Grade 05', 'Grade 06'],
		'Junior High School': ['Grade 07', 'Grade 08', 'Grade 09', 'Grade 10'],
		'Senior High School': ['Grade 11', 'Grade 12']
	};

	let sections = [];
	let filteredSections = [];

	// async function fetchSections() {
	// 	try {
	// 		const db = getFirestore(app);
	// 		const sectionsCollection = collection(db, 'csjd-main', 'data', 'sections');
	// 		const querySnapshot = await getDocs(sectionsCollection);

	// 		sections = querySnapshot.docs.map((doc) => doc.data());

	// 		filteredSections = sections.filter((section) => section.sectYR === selectedUserData.userYR);
	// 	} catch (error) {
	// 		console.error('Error fetching user data:', error);
	// 	}
	// }

	// functions below must exist on all documents

	const modlID = 'D01';
	const modlNM = 'Transactions';

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

	let postNM, postDC, postSD, postND, postCL, userLN, userFN, userMN;

	let data = [];
	let limitedData = [];

	async function fetchTransactions() {
		try {
			const q = collection(db, 'csjd-main', 'data', 'bulletin');
			const snapshot = await getDocs(q);
			data = snapshot.docs.map((doc) => {
				const rawData = doc.data();
				const formattedDate = new Date(rawData.postDT.seconds * 1000).toLocaleString();
				return { ...rawData, postDT: formattedDate };
			});

			// data.sort((a, b) => new Date(b.postDT) - new Date(a.postDT));

			limitedData = data.slice(currentPage * 6, (currentPage + 1) * 6);
		} catch (error) {
			console.error('Error fetching action logs from Firestore:', error);
		}
	}

	function handleSearchTransact(event) {
		searchQuery = event.target.value.toLowerCase();
		limitedData = data
			.filter(
				(user) =>
					user.postNM.toLowerCase().includes(searchQuery) ||
					user.postBY.toLowerCase().includes(searchQuery)
			)
			.slice(0, 6);
	}

	function handleTransact() {
		let postID = makeID();

		postNM = document.getElementById('postNM').value;
		postDC = document.getElementById('postDC').value;
		postSD = document.getElementById('postSD').value;
		postND = document.getElementById('postND').value;
		postCL = document.getElementById('postCL').value;

		let postBY = `${loclLN}, ${loclFN} ${loclMN}`;

		const logRef = doc(db, 'csjd-main', 'data', 'bulletin', postNM);

		const logData = {
			postID,
			postCL,
			postBY,
			postNM,
			postDC,
			postDT: new Date(),
			postSD,
			postND
		};

		setDoc(logRef, logData)
			.then(() => {
				console.log('Action logged successfully.');
				handleAction(
					'Successful',
					`User ${loclLN}, ${loclFN} ${loclMN} posted ${postID}`,
					`${loclLN}, ${loclFN} ${loclMN}`
				);
				alert('Posted.');
			})
			.catch((error) => {
				console.error('Error logging action:', error);
			});
	}

	let selectedRowData = [];

	function handleRowClick(data) {
		selectedRowData = data;
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

	const validateEndDate = () => {
		const startDate = new Date(document.getElementById('postSD').value);
		const endDate = new Date(document.getElementById('postND').value);

		if (startDate > endDate) {
			alert('End date must be later than the start date.');
			document.getElementById('postND').value = '';
		}
	};

	onMount(async () => {
		loclID = localStorage.getItem('loclID');
		loclLN = localStorage.getItem('loclLN');
		loclFN = localStorage.getItem('loclFN');
		loclMN = localStorage.getItem('loclMN');
		loclCL = localStorage.getItem('loclCL');

		checkModuleAccess();
		fetchUsers();
		fetchTransactions();
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
											class="menu-item ml-6">System Defaults</a>
									</div>
								</div>
							</li>
							<li>
								<input
									checked
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
											class="menu-item menu-active ml-6">Bulletin Management</a>
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

	{#if modlST}
		<div
			class="flex flex-col h-auto w-full lg:w-5/6 pt-20 pb-8 pl-6 lg:pl-80 pr-6 gap-8 bg-white dark:bg-backgroundPrimary">
			<div class="flex flex-col">
				<h1 class="text-xl font-semibold">Posts Masterlist</h1>
				<p class="text-xs">Below is a list of all posts.</p>
			</div>
			<div class="divider my-0" />
			<div class="flex flex-row justify-between">
				<div class="flex flex-row gap-2">
					<input
						class="input"
						placeholder="Search..."
						on:input={handleSearchTransact} />
				</div>
				<div class="flex flex-row gap-2">
					<button
						class="btn btn-outline-success"
						on:click={() => fetchTransactions()}>Refresh</button>
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
							<th>Post Title</th>
							<th>Post Type</th>
							<th>Posted By</th>
						</tr>
					</thead>
					<tbody>
						{#each limitedData as item (item.postID)}
							<tr on:click={() => handleRowClick(item)}>
								<td>{item.postNM}</td>
								<td>{item.postCL}</td>
								<td>{item.postBY}</td>
								<!-- <td>{item.axonST}</td> -->
							</tr>
						{/each}
					</tbody>
				</table>
			</div>
			<div class="divider my-0" />
			<div class="flex flex-col">
				<h1 class="text-xl font-semibold">Post Information</h1>
				<p class="text-xs">Select a student to load his/her information.</p>
			</div>
			<div class="flex flex-col">
				<h1 class="text-sm font-semibold underline">Personal Information</h1>
			</div>
			<div class="form-group flex lg:flex-row">
				<div class="form-field w-full">
					<label
						for="postNM"
						class="form-label">Post Name</label>
					<input
						id="postNM"
						class="input max-w-full"
						bind:value={selectedRowData.postNM} />
				</div>
			</div>
			<div class="form-group flex lg:flex-row">
				<div class="form-field w-full">
					<label
						for="postDC"
						class="form-label">Post Description</label>
					<textarea
						id="postDC"
						class="input max-w-full"
						bind:value={selectedRowData.postDC} />
				</div>
			</div>
			<div class="form-group flex lg:flex-row">
				<div class="form-field w-full">
					<label
						for="postSD"
						class="form-label">Start Date</label>
					<input
						id="postSD"
						type="date"
						class="input max-w-full"
						bind:value={selectedRowData.postSD} />
				</div>
				<div class="form-field w-full">
					<label
						for="postND"
						class="form-label">End Date</label>
					<input
						id="postND"
						type="date"
						class="input max-w-full"
						bind:value={selectedRowData.postND}
						on:change={validateEndDate} />
				</div>
			</div>
			<div class="form-group flex lg:flex-row">
				<div class="form-field w-full">
					<label
						for="postCL"
						class="form-label">Type of Post</label>
					<select
						class="select"
						id="postCL"
						bind:value={selectedRowData.postCL}>
						<option>Information</option>
						<option>Event</option>
						<option>Announcement</option>
					</select>
				</div>
			</div>
			<button
				class="btn btn-outline-success"
				on:click={() => handleTransact()}>Save Post</button>
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
