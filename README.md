<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Petualangan Sains: Mengenal Kalor (IPA Kelas VII)</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons CDN -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Chart.js for Lab Graph -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Google Font Inter & Fredoka -->
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        display: ['Fredoka', 'sans-serif'],
                    },
                    colors: {
                        brand: {
                            blue: '#0066FF',
                            darkblue: '#0040A8',
                            orange: '#FF6B00',
                            yellow: '#FFC700',
                            lightbg: '#F0F6FF',
                            accent: '#00E5FF'
                        }
                    }
                }
            }
        }
    </script>

    <style>
        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, #e0f2fe 0%, #f0f9ff 100%);
            min-height: 100vh;
            touch-action: manipulation;
        }
        h1, h2, h3, .font-display {
            font-family: 'Fredoka', sans-serif;
        }

        /* PID Touch friendly buttons */
        .pid-btn {
            min-height: 52px;
            min-width: 52px;
            transition: transform 0.15s ease, box-shadow 0.15s ease;
        }
        .pid-btn:active {
            transform: scale(0.96);
        }

        /* Pulse Animations for Heat & Ice */
        @keyframes flamePulse {
            0%, 100% { transform: scale(1); opacity: 0.9; }
            50% { transform: scale(1.08); opacity: 1; filter: drop-shadow(0 0 12px #ff6b00); }
        }
        .animate-flame {
            animation: flamePulse 1.2s infinite ease-in-out;
        }

        @keyframes particleMove {
            0% { transform: translateY(0) translateX(0); }
            50% { transform: translateY(-15px) translateX(5px); }
            100% { transform: translateY(0) translateX(0); }
        }
        .animate-particle {
            animation: particleMove 2s infinite ease-in-out;
        }

        /* Glassmorphism containers */
        .glass-card {
            background: rgba(255, 255, 255, 0.92);
            backdrop-filter: blur(12px);
            border: 2px solid rgba(255, 255, 255, 0.6);
            box-shadow: 0 10px 30px -5px rgba(0, 102, 255, 0.1);
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #0066FF;
            border-radius: 10px;
        }

        /* Drag & Drop Styles */
        .draggable-item {
            cursor: grab;
            user-select: none;
        }
        .draggable-item:active {
            cursor: grabbing;
        }
        .drop-target {
            transition: all 0.2s ease;
        }
        .drop-target.drag-over {
            background-color: #dbeafe;
            border-color: #0066FF;
            transform: scale(1.02);
        }

        /* Heat Wave Effect */
        @keyframes heatWave {
            0% { transform: translateY(0) scaleX(1); opacity: 0.8; }
            50% { transform: translateY(-10px) scaleX(1.05); opacity: 0.4; }
            100% { transform: translateY(-20px) scaleX(0.95); opacity: 0; }
        }
        .heat-wave {
            animation: heatWave 1.5s infinite linear;
        }
    </style>
</head>
<body class="text-slate-800 flex flex-col min-h-screen select-none">

    <!-- TOP HEADER / NAV BAR -->
    <header class="bg-gradient-to-r from-brand-darkblue via-brand-blue to-sky-500 text-white shadow-lg sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 py-3 flex items-center justify-between">
            <!-- Brand & Title -->
            <div class="flex items-center space-x-3 cursor-pointer" onclick="switchPage('home')">
                <div class="bg-white p-2 rounded-2xl shadow-inner text-brand-orange flex items-center justify-center">
                    <i data-lucide="flame" class="w-8 h-8 fill-brand-orange animate-pulse"></i>
                </div>
                <div>
                    <h1 class="text-xl md:text-2xl font-bold tracking-wide text-white leading-tight">PETUALANGAN SAINS</h1>
                    <p class="text-xs md:text-sm text-sky-200 font-medium">BAB KALOR • IPA KELAS VII SMP</p>
                </div>
            </div>

            <!-- Global Control Buttons (PID Large Targets) -->
            <div class="flex items-center space-x-2 md:space-x-3">
                <button onclick="switchPage('home')" class="pid-btn bg-white/20 hover:bg-white/30 text-white p-2 md:px-3 md:py-2 rounded-xl flex items-center space-x-1 font-semibold text-sm transition" title="Beranda">
                    <i data-lucide="home" class="w-5 h-5"></i>
                    <span class="hidden md:inline">Beranda</span>
                </button>
                <button onclick="openHelpModal()" class="pid-btn bg-amber-400 hover:bg-amber-300 text-slate-900 p-2 md:px-3 md:py-2 rounded-xl flex items-center space-x-1 font-bold text-sm shadow transition" title="Petunjuk">
                    <i data-lucide="help-circle" class="w-5 h-5"></i>
                    <span class="hidden md:inline">Petunjuk</span>
                </button>
                <button onclick="toggleSound()" id="sound-btn" class="pid-btn bg-white/20 hover:bg-white/30 text-white p-2 rounded-xl" title="Suara FX">
                    <i data-lucide="volume-2" class="w-5 h-5"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- NAVIGATION MENU BAR -->
    <nav class="bg-white/80 backdrop-blur-md border-b border-sky-100 shadow-sm sticky top-[68px] z-40 overflow-x-auto">
        <div class="max-w-7xl mx-auto px-4 flex space-x-2 py-2 min-w-max">
            <button onclick="switchPage('materi')" id="nav-materi" class="nav-tab px-4 py-2 rounded-xl font-bold text-sm md:text-base flex items-center space-x-2 text-slate-600 hover:bg-sky-50 transition">
                <i data-lucide="book-open" class="w-4 h-4"></i>
                <span>Materi Kalor</span>
            </button>
            <button onclick="switchPage('lab')" id="nav-lab" class="nav-tab px-4 py-2 rounded-xl font-bold text-sm md:text-base flex items-center space-x-2 text-slate-600 hover:bg-sky-50 transition">
                <i data-lucide="flask-conical" class="w-4 h-4 text-brand-blue"></i>
                <span>Lab Maya (Virtual)</span>
            </button>
            <button onclick="switchPage('game')" id="nav-game" class="nav-tab px-4 py-2 rounded-xl font-bold text-sm md:text-base flex items-center space-x-2 text-slate-600 hover:bg-sky-50 transition">
                <i data-lucide="gamepad-2" class="w-4 h-4 text-brand-orange"></i>
                <span>Misi Penjelajah</span>
            </button>
            <button onclick="switchPage('eval')" id="nav-eval" class="nav-tab px-4 py-2 rounded-xl font-bold text-sm md:text-base flex items-center space-x-2 text-slate-600 hover:bg-sky-50 transition">
                <i data-lucide="check-square" class="w-4 h-4 text-emerald-600"></i>
                <span>Kuis & Evaluasi</span>
            </button>
            <button onclick="switchPage('notes')" id="nav-notes" class="nav-tab px-4 py-2 rounded-xl font-bold text-sm md:text-base flex items-center space-x-2 text-slate-600 hover:bg-sky-50 transition">
                <i data-lucide="notebook-pen" class="w-4 h-4 text-purple-600"></i>
                <span>Catatan Saya</span>
            </button>
            <button onclick="switchPage('teacher')" id="nav-teacher" class="nav-tab px-4 py-2 rounded-xl font-bold text-sm md:text-base flex items-center space-x-2 text-slate-600 hover:bg-sky-50 transition">
                <i data-lucide="layout-dashboard" class="w-4 h-4 text-indigo-600"></i>
                <span>Dashboard Guru</span>
            </button>
        </div>
    </nav>

    <!-- MAIN CONTENT CONTAINER -->
    <main class="flex-grow max-w-7xl w-full mx-auto p-4 md:p-6">

        <!-- PAGE 1: HOME / BERANDA -->
        <section id="page-home" class="page-section space-y-8">
            <!-- Hero Banner -->
            <div class="glass-card rounded-3xl p-6 md:p-10 bg-gradient-to-br from-white via-sky-50 to-amber-50 border-2 border-sky-200 relative overflow-hidden shadow-xl">
                <div class="grid grid-cols-1 md:grid-cols-12 gap-8 items-center relative z-10">
                    <div class="md:col-span-7 space-y-4 text-center md:text-left">
                        <span class="inline-block px-4 py-1.5 rounded-full bg-brand-orange/10 text-brand-orange font-bold text-sm border border-brand-orange/20 shadow-sm">
                            🔬 Petualangan IPA Kelas VII SMP
                        </span>
                        <h2 class="text-3xl md:text-5xl font-extrabold text-slate-900 leading-tight">
                            Jelajahi Rahasia <span class="text-transparent bg-clip-text bg-gradient-to-r from-brand-orange via-amber-500 to-red-500">KALOR & ENERGI</span>
                        </h2>
                        <p class="text-slate-600 text-base md:text-lg leading-relaxed">
                            Aplikasi interaktif pembelajaran IPA! Lakukan eksperimen virtual, pecahkan tantangan sains, dan kuasai konsep kalor dengan cara yang seru!
                        </p>

                        <!-- Action Buttons -->
                        <div class="pt-4 flex flex-wrap gap-3 justify-center md:justify-start">
                            <button onclick="switchPage('materi')" class="pid-btn bg-brand-orange hover:bg-orange-600 text-white font-bold px-6 py-3.5 rounded-2xl shadow-lg shadow-orange-500/30 flex items-center space-x-2 text-lg transform hover:-translate-y-0.5 transition">
                                <i data-lucide="play-circle" class="w-6 h-6"></i>
                                <span>Mulai Belajar</span>
                            </button>
                            <button onclick="switchPage('lab')" class="pid-btn bg-brand-blue hover:bg-blue-700 text-white font-bold px-6 py-3.5 rounded-2xl shadow-lg shadow-blue-500/30 flex items-center space-x-2 text-lg transform hover:-translate-y-0.5 transition">
                                <i data-lucide="flask-conical" class="w-6 h-6"></i>
                                <span>Lab Maya</span>
                            </button>
                            <button onclick="switchPage('game')" class="pid-btn bg-emerald-500 hover:bg-emerald-600 text-white font-bold px-6 py-3.5 rounded-2xl shadow-lg shadow-emerald-500/30 flex items-center space-x-2 text-lg transform hover:-translate-y-0.5 transition">
                                <i data-lucide="gamepad-2" class="w-6 h-6"></i>
                                <span>Tantangan Kalor</span>
                            </button>
                        </div>
                    </div>

                    <!-- Hero Animated Visual Illustration -->
                    <div class="md:col-span-5 flex justify-center relative">
                        <div class="relative w-64 h-64 md:w-80 md:h-80 bg-gradient-to-tr from-sky-200 to-amber-100 rounded-full p-4 flex items-center justify-center border-4 border-white shadow-2xl">
                            <!-- Sun / Heat Source -->
                            <div class="absolute -top-2 -right-2 text-amber-500 animate-spin" style="animation-duration: 20s;">
                                <i data-lucide="sun" class="w-16 h-16 fill-amber-400"></i>
                            </div>
                            <!-- Melting Ice -->
                            <div class="bg-white/80 p-5 rounded-2xl shadow-lg border border-sky-100 text-center flex flex-col items-center space-y-2 transform -rotate-6">
                                <i data-lucide="snowflake" class="w-12 h-12 text-sky-400 animate-bounce"></i>
                                <span class="font-bold text-xs text-sky-700">Es Mencair (Menyerap Kalor)</span>
                            </div>
                            <!-- Transfer Arrow -->
                            <div class="absolute text-brand-orange animate-pulse">
                                <i data-lucide="arrow-right-left" class="w-12 h-12"></i>
                            </div>
                            <!-- Boiling Pot -->
                            <div class="bg-white/80 p-5 rounded-2xl shadow-lg border border-orange-100 text-center flex flex-col items-center space-y-2 transform rotate-6 translate-y-12">
                                <i data-lucide="flame" class="w-12 h-12 text-brand-orange animate-flame"></i>
                                <span class="font-bold text-xs text-orange-700">Air Mendidih</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- PERTANYAAN PEMANTIK (TRIGGER QUESTIONS) -->
            <div class="space-y-4">
                <div class="flex items-center space-x-3">
                    <div class="bg-brand-orange text-white p-2 rounded-xl">
                        <i data-lucide="lightbulb" class="w-6 h-6"></i>
                    </div>
                    <div>
                        <h3 class="text-2xl font-bold text-slate-900">Pertanyaan Pemantik Sains</h3>
                        <p class="text-sm text-slate-600">Klik kartu di bawah untuk mengungkapkan jawaban dan konsep ilmiahnya!</p>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <!-- Q1 -->
                    <div onclick="toggleTriggerAnswer(1)" class="glass-card p-5 rounded-2xl border-2 border-sky-200 cursor-pointer hover:border-brand-blue transition shadow-sm group">
                        <div class="flex items-start justify-between">
                            <span class="bg-sky-100 text-sky-700 font-bold px-3 py-1 rounded-lg text-xs">Tanya 1</span>
                            <i data-lucide="chevron-down" id="arrow-q1" class="w-5 h-5 text-slate-400 group-hover:text-brand-blue transition"></i>
                        </div>
                        <h4 class="font-bold text-lg text-slate-800 mt-2">Mengapa es batu di luar kulkas lama-kelamaan mencair?</h4>
                        <div id="ans-q1" class="hidden mt-3 pt-3 border-t border-slate-200 text-sm text-slate-600 bg-sky-50/80 p-3 rounded-xl border border-sky-100">
                            💡 <strong class="text-brand-blue">Penjelasan:</strong> Es batu memiliki suhu lebih rendah (0°C) dibanding udara sekitar. Es batu <strong>menyerap kalor</strong> dari udara, sehingga suhunya naik dan wujudnya berubah dari padat menjadi cair.
                        </div>
                    </div>

                    <!-- Q2 -->
                    <div onclick="toggleTriggerAnswer(2)" class="glass-card p-5 rounded-2xl border-2 border-sky-200 cursor-pointer hover:border-brand-blue transition shadow-sm group">
                        <div class="flex items-start justify-between">
                            <span class="bg-sky-100 text-sky-700 font-bold px-3 py-1 rounded-lg text-xs">Tanya 2</span>
                            <i data-lucide="chevron-down" id="arrow-q2" class="w-5 h-5 text-slate-400 group-hover:text-brand-blue transition"></i>
                        </div>
                        <h4 class="font-bold text-lg text-slate-800 mt-2">Mengapa sendok logam di air panas ikut menjadi panas?</h4>
                        <div id="ans-q2" class="hidden mt-3 pt-3 border-t border-slate-200 text-sm text-slate-600 bg-sky-50/80 p-3 rounded-xl border border-sky-100">
                            💡 <strong class="text-brand-blue">Penjelasan:</strong> Terjadi peristiwa <strong>Konduksi</strong>! Kalor dari air panas merambat melalui partikel-partikel logam sendok menuju gagangnya tanpa disertai perpindahan partikel logam tersebut.
                        </div>
                    </div>

                    <!-- Q3 -->
                    <div onclick="toggleTriggerAnswer(3)" class="glass-card p-5 rounded-2xl border-2 border-sky-200 cursor-pointer hover:border-brand-blue transition shadow-sm group">
                        <div class="flex items-start justify-between">
                            <span class="bg-sky-100 text-sky-700 font-bold px-3 py-1 rounded-lg text-xs">Tanya 3</span>
                            <i data-lucide="chevron-down" id="arrow-q3" class="w-5 h-5 text-slate-400 group-hover:text-brand-blue transition"></i>
                        </div>
                        <h4 class="font-bold text-lg text-slate-800 mt-2">Mengapa air dapat mendidih ketika dipanaskan?</h4>
                        <div id="ans-q3" class="hidden mt-3 pt-3 border-t border-slate-200 text-sm text-slate-600 bg-sky-50/80 p-3 rounded-xl border border-sky-100">
                            💡 <strong class="text-brand-blue">Penjelasan:</strong> Pemanasan terus menerus memberikan kalor pada air hingga mencapai titik didih (100°C). Terjadi <strong>Konveksi</strong> (aliran air panas naik dan air dingin turun) hingga seluruh air mendidih dan menguap.
                        </div>
                    </div>

                    <!-- Q4 -->
                    <div onclick="toggleTriggerAnswer(4)" class="glass-card p-5 rounded-2xl border-2 border-sky-200 cursor-pointer hover:border-brand-blue transition shadow-sm group">
                        <div class="flex items-start justify-between">
                            <span class="bg-sky-100 text-sky-700 font-bold px-3 py-1 rounded-lg text-xs">Tanya 4</span>
                            <i data-lucide="chevron-down" id="arrow-q4" class="w-5 h-5 text-slate-400 group-hover:text-brand-blue transition"></i>
                        </div>
                        <h4 class="font-bold text-lg text-slate-800 mt-2">Apakah Suhu dan Kalor memiliki arti yang sama?</h4>
                        <div id="ans-q4" class="hidden mt-3 pt-3 border-t border-slate-200 text-sm text-slate-600 bg-sky-50/80 p-3 rounded-xl border border-sky-100">
                            💡 <strong class="text-brand-blue">Penjelasan:</strong> Tidak sama! <strong>Suhu</strong> adalah ukuran/derajat panas dinginnya suatu benda (°C), sedangkan <strong>Kalor</strong> adalah bentuk energi panas yang berpindah dari benda bersuhu tinggi ke suhu rendah (Joule).
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- PAGE 2: MATERI PEMBELAJARAN (LESSONS) -->
        <section id="page-materi" class="page-section hidden space-y-6">
            <!-- Lesson Sub-Nav -->
            <div class="flex overflow-x-auto space-x-2 pb-2 scrollbar-none">
                <button onclick="showSubMateri(1)" id="submateri-btn-1" class="submateri-btn bg-brand-blue text-white px-4 py-2.5 rounded-xl font-bold text-sm whitespace-nowrap shadow-sm">1. Pengertian Kalor</button>
                <button onclick="showSubMateri(2)" id="submateri-btn-2" class="submateri-btn bg-white text-slate-700 hover:bg-sky-100 px-4 py-2.5 rounded-xl font-bold text-sm whitespace-nowrap shadow-sm">2. Beda Suhu & Kalor</button>
                <button onclick="showSubMateri(3)" id="submateri-btn-3" class="submateri-btn bg-white text-slate-700 hover:bg-sky-100 px-4 py-2.5 rounded-xl font-bold text-sm whitespace-nowrap shadow-sm">3. Pengaruh Kalor</button>
                <button onclick="showSubMateri(4)" id="submateri-btn-4" class="submateri-btn bg-white text-slate-700 hover:bg-sky-100 px-4 py-2.5 rounded-xl font-bold text-sm whitespace-nowrap shadow-sm">4. Perpindahan Kalor</button>
                <button onclick="showSubMateri(5)" id="submateri-btn-5" class="submateri-btn bg-white text-slate-700 hover:bg-sky-100 px-4 py-2.5 rounded-xl font-bold text-sm whitespace-nowrap shadow-sm">5. Rumus & Kalkulator</button>
            </div>

            <!-- SUB MATERI 1: PENGERTIAN KALOR -->
            <div id="submateri-1" class="submateri-content glass-card p-6 rounded-3xl space-y-6">
                <div class="border-b pb-4">
                    <span class="text-xs font-bold bg-brand-blue/10 text-brand-blue px-3 py-1 rounded-full">Materi 1</span>
                    <h3 class="text-2xl font-bold text-slate-900 mt-2">Apa itu Kalor?</h3>
                </div>
                <p class="text-slate-700 leading-relaxed text-base md:text-lg">
                    <strong class="text-brand-blue">Kalor</strong> adalah bentuk energi yang berpindah dari benda yang bersuhu lebih tinggi ke benda yang bersuhu lebih rendah karena adanya perbedaan suhu. Kalor terus berpindah hingga kedua benda mencapai suhu yang sama (kesetimbangan termal).
                </p>

                <!-- Interactive Heat Transfer Animation Card -->
                <div class="bg-gradient-to-r from-sky-50 to-orange-50 border-2 border-slate-200 rounded-2xl p-6 text-center space-y-4">
                    <h4 class="font-bold text-slate-800">Simulasi Arah Perpindahan Kalor Alami</h4>
                    <div class="flex flex-col md:flex-row items-center justify-center gap-6">
                        <!-- Hot Object -->
                        <div class="bg-red-500 text-white p-6 rounded-2xl shadow-md w-48 text-center animate-pulse">
                            <i data-lucide="thermometer-sun" class="w-10 h-10 mx-auto mb-2"></i>
                            <div class="font-bold text-lg">Benda A</div>
                            <div class="text-xs opacity-90">Suhu Tinggi (80°C)</div>
                        </div>

                        <!-- Direction Arrows -->
                        <div class="flex flex-col items-center space-y-1 text-brand-orange">
                            <span class="font-extrabold text-xs tracking-wider">KALOR BERPINDAH</span>
                            <i data-lucide="arrow-right" class="w-10 h-10 hidden md:block animate-bounce"></i>
                            <i data-lucide="arrow-down" class="w-10 h-10 md:hidden animate-bounce"></i>
                        </div>

                        <!-- Cold Object -->
                        <div class="bg-sky-500 text-white p-6 rounded-2xl shadow-md w-48 text-center">
                            <i data-lucide="snowflake" class="w-10 h-10 mx-auto mb-2"></i>
                            <div class="font-bold text-lg">Benda B</div>
                            <div class="text-xs opacity-90">Suhu Rendah (20°C)</div>
                        </div>
                    </div>
                </div>

                <!-- Examples list -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-start space-x-3">
                        <span class="text-2xl">🧊</span>
                        <div>
                            <h5 class="font-bold text-slate-800 text-sm">Es Batu Mencair</h5>
                            <p class="text-xs text-slate-600 mt-1">Es menyerap kalor dari lingkungan sekitarnya sehingga mencair.</p>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-start space-x-3">
                        <span class="text-2xl">🫖</span>
                        <div>
                            <h5 class="font-bold text-slate-800 text-sm">Air Dipanaskan</h5>
                            <p class="text-xs text-slate-600 mt-1">Air menerima kalor dari api kompor sehingga suhunya naik.</p>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-start space-x-3">
                        <span class="text-2xl">☕</span>
                        <div>
                            <h5 class="font-bold text-slate-800 text-sm">Teh Panas Mendingin</h5>
                            <p class="text-xs text-slate-600 mt-1">Teh melepaskan kalor ke udara dingin sekitarnya.</p>
                        </div>
                    </div>
                </div>
            </div>

            <!-- SUB MATERI 2: PERBEDAAN SUHU DAN KALOR -->
            <div id="submateri-2" class="submateri-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="border-b pb-4">
                    <span class="text-xs font-bold bg-brand-blue/10 text-brand-blue px-3 py-1 rounded-full">Materi 2</span>
                    <h3 class="text-2xl font-bold text-slate-900 mt-2">Perbedaan Suhu dan Kalor</h3>
                </div>

                <!-- Comparison Table -->
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse bg-white rounded-2xl overflow-hidden shadow-sm border border-slate-200">
                        <thead class="bg-brand-blue text-white font-bold text-sm">
                            <tr>
                                <th class="p-4">Faktor Pembanding</th>
                                <th class="p-4 bg-blue-700">Suhu (Temperature)</th>
                                <th class="p-4 bg-amber-600">Kalor (Heat Energy)</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-slate-200 text-sm text-slate-700">
                            <tr>
                                <td class="p-4 font-bold bg-slate-50">Pengertian</td>
                                <td class="p-4">Derajat panas atau dinginnya suatu benda.</td>
                                <td class="p-4">Bentuk energi panas yang berpindah karena beda suhu.</td>
                            </tr>
                            <tr>
                                <td class="p-4 font-bold bg-slate-50">Alat Ukur</td>
                                <td class="p-4">Termometer</td>
                                <td class="p-4">Kalorimeter</td>
                            </tr>
                            <tr>
                                <td class="p-4 font-bold bg-slate-50">Satuan SI</td>
                                <td class="p-4">Kelvin (K) atau Celsius (°C)</td>
                                <td class="p-4">Joule (J) atau Kalori (kal)</td>
                            </tr>
                            <tr>
                                <td class="p-4 font-bold bg-slate-50">Sifat</td>
                                <td class="p-4">Tidak bergantung pada jumlah/massa zat.</td>
                                <td class="p-4">Bergantung pada massa, jenis, dan perubahan suhu zat.</td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <!-- Interactive Visual Comparison: Two Beakers -->
                <div class="bg-amber-50/70 border-2 border-amber-200 rounded-2xl p-5 space-y-3">
                    <h4 class="font-bold text-slate-900 text-center">💡 Eksperimen Konseptual: Dua Gelas Air</h4>
                    <p class="text-xs md:text-sm text-slate-600 text-center">Meskipun suhunya sama (100°C), gelas dengan jumlah air lebih banyak memiliki total energi kalor yang lebih besar!</p>
                    <div class="grid grid-cols-2 gap-4 max-w-lg mx-auto pt-2">
                        <div class="bg-white p-4 rounded-xl border border-amber-200 text-center shadow-sm">
                            <div class="w-16 h-20 bg-sky-200 border-2 border-slate-400 mx-auto rounded-b-xl relative overflow-hidden flex items-end justify-center">
                                <div class="w-full bg-sky-400 h-1/2"></div>
                            </div>
                            <span class="block font-bold text-sm mt-2">Gelas Kecil (100 ml)</span>
                            <span class="text-xs text-slate-500">Suhu: 80°C | Kalor Kecil</span>
                        </div>
                        <div class="bg-white p-4 rounded-xl border border-amber-200 text-center shadow-sm">
                            <div class="w-20 h-20 bg-sky-200 border-2 border-slate-400 mx-auto rounded-b-xl relative overflow-hidden flex items-end justify-center">
                                <div class="w-full bg-sky-400 h-full"></div>
                            </div>
                            <span class="block font-bold text-sm mt-2">Gelas Besar (500 ml)</span>
                            <span class="text-xs text-slate-500">Suhu: 80°C | Kalor Lebih Besar</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- SUB MATERI 3: PENGARUH KALOR -->
            <div id="submateri-3" class="submateri-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="border-b pb-4">
                    <span class="text-xs font-bold bg-brand-blue/10 text-brand-blue px-3 py-1 rounded-full">Materi 3</span>
                    <h3 class="text-2xl font-bold text-slate-900 mt-2">Pengaruh Kalor Terhadap Benda</h3>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- Effect 1 -->
                    <div class="bg-gradient-to-br from-orange-50 to-amber-50 p-5 rounded-2xl border border-orange-200 flex space-x-4">
                        <div class="bg-brand-orange text-white p-3 rounded-xl h-fit">
                            <i data-lucide="trending-up" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <h4 class="font-bold text-lg text-slate-800">1. Mengubah Suhu Benda</h4>
                            <p class="text-xs text-slate-600 mt-1">Jika benda <strong>menyerap kalor</strong>, suhunya akan naik. Sebaliknya, jika benda <strong>melepas kalor</strong>, suhunya akan turun.</p>
                        </div>
                    </div>

                    <!-- Effect 2 -->
                    <div class="bg-gradient-to-br from-sky-50 to-blue-50 p-5 rounded-2xl border border-sky-200 flex space-x-4">
                        <div class="bg-brand-blue text-white p-3 rounded-xl h-fit">
                            <i data-lucide="refresh-cw" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <h4 class="font-bold text-lg text-slate-800">2. Mengubah Wujud Zat</h4>
                            <p class="text-xs text-slate-600 mt-1">Kalor dapat mengubah wujud: Mencair (padat ➔ cair), Menguap (cair ➔ gas), atau Menyublim (padat ➔ gas).</p>
                        </div>
                    </div>

                    <!-- Effect 3 -->
                    <div class="bg-gradient-to-br from-emerald-50 to-teal-50 p-5 rounded-2xl border border-emerald-200 flex space-x-4">
                        <div class="bg-emerald-600 text-white p-3 rounded-xl h-fit">
                            <i data-lucide="maximize" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <h4 class="font-bold text-lg text-slate-800">3. Menyebabkan Pemuaian</h4>
                            <p class="text-xs text-slate-600 mt-1">Benda padat, cair, dan gas akan bertambah ukuran (memuai) saat dipanaskan dan menyusut saat didinginkan.</p>
                        </div>
                    </div>

                    <!-- Interactive Phase Diagram Graphic -->
                    <div class="bg-white p-4 rounded-2xl border border-slate-200 flex flex-col items-center justify-center text-center">
                        <span class="text-xs font-bold text-slate-400 mb-2">DIAGRAM PERUBAHAN WUJUD ZAT</span>
                        <div class="flex items-center space-x-3 text-sm font-bold">
                            <span class="bg-sky-100 text-sky-800 px-3 py-1.5 rounded-lg">Padat (Es)</span>
                            <i data-lucide="arrow-right" class="w-4 h-4 text-brand-orange"></i>
                            <span class="bg-blue-100 text-blue-800 px-3 py-1.5 rounded-lg">Cair (Air)</span>
                            <i data-lucide="arrow-right" class="w-4 h-4 text-brand-orange"></i>
                            <span class="bg-amber-100 text-amber-800 px-3 py-1.5 rounded-lg">Gas (Uap)</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- SUB MATERI 4: PERPINDAHAN KALOR -->
            <div id="submateri-4" class="submateri-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="border-b pb-4">
                    <span class="text-xs font-bold bg-brand-blue/10 text-brand-blue px-3 py-1 rounded-full">Materi 4</span>
                    <h3 class="text-2xl font-bold text-slate-900 mt-2">3 Cara Perpindahan Kalor</h3>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <!-- Konduksi -->
                    <div class="bg-white p-5 rounded-2xl border-2 border-sky-100 hover:border-brand-blue transition shadow-sm space-y-3">
                        <div class="bg-sky-100 text-brand-blue w-12 h-12 rounded-xl flex items-center justify-center font-bold text-xl">1</div>
                        <h4 class="font-bold text-lg text-slate-800">Konduksi</h4>
                        <p class="text-xs text-slate-600 leading-relaxed">
                            Perpindahan kalor melalui zat perantara <strong>TANPA disertai perpindahan partikel-partikel</strong> zat tersebut. Terjadi pada benda padat (terutama konduktor logam).
                        </p>
                        <div class="bg-slate-50 p-3 rounded-xl border text-xs text-slate-700">
                            🍳 <strong>Contoh:</strong> Ujung sendok logam terasa panas saat dipakai mengaduk teh panas.
                        </div>
                    </div>

                    <!-- Konveksi -->
                    <div class="bg-white p-5 rounded-2xl border-2 border-sky-100 hover:border-brand-blue transition shadow-sm space-y-3">
                        <div class="bg-amber-100 text-brand-orange w-12 h-12 rounded-xl flex items-center justify-center font-bold text-xl">2</div>
                        <h4 class="font-bold text-lg text-slate-800">Konveksi</h4>
                        <p class="text-xs text-slate-600 leading-relaxed">
                            Perpindahan kalor yang <strong>DISERTAI perpindahan partikel-partikel</strong> zat perantaranya. Terjadi pada zat cair dan gas (fluida).
                        </p>
                        <div class="bg-slate-50 p-3 rounded-xl border text-xs text-slate-700">
                            🫖 <strong>Contoh:</strong> Gerakan memutar air mendidih dalam panci; Terjadinya angin laut & angin darat.
                        </div>
                    </div>

                    <!-- Radiasi -->
                    <div class="bg-white p-5 rounded-2xl border-2 border-sky-100 hover:border-brand-blue transition shadow-sm space-y-3">
                        <div class="bg-red-100 text-red-600 w-12 h-12 rounded-xl flex items-center justify-center font-bold text-xl">3</div>
                        <h4 class="font-bold text-lg text-slate-800">Radiasi</h4>
                        <p class="text-xs text-slate-600 leading-relaxed">
                            Perpindahan kalor secara langsung melalui gelombang elektromagnetik <strong>TANPA memerlukan zat perantara (medium)</strong>.
                        </p>
                        <div class="bg-slate-50 p-3 rounded-xl border text-xs text-slate-700">
                            ☀️ <strong>Contoh:</strong> Panas Matahari sampai ke Bumi melalui ruang hampa udara; Panas api unggun.
                        </div>
                    </div>
                </div>
            </div>

            <!-- SUB MATERI 5: PERHITUNGAN KALOR & KALKULATOR -->
            <div id="submateri-5" class="submateri-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="border-b pb-4">
                    <span class="text-xs font-bold bg-brand-blue/10 text-brand-blue px-3 py-1 rounded-full">Materi 5</span>
                    <h3 class="text-2xl font-bold text-slate-900 mt-2">Rumus Perhitungan Kalor</h3>
                </div>

                <!-- Formula Box -->
                <div class="bg-gradient-to-r from-brand-blue to-sky-600 text-white p-6 rounded-2xl text-center shadow-lg space-y-3">
                    <span class="text-xs font-bold uppercase tracking-widest text-sky-200">Rumus Utama</span>
                    <div class="text-4xl md:text-5xl font-extrabold tracking-wider font-display">
                        Q = m × c × ΔT
                    </div>
                    <div class="grid grid-cols-2 md:grid-cols-4 gap-2 pt-4 text-xs md:text-sm text-sky-100 border-t border-white/20">
                        <div><strong>Q</strong> = Kalor (Joule / J)</div>
                        <div><strong>m</strong> = Massa (kg)</div>
                        <div><strong>c</strong> = Kalor Jenis (J/kg°C)</div>
                        <div><strong>ΔT</strong> = Perubahan Suhu (°C)</div>
                    </div>
                </div>

                <!-- Sample Problem Walkthrough -->
                <div class="bg-white p-5 rounded-2xl border border-slate-200 space-y-3">
                    <h4 class="font-bold text-slate-800 flex items-center space-x-2">
                        <i data-lucide="calculator" class="w-5 h-5 text-brand-blue"></i>
                        <span>Contoh Soal & Langkah Penyelesaian</span>
                    </h4>
                    <p class="text-xs md:text-sm text-slate-600 bg-sky-50 p-3 rounded-xl border border-sky-100">
                        <strong>Soal:</strong> Air bermassa 2 kg memiliki kalor jenis 4.200 J/kg°C. Suhu air naik dari 20°C menjadi 30°C. Berapa kalor yang diterima air?
                    </p>
                    <div class="text-xs md:text-sm space-y-1 font-mono text-slate-700 bg-slate-50 p-4 rounded-xl">
                        <div>1. Diketahui: m = 2 kg, c = 4.200 J/kg°C, ΔT = 30°C - 20°C = 10°C</div>
                        <div>2. Ditanya: Q ?</div>
                        <div>3. Rumus: Q = m × c × ΔT</div>
                        <div>4. Hitung: Q = 2 × 4.200 × 10</div>
                        <div class="font-bold text-brand-blue">5. Jawaban: Q = 84.000 Joule (84 kJ)</div>
                    </div>
                </div>

                <!-- Interactive Heat Calculator Widget -->
                <div class="bg-amber-50/80 border-2 border-amber-200 p-6 rounded-2xl space-y-4">
                    <h4 class="font-bold text-slate-900 text-lg flex items-center space-x-2">
                        <span>🧮 Kalkulator Kalor Interaktif</span>
                    </h4>
                    <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                        <div>
                            <label class="block text-xs font-bold text-slate-700 mb-1">Massa Air (m in kg):</label>
                            <input type="number" id="calc-m" value="2" min="1" class="w-full p-2.5 border rounded-xl font-bold text-sm text-slate-800">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-700 mb-1">Kalor Jenis (c J/kg°C):</label>
                            <input type="number" id="calc-c" value="4200" class="w-full p-2.5 border rounded-xl font-bold text-sm text-slate-800">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-700 mb-1">Suhu Awal T1 (°C):</label>
                            <input type="number" id="calc-t1" value="20" class="w-full p-2.5 border rounded-xl font-bold text-sm text-slate-800">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-700 mb-1">Suhu Akhir T2 (°C):</label>
                            <input type="number" id="calc-t2" value="50" class="w-full p-2.5 border rounded-xl font-bold text-sm text-slate-800">
                        </div>
                    </div>
                    <button onclick="calculateHeatInApp()" class="pid-btn w-full bg-brand-orange hover:bg-orange-600 text-white font-bold py-3 rounded-xl shadow transition">
                        Hitung Kalor Sekarang!
                    </button>
                    <div id="calc-result" class="hidden bg-white p-4 rounded-xl border border-amber-300 font-mono text-sm space-y-1">
                        <!-- Dynamic Result Injection -->
                    </div>
                </div>
            </div>
        </section>

        <!-- PAGE 3: VIRTUAL LAB (LAB MAYA) -->
        <section id="page-lab" class="page-section hidden space-y-6">
            <!-- Lab Title Banner -->
            <div class="flex flex-col md:flex-row md:items-center justify-between bg-gradient-to-r from-brand-blue to-cyan-600 text-white p-6 rounded-3xl shadow-md">
                <div>
                    <span class="bg-white/20 px-3 py-1 rounded-full text-xs font-bold text-sky-100">Laboratorium Virtual</span>
                    <h3 class="text-2xl md:text-3xl font-bold mt-1">LAB MAYA: EKSPERIMEN KALOR</h3>
                    <p class="text-xs md:text-sm text-sky-100">Uji coba simulasi langsung interaktif untuk membuktikan konsep kalor!</p>
                </div>
                <!-- Lab Tabs -->
                <div class="flex space-x-2 mt-4 md:mt-0 overflow-x-auto">
                    <button onclick="switchLabExp(1)" id="lab-btn-1" class="lab-tab-btn bg-white text-brand-blue font-bold text-xs px-3 py-2 rounded-xl shadow">Exp 1: Suhu & Massa</button>
                    <button onclick="switchLabExp(2)" id="lab-btn-2" class="lab-tab-btn bg-white/20 text-white hover:bg-white/30 font-bold text-xs px-3 py-2 rounded-xl">Exp 2: Es Batu</button>
                    <button onclick="switchLabExp(3)" id="lab-btn-3" class="lab-tab-btn bg-white/20 text-white hover:bg-white/30 font-bold text-xs px-3 py-2 rounded-xl">Exp 3: Perpindahan</button>
                </div>
            </div>

            <!-- LAB EXPERIMENT 1: WATER HEATING & MASS -->
            <div id="lab-exp-1" class="lab-content glass-card p-6 rounded-3xl space-y-6">
                <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                    <!-- Left: Control Panel & Simulation Canvas -->
                    <div class="lg:col-span-6 bg-slate-900 text-white p-6 rounded-2xl flex flex-col justify-between space-y-4 relative overflow-hidden">
                        <div class="flex justify-between items-center z-10">
                            <span class="text-xs font-bold bg-sky-500/20 text-sky-300 px-3 py-1 rounded-full border border-sky-400/30">Simulasi Beaker</span>
                            <span id="lab1-timer" class="text-xs font-mono bg-slate-800 px-2.5 py-1 rounded text-amber-400">Waktu: 0 detik</span>
                        </div>

                        <!-- Visual Water Beaker Graphic -->
                        <div class="relative w-48 h-56 mx-auto bg-slate-800/80 border-4 border-slate-400 border-t-0 rounded-b-3xl flex items-end justify-center overflow-hidden my-4">
                            <!-- Water Fill Container -->
                            <div id="water-fill" class="w-full bg-sky-500/80 transition-all duration-300 relative flex items-center justify-center" style="height: 40%;">
                                <span id="water-particles" class="text-xs opacity-70">💧 💧 💧</span>
                            </div>
                            <!-- Thermometer Overlay -->
                            <div class="absolute right-4 top-2 bottom-2 w-5 bg-slate-700 rounded-full border border-slate-500 flex flex-col justify-end p-0.5">
                                <div id="thermo-mercury" class="w-full bg-red-500 rounded-full transition-all duration-300" style="height: 25%;"></div>
                            </div>
                        </div>

                        <!-- Flame Indicator -->
                        <div id="lab-flame" class="text-center invisible transition-all duration-300">
                            <i data-lucide="flame" class="w-12 h-12 mx-auto text-brand-orange animate-flame"></i>
                            <span class="text-xs font-bold text-amber-400">Pemanas Aktif</span>
                        </div>

                        <!-- Thermometer Readout -->
                        <div class="bg-slate-800 p-3 rounded-xl flex justify-between items-center text-sm font-mono border border-slate-700 z-10">
                            <span>Suhu Air Saat Ini:</span>
                            <span id="lab1-temp-display" class="text-xl font-bold text-amber-400">25.0 °C</span>
                        </div>

                        <!-- Control Sliders & Buttons -->
                        <div class="space-y-3 z-10">
                            <div>
                                <label class="block text-xs text-slate-300 mb-1 flex justify-between">
                                    <span>Massa Air (m):</span>
                                    <span id="mass-val-display" class="font-bold text-amber-300">1.0 kg</span>
                                </label>
                                <input type="range" id="lab1-mass" min="0.5" max="3.0" step="0.5" value="1.0" oninput="updateLab1Mass(this.value)" class="w-full accent-brand-orange">
                            </div>

                            <div class="flex gap-2">
                                <button onclick="startLab1Heating()" id="lab1-start-btn" class="pid-btn flex-1 bg-emerald-500 hover:bg-emerald-600 text-white font-bold py-2.5 rounded-xl text-sm transition">
                                    🔥 Mulai Pemanasan
                                </button>
                                <button onclick="resetLab1()" class="pid-btn bg-slate-700 hover:bg-slate-600 text-white px-4 py-2.5 rounded-xl text-sm font-bold transition">
                                    🔄 Reset
                                </button>
                            </div>
                        </div>
                    </div>

                    <!-- Right: Live Chart & Analysis -->
                    <div class="lg:col-span-6 space-y-4">
                        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                            <h4 class="font-bold text-sm text-slate-800 mb-2">Grafik Suhu (°C) vs Waktu (s)</h4>
                            <div class="h-56 relative">
                                <canvas id="lab1Chart"></canvas>
                            </div>
                        </div>

                        <!-- Analysis Box -->
                        <div class="bg-sky-50/80 p-4 rounded-2xl border border-sky-200 space-y-2 text-xs md:text-sm text-slate-700">
                            <h5 class="font-bold text-brand-blue flex items-center space-x-1">
                                <i data-lucide="info" class="w-4 h-4"></i>
                                <span>Hasil Pengamatan & Analisis:</span>
                            </h5>
                            <p id="lab1-analysis">
                                Klik tombol <strong>Mulai Pemanasan</strong> untuk memulai eksperimen. Perhatikan bagaimana massa air mempengaruhi kecepatan kenaikan suhu!
                            </p>
                        </div>
                    </div>
                </div>
            </div>

            <!-- LAB EXPERIMENT 2: ICE MELTING (PHASE CHANGE) -->
            <div id="lab-exp-2" class="lab-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                    <div class="lg:col-span-6 bg-slate-900 text-white p-6 rounded-2xl flex flex-col justify-between space-y-4">
                        <div class="flex justify-between items-center">
                            <span class="text-xs font-bold bg-sky-500/20 text-sky-300 px-3 py-1 rounded-full">Eksperimen Perubahan Wujud Es</span>
                            <span id="exp2-state-badge" class="text-xs font-bold bg-sky-500 text-white px-3 py-1 rounded-full">Wujud: Es Padat</span>
                        </div>

                        <!-- Visual Ice Simulation Graphic -->
                        <div class="relative w-48 h-48 mx-auto bg-slate-800 rounded-2xl border-2 border-slate-600 flex items-center justify-center p-4 my-2">
                            <div id="exp2-ice-visual" class="text-center transition-all duration-500">
                                <i data-lucide="snowflake" class="w-20 h-20 text-sky-300 mx-auto animate-pulse"></i>
                                <span class="block text-xs font-bold text-sky-200 mt-2">Es (0°C)</span>
                            </div>
                        </div>

                        <div class="bg-slate-800 p-3 rounded-xl flex justify-between items-center text-sm font-mono border border-slate-700">
                            <span>Suhu Termometer:</span>
                            <span id="exp2-temp" class="text-xl font-bold text-sky-300">-5.0 °C</span>
                        </div>

                        <div class="space-y-2">
                            <button onclick="addHeatToExp2()" class="pid-btn w-full bg-brand-orange hover:bg-orange-600 text-white font-bold py-3 rounded-xl transition flex items-center justify-center space-x-2">
                                <i data-lucide="flame" class="w-5 h-5"></i>
                                <span>Tambahkan Kalor (+ Energi)</span>
                            </button>
                            <button onclick="resetExp2()" class="pid-btn w-full bg-slate-700 hover:bg-slate-600 text-white font-bold py-2 rounded-xl text-xs">
                                Reset Eksperimen Es
                            </button>
                        </div>
                    </div>

                    <div class="lg:col-span-6 space-y-4">
                        <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm space-y-3">
                            <h4 class="font-bold text-slate-800 text-base">Konsep Penting Perubahan Wujud</h4>
                            <p class="text-xs md:text-sm text-slate-600 leading-relaxed">
                                Ketika es menerima kalor pada titik leburnya (0°C), kalor tersebut digunakan untuk <strong>memutuskan ikatan antar molekul</strong> es menjadi air cair, sehingga <strong>suhu tidak akan naik</strong> sampai seluruh es melebur sempurna!
                            </p>
                            <div class="bg-amber-50 p-3 rounded-xl border border-amber-200 text-xs text-amber-900 font-medium">
                                📌 <strong>Grafik Plateau:</strong> Garis mendatar pada grafik suhu menandakan fase perubahan wujud zat.
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- LAB EXPERIMENT 3: CONDUCTION, CONVECTION, RADIATION SIMULATION -->
            <div id="lab-exp-3" class="lab-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <!-- Conduction Interactive Card -->
                    <div onclick="selectExp3Type('konduksi')" class="exp3-card bg-white p-5 rounded-2xl border-2 border-brand-blue cursor-pointer hover:shadow-lg transition">
                        <div class="bg-blue-100 text-brand-blue p-3 rounded-xl w-fit mb-3">
                            <i data-lucide="zap" class="w-6 h-6"></i>
                        </div>
                        <h4 class="font-bold text-slate-800">Simulasi Konduksi</h4>
                        <p class="text-xs text-slate-600 mt-1">Amatilah bagaimana kalor merambat sepanjang batang logam.</p>
                    </div>

                    <!-- Convection Interactive Card -->
                    <div onclick="selectExp3Type('konveksi')" class="exp3-card bg-white p-5 rounded-2xl border-2 border-slate-200 cursor-pointer hover:shadow-lg transition">
                        <div class="bg-amber-100 text-brand-orange p-3 rounded-xl w-fit mb-3">
                            <i data-lucide="waves" class="w-6 h-6"></i>
                        </div>
                        <h4 class="font-bold text-slate-800">Simulasi Konveksi</h4>
                        <p class="text-xs text-slate-600 mt-1">Amatilah aliran arus konveksi air panas naik dan dingin turun.</p>
                    </div>

                    <!-- Radiation Interactive Card -->
                    <div onclick="selectExp3Type('radiasi')" class="exp3-card bg-white p-5 rounded-2xl border-2 border-slate-200 cursor-pointer hover:shadow-lg transition">
                        <div class="bg-red-100 text-red-600 p-3 rounded-xl w-fit mb-3">
                            <i data-lucide="sun" class="w-6 h-6"></i>
                        </div>
                        <h4 class="font-bold text-slate-800">Simulasi Radiasi</h4>
                        <p class="text-xs text-slate-600 mt-1">Amatilah perpindahan panas sinar matahari tanpa perantara.</p>
                    </div>
                </div>

                <!-- Exp 3 Visual Display Area -->
                <div id="exp3-display" class="bg-slate-900 text-white p-8 rounded-2xl text-center space-y-4">
                    <h4 id="exp3-title" class="text-xl font-bold text-amber-400">Simulasi Konduksi Pada Logam</h4>
                    <div id="exp3-canvas-area" class="h-40 flex items-center justify-center bg-slate-800 rounded-xl p-4">
                        <!-- Dynamic Animation element -->
                        <div class="flex items-center space-x-2">
                            <div class="text-red-500 animate-flame">🔥 Api</div>
                            <div class="w-48 h-4 bg-gradient-to-r from-red-500 via-amber-400 to-sky-400 rounded-full"></div>
                            <div class="text-sky-300">🧊 Ujung Dingin</div>
                        </div>
                    </div>
                    <p id="exp3-desc" class="text-xs md:text-sm text-slate-300 max-w-xl mx-auto">
                        Kalor merambat dari ujung logam yang dipanaskan api menuju ujung lainnya yang dingin melalui getaran partikel logam.
                    </p>
                </div>
            </div>
        </section>

        <!-- PAGE 4: GAMES / MISI PENJELAJAH -->
        <section id="page-game" class="page-section hidden space-y-6">
            <div class="flex items-center justify-between bg-gradient-to-r from-brand-orange to-amber-500 text-white p-6 rounded-3xl shadow-lg">
                <div>
                    <span class="bg-white/20 px-3 py-1 rounded-full text-xs font-bold text-amber-100">Permainan Edukatif</span>
                    <h3 class="text-2xl md:text-3xl font-bold mt-1">MISI PENJELAJAH KALOR</h3>
                    <p class="text-xs md:text-sm text-amber-100">Selesaikan misi tantangan sains dan kumpulkan poin!</p>
                </div>
                <!-- Game Score Counter -->
                <div class="bg-white/20 backdrop-blur-md px-5 py-3 rounded-2xl border border-white/30 text-center">
                    <span class="text-xs font-bold block text-amber-100">SKOR MISI</span>
                    <span id="game-score-display" class="text-2xl md:text-3xl font-extrabold text-white">0</span>
                </div>
            </div>

            <!-- GAME TABS / LEVELS -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-3">
                <button onclick="switchGameLevel(1)" id="gtab-1" class="gtab-btn bg-white border-2 border-brand-orange text-brand-orange p-3 rounded-2xl font-bold text-xs md:text-sm shadow-sm">Level 1: Tebak Konsep</button>
                <button onclick="switchGameLevel(2)" id="gtab-2" class="gtab-btn bg-white border-2 border-slate-200 text-slate-700 p-3 rounded-2xl font-bold text-xs md:text-sm shadow-sm">Level 2: Pasangkan</button>
                <button onclick="switchGameLevel(3)" id="gtab-3" class="gtab-btn bg-white border-2 border-slate-200 text-slate-700 p-3 rounded-2xl font-bold text-xs md:text-sm shadow-sm">Level 3: Detektif Kalor</button>
                <button onclick="switchGameLevel(4)" id="gtab-4" class="gtab-btn bg-white border-2 border-slate-200 text-slate-700 p-3 rounded-2xl font-bold text-xs md:text-sm shadow-sm">Level 4: Balapan Hitung</button>
            </div>

            <!-- GAME LEVEL 1: TEBAK KONSEP -->
            <div id="game-lvl-1" class="game-lvl-content glass-card p-6 rounded-3xl space-y-6">
                <div class="text-center space-y-2">
                    <span class="text-xs font-bold text-brand-orange bg-orange-100 px-3 py-1 rounded-full">Tebak Konsep Kalor</span>
                    <h4 id="g1-question" class="text-xl font-bold text-slate-800">Peristiwa mana yang menunjukkan perpindahan kalor secara KONVEKSI?</h4>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 max-w-2xl mx-auto" id="g1-options">
                    <!-- Dynamic Answer Option Buttons Injected by JS -->
                </div>

                <div id="g1-feedback" class="hidden text-center p-4 rounded-2xl font-bold text-sm"></div>
            </div>

            <!-- GAME LEVEL 2: PASANGKAN PERISTIWA (MATCHING / DRAG DROP) -->
            <div id="game-lvl-2" class="game-lvl-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="text-center">
                    <h4 class="font-bold text-xl text-slate-800">Pasangkan Peristiwa Sehari-hari dengan Konsepnya</h4>
                    <p class="text-xs text-slate-500 mt-1">Klik item peristiwa lalu klik kotak pasangan konsep yang sesuai!</p>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- Left: Phenomena Items -->
                    <div class="space-y-3" id="g2-items-col">
                        <!-- Dynamic Items -->
                    </div>
                    <!-- Right: Concept Drop Targets -->
                    <div class="space-y-3" id="g2-targets-col">
                        <!-- Dynamic Targets -->
                    </div>
                </div>
            </div>

            <!-- GAME LEVEL 3: DETEKTIF KALOR -->
            <div id="game-lvl-3" class="game-lvl-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="bg-slate-900 text-white p-6 rounded-2xl space-y-4">
                    <div class="flex items-center space-x-3">
                        <span class="text-3xl">🕵️‍♂️</span>
                        <div>
                            <h4 class="font-bold text-lg text-amber-400">Kasus Detektif Kalor #1</h4>
                            <p class="text-xs text-slate-300">Pecahkan misteri sains sehari-hari!</p>
                        </div>
                    </div>
                    <p class="text-sm text-slate-200 leading-relaxed bg-slate-800 p-4 rounded-xl border border-slate-700">
                        "Ibu memasak sup menggunakan panci stainless steel dengan gagang dilapisi plastik hitam. Saat sup mendidih, panci sangat panas tetapi gagang plastik tetap aman dipegang. Mengapa?"
                    </p>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-3" id="g3-options">
                        <!-- Dynamic Detective Options -->
                    </div>
                </div>
            </div>

            <!-- GAME LEVEL 4: BALAPAN HITUNG KALOR -->
            <div id="game-lvl-4" class="game-lvl-content glass-card p-6 rounded-3xl space-y-6 hidden">
                <div class="bg-amber-50 p-6 rounded-2xl border border-amber-200 space-y-4 text-center">
                    <span class="text-xs font-bold text-amber-700 bg-amber-200 px-3 py-1 rounded-full">Hitung Cepat Tantangan</span>
                    <h4 class="text-xl font-bold text-slate-800">Berapa besar kalor yang dibutuhkan?</h4>
                    <p id="g4-math-question" class="text-sm font-mono text-slate-700 bg-white p-4 rounded-xl border border-amber-300">
                        Hitung Q jika m = 1 kg, c = 4200 J/kg°C, dan ΔT = 10°C!
                    </p>
                    <div class="flex max-w-sm mx-auto gap-2">
                        <input type="number" id="g4-answer-input" placeholder="Masukkan angka Joule..." class="flex-1 p-3 border rounded-xl font-bold text-center">
                        <button onclick="checkG4MathAnswer()" class="pid-btn bg-emerald-500 hover:bg-emerald-600 text-white font-bold px-5 rounded-xl">Jawab</button>
                    </div>
                    <div id="g4-feedback" class="hidden text-sm font-bold p-3 rounded-xl"></div>
                </div>
            </div>
        </section>

        <!-- PAGE 5: KUIS & EVALUASI -->
        <section id="page-eval" class="page-section hidden space-y-6">
            <div class="bg-gradient-to-r from-emerald-600 to-teal-600 text-white p-6 rounded-3xl shadow-lg flex justify-between items-center">
                <div>
                    <span class="bg-white/20 px-3 py-1 rounded-full text-xs font-bold text-emerald-100">Evaluasi Pemahaman</span>
                    <h3 class="text-2xl md:text-3xl font-bold mt-1">KUIS SAINS KALOR</h3>
                    <p class="text-xs md:text-sm text-emerald-100">Uji kemampuanmu melalui 10 soal kuis interaktif!</p>
                </div>
                <button onclick="resetQuiz()" class="pid-btn bg-white text-emerald-800 font-bold px-4 py-2 rounded-xl text-xs shadow">Ulangi Kuis</button>
            </div>

            <!-- Quiz Container Card -->
            <div id="quiz-container" class="glass-card p-6 md:p-8 rounded-3xl space-y-6">
                <!-- Progress Header -->
                <div class="flex justify-between items-center text-xs md:text-sm font-bold text-slate-500 border-b pb-3">
                    <span id="quiz-progress-text">Soal 1 dari 10</span>
                    <span id="quiz-score-live">Skor: 0</span>
                </div>

                <!-- Question Text -->
                <h4 id="quiz-question-text" class="text-lg md:text-xl font-bold text-slate-800 leading-snug">
                    <!-- Dynamic Question Text -->
                </h4>

                <!-- Multiple Choice Options -->
                <div id="quiz-options-list" class="space-y-3">
                    <!-- Dynamic Options -->
                </div>

                <!-- Quiz Explanation / Feedback modal box -->
                <div id="quiz-explanation" class="hidden p-4 rounded-2xl bg-sky-50 border border-sky-200 text-xs md:text-sm text-slate-700">
                    <!-- Dynamic Explanation -->
                </div>

                <!-- Next Question Button -->
                <div class="flex justify-end pt-2">
                    <button id="quiz-next-btn" onclick="nextQuizQuestion()" class="pid-btn bg-brand-blue hover:bg-blue-700 text-white font-bold px-6 py-3 rounded-xl hidden">
                        Lanjut Soal Berikutnya ➔
                    </button>
                </div>
            </div>

            <!-- Quiz Final Result Summary Modal/Card -->
            <div id="quiz-result-card" class="glass-card p-8 rounded-3xl space-y-6 hidden text-center">
                <div class="w-20 h-20 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center mx-auto text-3xl font-bold">
                    🏆
                </div>
                <h3 class="text-3xl font-bold text-slate-900">Hasil Evaluasi Pembelajaran</h3>
                <div class="text-5xl font-extrabold text-brand-blue" id="final-score-val">100</div>
                <p id="final-feedback-msg" class="text-slate-600 text-sm max-w-md mx-auto">Sangat Baik! Kamu telah menguasai konsep kalor dengan sempurna!</p>

                <div class="flex justify-center space-x-3 pt-4">
                    <button onclick="resetQuiz()" class="pid-btn bg-brand-orange text-white font-bold px-6 py-3 rounded-xl shadow">Ulangi Kuis</button>
                    <button onclick="switchPage('notes')" class="pid-btn bg-purple-600 text-white font-bold px-6 py-3 rounded-xl shadow">Tulis Catatan</button>
                </div>
            </div>
        </section>

        <!-- PAGE 6: CATATAN SAYA -->
        <section id="page-notes" class="page-section hidden space-y-6">
            <div class="bg-gradient-to-r from-purple-600 to-indigo-600 text-white p-6 rounded-3xl shadow-lg">
                <h3 class="text-2xl md:text-3xl font-bold">CATATAN PEMBELAJARAN SAYA</h3>
                <p class="text-xs md:text-sm text-purple-100">Tuliskan rangkuman, kesimpulan eksperimen, dan hal penting yang kamu pelajari hari ini.</p>
            </div>

            <div class="glass-card p-6 rounded-3xl space-y-4">
                <div>
                    <label class="block font-bold text-slate-700 text-sm mb-2">Nama Murid / Kelompok:</label>
                    <input type="text" id="student-name" placeholder="Ketik namamu di sini..." class="w-full p-3 border rounded-xl text-slate-800 font-medium">
                </div>
                <div>
                    <label class="block font-bold text-slate-700 text-sm mb-2">Catatan & Kesimpulan Sains:</label>
                    <textarea id="student-notes-text" rows="8" placeholder="Tuliskan rangkuman konsep kalor, hasil lab maya, dan pertanyaanmu..." class="w-full p-4 border rounded-2xl text-slate-800 font-sans"></textarea>
                </div>

                <div class="flex flex-wrap gap-3">
                    <button onclick="saveStudentNotes()" class="pid-btn bg-purple-600 hover:bg-purple-700 text-white font-bold px-6 py-3 rounded-xl shadow flex items-center space-x-2">
                        <i data-lucide="save" class="w-5 h-5"></i>
                        <span>Simpan Catatan</span>
                    </button>
                    <button onclick="exportNotesTxt()" class="pid-btn bg-slate-700 hover:bg-slate-800 text-white font-bold px-6 py-3 rounded-xl shadow flex items-center space-x-2">
                        <i data-lucide="download" class="w-5 h-5"></i>
                        <span>Unduh File Teks (.txt)</span>
                    </button>
                </div>
            </div>
        </section>

        <!-- PAGE 7: DASHBOARD GURU -->
        <section id="page-teacher" class="page-section hidden space-y-6">
            <div class="bg-gradient-to-r from-indigo-700 to-slate-800 text-white p-6 rounded-3xl shadow-lg flex justify-between items-center">
                <div>
                    <span class="bg-white/20 px-3 py-1 rounded-full text-xs font-bold text-indigo-200">Mode Demonstrasi Kelas</span>
                    <h3 class="text-2xl md:text-3xl font-bold mt-1">DASHBOARD GURU</h3>
                    <p class="text-xs md:text-sm text-indigo-200">Pantau progres pembelajaran murid dan ekspor rekapitulasi data nilai.</p>
                </div>
                <button onclick="exportClassCSV()" class="pid-btn bg-emerald-500 hover:bg-emerald-600 text-white font-bold px-4 py-2.5 rounded-xl text-xs shadow flex items-center space-x-2">
                    <i data-lucide="file-spreadsheet" class="w-4 h-4"></i>
                    <span>Ekspor Hasil ke CSV</span>
                </button>
            </div>

            <!-- Overview Stats Grid -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm text-center">
                    <span class="text-xs font-bold text-slate-500">Total Murid Aktif</span>
                    <div class="text-3xl font-extrabold text-indigo-600 mt-1">32</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm text-center">
                    <span class="text-xs font-bold text-slate-500">Rata-rata Skor Kuis</span>
                    <div class="text-3xl font-extrabold text-emerald-600 mt-1">88.5</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm text-center">
                    <span class="text-xs font-bold text-slate-500">Eksperimen Selesai</span>
                    <div class="text-3xl font-extrabold text-brand-blue mt-1">100%</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm text-center">
                    <span class="text-xs font-bold text-slate-500">Tingkat Penguasaan</span>
                    <div class="text-3xl font-extrabold text-brand-orange mt-1">Tinggi</div>
                </div>
            </div>

            <!-- Student Data Table -->
            <div class="glass-card p-6 rounded-3xl overflow-x-auto">
                <h4 class="font-bold text-slate-800 text-base mb-4">Rekapitulasi Nilai & Progres Murid</h4>
                <table class="w-full text-left border-collapse text-sm">
                    <thead>
                        <tr class="bg-slate-100 text-slate-700 font-bold border-b">
                            <th class="p-3">Nama Murid</th>
                            <th class="p-3">Status Lab Maya</th>
                            <th class="p-3">Skor Misi Game</th>
                            <th class="p-3">Nilai Kuis</th>
                            <th class="p-3">Catatan Pembelajaran</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200 text-slate-600" id="teacher-table-body">
                        <!-- Dynamic Demo Rows -->
                    </tbody>
                </table>
            </div>
        </section>

    </main>

    <!-- HELP / INSTRUCTION MODAL -->
    <div id="help-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white max-w-lg w-full rounded-3xl p-6 shadow-2xl space-y-4 border-2 border-sky-200">
            <div class="flex justify-between items-center border-b pb-3">
                <h3 class="font-bold text-xl text-slate-800 flex items-center space-x-2">
                    <i data-lucide="help-circle" class="w-6 h-6 text-brand-blue"></i>
                    <span>Petunjuk Penggunaan Aplikasi</span>
                </h3>
                <button onclick="closeHelpModal()" class="text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-6 h-6"></i>
                </button>
            </div>
            <div class="text-xs md:text-sm text-slate-600 space-y-2 leading-relaxed">
                <p>1. <strong>Navigasi:</strong> Gunakan menu tab di bagian atas untuk berpindah antar halaman (Materi, Lab Maya, Misi Game, Kuis, Catatan, dan Dashboard Guru).</p>
                <p>2. <strong>Papan Interaktif Digital (PID):</strong> Seluruh tombol dan pilihan dirancang besar agar nyaman disentuh langsung di PID atau layar HP.</p>
                <p>3. <strong>Lab Maya:</strong> Geser slider parameter massa dan tekan tombol pemanasan untuk mengamati grafik perubahan suhu secara langsung.</p>
                <p>4. <strong>Kuis & Catatan:</strong> Kerjakan evaluasi kuis lalu rangkum pemahamanmu di halaman Catatan Saya.</p>
            </div>
            <button onclick="closeHelpModal()" class="pid-btn w-full bg-brand-blue text-white font-bold py-3 rounded-xl shadow">
                Saya Mengerti!
            </button>
        </div>
    </div>

    <!-- AUDIO SOUND FX SYSTEM USING WEB AUDIO API -->
    <script>
        // Web Audio API Sound Generator
        let audioCtx = null;
        let soundEnabled = true;

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
        }

        function playSound(type) {
            if (!soundEnabled) return;
            try {
                initAudio();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                if (type === 'click') {
                    osc.frequency.setValueAtTime(400, audioCtx.currentTime);
                    gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.05);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.05);
                } else if (type === 'correct') {
                    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime); // C5
                    osc.frequency.setValueAtTime(659.25, audioCtx.currentTime + 0.1); // E5
                    gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.25);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.25);
                } else if (type === 'wrong') {
                    osc.frequency.setValueAtTime(200, audioCtx.currentTime);
                    gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.2);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.2);
                }
            } catch (e) {
                console.log("Audio not allowed or blocked");
            }
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            const btn = document.getElementById('sound-btn');
            btn.innerHTML = soundEnabled ? '<i data-lucide="volume-2" class="w-5 h-5"></i>' : '<i data-lucide="volume-x" class="w-5 h-5 text-red-300"></i>';
            lucide.createIcons();
        }
    </script>

    <!-- CORE APPLICATION LOGIC JS -->
    <script>
        // Page Navigation Logic
        function switchPage(pageId) {
            playSound('click');
            document.querySelectorAll('.page-section').forEach(sec => sec.classList.add('hidden'));
            document.getElementById(`page-${pageId}`).classList.remove('hidden');

            // Update Nav Styles
            document.querySelectorAll('.nav-tab').forEach(tab => {
                tab.classList.remove('bg-brand-blue', 'text-white');
                tab.classList.add('text-slate-600');
            });
            const activeNav = document.getElementById(`nav-${pageId}`);
            if (activeNav) {
                activeNav.classList.add('bg-brand-blue', 'text-white');
                activeNav.classList.remove('text-slate-600');
            }

            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        // Trigger Answer Toggles
        function toggleTriggerAnswer(id) {
            playSound('click');
            const ans = document.getElementById(`ans-q${id}`);
            const arrow = document.getElementById(`arrow-q${id}`);
            if (ans.classList.contains('hidden')) {
                ans.classList.remove('hidden');
                arrow.style.transform = 'rotate(180deg)';
            } else {
                ans.classList.add('hidden');
                arrow.style.transform = 'rotate(0deg)';
            }
        }

        // Sub-Materi Navigation
        function showSubMateri(index) {
            playSound('click');
            document.querySelectorAll('.submateri-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(`submateri-${index}`).classList.remove('hidden');

            document.querySelectorAll('.submateri-btn').forEach(btn => {
                btn.classList.remove('bg-brand-blue', 'text-white');
                btn.classList.add('bg-white', 'text-slate-700');
            });
            const activeBtn = document.getElementById(`submateri-btn-${index}`);
            activeBtn.classList.add('bg-brand-blue', 'text-white');
            activeBtn.classList.remove('bg-white', 'text-slate-700');
        }

        // Heat Calculator Logic
        function calculateHeatInApp() {
            playSound('click');
            const m = parseFloat(document.getElementById('calc-m').value) || 0;
            const c = parseFloat(document.getElementById('calc-c').value) || 0;
            const t1 = parseFloat(document.getElementById('calc-t1').value) || 0;
            const t2 = parseFloat(document.getElementById('calc-t2').value) || 0;

            const deltaT = t2 - t1;
            const Q = m * c * deltaT;

            const resDiv = document.getElementById('calc-result');
            resDiv.classList.remove('hidden');
            resDiv.innerHTML = `
                <div class="font-bold text-slate-800">Langkah Penyelesaian:</div>
                <div>ΔT = ${t2}°C - ${t1}°C = ${deltaT}°C</div>
                <div>Q = ${m} kg × ${c} J/kg°C × ${deltaT}°C</div>
                <div class="text-base font-extrabold text-brand-orange mt-1">Hasil Q = ${Q.toLocaleString('id-ID')} Joule (${(Q/1000).toLocaleString('id-ID')} kJ)</div>
            `;
        }

        // ================= VIRTUAL LAB LOGIC =================
        let lab1Chart = null;
        let lab1Interval = null;
        let lab1Time = 0;
        let lab1Temp = 25.0;

        function initLab1Chart() {
            const ctx = document.getElementById('lab1Chart').getContext('2d');
            lab1Chart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: [0],
                    datasets: [{
                        label: 'Suhu Air (°C)',
                        data: [25.0],
                        borderColor: '#0066FF',
                        backgroundColor: 'rgba(0, 102, 255, 0.1)',
                        fill: true,
                        tension: 0.3
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { min: 20, max: 100 }
                    }
                }
            });
        }

        function updateLab1Mass(val) {
            document.getElementById('mass-val-display').innerText = `${parseFloat(val).toFixed(1)} kg`;
            const waterHeight = Math.min(80, val * 25);
            document.getElementById('water-fill').style.height = `${waterHeight}%`;
        }

        function startLab1Heating() {
            playSound('click');
            if (lab1Interval) clearInterval(lab1Interval);

            const mass = parseFloat(document.getElementById('lab1-mass').value);
            document.getElementById('lab-flame').classList.remove('invisible');

            lab1Interval = setInterval(() => {
                lab1Time += 1;
                // Temperature rise inversely proportional to mass
                const tempInc = (2.0 / mass);
                lab1Temp = Math.min(100.0, lab1Temp + tempInc);

                document.getElementById('lab1-timer').innerText = `Waktu: ${lab1Time} detik`;
                document.getElementById('lab1-temp-display').innerText = `${lab1Temp.toFixed(1)} °C`;
                document.getElementById('thermo-mercury').style.height = `${Math.min(100, lab1Temp)}%`;

                // Update Chart
                lab1Chart.data.labels.push(lab1Time);
                lab1Chart.data.datasets[0].data.push(lab1Temp);
                lab1Chart.update();

                if (lab1Temp >= 100.0) {
                    clearInterval(lab1Interval);
                    document.getElementById('lab-flame').classList.add('invisible');
                    document.getElementById('lab1-analysis').innerHTML = `
                        🔥 <strong>Air Mendidih!</strong> Pada massa ${mass} kg, dibutuhkan waktu ${lab1Time} detik untuk mencapai 100°C.
                        <br><em>Kesimpulan: Semakin besar massa air, semakin lambat kenaikan suhunya!</em>
                    `;
                }
            }, 500);
        }

        function resetLab1() {
            playSound('click');
            if (lab1Interval) clearInterval(lab1Interval);
            lab1Time = 0;
            lab1Temp = 25.0;
            document.getElementById('lab-flame').classList.add('invisible');
            document.getElementById('lab1-timer').innerText = `Waktu: 0 detik`;
            document.getElementById('lab1-temp-display').innerText = `25.0 °C`;
            document.getElementById('thermo-mercury').style.height = `25%`;
            if (lab1Chart) {
                lab1Chart.data.labels = [0];
                lab1Chart.data.datasets[0].data = [25.0];
                lab1Chart.update();
            }
        }

        function switchLabExp(num) {
            playSound('click');
            document.querySelectorAll('.lab-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(`lab-exp-${num}`).classList.remove('hidden');

            document.querySelectorAll('.lab-tab-btn').forEach(btn => {
                btn.classList.remove('bg-white', 'text-brand-blue');
                btn.classList.add('bg-white/20', 'text-white');
            });
            const activeBtn = document.getElementById(`lab-btn-${num}`);
            activeBtn.classList.add('bg-white', 'text-brand-blue');
            activeBtn.classList.remove('bg-white/20', 'text-white');
        }

        // Exp 2 Es Batu Logic
        let exp2Temp = -5.0;
        function addHeatToExp2() {
            playSound('click');
            const visual = document.getElementById('exp2-ice-visual');
            const badge = document.getElementById('exp2-state-badge');

            if (exp2Temp < 0) {
                exp2Temp += 2.5;
            } else if (exp2Temp === 0) {
                // Melting phase plateau
                visual.innerHTML = `
                    <i data-lucide="droplets" class="w-20 h-20 text-sky-400 mx-auto animate-bounce"></i>
                    <span class="block text-xs font-bold text-sky-200 mt-2">Es Sedang Mencair (0°C)</span>
                `;
                badge.innerText = "Wujud: Proses Mencair";
                badge.className = "text-xs font-bold bg-amber-500 text-white px-3 py-1 rounded-full";
                exp2Temp = 0.1; // move past plateau on next click
            } else {
                exp2Temp += 5.0;
                visual.innerHTML = `
                    <i data-lucide="waves" class="w-20 h-20 text-blue-400 mx-auto"></i>
                    <span class="block text-xs font-bold text-blue-200 mt-2">Air Cair (${exp2Temp.toFixed(1)}°C)</span>
                `;
                badge.innerText = "Wujud: Air Cair";
                badge.className = "text-xs font-bold bg-blue-600 text-white px-3 py-1 rounded-full";
            }

            document.getElementById('exp2-temp').innerText = `${exp2Temp.toFixed(1)} °C`;
            lucide.createIcons();
        }

        function resetExp2() {
            playSound('click');
            exp2Temp = -5.0;
            document.getElementById('exp2-temp').innerText = `-5.0 °C`;
            document.getElementById('exp2-ice-visual').innerHTML = `
                <i data-lucide="snowflake" class="w-20 h-20 text-sky-300 mx-auto animate-pulse"></i>
                <span class="block text-xs font-bold text-sky-200 mt-2">Es (-5°C)</span>
            `;
            const badge = document.getElementById('exp2-state-badge');
            badge.innerText = "Wujud: Es Padat";
            badge.className = "text-xs font-bold bg-sky-500 text-white px-3 py-1 rounded-full";
            lucide.createIcons();
        }

        // Exp 3 Modes
        function selectExp3Type(type) {
            playSound('click');
            const title = document.getElementById('exp3-title');
            const canvas = document.getElementById('exp3-canvas-area');
            const desc = document.getElementById('exp3-desc');

            if (type === 'konduksi') {
                title.innerText = "Simulasi Konduksi Pada Logam";
                canvas.innerHTML = `
                    <div class="flex items-center space-x-2">
                        <div class="text-red-500 animate-flame">🔥 Api</div>
                        <div class="w-48 h-4 bg-gradient-to-r from-red-500 via-amber-400 to-sky-400 rounded-full"></div>
                        <div class="text-sky-300">🧊 Sendok Logam</div>
                    </div>
                `;
                desc.innerText = "Kalor berpindah melalui perantara zat padat tanpa perpindahan partikel logam.";
            } else if (type === 'konveksi') {
                title.innerText = "Simulasi Konveksi Pada Air";
                canvas.innerHTML = `
                    <div class="flex flex-col items-center justify-center space-y-2">
                        <div class="text-amber-400 font-bold animate-bounce">⬆️ Air Panas Naik</div>
                        <div class="text-sky-400 font-bold animate-pulse">⬇️ Air Cold Turun</div>
                    </div>
                `;
                desc.innerText = "Kalor berpindah disertai gerakan partikel air yang memutar saat dipanaskan.";
            } else if (type === 'radiasi') {
                title.innerText = "Simulasi Radiasi Panas";
                canvas.innerHTML = `
                    <div class="flex items-center space-x-6">
                        <div class="text-amber-400 text-3xl animate-spin">☀️ Matahari</div>
                        <div class="text-amber-300 animate-pulse">〰️ 〰️ 〰️ Sinar Radiasi 〰️ 〰️ 〰️</div>
                        <div class="text-emerald-400 text-2xl">🌍 Bumi</div>
                    </div>
                `;
                desc.innerText = "Kalor memancar langsung melalui gelombang tanpa memerlukan zat perantara.";
            }
        }

        // ================= GAMES LOGIC =================
        let gameScore = 0;

        function addGameScore(pts) {
            gameScore += pts;
            document.getElementById('game-score-display').innerText = gameScore;
        }

        function switchGameLevel(lvl) {
            playSound('click');
            document.querySelectorAll('.game-lvl-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(`game-lvl-${lvl}`).classList.remove('hidden');

            document.querySelectorAll('.gtab-btn').forEach(btn => {
                btn.classList.remove('border-brand-orange', 'text-brand-orange');
                btn.classList.add('border-slate-200', 'text-slate-700');
            });
            const activeTab = document.getElementById(`gtab-${lvl}`);
            activeTab.classList.add('border-brand-orange', 'text-brand-orange');
            activeTab.classList.remove('border-slate-200', 'text-slate-700');
        }

        // Level 1 Tebak Konsep
        const g1Data = [
            {
                q: "1. Ujung sendok ikut panas saat dimasukkan ke air teh panas merupakan contoh...",
                opts: ["Konduksi", "Konveksi", "Radiasi", "Pemuaian"],
                ans: 0,
                exp: "Benar! Konduksi adalah perpindahan panas melalui benda padat seperti logam sendok."
            },
            {
                q: "2. Gerakan memutar air saat mendidih di dalam panci merupakan contoh...",
                opts: ["Konduksi", "Konveksi", "Radiasi", "Penguapan"],
                ans: 1,
                exp: "Benar! Konveksi terjadi pada zat cair/gas disertai aliran massa zat."
            }
        ];
        let g1Idx = 0;

        function renderG1() {
            const item = g1Data[g1Idx];
            document.getElementById('g1-question').innerText = item.q;
            const optsContainer = document.getElementById('g1-options');
            optsContainer.innerHTML = '';

            item.opts.forEach((opt, idx) => {
                const btn = document.createElement('button');
                btn.className = "pid-btn bg-white hover:bg-sky-50 p-4 rounded-2xl border-2 border-slate-200 font-bold text-slate-800 text-left shadow-sm";
                btn.innerText = opt;
                btn.onclick = () => checkG1(idx);
                optsContainer.appendChild(btn);
            });
            document.getElementById('g1-feedback').classList.add('hidden');
        }

        function checkG1(selected) {
            const item = g1Data[g1Idx];
            const fb = document.getElementById('g1-feedback');
            fb.classList.remove('hidden');

            if (selected === item.ans) {
                playSound('correct');
                addGameScore(20);
                fb.className = "text-center p-4 rounded-2xl font-bold text-sm bg-emerald-100 text-emerald-800 border border-emerald-300";
                fb.innerText = `✅ Jawaban Tepat! ${item.exp}`;
            } else {
                playSound('wrong');
                fb.className = "text-center p-4 rounded-2xl font-bold text-sm bg-red-100 text-red-800 border border-red-300";
                fb.innerText = `❌ Kurang tepat. Jawaban yang benar adalah: ${item.opts[item.ans]}`;
            }
        }

        // Level 2 Matching
        const g2Pairs = [
            { phenomenon: "Sendok Logam Panas", concept: "Konduksi" },
            { phenomenon: "Air Memutar Dipanaskan", concept: "Konveksi" },
            { phenomenon: "Panas Matahari Ke Bumi", concept: "Radiasi" },
            { phenomenon: "Es Batu Mencair", concept: "Menyerap Kalor" }
        ];

        function renderG2() {
            const itemsCol = document.getElementById('g2-items-col');
            const targetsCol = document.getElementById('g2-targets-col');
            itemsCol.innerHTML = '';
            targetsCol.innerHTML = '';

            g2Pairs.forEach((p, idx) => {
                const itemDiv = document.createElement('div');
                itemDiv.className = "bg-white p-3 rounded-xl border border-slate-200 font-bold text-xs shadow-sm flex justify-between items-center";
                itemDiv.innerHTML = `<span>${p.phenomenon}</span> <span class="text-brand-orange">➔</span>`;
                itemsCol.appendChild(itemDiv);

                const targetDiv = document.createElement('div');
                targetDiv.className = "bg-sky-50 p-3 rounded-xl border border-sky-200 font-bold text-xs text-brand-blue cursor-pointer hover:bg-sky-100";
                targetDiv.innerText = `Pasangan: ${p.concept}`;
                targetDiv.onclick = () => {
                    playSound('correct');
                    addGameScore(15);
                    targetDiv.className = "bg-emerald-100 p-3 rounded-xl border border-emerald-300 font-bold text-xs text-emerald-800";
                    targetDiv.innerText = `✅ Cocok: ${p.concept}`;
                };
                targetsCol.appendChild(targetDiv);
            });
        }

        // Level 3 Detektif
        function renderG3() {
            const container = document.getElementById('g3-options');
            container.innerHTML = `
                <button onclick="checkG3(true)" class="pid-btn bg-slate-800 hover:bg-slate-700 p-3 rounded-xl border border-slate-600 text-left text-xs text-white">
                    A. Plastik isolator panas yang buruk menghantarkan kalor sehingga tangan aman.
                </button>
                <button onclick="checkG3(false)" class="pid-btn bg-slate-800 hover:bg-slate-700 p-3 rounded-xl border border-slate-600 text-left text-xs text-white">
                    B. Plastik menyerap seluruh kalor sup sehingga panci menjadi dingin.
                </button>
            `;
        }

        function checkG3(isCorrect) {
            if (isCorrect) {
                playSound('correct');
                addGameScore(25);
                alert("🕵️‍♂️ Detektif Hebat! Plastik adalah ISOLATOR panas yang baik, sehingga tidak menghantarkan kalor ke tangan.");
            } else {
                playSound('wrong');
                alert("❌ Coba analisis lagi sifat bahan plastik terhadap kalor!");
            }
        }

        // Level 4 Math Challenge
        function checkG4MathAnswer() {
            const val = parseFloat(document.getElementById('g4-answer-input').value);
            const fb = document.getElementById('g4-feedback');
            fb.classList.remove('hidden');

            // Q = 1 * 4200 * 10 = 42000
            if (val === 42000) {
                playSound('correct');
                addGameScore(30);
                fb.className = "text-sm font-bold p-3 rounded-xl bg-emerald-100 text-emerald-800";
                fb.innerText = "🎉 Luar biasa! Perhitungan tepat: 1 × 4.200 × 10 = 42.000 Joule.";
            } else {
                playSound('wrong');
                fb.className = "text-sm font-bold p-3 rounded-xl bg-red-100 text-red-800";
                fb.innerText = "❌ Masih salah. Gunakan Q = m × c × ΔT = 1 × 4200 × 10.";
            }
        }

        // ================= QUIZ & EVALUATION LOGIC =================
        const quizQuestions = [
            {
                q: "1. Kalor didefinisikan sebagai...",
                opts: ["Derajat panas dinginnya benda", "Bentuk energi yang berpindah karena beda suhu", "Alat untuk mengukur suhu", "Jumlah partikel dalam zat"],
                ans: 1,
                exp: "Kalor adalah energi panas yang berpindah dari suhu tinggi ke suhu rendah."
            },
            {
                q: "2. Satuan kalor dalam Sistem Internasional (SI) adalah...",
                opts: ["Degree Celsius (°C)", "Kelvin (K)", "Joule (J)", "Watt (W)"],
                ans: 2,
                exp: "Satuan SI untuk energi kalor adalah Joule (J)."
            },
            {
                q: "3. Air bermassa 1 kg dipanaskan dari 20°C menjadi 30°C. Jika c air = 4.200 J/kg°C, besar kalornya adalah...",
                opts: ["42.000 J", "4.200 J", "84.000 J", "420 J"],
                ans: 0,
                exp: "Q = m × c × ΔT = 1 × 4200 × (30-20) = 42.000 J."
            },
            {
                q: "4. Perpindahan kalor pada zat padat tanpa diikuti perpindahan partikelnya disebut...",
                opts: ["Konveksi", "Konduksi", "Radiasi", "Evaporasi"],
                ans: 1,
                exp: "Konduksi terjadi pada perantara padat tanpa perpindahan partikel."
            },
            {
                q: "5. Asap cerobong pabrik yang membumbung tinggi merupakan peristiwa...",
                opts: ["Konduksi", "Radiasi", "Konveksi", "Sublimasi"],
                ans: 2,
                exp: "Gerakan gas/udara panas naik merupakan arus konveksi."
            }
        ];

        let currentQuizIdx = 0;
        let quizScore = 0;

        function renderQuizQuestion() {
            const item = quizQuestions[currentQuizIdx];
            document.getElementById('quiz-progress-text').innerText = `Soal ${currentQuizIdx + 1} dari ${quizQuestions.length}`;
            document.getElementById('quiz-score-live').innerText = `Skor: ${quizScore}`;
            document.getElementById('quiz-question-text').innerText = item.q;

            const optsList = document.getElementById('quiz-options-list');
            optsList.innerHTML = '';
            document.getElementById('quiz-explanation').classList.add('hidden');
            document.getElementById('quiz-next-btn').classList.add('hidden');

            item.opts.forEach((opt, idx) => {
                const btn = document.createElement('button');
                btn.className = "pid-btn w-full bg-white hover:bg-sky-50 p-4 rounded-2xl border-2 border-slate-200 text-left font-bold text-slate-800 text-sm shadow-sm transition flex items-center space-x-3";
                btn.innerHTML = `<span class="w-7 h-7 rounded-full bg-slate-100 flex items-center justify-center text-xs text-slate-600">${String.fromCharCode(65 + idx)}</span><span>${opt}</span>`;
                btn.onclick = () => selectQuizOption(idx, btn);
                optsList.appendChild(btn);
            });
        }

        function selectQuizOption(selected, element) {
            const item = quizQuestions[currentQuizIdx];
            const expDiv = document.getElementById('quiz-explanation');
            expDiv.classList.remove('hidden');

            // Disable all option buttons
            document.querySelectorAll('#quiz-options-list button').forEach(b => b.onclick = null);

            if (selected === item.ans) {
                playSound('correct');
                quizScore += 20;
                element.classList.add('border-emerald-500', 'bg-emerald-50', 'text-emerald-900');
                expDiv.className = "p-4 rounded-2xl bg-emerald-50 border border-emerald-200 text-xs md:text-sm text-emerald-900";
                expDiv.innerHTML = `✅ <strong>Benar!</strong> ${item.exp}`;
            } else {
                playSound('wrong');
                element.classList.add('border-red-500', 'bg-red-50', 'text-red-900');
                expDiv.className = "p-4 rounded-2xl bg-red-50 border border-red-200 text-xs md:text-sm text-red-900";
                expDiv.innerHTML = `❌ <strong>Salah!</strong> Jawaban yang benar adalah B. ${item.opts[item.ans]}. <br>${item.exp}`;
            }

            document.getElementById('quiz-score-live').innerText = `Skor: ${quizScore}`;
            document.getElementById('quiz-next-btn').classList.remove('hidden');
        }

        function nextQuizQuestion() {
            playSound('click');
            currentQuizIdx++;
            if (currentQuizIdx < quizQuestions.length) {
                renderQuizQuestion();
            } else {
                // Show final score card
                document.getElementById('quiz-container').classList.add('hidden');
                document.getElementById('quiz-result-card').classList.remove('hidden');
                document.getElementById('final-score-val').innerText = quizScore;
            }
        }

        function resetQuiz() {
            playSound('click');
            currentQuizIdx = 0;
            quizScore = 0;
            document.getElementById('quiz-container').classList.remove('hidden');
            document.getElementById('quiz-result-card').classList.add('hidden');
            renderQuizQuestion();
        }

        // ================= STUDENT NOTES & TEACHER DASHBOARD =================
        function saveStudentNotes() {
            playSound('correct');
            const name = document.getElementById('student-name').value || 'Murid';
            const text = document.getElementById('student-notes-text').value;
            localStorage.setItem('saved_student_notes', JSON.stringify({ name, text }));
            alert('✅ Catatan berhasil disimpan!');
        }

        function exportNotesTxt() {
            playSound('click');
            const name = document.getElementById('student-name').value || 'Murid';
            const text = document.getElementById('student-notes-text').value;
            const content = `CATATAN PEMBELAJARAN IPA - KALOR\nNama: ${name}\n\n${text}`;

            const blob = new Blob([content], { type: 'text/plain;charset=utf-8' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = `Catatan_Kalor_${name.replace(/\s+/g, '_')}.txt`;
            link.click();
        }

        function renderTeacherTable() {
            const tbody = document.getElementById('teacher-table-body');
            const demoData = [
                { name: "Ahmad Rizky", lab: "Selesai", game: "120 Pts", quiz: "90", notes: "Sudah Mengirim" },
                { name: "Siti Nurhaliza", lab: "Selesai", game: "150 Pts", quiz: "100", notes: "Sudah Mengirim" },
                { name: "Budi Santoso", lab: "Selesai", game: "90 Pts", quiz: "80", notes: "Belum Lengkap" },
                { name: "Dewi Lestari", lab: "Selesai", game: "110 Pts", quiz: "85", notes: "Sudah Mengirim" }
            ];

            tbody.innerHTML = '';
            demoData.forEach(d => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="p-3 font-bold text-slate-800">${d.name}</td>
                    <td class="p-3"><span class="bg-emerald-100 text-emerald-800 text-xs px-2.5 py-1 rounded-full font-bold">${d.lab}</span></td>
                    <td class="p-3 font-mono">${d.game}</td>
                    <td class="p-3 font-bold text-brand-blue">${d.quiz}</td>
                    <td class="p-3 text-xs">${d.notes}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function exportClassCSV() {
            playSound('click');
            const csvContent = "data:text/csv;charset=utf-8,Nama,Status Lab,Skor Game,Nilai Kuis\nAhmad Rizky,Selesai,120,90\nSiti Nurhaliza,Selesai,150,100\nBudi Santoso,Selesai,90,80\nDewi Lestari,Selesai,110,85";
            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", "Rekap_Nilai_Kalor_Kelas_VII.csv");
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        // Help Modal Controls
        function openHelpModal() {
            playSound('click');
            document.getElementById('help-modal').classList.remove('hidden');
        }
        function closeHelpModal() {
            playSound('click');
            document.getElementById('help-modal').classList.add('hidden');
        }

        // App Initialization
        window.onload = function() {
            lucide.createIcons();
            initLab1Chart();
            renderG1();
            renderG2();
            renderG3();
            renderQuizQuestion();
            renderTeacherTable();

            // Load saved notes if available
            const saved = localStorage.getItem('saved_student_notes');
            if (saved) {
                const parsed = JSON.parse(saved);
                document.getElementById('student-name').value = parsed.name || '';
                document.getElementById('student-notes-text').value = parsed.text || '';
            }
        };
    </script>
</body>
</html>
