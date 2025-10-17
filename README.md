<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DailyCash Admin Panel</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        :root {
            --primary-bg-start: #00c6ff;
            --primary-bg-end: #0072ff;
            --accent-color-start: #f5af19;
            --accent-color-end: #f12711;
            --content-bg: #f0f2f5;
            --sidebar-bg: #111827;
            --text-dark: #1f2937;
            --text-light: #f9fafb;
        }
        body { font-family: 'Poppins', sans-serif; background-color: var(--content-bg); }
        .admin-page { display: none; }
        .admin-page.active { display: block; animation: fadeIn 0.5s; }
        .btn-primary { background: linear-gradient(45deg, var(--accent-color-start), var(--accent-color-end)); color: white; transition: all 0.2s ease-in-out; box-shadow: 0 4px 15px rgba(241, 39, 17, 0.2); }
        .btn-primary:hover { transform: translateY(-2px); box-shadow: 0 6px 20px rgba(241, 39, 17, 0.3); }
        .input-field { border: 1px solid #d1d5db; transition: all 0.2s; }
        .input-field:focus { outline: none; border-color: var(--primary-bg-end); box-shadow: 0 0 0 3px rgba(0, 114, 255, 0.2); }
        #mobile-menu-button { display: none; }
        .sidebar { transition: transform 0.3s ease-in-out; }
        @media (max-width: 768px) {
            #mobile-menu-button { display: block; }
            .sidebar { transform: translateX(-100%); position: fixed; height: 100%; z-index: 40; }
            .sidebar.open { transform: translateX(0); }
            main { width: 100%; }
        }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>
    <div id="loader" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="animate-spin rounded-full h-16 w-16 border-t-2 border-b-2 border-yellow-400"></div>
    </div>
    
    <div id="auth-section" class="min-h-screen flex items-center justify-center bg-gray-100" style="background: linear-gradient(135deg, var(--primary-bg-start), var(--primary-bg-end));">
        <div class="bg-white p-8 rounded-lg shadow-lg w-full max-w-sm">
            <h2 class="text-2xl font-bold text-center text-gray-800 mb-6">Admin Panel Login</h2>
            <input id="login-email" type="email" placeholder="Email" class="w-full p-3 mb-4 input-field rounded-md">
            <input id="login-password" type="password" placeholder="Password" class="w-full p-3 mb-4 input-field rounded-md">
            <button id="login-btn" class="w-full p-3 btn-primary rounded-md font-semibold">Login</button>
            <p id="login-error" class="text-red-500 text-sm mt-2 text-center"></p>
        </div>
    </div>

    <div id="panel-section" class="hidden">
        <div class="relative min-h-screen md:flex">
            <div id="mobile-menu-overlay" class="fixed inset-0 bg-black bg-opacity-50 z-30 hidden md:hidden"></div>
            <aside class="sidebar w-64 bg-gray-900 text-gray-200 flex flex-col">
                <div class="p-4 text-2xl font-bold text-white flex justify-between items-center">
                    <span>RewardMe</span>
                    <button id="close-menu-button" class="md:hidden text-gray-400 hover:text-white"><i data-lucide="x"></i></button>
                </div>
                <nav class="mt-6 flex-grow" id="sidebar-nav">
                    <a href="#" data-page="dashboard" class="sidebar-link active flex items-center py-3 px-4 rounded-lg mx-2 transition-colors duration-200 hover:bg-gray-700"><i data-lucide="layout-dashboard" class="w-5 h-5 mr-3"></i>Dashboard</a>
                    <a href="#" data-page="users" class="sidebar-link flex items-center py-3 px-4 rounded-lg mx-2 transition-colors duration-200 hover:bg-gray-700"><i data-lucide="users" class="w-5 h-5 mr-3"></i>Users</a>
                    <a href="#" data-page="withdrawals" class="sidebar-link flex items-center py-3 px-4 rounded-lg mx-2 transition-colors duration-200 hover:bg-gray-700"><i data-lucide="banknote" class="w-5 h-5 mr-3"></i>Withdrawals</a>
                    <a href="#" data-page="notifications" class="sidebar-link flex items-center py-3 px-4 rounded-lg mx-2 transition-colors duration-200 hover:bg-gray-700"><i data-lucide="bell" class="w-5 h-5 mr-3"></i>Notifications</a>
                    <a href="#" data-page="settings" class="sidebar-link flex items-center py-3 px-4 rounded-lg mx-2 transition-colors duration-200 hover:bg-gray-700"><i data-lucide="settings" class="w-5 h-5 mr-3"></i>App Settings</a>
                </nav>
                <div class="p-4">
                    <button id="logout-btn" class="w-full bg-red-600 text-white py-3 rounded-lg hover:bg-red-700 font-semibold">Logout</button>
                </div>
            </aside>
            <main class="flex-1 p-4 md:p-6 overflow-y-auto">
                 <header class="flex justify-between items-center mb-6">
                    <button id="mobile-menu-button" class="text-gray-700"><i data-lucide="menu" class="w-6 h-6"></i></button>
                    <h1 id="page-title" class="text-2xl font-bold text-gray-800">Dashboard</h1>
                </header>
                <div id="dashboard" class="admin-page active"></div>
                <div id="users" class="admin-page"></div>
                <div id="withdrawals" class="admin-page"></div>
                <div id="notifications" class="admin-page"></div>
                <div id="settings" class="admin-page"></div>
            </main>
        </div>
    </div>

    <!-- Popups -->
    <div id="toast-notification" class="hidden fixed top-5 right-5 bg-green-600 text-white px-6 py-3 rounded-lg shadow-lg opacity-0 transition-opacity duration-300">
        <p id="toast-message"></p>
    </div>
    <div id="confirmation-popup" class="hidden fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-50 p-4"></div>
    <div id="balance-modal" class="hidden fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-50 p-4"></div>

    <script type="module">
        const firebaseConfig = {
  apiKey: "AIzaSyAQQKMWv8JWFjqQ1yLWH6dcTkCLcvNScXw",
  authDomain: "daily-cash-bd-9fb0d.firebaseapp.com",
  projectId: "daily-cash-bd-9fb0d",
  storageBucket: "daily-cash-bd-9fb0d.firebasestorage.app",
  messagingSenderId: "512869992491",
  appId: "1:512869992491:web:99eb7327b307c86d21426e",
  measurementId: "G-H70BEG9YDD"
};
        
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
        import { getAuth, onAuthStateChanged, signInWithEmailAndPassword, signOut } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";
        import { getFirestore, doc, getDoc, updateDoc, setDoc, collection, getDocs, query, where, writeBatch, addDoc, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        const loader = document.getElementById('loader');

        window.onload = () => {
            lucide.createIcons();
            setupEventListeners();
            onAuthStateChanged(auth, handleAuthStateChange);
        };

        function handleAuthStateChange(user) { if (user) { document.getElementById('auth-section').style.display = 'none'; document.getElementById('panel-section').style.display = 'block'; navigateTo('dashboard'); } else { document.getElementById('auth-section').style.display = 'flex'; document.getElementById('panel-section').style.display = 'none'; } }

        function setupEventListeners() {
            document.getElementById('login-btn').addEventListener('click', handleLogin);
            document.getElementById('logout-btn').addEventListener('click', () => signOut(auth));
            document.getElementById('sidebar-nav').addEventListener('click', (e) => { const link = e.target.closest('.sidebar-link'); if (link) { e.preventDefault(); navigateTo(link.dataset.page); closeMobileMenu(); } });
            const mobileMenuButton = document.getElementById('mobile-menu-button'); const closeMenuButton = document.getElementById('close-menu-button'); const mobileMenuOverlay = document.getElementById('mobile-menu-overlay'); const sidebar = document.querySelector('.sidebar');
            mobileMenuButton.addEventListener('click', () => { sidebar.classList.add('open'); mobileMenuOverlay.classList.remove('hidden'); });
            closeMenuButton.addEventListener('click', closeMobileMenu);
            mobileMenuOverlay.addEventListener('click', closeMobileMenu);
            function closeMobileMenu() { sidebar.classList.remove('open'); mobileMenuOverlay.classList.add('hidden'); }
        }
        
        async function handleLogin() { const email = document.getElementById('login-email').value; const password = document.getElementById('login-password').value; const errorEl = document.getElementById('login-error'); errorEl.textContent = ''; loader.style.display = 'flex'; try { await signInWithEmailAndPassword(auth, email, password); } catch (error) { errorEl.textContent = "Invalid email or password."; } finally { loader.style.display = 'none'; } }
        
        function navigateTo(pageId) {
            document.querySelectorAll('.admin-page').forEach(p => p.classList.remove('active'));
            document.getElementById(pageId).classList.add('active');
            document.querySelectorAll('.sidebar-link').forEach(l => l.classList.remove('active'));
            const activeLink = document.querySelector(`.sidebar-link[data-page="${pageId}"]`);
            if (activeLink) { activeLink.classList.add('active'); document.getElementById('page-title').textContent = activeLink.textContent; }
            const pageLoadActions = { 'dashboard': loadDashboard, 'users': loadUsers, 'withdrawals': loadWithdrawals, 'notifications': loadNotifications, 'settings': loadSettings };
            if (pageLoadActions[pageId]) pageLoadActions[pageId]();
        }

        async function loadDashboard() { const page = document.getElementById('dashboard'); page.innerHTML = `<div id="stats-grid" class="grid grid-cols-1 md:grid-cols-3 gap-6">Loading...</div>`; const usersSnap = await getDocs(collection(db, "users")); const withdrawalsSnap = await getDocs(collection(db, "withdrawals")); const pendingWithdrawals = withdrawalsSnap.docs.filter(d => d.data().status === 'pending').length; document.getElementById('stats-grid').innerHTML = ` <div class="bg-white p-6 rounded-lg shadow-md"><h3 class="text-gray-500">Total Users</h3><p class="text-3xl font-bold text-gray-800">${usersSnap.size}</p></div> <div class="bg-white p-6 rounded-lg shadow-md"><h3 class="text-gray-500">Total Withdrawals</h3><p class="text-3xl font-bold text-gray-800">${withdrawalsSnap.size}</p></div> <div class="bg-white p-6 rounded-lg shadow-md"><h3 class="text-gray-500">Pending Withdrawals</h3><p class="text-3xl font-bold text-orange-500">${pendingWithdrawals}</p></div> `; }

        async function loadUsers() {
            const page = document.getElementById('users');
            page.innerHTML = `<div class="bg-white shadow-md rounded-lg p-4">
                                <input type="text" id="user-search" placeholder="Search by name or email..." class="input-field w-full md:w-1/3 p-2 mb-4 rounded-md">
                                <div class="overflow-x-auto">Loading...</div>
                              </div>`;
            const usersSnap = await getDocs(collection(db, "users"));
            
            const renderTable = () => {
                const searchTerm = document.getElementById('user-search').value.toLowerCase();
                const filteredDocs = usersSnap.docs.filter(doc => {
                    const user = doc.data();
                    const name = user.name ? user.name.toLowerCase() : '';
                    const email = user.email ? user.email.toLowerCase() : '';
                    return name.includes(searchTerm) || email.includes(searchTerm);
                });

                let tableHTML = `<table class="w-full text-sm text-left text-gray-600">
                    <thead class="text-xs text-gray-700 uppercase bg-gray-50"><tr>
                        <th class="px-6 py-3">Name</th><th class="px-6 py-3">Email</th><th class="px-6 py-3">Balance</th><th class="px-6 py-3">Actions</th>
                    </tr></thead><tbody>`;
                filteredDocs.forEach(doc => {
                    const user = doc.data();
                    tableHTML += `<tr class="bg-white border-b hover:bg-gray-50">
                        <td class="px-6 py-4 font-medium text-gray-900">${user.name}</td>
                        <td class="px-6 py-4">${user.email || 'N/A'}</td>
                        <td class="px-6 py-4">${user.balance || 0}</td>
                        <td class="px-6 py-4 flex flex-col md:flex-row gap-2">
                            <button data-uid="${user.uid}" data-blocked="${user.isBlocked || false}" class="block-btn text-xs px-3 py-1 rounded-full ${user.isBlocked ? 'bg-green-100 text-green-800' : 'bg-red-100 text-red-800'}">${user.isBlocked ? 'Unblock' : 'Block'}</button>
                            <button data-uid="${user.uid}" data-name="${user.name}" data-balance="${user.balance || 0}" class="balance-btn text-xs px-3 py-1 rounded-full bg-blue-100 text-blue-800">Modify Balance</button>
                        </td>
                    </tr>`;
                });
                tableHTML += `</tbody></table>`;
                page.querySelector('.overflow-x-auto').innerHTML = tableHTML;
                document.querySelectorAll('.block-btn').forEach(btn => btn.addEventListener('click', toggleUserBlock));
                document.querySelectorAll('.balance-btn').forEach(btn => btn.addEventListener('click', openModifyBalanceModal));
            };

            document.getElementById('user-search').addEventListener('input', renderTable);
            renderTable();
        }

        function toggleUserBlock(e) { const { uid, blocked } = e.target.dataset; const isBlocked = blocked === 'true'; showConfirmation(`Are you sure you want to ${isBlocked ? 'unblock' : 'block'} this user?`, async () => { loader.style.display = 'flex'; await updateDoc(doc(db, "users", uid), { isBlocked: !isBlocked }); loader.style.display = 'none'; showToast(`User ${!isBlocked ? 'blocked' : 'unblocked'} successfully!`); loadUsers(); }); }

        function openModifyBalanceModal(e) {
            const { uid, name, balance } = e.target.dataset;
            const modal = document.getElementById('balance-modal');
            modal.innerHTML = `
                <div class="bg-white p-6 rounded-lg shadow-lg w-full max-w-sm text-left">
                    <h3 class="text-lg font-bold text-gray-800 mb-4">Modify Balance for ${name}</h3>
                    <p class="text-sm text-gray-600 mb-2">Current Balance: ${balance}</p>
                    <input type="number" id="new-balance-input" class="input-field w-full p-2 rounded-md" value="${balance}">
                    <div class="flex justify-end gap-4 mt-6">
                        <button id="cancel-balance-btn" class="bg-gray-300 text-gray-700 px-4 py-2 rounded-md font-semibold">Cancel</button>
                        <button id="save-balance-btn" class="btn-primary px-4 py-2 rounded-md font-semibold">Save</button>
                    </div>
                </div>
            `;
            modal.classList.remove('hidden');

            document.getElementById('cancel-balance-btn').onclick = () => modal.classList.add('hidden');
            document.getElementById('save-balance-btn').onclick = async () => {
                const newBalance = parseInt(document.getElementById('new-balance-input').value);
                if (isNaN(newBalance) || newBalance < 0) {
                    return showToast("Please enter a valid, non-negative number.", true);
                }
                
                loader.style.display = 'flex';
                try {
                    await updateDoc(doc(db, "users", uid), { balance: newBalance });
                    showToast("User balance updated successfully!");
                    modal.classList.add('hidden');
                    loadUsers();
                } catch (error) {
                    showToast("Failed to update balance.", true);
                } finally {
                    loader.style.display = 'none';
                }
            };
        }

        async function loadWithdrawals() { const page = document.getElementById('withdrawals'); page.innerHTML = `<div class="bg-white shadow-md rounded-lg p-4"><div class="overflow-x-auto">Loading...</div></div>`; const q = query(collection(db, "withdrawals"), where("status", "==", "pending")); const withdrawalsSnap = await getDocs(q); if(withdrawalsSnap.empty) { page.querySelector('.overflow-x-auto').innerHTML = `<p class="text-center p-4 text-gray-500">No pending requests.</p>`; return; } let tableHTML = `<table class="w-full text-sm text-left text-gray-600"> <thead class="text-xs text-gray-700 uppercase bg-gray-50"><tr> <th class="px-6 py-3">User</th><th class="px-6 py-3">Amount</th><th class="px-6 py-3">Method</th><th class="px-6 py-3">Details</th><th class="px-6 py-3">Actions</th> </tr></thead><tbody>`; withdrawalsSnap.forEach(doc => { const req = doc.data(); tableHTML += `<tr class="bg-white border-b hover:bg-gray-50"> <td class="px-6 py-4">${req.userName}</td> <td class="px-6 py-4 font-semibold">${req.amount}</td> <td class="px-6 py-4">${req.method}</td> <td class="px-6 py-4">${req.paymentDetail}</td> <td class="px-6 py-4 flex gap-2"> <button data-docid="${doc.id}" class="process-btn bg-green-500 text-white text-xs px-2 py-1 rounded" data-action="approved">Approve</button> <button data-docid="${doc.id}" data-uid="${req.userId}" data-amount="${req.amount}" class="process-btn bg-red-500 text-white text-xs px-2 py-1 rounded" data-action="rejected">Reject</button> </td> </tr>`; }); tableHTML += `</tbody></table>`; page.querySelector('.overflow-x-auto').innerHTML = tableHTML; document.querySelectorAll('.process-btn').forEach(btn => btn.addEventListener('click', processWithdrawal)); }
        
        function processWithdrawal(e) { const { docid, action, uid, amount } = e.target.dataset; showConfirmation(`Are you sure you want to ${action} this request?`, async () => { loader.style.display = 'flex'; const batch = writeBatch(db); const withdrawalRef = doc(db, "withdrawals", docid); batch.update(withdrawalRef, { status: action }); if (action === 'rejected' && uid && amount) { const userRef = doc(db, "users", uid); const userSnap = await getDoc(userRef); if (userSnap.exists()) { const currentBalance = userSnap.data().balance || 0; batch.update(userRef, { balance: currentBalance + parseInt(amount) }); } } await batch.commit(); loader.style.display = 'none'; showToast(`Withdrawal request has been ${action}.`); loadWithdrawals(); }); }
        
        async function loadNotifications() { const page = document.getElementById('notifications'); page.innerHTML = ` <div class="bg-white p-6 rounded-lg shadow-md"> <h2 class="text-xl font-bold mb-4 text-gray-800">Send Notification to All Users</h2> <div class="space-y-4"> <div> <label class="font-semibold text-gray-600">Title</label> <input type="text" id="notification-title" class="input-field w-full p-2 mt-1 rounded-md" placeholder="e.g., New Update!"> </div> <div> <label class="font-semibold text-gray-600">Message</label> <textarea id="notification-message" class="input-field w-full p-2 mt-1 rounded-md h-24" placeholder="Enter your message here..."></textarea> </div> <button id="send-notification-btn" class="btn-primary px-6 py-2 rounded-md font-semibold">Send Notification</button> </div> </div> `; document.getElementById('send-notification-btn').addEventListener('click', sendNotification); }

        async function sendNotification() { const title = document.getElementById('notification-title').value; const message = document.getElementById('notification-message').value; if (!title || !message) { return showToast('Please fill in both title and message.', true); } showConfirmation("Are you sure you want to send this notification to all users?", async () => { loader.style.display = 'flex'; try { await addDoc(collection(db, "notifications"), { title: title, message: message, createdAt: serverTimestamp() }); showToast('Notification sent successfully!'); document.getElementById('notification-title').value = ''; document.getElementById('notification-message').value = ''; } catch (error) { showToast('Failed to send notification.', true); console.error("Error sending notification:", error); } finally { loader.style.display = 'none'; } }); }
        
        async function loadSettings() { const page = document.getElementById('settings'); page.innerHTML = `<div>Loading settings...</div>`; const configSnap = await getDoc(doc(db, "config", "main")); const config = configSnap.exists() ? configSnap.data() : {}; renderSettings(config); }

        function renderSettings(config) {
             const page = document.getElementById('settings');
             page.innerHTML = `
                <div class="space-y-8">
                    <div class="bg-white p-6 rounded-lg shadow-md"><h2 class="text-xl font-bold mb-4 text-gray-800">Rewards & Payment</h2><div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div><label class="font-semibold text-gray-600">Daily Ad Watch Limit</label><input type="number" id="dailyAdLimit" class="input-field w-full p-2 mt-1 rounded-md" value="${config.dailyAdLimit !== undefined ? config.dailyAdLimit : 20}"></div>
                        <div><label class="font-semibold text-gray-600">Min Withdrawal</label><input type="number" id="minWithdrawal" class="input-field w-full p-2 mt-1 rounded-md" value="${config.minWithdrawal || 0}"></div>
                        <div><label class="font-semibold text-gray-600">Ad Watch Reward</label><input type="number" id="adWatchReward" class="input-field w-full p-2 mt-1 rounded-md" value="${config.adWatchReward || 0}"></div>
                        <div><label class="font-semibold text-gray-600">Color Game Reward</label><input type="number" id="colorGameReward" class="input-field w-full p-2 mt-1 rounded-md" value="${config.colorGameReward || 0}"></div>
                        <div><label class="font-semibold text-gray-600">Payment Methods (comma separated)</label><input type="text" id="paymentMethods" class="input-field w-full p-2 mt-1 rounded-md" value="${(config.paymentMethods || []).join(', ')}"></div>
                        <div class="col-span-1 md:col-span-2 grid grid-cols-2 gap-4 border-t pt-4 mt-4">
                            <div><label class="font-semibold text-gray-600">Coin Value (Coins)</label><input type="number" id="coinValueCoins" class="input-field w-full p-2 mt-1 rounded-md" value="${config.coinValueCoins || 1000}"></div>
                            <div><label class="font-semibold text-gray-600">Equivalent Value (INR)</label><input type="number" id="coinValueInr" class="input-field w-full p-2 mt-1 rounded-md" value="${config.coinValueInr || 10}"></div>
                        </div>
                    </div></div>
                    <div class="bg-white p-6 rounded-lg shadow-md"><h2 class="text-xl font-bold mb-4 text-gray-800">Referral System</h2><div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div><label class="font-semibold text-gray-600">Referral Bonus</label><input type="number" id="referralBonus" class="input-field w-full p-2 mt-1 rounded-md" value="${config.referralBonus || 0}"></div>
                    </div></div>
                    <div class="bg-white p-6 rounded-lg shadow-md"><h2 class="text-xl font-bold mb-4 text-gray-800">Special Ad Click Task</h2><div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <div><label class="font-semibold text-gray-600">Show after # Ads</label><input type="number" id="watchAdSpecialTaskCount" class="input-field w-full p-2 mt-1 rounded-md" value="${config.watchAdSpecialTaskCount || 0}"></div>
                        <div><label class="font-semibold text-gray-600">Task Reward</label><input type="number" id="watchAdSpecialTaskReward" class="input-field w-full p-2 mt-1 rounded-md" value="${config.watchAdSpecialTaskReward || 0}"></div>
                        <div><label class="font-semibold text-gray-600">Wait Timer (sec)</label><input type="number" id="watchAdSpecialTaskTimer" class="input-field w-full p-2 mt-1 rounded-md" value="${config.watchAdSpecialTaskTimer || 0}"></div>
                    </div></div>
                    <div id="dynamic-lists"></div>
                </div>
                <button id="save-settings-btn" class="btn-primary mt-8 px-8 py-3 rounded-md font-semibold">Save All Settings</button>
            `;
            
            renderListEditor('sliderBanners', 'Slider Banners', config.sliderBanners, ['img', 'link']);
            renderListEditor('socialLinks', 'Social Links (Profile Page)', config.socialLinks, ['name', 'icon', 'link']);
            renderListEditor('websitesToVisit', 'Visit & Earn Tasks', config.websitesToVisit, ['name', 'link', 'reward', 'timer']);
            renderListEditor('socialTasks', 'Social Follow Tasks', config.socialTasks, ['name', 'link', 'icon', 'reward']);
            
            document.getElementById('save-settings-btn').addEventListener('click', saveSettings);
        }
        
        function renderListEditor(key, title, items, fields) { const container = document.getElementById('dynamic-lists'); const section = document.createElement('div'); section.className = 'bg-white p-6 rounded-lg shadow-md mb-8'; let listHTML = `<h2 class="text-xl font-bold mb-4 text-gray-800">${title}</h2><div id="${key}-list" class="space-y-2">`; (items || []).forEach((item, index) => { listHTML += createItemHtml(key, index, fields, item); }); listHTML += `</div><button class="add-item-btn mt-4 text-sm btn-primary px-4 py-2 rounded-md" data-key="${key}">+ Add New</button>`; section.innerHTML = listHTML; container.appendChild(section); section.addEventListener('click', function(e) { if (e.target.closest('.add-item-btn')) { const key = e.target.closest('.add-item-btn').dataset.key; const listContainer = document.getElementById(`${key}-list`); const newIndex = listContainer.children.length; const newItemHtml = createItemHtml(key, newIndex, fields); listContainer.insertAdjacentHTML('beforeend', newItemHtml); } else if (e.target.closest('.remove-item-btn')) { e.target.closest('.item-row').remove(); } }); }

        function createItemHtml(key, index, fields, item = {}) { let inputsHtml = ''; fields.forEach(field => { inputsHtml += `<input type="text" class="input-field text-sm p-2 rounded-md" placeholder="${field}" data-key="${key}" data-field="${field}" value="${item[field] || ''}">`; }); return `<div class="item-row grid grid-cols-2 md:grid-cols-${fields.length + 1} gap-2 items-center border p-2 rounded">${inputsHtml}<button class="remove-item-btn text-red-500 font-bold text-xl p-1 justify-self-center">&times;</button></div>`; }
        
        function getSettingsFromUI() {
            const newConfig = {};
            ['dailyAdLimit', 'minWithdrawal', 'adWatchReward', 'colorGameReward', 'referralBonus', 'watchAdSpecialTaskCount', 'watchAdSpecialTaskReward', 'watchAdSpecialTaskTimer', 'coinValueCoins', 'coinValueInr'].forEach(id => {
                newConfig[id] = parseInt(document.getElementById(id).value);
                if(isNaN(newConfig[id])) newConfig[id] = 0;
            });
            
            newConfig.paymentMethods = document.getElementById('paymentMethods').value.split(',').map(s => s.trim()).filter(Boolean);

            ['sliderBanners', 'socialLinks', 'websitesToVisit', 'socialTasks'].forEach(key => {
                const listContainer = document.getElementById(`${key}-list`);
                if (!listContainer) return;
                newConfig[key] = [];
                const items = listContainer.querySelectorAll('.item-row');
                items.forEach(item => {
                    const inputs = item.querySelectorAll('input');
                    const newItem = {};
                    let hasValue = false;
                    inputs.forEach(input => {
                        const value = input.value.trim();
                        if (value) hasValue = true;
                        const numericFields = ['reward', 'timer'];
                        newItem[input.dataset.field] = numericFields.includes(input.dataset.field) ? (parseInt(value) || 0) : value;
                    });
                    if (hasValue) newConfig[key].push(newItem);
                });
            });
            return newConfig;
        }

        function saveSettings() { showConfirmation("Are you sure you want to save these settings?", async () => { const newConfig = getSettingsFromUI(); loader.style.display = 'flex'; try { await setDoc(doc(db, "config", "main"), newConfig, { merge: true }); showToast('Settings saved successfully!'); } catch (error) { showToast('Error: Could not save settings.', true); console.error("Error saving settings:", error); } finally { loader.style.display = 'none'; } }); }
        
        function showToast(message, isError = false) { const toast = document.getElementById('toast-notification'); const toastMessage = document.getElementById('toast-message'); toastMessage.textContent = message; toast.className = `fixed top-5 right-5 text-white px-6 py-3 rounded-lg shadow-lg opacity-0 transition-opacity duration-300 ${isError ? 'bg-red-600' : 'bg-green-600'}`; toast.classList.remove('hidden'); setTimeout(() => toast.classList.remove('opacity-0'), 50); setTimeout(() => { toast.classList.add('opacity-0'); setTimeout(() => toast.classList.add('hidden'), 300); }, 3000); }

        function showConfirmation(message, onConfirm) {
            const popup = document.getElementById('confirmation-popup');
            popup.innerHTML = `
                <div class="bg-white p-6 rounded-lg shadow-lg w-full max-w-sm text-center">
                    <p class="mb-6 text-gray-700">${message}</p>
                    <div class="flex justify-center space-x-4">
                        <button id="confirm-ok-btn" class="btn-primary px-6 py-2 rounded-md font-semibold">Yes</button>
                        <button id="confirm-cancel-btn" class="bg-gray-300 text-gray-700 px-6 py-2 rounded-md font-semibold">Cancel</button>
                    </div>
                </div>`;
            popup.classList.remove('hidden');
            document.getElementById('confirm-ok-btn').onclick = () => { popup.classList.add('hidden'); onConfirm(); };
            document.getElementById('confirm-cancel-btn').onclick = () => { popup.classList.add('hidden'); };
        }
    </script>
</body>
</html>

