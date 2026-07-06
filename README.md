<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Analku - Sistem Akuntansi & Stok</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f8fafc; }
        .tab-active { background-color: #e0e7ff; color: #4338ca; border-right: 4px solid #4338ca; }
        .glass-panel { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); }
        /* Scrollbar styling */
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; border-radius: 4px; }
        ::-webkit-scrollbar-thumb { background: #c1c1c1; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #a8a8a8; }
        .print-area { background: white; padding: 20px; border-radius: 8px; }
    </style>
</head>
<body class="text-slate-800 h-screen overflow-hidden flex">

    <!-- Sidebar -->
    <aside class="w-64 bg-white border-r border-slate-200 flex flex-col h-full shadow-sm z-10">
        <div class="p-6 border-b border-slate-100 flex items-center space-x-3">
            <div class="w-10 h-10 rounded-xl bg-indigo-600 text-white flex items-center justify-center text-xl font-bold shadow-lg shadow-indigo-200">
                A
            </div>
            <h1 class="text-2xl font-bold text-indigo-900 tracking-tight">Analku.</h1>
        </div>
        
        <nav class="flex-1 py-4 overflow-y-auto" id="main-nav">
            <button onclick="switchTab('dashboard')" id="nav-dashboard" class="w-full flex items-center space-x-3 px-6 py-3.5 text-left text-slate-600 hover:bg-slate-50 transition-colors tab-active">
                <i class="fas fa-chart-pie w-5"></i>
                <span class="font-medium">Dashboard Rekap</span>
            </button>
            
            <div class="px-6 py-3 mt-2 text-xs font-semibold text-slate-400 uppercase tracking-wider">Operasional</div>
            
            <button onclick="switchTab('stock')" id="nav-stock" class="w-full flex items-center space-x-3 px-6 py-3.5 text-left text-slate-600 hover:bg-slate-50 transition-colors">
                <i class="fas fa-box-open w-5"></i>
                <span class="font-medium">Manajemen Stok</span>
            </button>
            <button onclick="switchTab('sales')" id="nav-sales" class="w-full flex items-center space-x-3 px-6 py-3.5 text-left text-slate-600 hover:bg-slate-50 transition-colors">
                <i class="fas fa-shopping-cart w-5"></i>
                <span class="font-medium">Kasir & Penjualan</span>
            </button>

            <div class="px-6 py-3 mt-2 text-xs font-semibold text-slate-400 uppercase tracking-wider">Akuntansi</div>

            <button onclick="switchTab('journal')" id="nav-journal" class="w-full flex items-center space-x-3 px-6 py-3.5 text-left text-slate-600 hover:bg-slate-50 transition-colors">
                <i class="fas fa-book w-5"></i>
                <span class="font-medium">Jurnal Transaksi</span>
            </button>
            <button onclick="switchTab('worksheet')" id="nav-worksheet" class="w-full flex items-center space-x-3 px-6 py-3.5 text-left text-slate-600 hover:bg-slate-50 transition-colors">
                <i class="fas fa-table w-5"></i>
                <span class="font-medium">Neraca Lajur</span>
            </button>
            <button onclick="switchTab('income')" id="nav-income" class="w-full flex items-center space-x-3 px-6 py-3.5 text-left text-slate-600 hover:bg-slate-50 transition-colors">
                <i class="fas fa-file-invoice-dollar w-5"></i>
                <span class="font-medium">Laba Rugi</span>
            </button>
        </nav>
        <div class="p-4 border-t border-slate-100 text-center text-xs text-slate-400">
            Analku Sistem v1.0
        </div>
    </aside>

    <!-- Main Content -->
    <main class="flex-1 flex flex-col h-full bg-slate-50 relative overflow-hidden">
        <!-- Header -->
        <header class="h-20 bg-white/80 backdrop-blur-md border-b border-slate-200 flex items-center justify-between px-8 z-10">
            <div>
                <h2 id="page-title" class="text-xl font-bold text-slate-800">Dashboard Rekapitulasi</h2>
                <p id="page-subtitle" class="text-sm text-slate-500">Ringkasan keuangan bulan berjalan</p>
            </div>
            <div class="flex items-center space-x-4">
                <div class="bg-indigo-50 text-indigo-700 px-4 py-2 rounded-lg text-sm font-medium border border-indigo-100">
                    <i class="far fa-calendar-alt mr-2"></i> <span id="current-month-display">Bulan Ini</span>
                </div>
                <button onclick="downloadPDF()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-5 py-2.5 rounded-lg shadow-sm shadow-indigo-200 transition-all font-medium flex items-center space-x-2">
                    <i class="fas fa-file-pdf"></i>
                    <span>Download PDF</span>
                </button>
            </div>
        </header>

        <!-- Content Area -->
        <div class="flex-1 overflow-auto p-8" id="pdf-content-area">
            <div id="content-container" class="max-w-6xl mx-auto space-y-6">
                <!-- Dinamis konten dirender disini -->
            </div>
        </div>
    </main>

    <!-- Custom Modal System -->
    <div id="custom-modal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 hidden flex items-center justify-center opacity-0 transition-opacity duration-300">
        <div class="bg-white rounded-2xl shadow-2xl w-full max-w-md transform scale-95 transition-transform duration-300" id="modal-panel">
            <div class="p-6 border-b border-slate-100 flex items-center justify-between">
                <h3 id="modal-title" class="text-lg font-bold text-slate-800 flex items-center gap-2">
                    <i class="fas fa-info-circle text-indigo-500" id="modal-icon"></i> Title
                </h3>
                <button onclick="closeModal()" class="text-slate-400 hover:text-slate-600">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <div class="p-6">
                <p id="modal-message" class="text-slate-600 text-sm leading-relaxed">Message goes here.</p>
            </div>
            <div class="p-4 bg-slate-50 border-t border-slate-100 rounded-b-2xl flex justify-end space-x-3" id="modal-actions">
                <!-- Buttons injected here -->
            </div>
        </div>
    </div>

    <!-- Modals for Forms (Add Stock, Add Journal, Add Sale) -->
    <div id="form-modal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-40 hidden flex items-center justify-center">
        <div class="bg-white rounded-2xl shadow-2xl w-full max-w-lg overflow-hidden flex flex-col max-h-[90vh]">
            <div class="p-5 border-b border-slate-100 flex items-center justify-between bg-white">
                <h3 id="form-modal-title" class="text-lg font-bold text-slate-800">Form</h3>
                <button onclick="closeFormModal()" class="text-slate-400 hover:text-slate-600">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <div class="p-6 overflow-y-auto bg-slate-50 flex-1" id="form-modal-body">
                <!-- Form fields injected here -->
            </div>
            <div class="p-4 border-t border-slate-100 bg-white flex justify-end space-x-3">
                <button onclick="closeFormModal()" class="px-4 py-2 text-slate-600 font-medium hover:bg-slate-100 rounded-lg transition-colors">Batal</button>
                <button id="form-modal-save" class="px-6 py-2 bg-indigo-600 text-white font-medium hover:bg-indigo-700 rounded-lg shadow-sm transition-colors">Simpan</button>
            </div>
        </div>
    </div>

    <script>
        // --- DATA & STATE MANAGEMENT ---
        const State = {
            currentTab: 'dashboard',
            accounts: [
                { id: '111', name: 'Kas', type: 'Asset', normal: 'Debit' },
                { id: '112', name: 'Piutang Usaha', type: 'Asset', normal: 'Debit' },
                { id: '113', name: 'Persediaan Barang', type: 'Asset', normal: 'Debit' },
                { id: '121', name: 'Peralatan', type: 'Asset', normal: 'Debit' },
                { id: '211', name: 'Hutang Usaha', type: 'Liability', normal: 'Credit' },
                { id: '311', name: 'Modal Pemilik', type: 'Equity', normal: 'Credit' },
                { id: '411', name: 'Penjualan', type: 'Revenue', normal: 'Credit' },
                { id: '511', name: 'Harga Pokok Penjualan (HPP)', type: 'Expense', normal: 'Debit' },
                { id: '512', name: 'Beban Operasional', type: 'Expense', normal: 'Debit' },
                { id: '513', name: 'Beban Sewa', type: 'Expense', normal: 'Debit' }
            ],
            inventory: [], // Diubah menjadi kosong agar user bisa menambah secara manual
            sales: [],
            journals: []
        };

        // Populate initial mock data so app looks alive (Sekarang kosong)
        function initMockData() {
            // Memulai aplikasi dengan kondisi bersih tanpa transaksi awal
        }

        // Helper untuk memuat data demo jika pengguna ingin melakukan simulasi instan
        function loadDemoData() {
            const today = new Date().toISOString().split('T')[0];
            
            State.inventory = [
                { id: 'ITM-001', name: 'Kopi Arabika 1Kg', qty: 50, costPrice: 80000, sellPrice: 120000 },
                { id: 'ITM-002', name: 'Gula Aren Cair 1L', qty: 30, costPrice: 25000, sellPrice: 40000 },
                { id: 'ITM-003', name: 'Suku UHT 1L', qty: 100, costPrice: 15000, sellPrice: 22000 }
            ];

            // Setoran Modal Awal
            addJournalRaw(today, 'Setoran Modal Awal', 'Sederhana', [
                { accId: '111', debit: 50000000, credit: 0 },
                { accId: '311', debit: 0, credit: 50000000 }
            ]);

            // Pembelian Persediaan Barang
            addJournalRaw(today, 'Pembelian Persediaan Barang', 'Sederhana', [
                { accId: '113', debit: 6250000, credit: 0 },
                { accId: '111', debit: 0, credit: 6250000 }
            ]);

            // Simulasi penjualan
            recordSaleRaw('ITM-001', 5, today);
            recordSaleRaw('ITM-003', 10, today);

            // Operasional
            addJournalRaw(today, 'Bayar Sewa Ruko Bulan ini', 'Sederhana', [
                { accId: '513', debit: 2000000, credit: 0 },
                { accId: '111', debit: 0, credit: 2000000 }
            ]);
            
            renderView();
            showModal('Data Demo Dimuat', 'Sistem berhasil diisi dengan data simulasi operasional & keuangan.', 'success');
        }

        // --- CORE LOGIC & HELPERS ---
        const formatRp = (num) => new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(num);
        const getAccName = (id) => State.accounts.find(a => a.id === id)?.name || 'Unknown';
        const getAccType = (id) => State.accounts.find(a => a.id === id)?.type || '';
        const getAccNormal = (id) => State.accounts.find(a => a.id === id)?.normal || '';
        const generateId = (prefix) => prefix + '-' + Math.random().toString(36).substr(2, 6).toUpperCase();

        let modalConfirmCallback = null;

        function showModal(title, message, type = 'info', onConfirm = null) {
            const modal = document.getElementById('custom-modal');
            const panel = document.getElementById('modal-panel');
            document.getElementById('modal-title').innerHTML = `<i class="fas ${type === 'danger' ? 'fa-exclamation-triangle text-red-500' : type === 'success' ? 'fa-check-circle text-green-500' : 'fa-info-circle text-indigo-500'}"></i> ${title}`;
            document.getElementById('modal-message').innerText = message;
            
            const actions = document.getElementById('modal-actions');
            actions.innerHTML = '';
            
            if (onConfirm) {
                modalConfirmCallback = onConfirm;
                actions.innerHTML = `
                    <button onclick="closeModal()" class="px-4 py-2 text-slate-600 font-medium hover:bg-slate-200 rounded-lg transition-colors">Batal</button>
                    <button onclick="executeModalConfirm()" class="px-4 py-2 bg-${type === 'danger' ? 'red' : 'indigo'}-600 text-white font-medium hover:bg-${type === 'danger' ? 'red' : 'indigo'}-700 rounded-lg shadow-sm transition-colors">Ya, Lanjutkan</button>
                `;
            } else {
                actions.innerHTML = `
                    <button onclick="closeModal()" class="px-4 py-2 bg-indigo-600 text-white font-medium hover:bg-indigo-700 rounded-lg shadow-sm transition-colors">Mengerti</button>
                `;
            }

            modal.classList.remove('hidden');
            setTimeout(() => {
                modal.classList.remove('opacity-0');
                panel.classList.remove('scale-95');
            }, 10);
        }

        function closeModal() {
            const modal = document.getElementById('custom-modal');
            const panel = document.getElementById('modal-panel');
            modal.classList.add('opacity-0');
            panel.classList.add('scale-95');
            setTimeout(() => modal.classList.add('hidden'), 300);
            modalConfirmCallback = null;
        }

        function executeModalConfirm() {
            if (modalConfirmCallback) modalConfirmCallback();
            closeModal();
        }

        // Form Modal
        function openFormModal(title, contentHTML, saveCallback) {
            document.getElementById('form-modal-title').innerText = title;
            document.getElementById('form-modal-body').innerHTML = contentHTML;
            document.getElementById('form-modal').classList.remove('hidden');
            const saveBtn = document.getElementById('form-modal-save');
            saveBtn.onclick = () => {
                if(saveCallback()) closeFormModal();
            };
        }

        function closeFormModal() {
            document.getElementById('form-modal').classList.add('hidden');
        }

        function addJournalRaw(date, desc, type, entries) {
            const totalDeb = entries.reduce((s, e) => s + Number(e.debit), 0);
            const totalCre = entries.reduce((s, e) => s + Number(e.credit), 0);
            
            if (totalDeb !== totalCre) {
                console.error("Jurnal tidak seimbang!");
                return false;
            }

            const journal = {
                id: generateId('JRN'),
                date: date,
                description: desc,
                type: type, // 'Sederhana' or 'Penyesuaian'
                entries: entries
            };
            State.journals.push(journal);
            return true;
        }

        function recordSaleRaw(itemId, qty, date) {
            const item = State.inventory.find(i => i.id === itemId);
            if (!item || item.qty < qty) return false;

            const totalPenjualan = item.sellPrice * qty;
            const totalHPP = item.costPrice * qty;

            // Update Stock
            item.qty -= qty;

            // Record Sale
            const sale = {
                id: generateId('SAL'),
                date: date,
                itemName: item.name,
                qty: qty,
                sellPrice: item.sellPrice,
                costPrice: item.costPrice,
                total: totalPenjualan,
                hpp: totalHPP
            };
            State.sales.push(sale);

            // Auto Journaling Integration!
            const entries = [
                // Pendapatan
                { accId: '111', debit: totalPenjualan, credit: 0 },   // Kas bertambah
                { accId: '411', debit: 0, credit: totalPenjualan },   // Penjualan bertambah
                // HPP & Persediaan
                { accId: '511', debit: totalHPP, credit: 0 },         // HPP bertambah (Expense)
                { accId: '113', debit: 0, credit: totalHPP }          // Persediaan berkurang
            ];
            
            addJournalRaw(date, `Penjualan ${qty}x ${item.name}`, 'Sederhana', entries);
            return true;
        }

        function deleteJournal(id) {
            showModal('Hapus Jurnal', 'Anda yakin ingin menghapus jurnal ini? Ini dapat mempengaruhi neraca.', 'danger', () => {
                State.journals = State.journals.filter(j => j.id !== id);
                renderView();
            });
        }

        function calculateWorksheet() {
            let worksheet = {};
            
            // Initialize worksheet skeleton
            State.accounts.forEach(acc => {
                worksheet[acc.id] = {
                    name: acc.name,
                    normal: acc.normal,
                    type: acc.type,
                    unadjDebit: 0, unadjCredit: 0,
                    adjDebit: 0, adjCredit: 0,
                    adjBalDebit: 0, adjBalCredit: 0,
                    isDebit: 0, isCredit: 0, // Income Statement
                    bsDebit: 0, bsCredit: 0  // Balance Sheet
                };
            });

            // 1. Unadjusted Trial Balance (from Jurnal Sederhana) & 2. Adjustments
            State.journals.forEach(j => {
                j.entries.forEach(e => {
                    if (!worksheet[e.accId]) return;
                    if (j.type === 'Sederhana') {
                        worksheet[e.accId].unadjDebit += Number(e.debit);
                        worksheet[e.accId].unadjCredit += Number(e.credit);
                    } else if (j.type === 'Penyesuaian') {
                        worksheet[e.accId].adjDebit += Number(e.debit);
                        worksheet[e.accId].adjCredit += Number(e.credit);
                    }
                });
            });

            // 3. Adjusted Trial Balance & Categorization
            Object.values(worksheet).forEach(row => {
                // Determine raw unadjusted balance based on normal balance
                let rawUnadjBal = row.unadjDebit - row.unadjCredit;
                if (row.normal === 'Credit') rawUnadjBal = row.unadjCredit - row.unadjDebit;

                // Apply adjustments
                let netAdj = row.adjDebit - row.adjCredit;
                if (row.normal === 'Credit') netAdj = row.adjCredit - row.adjDebit;

                let finalBal = rawUnadjBal + netAdj;

                if (finalBal > 0) {
                    if (row.normal === 'Debit') row.adjBalDebit = finalBal;
                    else row.adjBalCredit = finalBal;
                } else if (finalBal < 0) {
                    // Reversed normal balance situation
                    if (row.normal === 'Debit') row.adjBalCredit = Math.abs(finalBal);
                    else row.adjBalDebit = Math.abs(finalBal);
                }

                // Distribute to Laba Rugi (Revenue/Expense) or Neraca (Asset/Liab/Equity)
                if (row.type === 'Revenue' || row.type === 'Expense') {
                    row.isDebit = row.adjBalDebit;
                    row.isCredit = row.adjBalCredit;
                } else {
                    row.bsDebit = row.adjBalDebit;
                    row.bsCredit = row.adjBalCredit;
                }
            });

            return worksheet;
        }

        function renderDashboard() {
            const totalPenjualan = State.sales.reduce((s, x) => s + x.total, 0);
            const totalHPP = State.sales.reduce((s, x) => s + x.hpp, 0);
            const labaKotor = totalPenjualan - totalHPP;
            
            // Calculate Expenses from Journals (only Expense accounts, excluding HPP for operational calc, but HPP is already expense)
            let totalBebanOperasional = 0;
            const worksheet = calculateWorksheet();
            Object.keys(worksheet).forEach(id => {
                const w = worksheet[id];
                if (w.type === 'Expense' && id !== '511') { // 511 is HPP
                    let bal = w.adjBalDebit - w.adjBalCredit;
                    totalBebanOperasional += bal;
                }
            });

            const labaBersih = labaKotor - totalBebanOperasional;

            // Banner panduan jika aplikasi masih kosong sepenuhnya
            let emptyStateBanner = '';
            if (State.inventory.length === 0 && State.journals.length === 0) {
                emptyStateBanner = `
                    <div class="bg-indigo-50 border-2 border-dashed border-indigo-200 rounded-2xl p-8 text-center mb-6">
                        <div class="w-16 h-16 bg-indigo-100 text-indigo-600 rounded-full flex items-center justify-center mx-auto mb-4">
                            <i class="fas fa-magic text-2xl"></i>
                        </div>
                        <h3 class="text-lg font-bold text-slate-800">Sistem Keuangan & Stok Anda Siap Digunakan!</h3>
                        <p class="text-sm text-slate-500 max-w-md mx-auto mt-1 mb-6">Mulai tambahkan stok barang di menu operasional, melakukan transaksi kasir, atau mencatat jurnal umum manual. Anda juga dapat memuat data simulasi untuk mencoba sistem.</p>
                        <div class="flex justify-center space-x-3">
                            <button onclick="switchTab('stock')" class="bg-indigo-600 hover:bg-indigo-700 text-white px-5 py-2.5 rounded-lg shadow-sm font-medium text-sm flex items-center gap-2">
                                <i class="fas fa-plus"></i> Tambah Barang Baru
                            </button>
                            <button onclick="loadDemoData()" class="bg-white hover:bg-slate-50 text-indigo-600 border border-indigo-200 px-5 py-2.5 rounded-lg font-medium text-sm flex items-center gap-2">
                                <i class="fas fa-cloud-download-alt"></i> Muat Data Demo Simulasi
                            </button>
                        </div>
                    </div>
                `;
            }

            const html = `
                ${emptyStateBanner}
                <div class="grid grid-cols-1 md:grid-cols-4 gap-6">
                    <div class="glass-panel p-6 rounded-2xl border border-slate-100 shadow-sm relative overflow-hidden group hover:shadow-md transition-all">
                        <div class="absolute -right-6 -top-6 w-24 h-24 bg-blue-50 rounded-full group-hover:scale-110 transition-transform"></div>
                        <div class="relative z-10">
                            <p class="text-sm font-medium text-slate-500 mb-1">Total Penjualan</p>
                            <h3 class="text-2xl font-bold text-slate-800">${formatRp(totalPenjualan)}</h3>
                            <p class="text-xs text-green-500 mt-2 flex items-center"><i class="fas fa-arrow-up mr-1"></i> Bulan ini</p>
                        </div>
                    </div>
                    <div class="glass-panel p-6 rounded-2xl border border-slate-100 shadow-sm relative overflow-hidden group hover:shadow-md transition-all">
                        <div class="absolute -right-6 -top-6 w-24 h-24 bg-orange-50 rounded-full group-hover:scale-110 transition-transform"></div>
                        <div class="relative z-10">
                            <p class="text-sm font-medium text-slate-500 mb-1">Total HPP</p>
                            <h3 class="text-2xl font-bold text-slate-800">${formatRp(totalHPP)}</h3>
                            <p class="text-xs text-orange-500 mt-2 flex items-center"><i class="fas fa-box mr-1"></i> Beban Pokok</p>
                        </div>
                    </div>
                    <div class="glass-panel p-6 rounded-2xl border border-slate-100 shadow-sm relative overflow-hidden group hover:shadow-md transition-all">
                        <div class="absolute -right-6 -top-6 w-24 h-24 bg-red-50 rounded-full group-hover:scale-110 transition-transform"></div>
                        <div class="relative z-10">
                            <p class="text-sm font-medium text-slate-500 mb-1">Beban Operasional</p>
                            <h3 class="text-2xl font-bold text-slate-800">${formatRp(totalBebanOperasional)}</h3>
                            <p class="text-xs text-slate-500 mt-2 flex items-center"><i class="fas fa-receipt mr-1"></i> Non-HPP</p>
                        </div>
                    </div>
                    <div class="glass-panel p-6 rounded-2xl border border-slate-100 shadow-sm relative overflow-hidden group hover:shadow-md transition-all bg-indigo-600 text-white">
                        <div class="absolute -right-6 -top-6 w-24 h-24 bg-indigo-500 rounded-full group-hover:scale-110 transition-transform"></div>
                        <div class="relative z-10">
                            <p class="text-sm font-medium text-indigo-100 mb-1">Laba Bersih</p>
                            <h3 class="text-2xl font-bold text-white">${formatRp(labaBersih)}</h3>
                            <p class="text-xs text-indigo-200 mt-2 flex items-center"><i class="fas fa-wallet mr-1"></i> Profit</p>
                        </div>
                    </div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mt-6">
                    <div class="glass-panel rounded-2xl border border-slate-100 shadow-sm p-6">
                        <h4 class="text-lg font-bold text-slate-800 mb-4 border-b border-slate-100 pb-2">Transaksi Terakhir</h4>
                        <div class="space-y-4">
                            ${State.journals.slice(-5).reverse().map(j => `
                                <div class="flex justify-between items-center p-3 hover:bg-slate-50 rounded-lg transition-colors">
                                    <div class="flex items-center space-x-3">
                                        <div class="w-10 h-10 rounded-full ${j.type === 'Sederhana' ? 'bg-blue-100 text-blue-600' : 'bg-purple-100 text-purple-600'} flex items-center justify-center">
                                            <i class="fas ${j.type === 'Sederhana' ? 'fa-exchange-alt' : 'fa-sliders-h'}"></i>
                                        </div>
                                        <div>
                                            <p class="text-sm font-bold text-slate-800">${j.description}</p>
                                            <p class="text-xs text-slate-500">${j.date} • ${j.type}</p>
                                        </div>
                                    </div>
                                    <div class="text-right">
                                        <p class="text-sm font-bold text-slate-700">${formatRp(j.entries.reduce((s,e)=>s+Number(e.debit),0))}</p>
                                    </div>
                                </div>
                            `).join('') || '<p class="text-sm text-slate-500 py-4 text-center">Belum ada transaksi jurnal</p>'}
                        </div>
                    </div>
                    
                    <div class="glass-panel rounded-2xl border border-slate-100 shadow-sm p-6">
                        <h4 class="text-lg font-bold text-slate-800 mb-4 border-b border-slate-100 pb-2">Peringatan Stok Kurang</h4>
                        <div class="space-y-4">
                            ${State.inventory.length === 0 ? '<p class="text-sm text-slate-500 py-4 text-center">Belum ada barang di inventaris</p>' : State.inventory.filter(i => i.qty < 20).map(i => `
                                <div class="flex justify-between items-center p-3 bg-red-50/50 rounded-lg border border-red-100">
                                    <div class="flex items-center space-x-3">
                                        <div class="w-8 h-8 rounded-full bg-red-100 text-red-600 flex items-center justify-center">
                                            <i class="fas fa-exclamation-triangle text-xs"></i>
                                        </div>
                                        <div>
                                            <p class="text-sm font-bold text-slate-800">${i.name}</p>
                                        </div>
                                    </div>
                                    <div class="text-right">
                                        <span class="px-2 py-1 bg-red-100 text-red-700 rounded text-xs font-bold">Sisa: ${i.qty}</span>
                                    </div>
                                </div>
                            `).join('') || '<p class="text-sm text-green-600 py-4 text-center"><i class="fas fa-check-circle mr-1"></i> Semua stok aman</p>'}
                        </div>
                    </div>
                </div>
            `;
            document.getElementById('content-container').innerHTML = html;
        }

        function renderStockAndSales() {
            let html = `
                <!-- Tabs for Sub-view -->
                <div class="flex space-x-2 border-b border-slate-200 mb-6">
                    <button onclick="toggleStockSubTab('stock')" id="subtab-stock" class="px-4 py-2 text-sm font-bold text-indigo-600 border-b-2 border-indigo-600">Daftar Stok & Barang</button>
                    <button onclick="toggleStockSubTab('pos')" id="subtab-pos" class="px-4 py-2 text-sm font-medium text-slate-500 hover:text-slate-800">Kasir / Penjualan</button>
                    <button onclick="toggleStockSubTab('history')" id="subtab-history" class="px-4 py-2 text-sm font-medium text-slate-500 hover:text-slate-800">Riwayat Penjualan</button>
                </div>

                <div id="stock-view-container" class="print-area">
                    <!-- Default to Stock -->
                </div>
            `;
            document.getElementById('content-container').innerHTML = html;
            toggleStockSubTab('stock');
        }

        function toggleStockSubTab(tab) {
            ['stock', 'pos', 'history'].forEach(t => {
                const el = document.getElementById(`subtab-${t}`);
                if(el) {
                    if (t === tab) {
                        el.classList.add('text-indigo-600', 'border-b-2', 'border-indigo-600', 'font-bold');
                        el.classList.remove('text-slate-500', 'font-medium');
                    } else {
                        el.classList.remove('text-indigo-600', 'border-b-2', 'border-indigo-600', 'font-bold');
                        el.classList.add('text-slate-500', 'font-medium');
                    }
                }
            });

            const container = document.getElementById('stock-view-container');
            if (tab === 'stock') {
                container.innerHTML = `
                    <div class="flex justify-between items-center mb-4">
                        <h3 class="text-lg font-bold text-slate-800">Manajemen Inventaris</h3>
                        <button onclick="formAddStock()" class="bg-indigo-600 text-white px-4 py-2 rounded-lg text-sm font-medium shadow-sm hover:bg-indigo-700 transition-colors">
                            <i class="fas fa-plus mr-1"></i> Item Baru
                        </button>
                    </div>
                    <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr class="bg-slate-50 border-b border-slate-200 text-slate-500 text-xs uppercase tracking-wider">
                                    <th class="p-4 font-medium">ID/Kode</th>
                                    <th class="p-4 font-medium">Nama Barang</th>
                                    <th class="p-4 font-medium text-right">Stok</th>
                                    <th class="p-4 font-medium text-right">Harga Beli (HPP)</th>
                                    <th class="p-4 font-medium text-right">Harga Jual</th>
                                    <th class="p-4 font-medium text-center w-36">Aksi</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-slate-100 text-sm">
                                ${State.inventory.length === 0 ? '<tr><td colspan="6" class="p-8 text-center text-slate-400">Belum ada barang. Klik "+ Item Baru" di atas untuk menambahkan.</td></tr>' : State.inventory.map(item => `
                                    <tr class="hover:bg-slate-50 transition-colors">
                                        <td class="p-4 font-medium text-slate-700">${item.id}</td>
                                        <td class="p-4 text-slate-800 font-bold">${item.name}</td>
                                        <td class="p-4 text-right">
                                            <span class="px-2 py-1 rounded-md ${item.qty < 20 ? 'bg-red-100 text-red-700' : 'bg-green-100 text-green-700'} font-bold">
                                                ${item.qty}
                                            </span>
                                        </td>
                                        <td class="p-4 text-right text-slate-600">${formatRp(item.costPrice)}</td>
                                        <td class="p-4 text-right text-indigo-600 font-bold">${formatRp(item.sellPrice)}</td>
                                        <td class="p-4 text-center whitespace-nowrap">
                                            <button onclick="adjustStockQtyPrompt('${item.id}')" title="Tambah/Kurang Stok Manual" class="text-indigo-600 hover:text-indigo-800 mx-1 bg-indigo-50 hover:bg-indigo-100 p-1.5 rounded transition-colors"><i class="fas fa-boxes"></i></button>
                                            <button onclick="formEditStock('${item.id}')" title="Edit Item" class="text-slate-500 hover:text-slate-800 mx-1 bg-slate-100 hover:bg-slate-200 p-1.5 rounded transition-colors"><i class="fas fa-edit"></i></button>
                                            <button onclick="deleteStock('${item.id}')" title="Hapus Item" class="text-red-500 hover:text-red-700 mx-1 bg-red-50 hover:bg-red-100 p-1.5 rounded transition-colors"><i class="fas fa-trash"></i></button>
                                        </td>
                                    </tr>
                                `).join('')}
                            </tbody>
                        </table>
                    </div>
                `;
            } else if (tab === 'pos') {
                container.innerHTML = `
                    <div class="max-w-2xl mx-auto glass-panel p-6 rounded-2xl border border-slate-200 shadow-sm">
                        <h3 class="text-lg font-bold text-slate-800 mb-4 border-b border-slate-100 pb-2"><i class="fas fa-cash-register text-indigo-500 mr-2"></i> Form Kasir Baru</h3>
                        <div class="space-y-4">
                            <div>
                                <label class="block text-sm font-medium text-slate-700 mb-1">Tanggal</label>
                                <input type="date" id="pos-date" value="${new Date().toISOString().split('T')[0]}" class="w-full border border-slate-300 rounded-lg px-4 py-2 focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all">
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-slate-700 mb-1">Pilih Barang</label>
                                <select id="pos-item" class="w-full border border-slate-300 rounded-lg px-4 py-2 focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all">
                                    <option value="">-- Pilih Barang --</option>
                                    ${State.inventory.map(i => `<option value="${i.id}">${i.name} (Stok: ${i.qty} | ${formatRp(i.sellPrice)})</option>`).join('')}
                                </select>
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-slate-700 mb-1">Kuantitas (Qty)</label>
                                <input type="number" id="pos-qty" min="1" value="1" class="w-full border border-slate-300 rounded-lg px-4 py-2 focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all">
                            </div>
                            <div class="pt-4">
                                <button onclick="processSale()" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 rounded-lg shadow-md transition-all flex items-center justify-center space-x-2">
                                    <i class="fas fa-check-circle"></i>
                                    <span>Proses Penjualan & Jurnal Otomatis</span>
                                </button>
                                <p class="text-xs text-slate-400 text-center mt-2">Sistem otomatis mencatat ke Jurnal (Kas, Penjualan, HPP, Persediaan)</p>
                            </div>
                        </div>
                    </div>
                `;
            } else if (tab === 'history') {
                container.innerHTML = `
                    <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr class="bg-slate-50 border-b border-slate-200 text-slate-500 text-xs uppercase tracking-wider">
                                    <th class="p-4 font-medium">Tanggal</th>
                                    <th class="p-4 font-medium">Item</th>
                                    <th class="p-4 font-medium text-center">Qty</th>
                                    <th class="p-4 font-medium text-right">Total Penjualan</th>
                                    <th class="p-4 font-medium text-right">HPP Tercatat</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-slate-100 text-sm">
                                ${State.sales.slice().reverse().map(sale => `
                                    <tr class="hover:bg-slate-50 transition-colors">
                                        <td class="p-4 font-medium text-slate-600">${sale.date}</td>
                                        <td class="p-4 text-slate-800 font-bold">${sale.itemName}</td>
                                        <td class="p-4 text-center font-bold text-slate-700">${sale.qty}</td>
                                        <td class="p-4 text-right text-green-600 font-bold">${formatRp(sale.total)}</td>
                                        <td class="p-4 text-right text-slate-500">${formatRp(sale.hpp)}</td>
                                    </tr>
                                `).join('') || '<tr><td colspan="5" class="p-8 text-center text-slate-400">Belum ada data penjualan</td></tr>'}
                            </tbody>
                        </table>
                    </div>
                `;
            }
        }

        function formAddStock() {
            const html = `
                <div class="space-y-4">
                    <div><label class="block text-sm font-medium mb-1">Kode/ID Item</label>
                    <input type="text" id="fs-id" class="w-full border rounded p-2" value="${generateId('ITM')}"></div>
                    <div><label class="block text-sm font-medium mb-1">Nama Barang</label>
                    <input type="text" id="fs-name" class="w-full border rounded p-2" placeholder="Contoh: Kopi robusta"></div>
                    <div><label class="block text-sm font-medium mb-1">Kuantitas Awal</label>
                    <input type="number" id="fs-qty" class="w-full border rounded p-2" value="0"></div>
                    <div><label class="block text-sm font-medium mb-1">Harga Beli (HPP) / Satuan</label>
                    <input type="number" id="fs-cost" class="w-full border rounded p-2" placeholder="Harga modal beli"></div>
                    <div><label class="block text-sm font-medium mb-1">Harga Jual / Satuan</label>
                    <input type="number" id="fs-sell" class="w-full border rounded p-2" placeholder="Harga jual kasir"></div>
                </div>
            `;
            openFormModal('Tambah Barang Baru', html, () => {
                const id = document.getElementById('fs-id').value;
                const name = document.getElementById('fs-name').value;
                const qty = parseInt(document.getElementById('fs-qty').value) || 0;
                const costPrice = parseFloat(document.getElementById('fs-cost').value) || 0;
                const sellPrice = parseFloat(document.getElementById('fs-sell').value) || 0;
                
                if(!name || costPrice <= 0 || sellPrice <= 0) {
                    showModal('Error Validasi', 'Mohon lengkapi nama dan pastikan harga lebih dari 0.', 'danger');
                    return false;
                }

                State.inventory.push({ id, name, qty, costPrice, sellPrice });
                
                renderView();
                showModal('Sukses', 'Barang berhasil ditambahkan ke inventaris.', 'success');
                return true;
            });
        }

        // Fitur Edit Barang
        function formEditStock(id) {
            const item = State.inventory.find(i => i.id === id);
            if (!item) return;

            const html = `
                <div class="space-y-4">
                    <div><label class="block text-sm font-medium mb-1">Kode/ID Item (Tidak dapat diubah)</label>
                    <input type="text" id="fe-id" class="w-full border rounded p-2 bg-slate-100" value="${item.id}" readonly></div>
                    <div><label class="block text-sm font-medium mb-1">Nama Barang</label>
                    <input type="text" id="fe-name" class="w-full border rounded p-2" value="${item.name}"></div>
                    <div><label class="block text-sm font-medium mb-1">Kuantitas Stok</label>
                    <input type="number" id="fe-qty" class="w-full border rounded p-2" value="${item.qty}"></div>
                    <div><label class="block text-sm font-medium mb-1">Harga Beli (HPP) / Satuan</label>
                    <input type="number" id="fe-cost" class="w-full border rounded p-2" value="${item.costPrice}"></div>
                    <div><label class="block text-sm font-medium mb-1">Harga Jual / Satuan</label>
                    <input type="number" id="fe-sell" class="w-full border rounded p-2" value="${item.sellPrice}"></div>
                </div>
            `;
            openFormModal('Edit Detail Barang', html, () => {
                const name = document.getElementById('fe-name').value;
                const qty = parseInt(document.getElementById('fe-qty').value) || 0;
                const costPrice = parseFloat(document.getElementById('fe-cost').value) || 0;
                const sellPrice = parseFloat(document.getElementById('fe-sell').value) || 0;
                
                if(!name || costPrice <= 0 || sellPrice <= 0) {
                    showModal('Error Validasi', 'Mohon lengkapi nama dan pastikan harga lebih dari 0.', 'danger');
                    return false;
                }

                item.name = name;
                item.qty = qty;
                item.costPrice = costPrice;
                item.sellPrice = sellPrice;
                
                renderView();
                showModal('Sukses', 'Barang berhasil diperbarui.', 'success');
                return true;
            });
        }

        // Fitur Hapus Barang
        function deleteStock(id) {
            const item = State.inventory.find(i => i.id === id);
            if (!item) return;

            showModal('Hapus Barang', `Apakah Anda yakin ingin menghapus <strong>${item.name}</strong> dari inventaris?`, 'danger', () => {
                State.inventory = State.inventory.filter(i => i.id !== id);
                renderView();
                showModal('Sukses', 'Barang berhasil dihapus.', 'success');
            });
        }

        // Fitur Sesuaikan Stok Tambah / Kurang Cepat
        function adjustStockQtyPrompt(id) {
            const item = State.inventory.find(i => i.id === id);
            if (!item) return;

            const html = `
                <div class="space-y-4">
                    <p class="text-sm text-slate-600">Sesuaikan jumlah stok untuk item: <strong>${item.name}</strong> (Stok saat ini: <strong>${item.qty}</strong>)</p>
                    <div>
                        <label class="block text-sm font-medium mb-1">Pilih Tindakan</label>
                        <select id="adj-type" class="w-full border rounded p-2 focus:ring-2 focus:ring-indigo-500">
                            <option value="add">Tambah Stok (+)</option>
                            <option value="sub">Kurangi Stok (-)</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">Jumlah Unit</label>
                        <input type="number" id="adj-qty" class="w-full border rounded p-2" min="1" value="1">
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">Keterangan / Alasan Penyesuaian</label>
                        <input type="text" id="adj-note" class="w-full border rounded p-2" placeholder="Contoh: Koreksi hasil opname, barang rusak, dll.">
                    </div>
                </div>
            `;

            openFormModal('Sesuaikan Stok Barang', html, () => {
                const type = document.getElementById('adj-type').value;
                const qty = parseInt(document.getElementById('adj-qty').value) || 0;
                const note = document.getElementById('adj-note').value || 'Penyesuaian Stok Manual';
                
                if (qty <= 0) {
                    showModal('Error', 'Masukkan kuantitas penyesuaian yang valid.', 'danger');
                    return false;
                }

                if (type === 'sub' && item.qty < qty) {
                    showModal('Error', `Stok tidak boleh kurang dari 0. Jumlah stok saat ini hanya: ${item.qty}`, 'danger');
                    return false;
                }

                if (type === 'add') {
                    item.qty += qty;
                } else {
                    item.qty -= qty;
                }

                renderView();
                showModal('Sukses', `Stok berhasil diperbarui. Jumlah baru: ${item.qty}`, 'success');
                return true;
            });
        }

        function processSale() {
            const itemId = document.getElementById('pos-item').value;
            const qty = parseInt(document.getElementById('pos-qty').value) || 0;
            const date = document.getElementById('pos-date').value;

            if (!itemId || qty <= 0) {
                showModal('Error', 'Pilih barang dan masukkan kuantitas valid.', 'danger');
                return;
            }

            const item = State.inventory.find(i => i.id === itemId);
            if (item.qty < qty) {
                showModal('Stok Tidak Cukup', `Stok ${item.name} hanya tersisa ${item.qty}.`, 'danger');
                return;
            }

            if(recordSaleRaw(itemId, qty, date)) {
                showModal('Penjualan Berhasil', 'Transaksi tercatat dan Jurnal Otomatis telah dibuat.', 'success');
                renderView(); // re-renders current tab (sales)
            }
        }

        function renderJournal() {
            let html = `
                <div class="flex justify-between items-center mb-6">
                    <div>
                        <h3 class="text-xl font-bold text-slate-800">Buku Jurnal</h3>
                        <p class="text-sm text-slate-500">Jurnal Umum dan Jurnal Penyesuaian</p>
                    </div>
                    <button onclick="formAddJournal()" class="bg-indigo-600 text-white px-5 py-2.5 rounded-lg text-sm font-medium shadow-sm hover:bg-indigo-700 transition-colors">
                        <i class="fas fa-plus mr-1"></i> Entri Jurnal Baru
                    </button>
                </div>
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden print-area">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-slate-800 text-white text-xs uppercase tracking-wider">
                                <th class="p-3 font-medium">Tanggal</th>
                                <th class="p-3 font-medium">Keterangan / Akun</th>
                                <th class="p-3 font-medium text-center">Ref</th>
                                <th class="p-3 font-medium text-right">Debit</th>
                                <th class="p-3 font-medium text-right">Kredit</th>
                                <th class="p-3 font-medium text-center w-16">Aksi</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-slate-200 text-sm">
            `;

            if (State.journals.length === 0) {
                html += `<tr><td colspan="6" class="p-8 text-center text-slate-400">Tidak ada entri jurnal.</td></tr>`;
            }

            State.journals.forEach(j => {
                // Main Header Row for Journal
                html += `
                    <tr class="bg-slate-50">
                        <td class="p-3 font-bold text-slate-700 align-top" rowspan="${j.entries.length + 1}">${j.date}<br><span class="text-xs font-normal text-slate-400 px-2 py-0.5 rounded bg-slate-200">${j.type}</span></td>
                        <td class="p-3 font-bold text-slate-800 italic" colspan="4">${j.description}</td>
                        <td class="p-3 text-center align-top" rowspan="${j.entries.length + 1}">
                            <button onclick="deleteJournal('${j.id}')" class="text-slate-400 hover:text-red-600"><i class="fas fa-trash"></i></button>
                        </td>
                    </tr>
                `;
                // Entries Rows
                j.entries.forEach(e => {
                    const isDebit = e.debit > 0;
                    const accName = getAccName(e.accId);
                    html += `
                        <tr class="hover:bg-blue-50/30 transition-colors">
                            <td class="p-2 ${isDebit ? 'pl-4' : 'pl-12'} text-slate-700">${accName}</td>
                            <td class="p-2 text-center text-slate-500 text-xs">${e.accId}</td>
                            <td class="p-2 text-right ${isDebit ? 'font-bold text-slate-800' : 'text-slate-400'}">${isDebit ? formatRp(e.debit) : '-'}</td>
                            <td class="p-2 text-right ${!isDebit ? 'font-bold text-slate-800' : 'text-slate-400'}">${!isDebit ? formatRp(e.credit) : '-'}</td>
                        </tr>
                    `;
                });
            });

            html += `</tbody></table></div>`;
            document.getElementById('content-container').innerHTML = html;
        }

        function formAddJournal() {
            let options = State.accounts.map(a => `<option value="${a.id}">${a.id} - ${a.name}</option>`).join('');
            const html = `
                <div class="space-y-4">
                    <div class="grid grid-cols-2 gap-4">
                        <div>
                            <label class="block text-sm font-medium mb-1">Tanggal</label>
                            <input type="date" id="fj-date" class="w-full border rounded p-2" value="${new Date().toISOString().split('T')[0]}">
                        </div>
                        <div>
                            <label class="block text-sm font-medium mb-1">Tipe Jurnal</label>
                            <select id="fj-type" class="w-full border rounded p-2">
                                <option value="Sederhana">Jurnal Sederhana / Umum</option>
                                <option value="Penyesuaian">Jurnal Penyesuaian</option>
                            </select>
                        </div>
                    </div>
                    <div>
                        <label class="block text-sm font-medium mb-1">Keterangan Transaksi</label>
                        <input type="text" id="fj-desc" class="w-full border rounded p-2" placeholder="Misal: Bayar Listrik">
                    </div>
                    
                    <div class="bg-slate-100 p-3 rounded border border-slate-200">
                        <h4 class="text-xs font-bold uppercase mb-2">Entri Debit</h4>
                        <div class="flex space-x-2">
                            <select id="fj-deb-acc" class="flex-1 border rounded p-2 text-sm">${options}</select>
                            <input type="number" id="fj-deb-amt" class="w-1/3 border rounded p-2 text-sm" placeholder="Nominal">
                        </div>
                    </div>

                    <div class="bg-slate-100 p-3 rounded border border-slate-200">
                        <h4 class="text-xs font-bold uppercase mb-2">Entri Kredit</h4>
                        <div class="flex space-x-2">
                            <select id="fj-cre-acc" class="flex-1 border rounded p-2 text-sm">${options}</select>
                            <input type="number" id="fj-cre-amt" class="w-1/3 border rounded p-2 text-sm" placeholder="Nominal">
                        </div>
                    </div>
                </div>
            `;

            openFormModal('Entri Jurnal Baru', html, () => {
                const date = document.getElementById('fj-date').value;
                const type = document.getElementById('fj-type').value;
                const desc = document.getElementById('fj-desc').value;
                
                const debAcc = document.getElementById('fj-deb-acc').value;
                const debAmt = parseFloat(document.getElementById('fj-deb-amt').value) || 0;
                
                const creAcc = document.getElementById('fj-cre-acc').value;
                const creAmt = parseFloat(document.getElementById('fj-cre-amt').value) || 0;

                if (!desc || debAmt <= 0 || creAmt <= 0) {
                    showModal('Error', 'Lengkapi deskripsi dan nominal (harus > 0).', 'danger');
                    return false;
                }
                if (debAmt !== creAmt) {
                    showModal('Balance Error', `Debit (${formatRp(debAmt)}) tidak sama dengan Kredit (${formatRp(creAmt)}).`, 'danger');
                    return false;
                }

                const entries = [
                    { accId: debAcc, debit: debAmt, credit: 0 },
                    { accId: creAcc, debit: 0, credit: creAmt }
                ];

                addJournalRaw(date, desc, type, entries);
                renderView();
                return true;
            });
        }


        function renderWorksheet() {
            const w = calculateWorksheet();
            
            let html = `
                <div class="mb-4">
                    <h3 class="text-xl font-bold text-slate-800">Neraca Lajur (Worksheet)</h3>
                    <p class="text-sm text-slate-500">Kalkulasi 10 Kolom Terintegrasi</p>
                </div>
                <div class="bg-white shadow-sm border border-slate-200 overflow-x-auto print-area pb-4">
                    <table class="w-full text-left border-collapse min-w-[1000px]">
                        <thead>
                            <tr class="bg-slate-800 text-white text-xs uppercase tracking-wider text-center">
                                <th class="p-2 border border-slate-700 w-16" rowspan="2">No. Akun</th>
                                <th class="p-2 border border-slate-700" rowspan="2">Nama Akun</th>
                                <th class="p-2 border border-slate-700" colspan="2">Neraca Saldo</th>
                                <th class="p-2 border border-slate-700" colspan="2">Penyesuaian</th>
                                <th class="p-2 border border-slate-700" colspan="2">NS Disesuaikan</th>
                                <th class="p-2 border border-slate-700" colspan="2">Laba Rugi</th>
                                <th class="p-2 border border-slate-700" colspan="2">Neraca</th>
                            </tr>
                            <tr class="bg-slate-700 text-slate-200 text-[10px] uppercase text-center">
                                <th class="p-1 border border-slate-600">Debit</th><th class="p-1 border border-slate-600">Kredit</th>
                                <th class="p-1 border border-slate-600">Debit</th><th class="p-1 border border-slate-600">Kredit</th>
                                <th class="p-1 border border-slate-600">Debit</th><th class="p-1 border border-slate-600">Kredit</th>
                                <th class="p-1 border border-slate-600">Debit</th><th class="p-1 border border-slate-600">Kredit</th>
                                <th class="p-1 border border-slate-600">Debit</th><th class="p-1 border border-slate-600">Kredit</th>
                            </tr>
                        </thead>
                        <tbody class="text-xs text-right">
            `;

            let sums = { ud:0, uc:0, ad:0, ac:0, abd:0, abc:0, id:0, ic:0, bd:0, bc:0 };

            Object.keys(w).forEach(id => {
                const row = w[id];
                // Only show if there's any activity
                const hasActivity = row.unadjDebit || row.unadjCredit || row.adjDebit || row.adjCredit || row.adjBalDebit || row.adjBalCredit;
                if (!hasActivity) return;

                sums.ud += row.unadjDebit; sums.uc += row.unadjCredit;
                sums.ad += row.adjDebit;   sums.ac += row.adjCredit;
                sums.abd += row.adjBalDebit; sums.abc += row.adjBalCredit;
                sums.id += row.isDebit;    sums.ic += row.isCredit;
                sums.bd += row.bsDebit;    sums.bc += row.bsCredit;

                const c = (val) => val === 0 ? '-' : val.toLocaleString('id-ID');
                
                html += `
                    <tr class="hover:bg-slate-50 border-b border-slate-100">
                        <td class="p-2 text-center text-slate-500">${id}</td>
                        <td class="p-2 text-left text-slate-800 font-medium">${row.name}</td>
                        <td class="p-2 ${row.unadjDebit ? 'text-slate-800' : 'text-slate-300'}">${c(row.unadjDebit)}</td>
                        <td class="p-2 ${row.unadjCredit ? 'text-slate-800' : 'text-slate-300'}">${c(row.unadjCredit)}</td>
                        <td class="p-2 bg-blue-50/30 ${row.adjDebit ? 'text-blue-800' : 'text-slate-300'}">${c(row.adjDebit)}</td>
                        <td class="p-2 bg-blue-50/30 ${row.adjCredit ? 'text-blue-800' : 'text-slate-300'}">${c(row.adjCredit)}</td>
                        <td class="p-2 bg-indigo-50/30 font-medium ${row.adjBalDebit ? 'text-indigo-800' : 'text-slate-300'}">${c(row.adjBalDebit)}</td>
                        <td class="p-2 bg-indigo-50/30 font-medium ${row.adjBalCredit ? 'text-indigo-800' : 'text-slate-300'}">${c(row.adjBalCredit)}</td>
                        <td class="p-2 bg-orange-50/30 ${row.isDebit ? 'text-orange-800' : 'text-slate-300'}">${c(row.isDebit)}</td>
                        <td class="p-2 bg-orange-50/30 ${row.isCredit ? 'text-orange-800' : 'text-slate-300'}">${c(row.isCredit)}</td>
                        <td class="p-2 bg-emerald-50/30 ${row.bsDebit ? 'text-emerald-800' : 'text-slate-300'}">${c(row.bsDebit)}</td>
                        <td class="p-2 bg-emerald-50/30 ${row.bsCredit ? 'text-emerald-800' : 'text-slate-300'}">${c(row.bsCredit)}</td>
                    </tr>
                `;
            });

            // Net Income Calculation
            const netIncome = sums.ic - sums.id;
            let niRow = '';
            if (netIncome !== 0) {
                const isProfit = netIncome > 0;
                niRow = `
                    <tr class="font-bold text-red-600 bg-red-50/50">
                        <td colspan="8" class="p-2 text-left">${isProfit ? 'Laba Bersih' : 'Rugi Bersih'}</td>
                        <td class="p-2">${isProfit ? netIncome.toLocaleString('id-ID') : '-'}</td>
                        <td class="p-2">${!isProfit ? Math.abs(netIncome).toLocaleString('id-ID') : '-'}</td>
                        <td class="p-2">${!isProfit ? Math.abs(netIncome).toLocaleString('id-ID') : '-'}</td>
                        <td class="p-2">${isProfit ? netIncome.toLocaleString('id-ID') : '-'}</td>
                    </tr>
                `;
            }

            const c2 = (val) => val.toLocaleString('id-ID');
            html += `
                            <tr class="bg-slate-100 font-bold border-t-2 border-slate-300 text-slate-800">
                                <td colspan="2" class="p-2 text-center">TOTAL</td>
                                <td class="p-2">${c2(sums.ud)}</td><td class="p-2">${c2(sums.uc)}</td>
                                <td class="p-2">${c2(sums.ad)}</td><td class="p-2">${c2(sums.ac)}</td>
                                <td class="p-2">${c2(sums.abd)}</td><td class="p-2">${c2(sums.abc)}</td>
                                <td class="p-2">${c2(sums.id)}</td><td class="p-2">${c2(sums.ic)}</td>
                                <td class="p-2">${c2(sums.bd)}</td><td class="p-2">${c2(sums.bc)}</td>
                            </tr>
                            ${niRow}
                            <tr class="bg-slate-800 text-white font-bold border-t-4 border-slate-900">
                                <td colspan="8" class="p-2 text-center">TOTAL BALANCE</td>
                                <td class="p-2">${c2(sums.id + (netIncome > 0 ? netIncome : 0))}</td>
                                <td class="p-2">${c2(sums.ic + (netIncome < 0 ? Math.abs(netIncome) : 0))}</td>
                                <td class="p-2">${c2(sums.bd + (netIncome < 0 ? Math.abs(netIncome) : 0))}</td>
                                <td class="p-2">${c2(sums.bc + (netIncome > 0 ? netIncome : 0))}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            `;
            document.getElementById('content-container').innerHTML = html;
        }

        function renderIncomeStatement() {
            const w = calculateWorksheet();
            let revenues = [];
            let expenses = [];
            
            let totalRev = 0;
            let totalExp = 0;

            Object.keys(w).forEach(id => {
                const row = w[id];
                if (row.type === 'Revenue') {
                    if(row.isCredit > 0) revenues.push({name: row.name, val: row.isCredit});
                    totalRev += row.isCredit;
                } else if (row.type === 'Expense') {
                    if(row.isDebit > 0) expenses.push({name: row.name, val: row.isDebit});
                    totalExp += row.isDebit;
                }
            });

            const netIncome = totalRev - totalExp;

            let html = `
                <div class="max-w-3xl mx-auto glass-panel p-10 rounded-xl shadow-md border border-slate-200 print-area">
                    <div class="text-center mb-8 border-b-2 border-slate-800 pb-6">
                        <h1 class="text-2xl font-bold text-slate-900 uppercase tracking-widest">ANALKU KORPORASI</h1>
                        <h2 class="text-xl font-semibold text-slate-700 mt-1">Laporan Laba Rugi</h2>
                        <p class="text-slate-500 text-sm mt-1">Periode Bulan Ini</p>
                    </div>

                    <div class="space-y-6">
                        <!-- PENDAPATAN -->
                        <div>
                            <h3 class="font-bold text-slate-800 text-lg border-b border-slate-200 pb-2 mb-3">Pendapatan</h3>
                            <div class="space-y-2 px-4">
                                ${revenues.map(r => `
                                    <div class="flex justify-between text-slate-700">
                                        <span>${r.name}</span>
                                        <span>${formatRp(r.val)}</span>
                                    </div>
                                `).join('') || '<div class="text-slate-400 italic">Tidak ada pendapatan tercatat.</div>'}
                            </div>
                            <div class="flex justify-between font-bold text-slate-900 mt-3 pt-2 border-t border-slate-100 px-4">
                                <span>Total Pendapatan</span>
                                <span>${formatRp(totalRev)}</span>
                            </div>
                        </div>

                        <!-- BEBAN -->
                        <div>
                            <h3 class="font-bold text-slate-800 text-lg border-b border-slate-200 pb-2 mb-3">Beban Operasional & HPP</h3>
                            <div class="space-y-2 px-4">
                                ${expenses.map(e => `
                                    <div class="flex justify-between text-slate-700">
                                        <span>${e.name}</span>
                                        <span>${formatRp(e.val)}</span>
                                    </div>
                                `).join('') || '<div class="text-slate-400 italic">Tidak ada beban tercatat.</div>'}
                            </div>
                            <div class="flex justify-between font-bold text-slate-900 mt-3 pt-2 border-t border-slate-100 px-4">
                                <span>Total Beban</span>
                                <span>(${formatRp(totalExp)})</span>
                            </div>
                        </div>

                        <!-- LABA/RUGI BERSIH -->
                        <div class="flex justify-between items-center bg-slate-800 text-white p-4 rounded-lg mt-6 shadow-inner">
                            <span class="text-lg font-bold">${netIncome >= 0 ? 'Laba Bersih' : 'Rugi Bersih'}</span>
                            <span class="text-xl font-black">${formatRp(Math.abs(netIncome))}</span>
                        </div>
                    </div>
                </div>
            `;
            document.getElementById('content-container').innerHTML = html;
        }

        const titles = {
            'dashboard': ['Dashboard Rekapitulasi', 'Ringkasan keuangan bulan berjalan'],
            'stock': ['Manajemen Inventaris & Penjualan', 'Kontrol stok dan kasir penjualan'],
            'sales': ['Kasir & Penjualan', 'Catat transaksi dan potong stok otomatis'], // Alias for stock tab pos
            'journal': ['Buku Jurnal Umum', 'Catatan semua transaksi akuntansi'],
            'worksheet': ['Neraca Lajur (10 Kolom)', 'Alat bantu penyusunan laporan keuangan'],
            'income': ['Laporan Laba Rugi', 'Analisis profitabilitas perusahaan']
        };

        function switchTab(tab) {
            // Special handling for sales (redirects to stock POS)
            let actualTab = tab;
            let subTab = 'stock';
            if(tab === 'sales') {
                actualTab = 'stock';
                subTab = 'pos';
            }

            State.currentTab = actualTab;
            
            // Update UI Nav
            document.querySelectorAll('#main-nav button').forEach(btn => {
                btn.classList.remove('tab-active', 'bg-slate-50');
                if (btn.id === `nav-${tab}`) {
                    btn.classList.add('tab-active', 'bg-slate-50');
                }
            });

            // Update Header
            document.getElementById('page-title').innerText = titles[tab][0];
            document.getElementById('page-subtitle').innerText = titles[tab][1];

            renderView();
            
            // Auto trigger subtab if coming from sales link
            if(tab === 'sales') {
                setTimeout(() => toggleStockSubTab('pos'), 50);
            }
        }

        function renderView() {
            switch(State.currentTab) {
                case 'dashboard': renderDashboard(); break;
                case 'stock': renderStockAndSales(); break;
                case 'journal': renderJournal(); break;
                case 'worksheet': renderWorksheet(); break;
                case 'income': renderIncomeStatement(); break;
            }
        }

        function downloadPDF() {
            const element = document.getElementById('pdf-content-area');
            const opt = {
                margin:       10,
                filename:     `Laporan_Analku_${State.currentTab}_${new Date().getTime()}.pdf`,
                image:        { type: 'jpeg', quality: 0.98 },
                html2canvas:  { scale: 2, useCORS: true },
                jsPDF:        { unit: 'mm', format: 'a4', orientation: State.currentTab === 'worksheet' ? 'landscape' : 'portrait' }
            };

            // Temporary styling for better print
            const oldBg = element.style.backgroundColor;
            element.style.backgroundColor = 'white';
            
            html2pdf().set(opt).from(element).save().then(() => {
                element.style.backgroundColor = oldBg;
            });
        }

        window.onload = function() {
            // Set current month display
            const options = { month: 'long', year: 'numeric' };
            document.getElementById('current-month-display').innerText = new Date().toLocaleDateString('id-ID', options);
            
            initMockData();
            switchTab('dashboard'); // Start
        };

    </script>
</body>
</html>
