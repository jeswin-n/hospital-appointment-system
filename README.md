<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CarePulse - Hospital Appointment & Data Structures Demo</title>
    <!-- Tailwind CSS for styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Fira+Code:wght@400;500&display=swap" rel="stylesheet">
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['Fira Code', 'monospace'],
                    },
                    colors: {
                        medical: {
                            50: '#f0f9ff',
                            100: '#e0f2fe',
                            500: '#0284c7',
                            600: '#0284c7',
                            700: '#0369a1',
                            800: '#075985',
                            900: '#0c4a6e',
                        },
                        tealmed: {
                            500: '#0d9488',
                            600: '#0d9488',
                            700: '#0f766e',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f8fafc; }
        .node-arrow::after {
            content: "➔";
            position: absolute;
            right: -20px;
            top: 50%;
            transform: translateY(-50%);
            font-size: 18px;
            color: #94a3b8;
        }
        .toast-enter { transform: translateY(-100%); opacity: 0; }
        .toast-enter-active { transform: translateY(0); opacity: 1; transition: all 300ms ease-out; }
        .toast-exit { transform: translateY(0); opacity: 1; }
        .toast-exit-active { transform: translateY(-100%); opacity: 0; transition: all 300ms ease-in; }
        
        /* Custom scrollbar for visualizer */
        .custom-scrollbar::-webkit-scrollbar { height: 8px; width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: #f1f5f9; border-radius: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
    </style>
</head>
<body class="text-slate-800 antialiased min-h-screen flex flex-col justify-between">

    <!-- Toast Notification Container -->
    <div id="toast-container" class="fixed top-5 right-5 z-50 flex flex-col gap-2 max-w-sm w-full pointer-events-none"></div>

    <!-- MAIN HEADER / NAVIGATION -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-40 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between h-16 items-center">
                <!-- Brand Logo & Title -->
                <div class="flex items-center space-x-3 cursor-pointer" onclick="switchTab('dashboard')">
                    <div class="bg-gradient-to-tr from-blue-600 to-teal-500 text-white p-2.5 rounded-xl shadow-md">
                        <i class="fa-solid font-bold fa-hospital-user text-xl"></i>
                    </div>
                    <div>
                        <span class="text-xl font-bold bg-gradient-to-r from-blue-900 via-blue-700 to-teal-700 bg-clip-text text-transparent">CarePulse</span>
                        <span class="text-xs block text-slate-500 font-medium -mt-1">Hospital Management System</span>
                    </div>
                </div>

                <!-- Desktop Navigation Menu -->
                <nav class="hidden md:flex space-x-1 lg:space-x-2">
                    <button id="nav-dashboard" onclick="switchTab('dashboard')" class="nav-btn px-3 py-2 rounded-lg text-sm font-medium transition-all flex items-center gap-2 text-blue-600 bg-blue-50">
                        <i class="fa-solid fa-chart-pie"></i> Dashboard
                    </button>
                    <button id="nav-book" onclick="switchTab('book')" class="nav-btn px-3 py-2 rounded-lg text-sm font-medium transition-all flex items-center gap-2 text-slate-600 hover:bg-slate-100">
                        <i class="fa-solid fa-calendar-plus"></i> Book Appointment
                    </button>
                    <button id="nav-view" onclick="switchTab('view')" class="nav-btn px-3 py-2 rounded-lg text-sm font-medium transition-all flex items-center gap-2 text-slate-600 hover:bg-slate-100">
                        <i class="fa-solid fa-list-check"></i> View All
                    </button>
                    <button id="nav-search" onclick="switchTab('search')" class="nav-btn px-3 py-2 rounded-lg text-sm font-medium transition-all flex items-center gap-2 text-slate-600 hover:bg-slate-100">
                        <i class="fa-solid fa-magnifying-glass"></i> Search
                    </button>
                    <button id="nav-undo" onclick="switchTab('undo')" class="nav-btn px-3 py-2 rounded-lg text-sm font-medium transition-all flex items-center gap-2 text-slate-600 hover:bg-slate-100 relative">
                        <i class="fa-solid fa-rotate-left"></i> Undo Stack
                        <span id="undo-badge" class="ml-1 bg-amber-500 text-white text-xs px-1.5 py-0.5 rounded-full font-bold">0</span>
                    </button>
                    <button id="nav-visualizer" onclick="switchTab('visualizer')" class="nav-btn px-3 py-2 rounded-lg text-sm font-medium transition-all flex items-center gap-2 text-purple-700 hover:bg-purple-50 bg-purple-50/50 border border-purple-200">
                        <i class="fa-solid fa-network-wired text-purple-600"></i> DS Visualizer
                    </button>
                </nav>

                <!-- DS Project Badge -->
                <div class="hidden lg:flex items-center">
                    <span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full text-xs font-semibold bg-emerald-50 text-emerald-700 border border-emerald-200">
                        <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span> B.Tech DS Project
                    </span>
                </div>

                <!-- Mobile menu button -->
                <div class="flex md:hidden">
                    <button onclick="toggleMobileMenu()" class="text-slate-600 hover:text-slate-900 p-2">
                        <i class="fa-solid fa-bars text-xl"></i>
                    </button>
                </div>
            </div>
        </div>

        <!-- Mobile Navigation Menu -->
        <div id="mobile-menu" class="hidden md:hidden border-t border-slate-200 bg-white px-4 pt-2 pb-4 space-y-1">
            <button onclick="switchTab('dashboard')" class="w-full text-left px-3 py-2 rounded-md text-base font-medium text-slate-700 hover:bg-slate-100 flex items-center gap-2"><i class="fa-solid fa-chart-pie w-5"></i> Dashboard</button>
            <button onclick="switchTab('book')" class="w-full text-left px-3 py-2 rounded-md text-base font-medium text-slate-700 hover:bg-slate-100 flex items-center gap-2"><i class="fa-solid fa-calendar-plus w-5"></i> Book Appointment</button>
            <button onclick="switchTab('view')" class="w-full text-left px-3 py-2 rounded-md text-base font-medium text-slate-700 hover:bg-slate-100 flex items-center gap-2"><i class="fa-solid fa-list-check w-5"></i> View Appointments</button>
            <button onclick="switchTab('search')" class="w-full text-left px-3 py-2 rounded-md text-base font-medium text-slate-700 hover:bg-slate-100 flex items-center gap-2"><i class="fa-solid fa-magnifying-glass w-5"></i> Search</button>
            <button onclick="switchTab('undo')" class="w-full text-left px-3 py-2 rounded-md text-base font-medium text-slate-700 hover:bg-slate-100 flex items-center gap-2"><i class="fa-solid fa-rotate-left w-5"></i> Undo Stack</button>
            <button onclick="switchTab('visualizer')" class="w-full text-left px-3 py-2 rounded-md text-base font-medium text-purple-700 hover:bg-purple-50 flex items-center gap-2"><i class="fa-solid fa-network-wired w-5"></i> DS Visualizer</button>
        </div>
    </header>

    <!-- MAIN CONTENT CONTAINER -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 flex-grow w-full">

        <!-- 1. DASHBOARD VIEW -->
        <section id="view-dashboard" class="tab-content space-y-6">
            <!-- Banner -->
            <div class="bg-gradient-to-r from-blue-900 via-blue-800 to-teal-800 text-white rounded-2xl p-6 sm:p-8 shadow-lg relative overflow-hidden">
                <div class="absolute -right-10 -bottom-10 opacity-10 text-white text-9xl pointer-events-none">
                    <i class="fa-solid fa-hospital"></i>
                </div>
                <div class="relative z-10 max-w-2xl">
                    <h1 class="text-2xl sm:text-3xl font-bold tracking-tight">Hospital Appointment Portal</h1>
                    <p class="mt-2 text-blue-10-300 text-blue-100 text-sm sm:text-base leading-relaxed">
                        Demonstration of <span class="font-semibold text-teal-300 underline underline-offset-4">Linked List</span> for dynamic active appointment storage and <span class="font-semibold text-amber-300 underline underline-offset-4">LIFO Stack</span> for instant cancellation undo capabilities.
                    </p>
                    <div class="mt-5 flex flex-wrap gap-3">
                        <button onclick="switchTab('book')" class="bg-teal-500 hover:bg-teal-600 text-white px-4 py-2.5 rounded-xl font-semibold text-sm transition-all shadow-md flex items-center gap-2">
                            <i class="fa-solid fa-plus"></i> New Appointment
                        </button>
                        <button onclick="switchTab('visualizer')" class="bg-white/10 hover:bg-white/20 text-white border border-white/20 px-4 py-2.5 rounded-xl font-semibold text-sm transition-all flex items-center gap-2 backdrop-blur-sm">
                            <i class="fa-solid fa-microscope"></i> Open DS Inspector
                        </button>
                    </div>
                </div>
            </div>

            <!-- Stats Counter Cards -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-5">
                <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center gap-4">
                    <div class="w-12 h-12 rounded-xl bg-blue-100 text-blue-600 flex items-center gap-0 justify-center text-xl font-bold">
                        <i class="fa-solid fa-users text-lg"></i>
                    </div>
                    <div>
                        <p class="text-xs font-semibold uppercase tracking-wider text-slate-500">Active Appointments</p>
                        <h3 id="stat-total" class="text-2xl font-bold text-slate-800">0</h3>
                        <p class="text-xs text-blue-600 font-medium">In Linked List</p>
                    </div>
                </div>

                <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center gap-4">
                    <div class="w-12 h-12 rounded-xl bg-teal-100 text-teal-600 flex justify-center items-center text-xl font-bold">
                        <i class="fa-solid fa-calendar-day text-lg"></i>
                    </div>
                    <div>
                        <p class="text-xs font-semibold uppercase tracking-wider text-slate-500">Next Patient</p>
                        <h3 id="stat-next" class="text-base font-bold text-slate-800 truncate max-w-[150px]">None</h3>
                        <p class="text-xs text-teal-600 font-medium">Head of Linked List</p>
                    </div>
                </div>

                <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center gap-4">
                    <div class="w-12 h-12 rounded-xl bg-amber-100 text-amber-600 flex justify-center items-center text-xl font-bold">
                        <i class="fa-solid fa-box-archive text-lg"></i>
                    </div>
                    <div>
                        <p class="text-xs font-semibold uppercase tracking-wider text-slate-500">Cancelled Stack</p>
                        <h3 id="stat-cancelled" class="text-2xl font-bold text-slate-800">0</h3>
                        <p class="text-xs text-amber-600 font-medium">LIFO Stack Size</p>
                    </div>
                </div>

                <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center gap-4">
                    <div class="w-12 h-12 rounded-xl bg-purple-100 text-purple-600 flex justify-center items-center text-xl font-bold">
                        <i class="fa-solid fa-layer-group text-lg"></i>
                    </div>
                    <div>
                        <p class="text-xs font-semibold uppercase tracking-wider text-slate-500">DS Memory Status</p>
                        <h3 class="text-sm font-bold text-emerald-600">Pointers Synced</h3>
                        <p class="text-xs text-slate-400">Head/Tail pointers live</p>
                    </div>
                </div>
            </div>

            <!-- Quick Action & Activity Grid -->
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <!-- Upcoming Appointments Quick List -->
                <div class="lg:col-span-2 bg-white rounded-2xl border border-slate-200/80 p-6 shadow-sm">
                    <div class="flex justify-between items-center mb-4">
                        <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-clock text-blue-600"></i> Upcoming Appointments (Linked List View)
                        </h2>
                        <button onclick="switchTab('view')" class="text-xs text-blue-600 font-semibold hover:underline">View All &rarr;</button>
                    </div>
                    <div id="dashboard-list-container" class="space-y-3">
                        <!-- Populated by JS -->
                    </div>
                </div>

                <!-- Live Data Structure Summary Sidebar -->
                <div class="bg-slate-900 text-white rounded-2xl p-6 shadow-sm flex flex-col justify-between">
                    <div>
                        <div class="flex items-center justify-between pb-4 border-b border-slate-800">
                            <span class="font-mono text-xs text-teal-400 font-semibold uppercase tracking-wider">&lt;Data Structures&gt;</span>
                            <span class="text-xs bg-slate-800 px-2 py-1 rounded text-slate-300 font-mono">JS Class Engine</span>
                        </div>
                        
                        <div class="mt-4 space-y-4 font-mono text-xs">
                            <div class="p-3 bg-slate-800/80 rounded-xl border border-slate-700">
                                <span class="text-blue-400 font-semibold">AppointmentLinkedList</span>
                                <div class="text-slate-300 mt-1">Head: <span id="ds-head-id" class="text-amber-300">null</span></div>
                                <div class="text-slate-300">Tail: <span id="ds-tail-id" class="text-amber-300">null</span></div>
                                <div class="text-slate-400 text-[11px] mt-1">Time: Insert O(1), Delete O(N)</div>
                            </div>

                            <div class="p-3 bg-slate-800/80 rounded-xl border border-slate-700">
                                <span class="text-amber-400 font-semibold">CancellationStack</span>
                                <div class="text-slate-300 mt-1">Top Element: <span id="ds-stack-top" class="text-amber-300">Empty</span></div>
                                <div class="text-slate-300">Size: <span id="ds-stack-size" class="text-amber-300">0</span></div>
                                <div class="text-slate-400 text-[11px] mt-1">Time: Push O(1), Pop O(1) LIFO</div>
                            </div>
                        </div>
                    </div>

                    <div class="mt-6 pt-4 border-t border-slate-800 text-center">
                        <button onclick="switchTab('undo')" class="w-full bg-amber-500 hover:bg-amber-600 text-slate-950 font-bold py-2.5 px-4 rounded-xl text-xs transition-all flex justify-center items-center gap-2">
                            <i class="fa-solid fa-rotate-left"></i> Quick Undo Last Cancellation
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- 2. BOOK APPOINTMENT FORM VIEW -->
        <section id="view-book" class="tab-content hidden space-y-6 max-w-3xl mx-auto">
            <div class="bg-white rounded-2xl border border-slate-200/80 p-6 sm:p-8 shadow-sm">
                <div class="border-b border-slate-100 pb-4 mb-6">
                    <h2 class="text-xl font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-plus text-blue-600"></i> Book Patient Appointment
                    </h2>
                    <p class="text-sm text-slate-500 mt-1">Submitting will instantiate an <code class="bg-slate-100 px-1.5 py-0.5 rounded text-blue-700 font-mono text-xs">AppointmentNode</code> and append it to the Tail of the Linked List.</p>
                </div>

                <form id="appointment-form" onsubmit="handleBookAppointment(event)" class="space-y-5">
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-5">
                        <!-- Patient Name -->
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-2">Patient Name *</label>
                            <div class="relative">
                                <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400"><i class="fa-solid fa-user"></i></span>
                                <input type="text" id="patientName" required placeholder="John Doe" class="w-full pl-10 pr-4 py-2.5 border border-slate-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none text-sm transition-all">
                            </div>
                        </div>

                        <!-- Contact Number -->
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-2">Contact Number *</label>
                            <div class="relative">
                                <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400"><i class="fa-solid fa-phone"></i></span>
                                <input type="tel" id="contact" required placeholder="+1 (555) 000-0000" class="w-full pl-10 pr-4 py-2.5 border border-slate-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none text-sm transition-all">
                            </div>
                        </div>

                        <!-- Email -->
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-2">Email Address</label>
                            <div class="relative">
                                <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400"><i class="fa-solid fa-envelope"></i></span>
                                <input type="email" id="email" placeholder="patient@example.com" class="w-full pl-10 pr-4 py-2.5 border border-slate-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none text-sm transition-all">
                            </div>
                        </div>

                        <!-- Department Selection -->
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-2">Department *</label>
                            <div class="relative">
                                <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400"><i class="fa-solid fa-building-user"></i></span>
                                <select id="department" onchange="updateDoctorsList()" required class="w-full pl-10 pr-4 py-2.5 border border-slate-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none text-sm bg-white transition-all">
                                    <option value="">Select Department</option>
                                    <option value="Cardiology">Cardiology</option>
                                    <option value="Neurology">Neurology</option>
                                    <option value="Orthopedics">Orthopedics</option>
                                    <option value="Pediatrics">Pediatrics</option>
                                    <option value="General Medicine">General Medicine</option>
                                    <option value="Dermatology">Dermatology</option>
                                </select>
                            </div>
                        </div>

                        <!-- Doctor Selection -->
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-2">Doctor *</label>
                            <div class="relative">
                                <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400"><i class="fa-solid fa-user-doctor"></i></span>
                                <select id="doctor" required class="w-full pl-10 pr-4 py-2.5 border border-slate-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none text-sm bg-white transition-all">
                                    <option value="">Select Department First</option>
                                </select>
                            </div>
                        </div>

                        <!-- Appointment Date -->
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-2">Date *</label>
                            <div class="relative">
                                <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400"><i class="fa-solid fa-calendar"></i></span>
                                <input type="date" id="appointmentDate" required class="w-full pl-10 pr-4 py-2.5 border border-slate-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none text-sm transition-all">
                            </div>
                        </div>

                        <!-- Appointment Time -->
                        <div class="sm:col-span-2">
                            <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-2">Time Slot *</label>
                            <div class="grid grid-cols-2 sm:grid-cols-4 gap-2" id="time-slots-container">
                                <!-- Generated by JS -->
                            </div>
                            <input type="hidden" id="appointmentTime" required>
                        </div>

                        <!-- Reason for Visit -->
                        <div class="sm:col-span-2">
                            <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-2">Reason for Visit *</label>
                            <textarea id="reason" rows="3" required placeholder="Describe symptoms or purpose of consultation..." class="w-full p-3 border border-slate-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none text-sm transition-all"></textarea>
                        </div>
                    </div>

                    <div class="pt-4 flex justify-end gap-3 border-t border-slate-100">
                        <button type="button" onclick="switchTab('dashboard')" class="px-5 py-2.5 rounded-xl border border-slate-300 text-slate-600 font-semibold text-sm hover:bg-slate-50 transition-all">Cancel</button>
                        <button type="submit" class="px-6 py-2.5 rounded-xl bg-blue-600 hover:bg-blue-700 text-white font-semibold text-sm shadow-md transition-all flex items-center gap-2">
                            <i class="fa-solid fa-check"></i> Complete Booking
                        </button>
                    </div>
                </form>
            </div>
        </section>

        <!-- 3. VIEW ALL APPOINTMENTS VIEW -->
        <section id="view-view" class="tab-content hidden space-y-6">
            <div class="bg-white rounded-2xl border border-slate-200/80 p-6 shadow-sm">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 border-b border-slate-100 pb-4 mb-6">
                    <div>
                        <h2 class="text-xl font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-list-check text-blue-600"></i> Active Appointments
                        </h2>
                        <p class="text-xs text-slate-500 mt-1">Traversing through Linked List nodes sequentially.</p>
                    </div>
                    
                    <div class="flex items-center gap-2 w-full sm:w-auto">
                        <span class="text-xs font-semibold text-slate-500 uppercase">Sort Linked List:</span>
                        <select id="view-sort" onchange="renderViewAppointments()" class="text-xs border border-slate-300 rounded-lg px-3 py-1.5 outline-none bg-slate-50">
                            <option value="original">Original Order (LL)</option>
                            <option value="date">By Date</option>
                            <option value="patient">By Patient Name</option>
                        </select>
                    </div>
                </div>

                <!-- Table Container -->
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-slate-50 border-b border-slate-200 text-[11px] font-bold text-slate-500 uppercase tracking-wider">
                                <th class="p-3 rounded-l-lg">ID</th>
                                <th class="p-3">Patient</th>
                                <th class="p-3">Doctor & Dept</th>
                                <th class="p-3">Date & Time</th>
                                <th class="p-3">Reason</th>
                                <th class="p-3 text-right rounded-r-lg">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="appointments-table-body" class="divide-y divide-slate-100 text-sm">
                            <!-- Dynamically loaded from Linked List -->
                        </tbody>
                    </table>
                </div>

                <div id="empty-view-msg" class="hidden py-12 text-center">
                    <div class="w-16 h-16 bg-slate-100 text-slate-400 rounded-full flex items-center justify-center mx-auto mb-3 text-2xl">
                        <i class="fa-solid fa-calendar-xmark"></i>
                    </div>
                    <h3 class="text-slate-700 font-bold">No Active Appointments</h3>
                    <p class="text-xs text-slate-400 mt-1">The Linked List is currently empty.</p>
                    <button onclick="switchTab('book')" class="mt-4 bg-blue-600 text-white px-4 py-2 rounded-xl text-xs font-semibold">Book First Appointment</button>
                </div>
            </div>
        </section>

        <!-- 4. SEARCH APPOINTMENTS VIEW -->
        <section id="view-search" class="tab-content hidden space-y-6 max-w-4xl mx-auto">
            <div class="bg-white rounded-2xl border border-slate-200/80 p-6 shadow-sm">
                <div class="border-b border-slate-100 pb-4 mb-6">
                    <h2 class="text-xl font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-magnifying-glass text-blue-600"></i> Search Appointments
                    </h2>
                    <p class="text-xs text-slate-500 mt-1">Linear search algorithm scanning Linked List nodes in O(N) time complexity.</p>
                </div>

                <!-- Search Input bar -->
                <div class="relative mb-6">
                    <span class="absolute inset-y-0 left-0 flex items-center pl-4 text-slate-400 text-lg">
                        <i class="fa-solid fa-search"></i>
                    </span>
                    <input type="text" id="search-query" onkeyup="handleSearch()" placeholder="Search by Patient Name, ID (e.g. APT-1001), Doctor, or Department..." class="w-full pl-11 pr-4 py-3 border border-slate-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none text-sm transition-all shadow-sm">
                </div>

                <!-- Results -->
                <div id="search-results-container" class="space-y-3">
                    <!-- Search Results dynamically injected here -->
                </div>
            </div>
        </section>

        <!-- 5. UNDO STACK VIEW -->
        <section id="view-undo" class="tab-content hidden space-y-6 max-w-3xl mx-auto">
            <div class="bg-white rounded-2xl border border-slate-200/80 p-6 sm:p-8 shadow-sm">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 border-b border-slate-100 pb-4 mb-6">
                    <div>
                        <h2 class="text-xl font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-rotate-left text-amber-500"></i> Cancellation Stack Management
                        </h2>
                        <p class="text-xs text-slate-500 mt-1">LIFO Principle: Most recently cancelled appointment sits on top of stack.</p>
                    </div>

                    <button onclick="handleUndoCancellation()" class="bg-amber-500 hover:bg-amber-600 text-white font-bold px-5 py-2.5 rounded-xl text-sm shadow-md transition-all flex items-center gap-2">
                        <i class="fa-solid fa-arrow-up-from-bracket"></i> Undo Last Cancellation
                    </button>
                </div>

                <!-- Stack Cards Visual Representation -->
                <div class="space-y-4">
                    <div class="flex justify-between items-center text-xs font-semibold text-slate-500 uppercase tracking-wider px-1">
                        <span>Stack Frame Hierarchy</span>
                        <span id="stack-count-label">0 Elements in Stack</span>
                    </div>

                    <div id="cancellation-stack-list" class="space-y-3">
                        <!-- Populated dynamically -->
                    </div>
                </div>
            </div>
        </section>

        <!-- 6. DATA STRUCTURE VISUALIZER & INSPECTOR VIEW -->
        <section id="view-visualizer" class="tab-content hidden space-y-6">
            <div class="bg-slate-900 text-white rounded-2xl p-6 sm:p-8 shadow-xl border border-slate-800">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 border-b border-slate-800 pb-6 mb-6">
                    <div>
                        <div class="inline-flex items-center gap-2 px-3 py-1 rounded-full text-xs font-mono bg-purple-500/10 text-purple-400 border border-purple-500/20 mb-2">
                            <i class="fa-solid fa-code"></i> Data Structures Inspector
                        </div>
                        <h2 class="text-2xl font-bold text-white">Live Memory & Pointer Visualization</h2>
                        <p class="text-xs text-slate-400 mt-1">Visualizing pointer references for <span class="text-blue-400 font-mono">Linked List</span> and push/pop operations for <span class="text-amber-400 font-mono">LIFO Stack</span>.</p>
                    </div>

                    <div class="flex items-center gap-3">
                        <button onclick="seedSampleData()" class="px-3.5 py-2 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-mono border border-slate-700 transition-all">
                            <i class="fa-solid fa-database"></i> Reset Sample Data
                        </button>
                    </div>
                </div>

                <!-- Linked List Visualizer -->
                <div class="space-y-4 mb-10">
                    <div class="flex justify-between items-center">
                        <h3 class="text-sm font-mono font-semibold text-blue-400 flex items-center gap-2">
                            <i class="fa-solid fa-link"></i> AppointmentLinkedList (Head to Tail Traversal)
                        </h3>
                        <span class="text-xs font-mono text-slate-400">Total Nodes: <span id="viz-node-count" class="text-white font-bold">0</span></span>
                    </div>

                    <!-- Flow container -->
                    <div class="p-6 bg-slate-950 rounded-2xl border border-slate-800 overflow-x-auto custom-scrollbar">
                        <div id="viz-linked-list" class="flex items-center gap-6 min-w-max py-4">
                            <!-- Visual nodes dynamically generated -->
                        </div>
                    </div>
                </div>

                <!-- Stack Visualizer -->
                <div class="space-y-4">
                    <div class="flex justify-between items-center">
                        <h3 class="text-sm font-mono font-semibold text-amber-400 flex items-center gap-2">
                            <i class="fa-solid fa-layer-group"></i> CancellationStack (LIFO - Top to Bottom)
                        </h3>
                        <span class="text-xs font-mono text-slate-400">Top Index: <span id="viz-stack-top-idx" class="text-white font-bold">-1</span></span>
                    </div>

                    <div class="p-6 bg-slate-950 rounded-2xl border border-slate-800">
                        <div id="viz-stack-container" class="flex flex-col-reverse gap-3 max-w-xl mx-auto">
                            <!-- Visual Stack Frames injected here -->
                        </div>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- FOOTER -->
    <footer class="bg-white border-t border-slate-200 py-6 mt-12">
        <div class="max-w-7xl mx-auto px-4 text-center text-xs text-slate-500 space-y-2">
            <p class="font-semibold text-slate-700">CarePulse Hospital Appointment Management System</p>
            <p>Designed for B.Tech Computer Science & Engineering - Data Structures & Algorithms Project</p>
            <p class="text-[11px] text-slate-400">Demonstrating Custom Linked List (`Node.next`) & Stack (`LIFO Array Push/Pop` Operations)</p>
        </div>
    </footer>

    <script>
        /**
         * ==========================================
         * 1. DATA STRUCTURE CLASSES (LINKED LIST & STACK)
         * ==========================================
         */

        /**
         * AppointmentNode Class
         * Represents a single element/node in the Linked List.
         */
        class AppointmentNode {
            constructor(data) {
                this.data = data; // Stores appointment payload
                this.next = null; // Pointer to the next node in list
            }
        }

        /**
         * AppointmentLinkedList Class
         * Manages active appointments dynamically.
         */
        class AppointmentLinkedList {
            constructor() {
                this.head = null;
                this.tail = null;
                this.length = 0;
            }

            /**
             * Insert new appointment at tail - O(1) Time Complexity
             */
            append(data) {
                const newNode = new AppointmentNode(data);
                if (!this.head) {
                    this.head = newNode;
                    this.tail = newNode;
                } else {
                    this.tail.next = newNode;
                    this.tail = newNode;
                }
                this.length++;
                return newNode;
            }

            /**
             * Remove appointment node by ID - O(N) Time Complexity
             */
            removeById(id) {
                if (!this.head) return null;

                let removedNode = null;

                // Case 1: Head node is to be removed
                if (this.head.data.id === id) {
                    removedNode = this.head;
                    this.head = this.head.next;
                    if (!this.head) {
                        this.tail = null;
                    }
                    this.length--;
                    return removedNode.data;
                }

                // Case 2: Traverse to find matching node
                let current = this.head;
                while (current.next && current.next.data.id !== id) {
                    current = current.next;
                }

                // Node found
                if (current.next) {
                    removedNode = current.next;
                    current.next = current.next.next;
                    
                    // If tail was removed, update tail pointer
                    if (!current.next) {
                        this.tail = current;
                    }
                    this.length--;
                    return removedNode.data;
                }

                return null; // ID not found
            }

            /**
             * Traverse Linked List and return Array representation
             */
            toArray() {
                const appointments = [];
                let current = this.head;
                while (current) {
                    appointments.push(current.data);
                    current = current.next;
                }
                return appointments;
            }

            /**
             * Search Linked List by query - O(N) Time Complexity
             */
            search(query) {
                const results = [];
                const q = query.toLowerCase().trim();
                let current = this.head;

                while (current) {
                    const apt = current.data;
                    if (
                        apt.id.toLowerCase().includes(q) ||
                        apt.patientName.toLowerCase().includes(q) ||
                        apt.doctor.toLowerCase().includes(q) ||
                        apt.department.toLowerCase().includes(q) ||
                        apt.appointmentDate.includes(q)
                    ) {
                        results.push(apt);
                    }
                    current = current.next;
                }
                return results;
            }

            /**
             * Clear entire list
             */
            clear() {
                this.head = null;
                this.tail = null;
                this.length = 0;
            }
        }

        /**
         * CancellationStack Class
         * Stores cancelled appointments using Last-In, First-Out (LIFO) stack.
         */
        class CancellationStack {
            constructor() {
                this.items = [];
            }

            /**
             * Push item onto Stack - O(1)
             */
            push(item) {
                this.items.push(item);
            }

            /**
             * Pop top item from Stack - O(1)
             */
            pop() {
                if (this.isEmpty()) return null;
                return this.items.pop();
            }

            /**
             * View top item without removing
             */
            peek() {
                if (this.isEmpty()) return null;
                return this.items[this.items.length - 1];
            }

            /**
             * Check if Stack is empty
             */
            isEmpty() {
                return this.items.length === 0;
            }

            /**
             * Get size of Stack
             */
            size() {
                return this.items.length;
            }

            /**
             * Get array elements (Top to Bottom order)
             */
            getElements() {
                return [...this.items].reverse();
            }

            /**
             * Clear stack
             */
            clear() {
                this.items = [];
            }
        }

        // Global Data Structure Instances
        const appointmentList = new AppointmentLinkedList();
        const cancellationStack = new CancellationStack();

        /**
         * ==========================================
         * 2. INITIALIZATION & STORAGE SYNCRONIZATION
         * ==========================================
         */

        const DOCTORS_BY_DEPT = {
            'Cardiology': ['Dr. Robert Chen (Cardiologist)', 'Dr. Elena Rostova (Electrophysiologist)'],
            'Neurology': ['Dr. Sarah Jenkins (Neurologist)', 'Dr. Marcus Vance (Neurosurgeon)'],
            'Orthopedics': ['Dr. James Wilson (Orthopedic Surgeon)', 'Dr. Priya Patel (Sports Med)'],
            'Pediatrics': ['Dr. Emily Thomas (Pediatrician)', 'Dr. Alan Harper (Child Specialist)'],
            'General Medicine': ['Dr. Gregory House (General Physician)', 'Dr. Lisa Cuddy (Internal Med)'],
            'Dermatology': ['Dr. Hannah Abbott (Dermatologist)', 'Dr. Neil Caffrey (Cosmetic Derm)']
        };

        const TIME_SLOTS = [
            '09:00 AM', '10:00 AM', '11:30 AM', 
            '02:00 PM', '03:30 PM', '05:00 PM'
        ];

        window.onload = function() {
            // Initialize time slots in form
            renderTimeSlots();
            
            // Load state from localStorage or load samples
            loadFromLocalStorage();

            // Render all UI components
            refreshAllUI();
        };

        function saveToLocalStorage() {
            const listData = appointmentList.toArray();
            const stackData = cancellationStack.items;

            localStorage.setItem('carepulse_appointments', JSON.stringify(listData));
            localStorage.setItem('carepulse_stack', JSON.stringify(stackData));
        }

        function loadFromLocalStorage() {
            const listData = JSON.parse(localStorage.getItem('carepulse_appointments') || '[]');
            const stackData = JSON.parse(localStorage.getItem('carepulse_stack') || '[]');

            appointmentList.clear();
            cancellationStack.clear();

            if (listData.length === 0 && stackData.length === 0) {
                // Seed initial realistic data for demonstration
                seedSampleData();
            } else {
                listData.forEach(item => appointmentList.append(item));
                stackData.forEach(item => cancellationStack.push(item));
            }
        }

        function seedSampleData() {
            appointmentList.clear();
            cancellationStack.clear();

            const sampleAppointments = [
                {
                    id: 'APT-1001',
                    patientName: 'Alice Smith',
                    contact: '+1 555-0192',
                    email: 'alice@example.com',
                    department: 'Cardiology',
                    doctor: 'Dr. Robert Chen (Cardiologist)',
                    appointmentDate: getRelativeDate(1),
                    appointmentTime: '09:00 AM',
                    reason: 'Annual routine cardiac checkup and ECG analysis.'
                },
                {
                    id: 'APT-1002',
                    patientName: 'Michael Brown',
                    contact: '+1 555-0184',
                    email: 'mbrown@example.com',
                    department: 'Orthopedics',
                    doctor: 'Dr. James Wilson (Orthopedic Surgeon)',
                    appointmentDate: getRelativeDate(2),
                    appointmentTime: '11:30 AM',
                    reason: 'Post-knee surgery rehabilitation consultation.'
                },
                {
                    id: 'APT-1003',
                    patientName: 'Sophia Martinez',
                    contact: '+1 555-0143',
                    email: 'sophia.m@example.com',
                    department: 'Neurology',
                    doctor: 'Dr. Sarah Jenkins (Neurologist)',
                    appointmentDate: getRelativeDate(3),
                    appointmentTime: '02:00 PM',
                    reason: 'Persistent migraine headaches for past 2 weeks.'
                }
            ];

            const sampleCancelled = {
                id: 'APT-1000',
                patientName: 'David Miller',
                contact: '+1 555-0111',
                email: 'david@example.com',
                department: 'Dermatology',
                doctor: 'Dr. Hannah Abbott (Dermatologist)',
                appointmentDate: getRelativeDate(0),
                appointmentTime: '10:00 AM',
                reason: 'Skin rash treatment.'
            };

            sampleAppointments.forEach(apt => appointmentList.append(apt));
            cancellationStack.push(sampleCancelled);

            saveToLocalStorage();
            refreshAllUI();
            showToast('Loaded sample dataset successfully!', 'info');
        }

        function getRelativeDate(daysOffset) {
            const date = new Date();
            date.setDate(date.getDate() + daysOffset);
            return date.toISOString().split('T')[0];
        }

        /**
         * ==========================================
         * 3. APPLICATION LOGIC & EVENT HANDLERS
         * ==========================================
         */

        function switchTab(tabName) {
            // Hide all tab content
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            
            // Reset nav styles
            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.classList.remove('bg-blue-50', 'text-blue-600');
                btn.classList.add('text-slate-600');
            });

            // Show current tab
            const target = document.getElementById(`view-${tabName}`);
            if (target) target.classList.remove('hidden');

            // Highlight nav item
            const navBtn = document.getElementById(`nav-${tabName}`);
            if (navBtn && tabName !== 'visualizer') {
                navBtn.classList.add('bg-blue-50', 'text-blue-600');
                navBtn.classList.remove('text-slate-600');
            }

            // Close mobile menu if open
            document.getElementById('mobile-menu').classList.add('hidden');

            // Specific tab refresh trigger
            if (tabName === 'search') handleSearch();
        }

        function toggleMobileMenu() {
            const menu = document.getElementById('mobile-menu');
            menu.classList.toggle('hidden');
        }

        function updateDoctorsList() {
            const dept = document.getElementById('department').value;
            const doctorSelect = document.getElementById('doctor');
            doctorSelect.innerHTML = '<option value="">Select Doctor</option>';

            if (dept && DOCTORS_BY_DEPT[dept]) {
                DOCTORS_BY_DEPT[dept].forEach(doc => {
                    const opt = document.createElement('option');
                    opt.value = doc;
                    opt.textContent = doc;
                    doctorSelect.appendChild(opt);
                });
            }
        }

        function renderTimeSlots() {
            const container = document.getElementById('time-slots-container');
            container.innerHTML = '';

            TIME_SLOTS.forEach((slot, index) => {
                const btn = document.createElement('button');
                btn.type = 'button';
                btn.className = `slot-btn border text-xs font-semibold py-2 rounded-xl border-slate-200 text-slate-700 hover:border-blue-500 hover:text-blue-600 transition-all`;
                btn.textContent = slot;
                btn.onclick = () => {
                    document.querySelectorAll('.slot-btn').forEach(b => {
                        b.classList.remove('bg-blue-600', 'text-white', 'border-blue-600');
                        b.classList.add('border-slate-200', 'text-slate-700');
                    });
                    btn.classList.add('bg-blue-600', 'text-white', 'border-blue-600');
                    btn.classList.remove('border-slate-200', 'text-slate-700');
                    document.getElementById('appointmentTime').value = slot;
                };
                container.appendChild(btn);
            });
        }

        /**
         * Book Appointment -> Appends Node to Linked List
         */
        function handleBookAppointment(e) {
            e.preventDefault();

            const timeSlot = document.getElementById('appointmentTime').value;
            if (!timeSlot) {
                showToast('Please select a time slot for the appointment.', 'warning');
                return;
            }

            const newAppointment = {
                id: 'APT-' + Math.floor(1000 + Math.random() * 9000),
                patientName: document.getElementById('patientName').value.trim(),
                contact: document.getElementById('contact').value.trim(),
                email: document.getElementById('email').value.trim() || 'N/A',
                department: document.getElementById('department').value,
                doctor: document.getElementById('doctor').value,
                appointmentDate: document.getElementById('appointmentDate').value,
                appointmentTime: timeSlot,
                reason: document.getElementById('reason').value.trim()
            };

            // DATA STRUCTURE OPERATION: Append Node to Linked List
            appointmentList.append(newAppointment);

            // Persist & Sync UI
            saveToLocalStorage();
            refreshAllUI();

            // Reset Form
            document.getElementById('appointment-form').reset();
            document.getElementById('appointmentTime').value = '';
            document.querySelectorAll('.slot-btn').forEach(b => {
                b.classList.remove('bg-blue-600', 'text-white', 'border-blue-600');
            });

            showToast(`Appointment ${newAppointment.id} successfully added to Linked List!`, 'success');
            switchTab('view');
        }

        /**
         * Cancel Appointment -> Removes Node from Linked List & Pushes to Cancellation Stack
         */
        function cancelAppointment(id) {
            // DATA STRUCTURE OPERATION 1: Remove Node from Linked List
            const removedData = appointmentList.removeById(id);

            if (removedData) {
                // DATA STRUCTURE OPERATION 2: Push removed item onto Cancellation Stack (LIFO)
                cancellationStack.push(removedData);

                saveToLocalStorage();
                refreshAllUI();

                showToast(`Appointment ${id} removed from Linked List & pushed to Stack!`, 'warning');
            } else {
                showToast(`Failed to locate appointment ${id} in Linked List.`, 'error');
            }
        }

        /**
         * Undo Last Cancellation -> Pops item from Stack & Appends back to Linked List
         */
        function handleUndoCancellation() {
            if (cancellationStack.isEmpty()) {
                showToast('No cancellation available to undo. Stack is empty!', 'error');
                return;
            }

            // DATA STRUCTURE OPERATION 1: Pop from Cancellation Stack (LIFO)
            const restoredAppointment = cancellationStack.pop();

            // DATA STRUCTURE OPERATION 2: Re-insert into Linked List
            appointmentList.append(restoredAppointment);

            saveToLocalStorage();
            refreshAllUI();

            showToast(`Restored ${restoredAppointment.id} (${restoredAppointment.patientName}) back to Linked List!`, 'success');
        }

        function handleSearch() {
            const query = document.getElementById('search-query').value;
            const container = document.getElementById('search-results-container');
            
            if (!query.trim()) {
                container.innerHTML = `<div class="text-center py-8 text-slate-400 text-xs">Start typing above to execute a linear search across the Linked List.</div>`;
                return;
            }

            // DATA STRUCTURE OPERATION: Linear search traversal
            const results = appointmentList.search(query);

            if (results.length === 0) {
                container.innerHTML = `<div class="text-center py-8 text-slate-500 text-sm">No matching appointments found for "<span class="font-semibold text-slate-700">${query}</span>".</div>`;
                return;
            }

            container.innerHTML = results.map(apt => `
                <div class="p-4 rounded-xl border border-slate-200 bg-slate-50 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 hover:border-blue-300 transition-all">
                    <div>
                        <div class="flex items-center gap-2">
                            <span class="font-mono text-xs font-bold text-blue-600 bg-blue-100 px-2 py-0.5 rounded">${apt.id}</span>
                            <h4 class="font-bold text-slate-800 text-sm">${apt.patientName}</h4>
                        </div>
                        <p class="text-xs text-slate-500 mt-1">
                            <i class="fa-solid fa-user-doctor text-teal-600 mr-1"></i> ${apt.doctor} (${apt.department})
                        </p>
                        <p class="text-xs text-slate-500">
                            <i class="fa-solid fa-clock text-blue-500 mr-1"></i> ${apt.appointmentDate} at ${apt.appointmentTime}
                        </p>
                    </div>
                    <button onclick="cancelAppointment('${apt.id}')" class="bg-red-50 hover:bg-red-100 text-red-600 text-xs font-semibold px-3 py-2 rounded-lg border border-red-200 transition-all">
                        Cancel Appointment
                    </button>
                </div>
            `).join('');
        }

        /**
         * ==========================================
         * 4. UI RENDERING & VISUALIZERS
         * ==========================================
         */

        function refreshAllUI() {
            updateBadgeCounters();
            renderDashboard();
            renderViewAppointments();
            renderCancellationStack();
            renderVisualizer();
        }

        function updateBadgeCounters() {
            document.getElementById('undo-badge').textContent = cancellationStack.size();
            document.getElementById('stat-total').textContent = appointmentList.length;
            document.getElementById('stat-cancelled').textContent = cancellationStack.size();
            
            const headNode = appointmentList.head;
            document.getElementById('stat-next').textContent = headNode ? headNode.data.patientName : 'None';

            // DS Summary Card
            document.getElementById('ds-head-id').textContent = headNode ? headNode.data.id : 'null';
            document.getElementById('ds-tail-id').textContent = appointmentList.tail ? appointmentList.tail.data.id : 'null';
            
            const stackTop = cancellationStack.peek();
            document.getElementById('ds-stack-top').textContent = stackTop ? stackTop.id : 'Empty';
            document.getElementById('ds-stack-size').textContent = cancellationStack.size();
        }

        function renderDashboard() {
            const container = document.getElementById('dashboard-list-container');
            const appointments = appointmentList.toArray().slice(0, 4); // Display first 4

            if (appointments.length === 0) {
                container.innerHTML = `<div class="p-6 text-center text-slate-400 text-xs bg-slate-50 rounded-xl border border-dashed border-slate-200">No upcoming appointments in Linked List.</div>`;
                return;
            }

            container.innerHTML = appointments.map((apt, index) => `
                <div class="p-4 rounded-xl border border-slate-200/80 bg-slate-50/50 hover:bg-white hover:border-blue-200 transition-all flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3">
                    <div class="flex items-center gap-3">
                        <div class="w-8 h-8 rounded-full ${index === 0 ? 'bg-blue-600 text-white' : 'bg-slate-200 text-slate-600'} text-xs font-mono font-bold flex items-center justify-center">
                            ${index + 1}
                        </div>
                        <div>
                            <div class="flex items-center gap-2">
                                <h4 class="font-bold text-slate-800 text-sm">${apt.patientName}</h4>
                                <span class="font-mono text-[10px] text-slate-500 bg-slate-200 px-1.5 py-0.5 rounded">${apt.id}</span>
                            </div>
                            <p class="text-xs text-slate-500"><i class="fa-solid fa-user-doctor text-teal-600 mr-1"></i> ${apt.doctor}</p>
                        </div>
                    </div>
                    <div class="text-right flex sm:flex-col justify-between w-full sm:w-auto items-center sm:items-end border-t sm:border-0 pt-2 sm:pt-0 border-slate-200">
                        <span class="text-xs font-semibold text-slate-700"><i class="fa-regular fa-calendar mr-1"></i> ${apt.appointmentDate}</span>
                        <span class="text-xs text-slate-500">${apt.appointmentTime}</span>
                    </div>
                </div>
            `).join('');
        }

        function renderViewAppointments() {
            const tbody = document.getElementById('appointments-table-body');
            const emptyMsg = document.getElementById('empty-view-msg');
            const sortBy = document.getElementById('view-sort').value;

            let appointments = appointmentList.toArray();

            if (appointments.length === 0) {
                tbody.innerHTML = '';
                emptyMsg.classList.remove('hidden');
                return;
            }

            emptyMsg.classList.add('hidden');

            if (sortBy === 'date') {
                appointments.sort((a, b) => new Date(a.appointmentDate) - new Date(b.appointmentDate));
            } else if (sortBy === 'patient') {
                appointments.sort((a, b) => a.patientName.localeCompare(b.patientName));
            }

            tbody.innerHTML = appointments.map(apt => `
                <tr class="hover:bg-slate-50 transition-all">
                    <td class="p-3 font-mono text-xs font-bold text-blue-600">${apt.id}</td>
                    <td class="p-3">
                        <div class="font-semibold text-slate-800">${apt.patientName}</div>
                        <div class="text-xs text-slate-400">${apt.contact}</div>
                    </td>
                    <td class="p-3">
                        <div class="text-xs font-semibold text-slate-700">${apt.doctor}</div>
                        <div class="text-xs text-teal-600">${apt.department}</div>
                    </td>
                    <td class="p-3">
                        <div class="text-xs font-medium text-slate-800">${apt.appointmentDate}</div>
                        <div class="text-xs text-slate-400">${apt.appointmentTime}</div>
                    </td>
                    <td class="p-3 text-xs text-slate-600 max-w-xs truncate">${apt.reason}</td>
                    <td class="p-3 text-right">
                        <button onclick="cancelAppointment('${apt.id}')" title="Cancel & Push to Stack" class="text-xs bg-red-50 hover:bg-red-100 text-red-600 px-3 py-1.5 rounded-lg border border-red-200 font-medium transition-all">
                            <i class="fa-solid fa-xmark mr-1"></i> Cancel
                        </button>
                    </td>
                </tr>
            `).join('');
        }

        function renderCancellationStack() {
            const container = document.getElementById('cancellation-stack-list');
            const elements = cancellationStack.getElements(); // Top to bottom
            const label = document.getElementById('stack-count-label');

            label.textContent = `${cancellationStack.size()} Elements in Stack`;

            if (elements.length === 0) {
                container.innerHTML = `
                    <div class="p-8 text-center text-slate-400 bg-slate-50 rounded-2xl border border-dashed border-slate-200">
                        <i class="fa-solid fa-box-open text-3xl text-slate-300 mb-2"></i>
                        <p class="text-xs font-medium">Cancellation Stack is currently empty.</p>
                        <p class="text-[11px] text-slate-400 mt-1">When an active appointment is cancelled, it gets pushed here.</p>
                    </div>
                `;
                return;
            }

            container.innerHTML = elements.map((apt, idx) => `
                <div class="p-4 rounded-xl border ${idx === 0 ? 'border-amber-300 bg-amber-50/40 shadow-sm' : 'border-slate-200 bg-white'} transition-all flex justify-between items-center">
                    <div class="flex items-center gap-3">
                        <div class="px-2.5 py-1 rounded-lg ${idx === 0 ? 'bg-amber-500 text-white' : 'bg-slate-200 text-slate-600'} text-xs font-mono font-bold">
                            ${idx === 0 ? 'TOP' : `[${elements.length - 1 - idx}]`}
                        </div>
                        <div>
                            <div class="flex items-center gap-2">
                                <h4 class="font-bold text-slate-800 text-sm">${apt.patientName}</h4>
                                <span class="font-mono text-xs text-slate-500">${apt.id}</span>
                            </div>
                            <p class="text-xs text-slate-500">${apt.doctor} • ${apt.department}</p>
                        </div>
                    </div>
                    <div class="text-right">
                        <span class="inline-block text-[11px] bg-red-100 text-red-700 px-2 py-0.5 rounded font-semibold">Cancelled</span>
                    </div>
                </div>
            `).join('');
        }

        /**
         * Dynamic DS Inspector Visualizer
         */
        function renderVisualizer() {
            // 1. Render Linked List Memory Nodes
            const vizListContainer = document.getElementById('viz-linked-list');
            const nodeCountEl = document.getElementById('viz-node-count');
            
            nodeCountEl.textContent = appointmentList.length;

            if (appointmentList.length === 0) {
                vizListContainer.innerHTML = `
                    <div class="text-slate-500 font-mono text-xs py-4">Linked List is empty: (HEAD -> null)</div>
                `;
            } else {
                let html = '';
                let current = appointmentList.head;
                let index = 0;

                while (current) {
                    const isHead = (current === appointmentList.head);
                    const isTail = (current === appointmentList.tail);

                    html += `
                        <div class="flex items-center gap-3">
                            <!-- Node Box -->
                            <div class="bg-slate-900 border-2 ${isHead ? 'border-blue-500' : isTail ? 'border-teal-500' : 'border-slate-700'} rounded-xl p-3 min-w-[200px] shadow-lg relative">
                                <!-- Pointer Labels -->
                                <div class="flex justify-between items-center mb-2">
                                    <span class="text-[10px] font-mono px-1.5 py-0.5 rounded bg-slate-800 text-slate-400">Idx: ${index}</span>
                                    <div class="flex gap-1">
                                        ${isHead ? '<span class="text-[10px] font-mono bg-blue-600 text-white px-1.5 py-0.5 rounded font-bold">HEAD</span>' : ''}
                                        ${isTail ? '<span class="text-[10px] font-mono bg-teal-600 text-white px-1.5 py-0.5 rounded font-bold">TAIL</span>' : ''}
                                    </div>
                                </div>

                                <!-- Node Data -->
                                <div class="font-mono text-xs text-white font-bold truncate">${current.data.patientName}</div>
                                <div class="font-mono text-[11px] text-blue-400">${current.data.id}</div>
                                
                                <!-- Next Pointer Visual -->
                                <div class="mt-3 pt-2 border-t border-slate-800 flex justify-between items-center text-[10px] font-mono text-slate-400">
                                    <span>next:</span>
                                    <span class="${current.next ? 'text-amber-400' : 'text-slate-600'}">${current.next ? current.next.data.id : 'null'}</span>
                                </div>
                            </div>

                            <!-- Next Arrow -->
                            ${current.next ? '<div class="text-slate-600 font-bold text-xl">➔</div>' : '<div class="text-slate-600 font-mono text-xs">➔ null</div>'}
                        </div>
                    `;
                    current = current.next;
                    index++;
                }

                vizListContainer.innerHTML = html;
            }

            // 2. Render Stack Frames
            const stackVizContainer = document.getElementById('viz-stack-container');
            const stackTopIdxEl = document.getElementById('viz-stack-top-idx');
            
            stackTopIdxEl.textContent = cancellationStack.size() - 1;

            if (cancellationStack.isEmpty()) {
                stackVizContainer.innerHTML = `
                    <div class="text-center text-slate-500 font-mono text-xs py-8 border border-dashed border-slate-800 rounded-xl">
                        Stack Memory Empty (TOP = -1)
                    </div>
                `;
            } else {
                const stackItems = cancellationStack.items; // Array bottom to top
                stackVizContainer.innerHTML = stackItems.map((apt, idx) => {
                    const isTop = (idx === stackItems.length - 1);
                    return `
                        <div class="bg-slate-900 border ${isTop ? 'border-amber-500 bg-amber-500/10' : 'border-slate-800'} p-3 rounded-xl flex justify-between items-center font-mono text-xs transition-all">
                            <div class="flex items-center gap-3">
                                <span class="text-slate-500"> [${idx}] </span>
                                <div>
                                    <span class="text-white font-bold">${apt.patientName}</span>
                                    <span class="text-slate-400 text-[11px]"> (${apt.id})</span>
                                </div>
                            </div>
                            ${isTop ? '<span class="bg-amber-500 text-slate-950 font-bold text-[10px] px-2 py-0.5 rounded">TOP OF STACK</span>' : ''}
                        </div>
                    `;
                }).join('');
            }
        }

        /**
         * ==========================================
         * 5. TOAST NOTIFICATION SYSTEM
         * ==========================================
         */
        function showToast(message, type = 'info') {
            const container = document.getElementById('toast-container');
            
            const toast = document.createElement('div');
            const bgColors = {
                success: 'bg-emerald-600 text-white',
                warning: 'bg-amber-500 text-white',
                error: 'bg-rose-600 text-white',
                info: 'bg-blue-600 text-white'
            };
            const icons = {
                success: 'fa-circle-check',
                warning: 'fa-triangle-exclamation',
                error: 'fa-circle-xmark',
                info: 'fa-circle-info'
            };

            toast.className = `${bgColors[type]} p-4 rounded-xl shadow-xl flex items-center justify-between gap-3 pointer-events-auto transform transition-all duration-300 toast-enter`;
            
            toast.innerHTML = `
                <div class="flex items-center gap-2 text-xs font-semibold">
                    <i class="fa-solid ${icons[type]} text-base"></i>
                    <span>${message}</span>
                </div>
                <button onclick="this.parentElement.remove()" class="text-white opacity-70 hover:opacity-100 text-xs">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            `;

            container.appendChild(toast);

            setTimeout(() => {
                toast.classList.remove('toast-enter-active');
            }, 10);

            setTimeout(() => {
                toast.classList.add('opacity-0', '-translate-y-2');
                setTimeout(() => toast.remove(), 300);
            }, 4000);
        }
    </script>
</body>
</html>
