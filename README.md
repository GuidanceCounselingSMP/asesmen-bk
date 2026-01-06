<!doctype html>
<html lang="id" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Asesmen BK Interaktif - Guru Mode</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Three.js -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  
  <style>
        body { box-sizing: border-box; }
        html, body {
            margin: 0; padding: 0; width: 100%; height: 100%;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden;
        }
        
        .app-wrapper {
            width: 100%; height: 100%;
            overflow-y: auto; overflow-x: hidden;
            position: relative; z-index: 10;
        }
        
        /* Glassmorphism */
        .glass {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 20px;
            box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
            transition: transform 0.3s ease;
        }
        
        .nav-btn {
            padding: 12px 24px; border-radius: 99px;
            transition: all 0.3s ease; font-weight: 600;
            border: 1px solid rgba(255,255,255,0.1); cursor: pointer;
            opacity: 0.7; /* Default opacity for locked state feel */
        }
        .nav-btn:hover { background: rgba(255, 255, 255, 0.1); transform: translateY(-2px); opacity: 1; }
        .nav-btn.active { background: white; color: #4f46e5; box-shadow: 0 0 20px rgba(255,255,255,0.3); opacity: 1; }
        
        /* Traffic Light */
        .traffic-light {
            width: 80px; height: 80px; border-radius: 50%;
            cursor: pointer; transition: all 0.3s ease;
            border: 4px solid rgba(255,255,255,0.3);
            opacity: 0.6;
        }
        .traffic-light:hover { transform: scale(1.1); opacity: 0.8; }
        .traffic-light.selected {
            transform: scale(1.2); opacity: 1;
            box-shadow: 0 0 40px currentColor; border-color: white;
        }
        
        /* Animations */
        @keyframes float {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-10px); }
        }
        .float-animation { animation: float 4s ease-in-out infinite; }
        
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes scaleIn { from { opacity: 0; transform: scale(0.9); } to { opacity: 1; transform: scale(1); } }

        /* Teapot Animation */
        @keyframes shake {
            0% { transform: rotate(0deg); }
            25% { transform: rotate(10deg); }
            50% { transform: rotate(0deg); }
            75% { transform: rotate(-10deg); }
            100% { transform: rotate(0deg); }
        }
        .shake-animation { animation: shake 0.5s ease-in-out infinite; }
        
        @keyframes genieAppear {
            0% { transform: scale(0); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }
        .genie-effect { animation: genieAppear 0.8s ease-out forwards; }

        /* Form Elements */
        .input-field {
            width: 100%; padding: 16px; border-radius: 16px;
            background: rgba(255, 255, 255, 0.15);
            border: 2px solid rgba(255, 255, 255, 0.2);
            color: white; outline: none; transition: all 0.3s ease; font-size: 16px;
        }
        .input-field::placeholder { color: rgba(255, 255, 255, 0.6); }
        .input-field:focus { border-color: white; background: rgba(255, 255, 255, 0.25); }
        
        .primary-btn {
            background: white; color: #4f46e5; font-weight: bold;
            padding: 14px 32px; border-radius: 16px; width: 100%;
            cursor: pointer; transition: all 0.3s ease; border: none; font-size: 16px;
        }
        .primary-btn:hover { transform: translateY(-2px); box-shadow: 0 10px 30px rgba(0,0,0,0.2); }
        .primary-btn:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }
        
        .export-btn {
            color: white; padding: 8px 16px; border-radius: 10px;
            font-size: 14px; font-weight: bold; cursor: pointer;
            transition: all 0.3s ease; border: none;
        }
        .export-btn:hover { transform: translateY(-2px); filter: brightness(1.1); }
        
        /* Mountain Interaction */
        .mountain-container { position: relative; width: 100%; height: 400px; margin-bottom: 20px; overflow: hidden; border-radius: 16px; }
        .climber-position {
            position: absolute; width: 40px; height: 40px;
            background: rgba(255,255,255,0.2); border-radius: 50%;
            cursor: pointer; transition: all 0.3s ease;
            display: flex; align-items: center; justify-content: center;
            font-size: 20px; border: 2px solid white; z-index: 5;
        }
        .climber-position:hover, .climber-position.selected {
            transform: scale(1.3); background: #ef4444; box-shadow: 0 0 20px rgba(239, 68, 68, 0.6);
        }
        .mountain-level {
            position: absolute; right: 10px; font-weight: bold;
            color: white; font-size: 14px; text-shadow: 1px 1px 3px black;
            pointer-events: none;
        }

        /* Hope Jars */
        .hope-box {
            background: rgba(255, 255, 255, 0.1);
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 12px; padding: 15px; margin-bottom: 12px;
            transition: all 0.3s ease;
        }
        .hope-box:hover { background: rgba(255, 255, 255, 0.2); }
        
        /* Modals */
        .summary-modal {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.85); display: flex;
            align-items: center; justify-content: center;
            z-index: 2000; animation: fadeIn 0.3s ease;
        }
        .summary-content {
            background: white; padding: 40px; border-radius: 20px;
            max-width: 900px; width: 90%; max-height: 90vh;
            overflow-y: auto; box-shadow: 0 20px 60px rgba(0,0,0,0.5);
            animation: scaleIn 0.3s ease; color: #333;
        }
        
        /* Table */
        .data-table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        .data-table th, .data-table td { border: 1px solid #ddd; padding: 12px; text-align: left; font-size: 14px; }
        .data-table th { background-color: #f3f4f6; font-weight: bold; color: #333; }
        .data-table tr:nth-child(even) { background-color: #f9fafb; }
        .data-table tr:hover { background-color: #f0fdf4; }

        .close-summary-btn {
            background: #4f46e5; color: white; padding: 12px 30px;
            border-radius: 25px; border: none; font-weight: bold;
            cursor: pointer; margin-top: 20px; width: 100%;
        }
        .close-summary-btn:hover { background: #4338ca; }

        /* Class Buttons */
        .class-btn:hover { transform: translateY(-5px); background: rgba(255,255,255,0.2); }

        /* Sound Controls Container */
        .sound-controls {
            position: fixed; bottom: 20px; right: 20px;
            display: flex; gap: 10px; z-index: 100;
        }

        /* Music Button */
        .music-btn {
            width: 50px; height: 50px; border-radius: 50%;
            background: rgba(255,255,255,0.2); backdrop-filter: blur(10px);
            border: 2px solid rgba(255,255,255,0.5);
            color: white; font-size: 24px; cursor: pointer;
            display: flex; align-items: center; justify-content: center;
            transition: all 0.3s ease;
        }
        .music-btn:hover { transform: scale(1.1); background: rgba(255,255,255,0.4); }
        .music-btn.playing { animation: spin 4s linear infinite; border-color: #10b981; }

        /* Pulse animation for inactive state hint */
        .music-btn:not(:hover):not(.playing) { animation: pulse 2s infinite; }
        
        /* Mood Buttons */
        .mood-btn {
            font-size: 3rem; padding: 10px; border-radius: 15px; border: 2px solid transparent;
            transition: all 0.2s; background: #f3f4f6; cursor: pointer;
        }
        .mood-btn:hover { transform: scale(1.1); background: #e5e7eb; }
        .mood-btn.selected { background: #dbeafe; border-color: #3b82f6; transform: scale(1.15); }

        @keyframes spin { 100% { transform: rotate(360deg); } }
        @keyframes pulse { 0%, 100% { box-shadow: 0 0 0 0 rgba(255,255,255,0.4); } 70% { box-shadow: 0 0 0 10px rgba(255,255,255,0); } }

    </style>
 </head>
 <body class="h-full bg-slate-900 text-white">
  
  <!-- Audio Element -->
  <audio id="bg-music" loop>
      <source src="https://cdn.pixabay.com/audio/2022/05/27/audio_1808fbf07a.mp3" type="audio/mp3">
      Browser Anda tidak mendukung elemen audio.
  </audio>

  <!-- Sound Controls -->
  <div class="sound-controls">
      <button id="music-toggle" class="music-btn" onclick="toggleMusic()" title="Putar/Jeda Musik">🎵</button>
      <button id="mute-btn" class="music-btn" onclick="stopMusic()" title="Matikan Suara" style="background: rgba(239, 68, 68, 0.4); border-color: #fca5a5;">🔇</button>
  </div>
  
  <!-- Canvas Background -->
  <canvas id="bg-canvas" style="position: fixed; top: 0; left: 0; width: 100%; height: 100%; z-index: 0; background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%);"></canvas>
  
  <div class="app-wrapper" id="app">
    
    <!-- 1. Welcome Screen -->
    <div id="welcome-screen" class="p-6 min-h-screen flex items-center justify-center relative z-10">
        <div class="max-w-3xl w-full text-center">
            <div class="glass p-12 float-animation">
                <h1 class="text-5xl font-bold mb-4 text-transparent bg-clip-text bg-gradient-to-r from-cyan-300 to-purple-400" style="text-shadow: 0 0 20px rgba(167, 139, 250, 0.5);">Asesmen BK Interaktif</h1>
                <p class="text-3xl mb-2 font-light">Kelas VIII</p>
                <p class="text-2xl mb-8">Bersama <span class="text-yellow-300 font-bold">Bu Ammy</span> 👩‍🏫</p>
                <div class="text-6xl mb-6">🏫</div>
                <p class="text-lg text-white/90 mb-8 leading-relaxed">Selamat datang! Mari kita mulai asesmen untuk mengenal lebih dekat kesiapan, tantangan, dan harapanmu dalam belajar.</p>
                <button onclick="startApp()" class="primary-btn text-xl py-4 shadow-lg bg-gradient-to-r from-blue-500 to-indigo-600 border-none text-white hover:from-blue-600 hover:to-indigo-700">Mulai Asesmen 🚀</button>
            </div>
            <div class="glass p-6 mt-6">
                <h3 class="text-xl font-bold mb-2">🎯 Visi Misi SMP YPS Singkole</h3>
                <p class="text-yellow-300 font-bold text-lg">TANGGUH, MANDIRI, DAN BERETIKA</p>
            </div>
        </div>
    </div>

    <!-- 2. Class Selection Screen -->
    <div id="class-selection-screen" class="p-6 hidden min-h-screen flex items-center justify-center relative z-10">
        <div class="max-w-5xl w-full">
            <div class="glass p-8">
                <button onclick="backToWelcome()" class="nav-btn text-white mb-6">← Kembali</button>
                <h2 class="text-4xl font-bold text-center mb-2">Pilih Kelasmu</h2>
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6 mt-8">
                    <button onclick="selectClass('8 Discipline')" class="class-btn glass p-6 rounded-xl border border-white/20"><div class="text-4xl mb-2">📚</div><div class="text-xl font-bold">8 Discipline</div></button>
                    <button onclick="selectClass('8 Respect')" class="class-btn glass p-6 rounded-xl border border-white/20"><div class="text-4xl mb-2">📖</div><div class="text-xl font-bold">8 Respect</div></button>
                    <button onclick="selectClass('8 Resilient')" class="class-btn glass p-6 rounded-xl border border-white/20"><div class="text-4xl mb-2">📝</div><div class="text-xl font-bold">8 Resilient</div></button>
                    <button onclick="selectClass('8 Creative')" class="class-btn glass p-6 rounded-xl border border-white/20"><div class="text-4xl mb-2">📐</div><div class="text-xl font-bold">8 Creative</div></button>
                    <button onclick="selectClass('8 Collaborative')" class="class-btn glass p-6 rounded-xl border border-white/20"><div class="text-4xl mb-2">🎓</div><div class="text-xl font-bold">8 Collaborative</div></button>
                    <button onclick="selectClass('8 Independent')" class="class-btn glass p-6 rounded-xl border border-white/20"><div class="text-4xl mb-2">🏆</div><div class="text-xl font-bold">8 Independent</div></button>
                </div>
            </div>
        </div>
    </div>

    <!-- 3. Main Application Screen -->
    <div id="main-screen" class="hidden relative z-10 min-h-screen p-4">
        
        <!-- Navigation Tabs -->
        <div id="nav-container" class="hidden flex justify-center gap-2 mb-6 flex-wrap">
            <button onclick="switchSession(1)" id="nav-1" class="nav-btn active">🚦 Kesiapan</button> 
            <button onclick="switchSession(2)" id="nav-2" class="nav-btn">⛰️ Tantangan</button> 
            <button onclick="switchSession(3)" id="nav-3" class="nav-btn">🏺 Harapan</button>
        </div>

        <!-- Info Bar -->
        <div class="glass p-4 mb-6 max-w-4xl mx-auto flex flex-col sm:flex-row justify-between items-center gap-4">
            <div>
                <h1 id="session-title" class="text-xl font-bold">Asesmen BK</h1>
                <p id="current-user-display" class="text-sm text-yellow-300">Siswa: Belum Diisi</p>
            </div>
            <div class="flex gap-2">
                <button onclick="resetForm(true)" class="export-btn bg-red-500">❌ Batal</button>
            </div>
        </div>

        <!-- Main Interaction Box -->
        <div class="glass p-6 max-w-2xl mx-auto min-h-[500px] flex flex-col relative">
            
            <!-- Step 1: Input Nama -->
            <div id="step-name" class="flex-1 flex flex-col justify-center">
                <h2 class="text-3xl font-bold text-center mb-8">Siapa namamu?</h2>
                <input type="text" id="student-name" class="input-field mb-6 text-center text-xl" placeholder="Ketik nama lengkapmu di sini..." autocomplete="off">
                <button onclick="startInput()" class="primary-btn text-lg">Lanjut ➡️</button>
            </div>

            <!-- Step 2: Sesi Interaksi -->
            <div id="step-interaction" class="hidden flex-col h-full">
                
                <!-- CONTENT SESSION 1: Traffic Lights -->
                <div id="session-1-content" class="session-content h-full flex flex-col items-center justify-center">
                    <h3 class="text-2xl font-bold mb-8 text-center">Bagaimana kesiapan belajarmu saat ini?</h3>
                    <div class="flex gap-6 mb-8 justify-center">
                        <div class="traffic-light bg-red-500" onclick="selectTrafficLight('Merah', this)" title="Belum Siap"></div>
                        <div class="traffic-light bg-yellow-400" onclick="selectTrafficLight('Kuning', this)" title="Ragu-ragu"></div>
                        <div class="traffic-light bg-emerald-500" onclick="selectTrafficLight('Hijau', this)" title="Siap Belajar"></div>
                    </div>
                    <p id="traffic-desc" class="text-center text-lg italic h-8 mb-4"></p>
                    <button onclick="nextSession(2)" class="primary-btn w-auto px-8 hidden animate-bounce" id="btn-next-1">Lanjut ke Tantangan ➡️</button>
                </div>

                <!-- CONTENT SESSION 2: Mountain -->
                <div id="session-2-content" class="session-content hidden">
                    <h3 class="text-xl font-bold mb-4 text-center">Seberapa berat tantanganmu semester lalu?</h3>
                    <div class="mountain-container bg-blue-300 relative rounded-xl shadow-inner">
                        <svg width="100%" height="100%" viewBox="0 0 300 400" preserveAspectRatio="none">
                            <defs>
                                <linearGradient id="skyGrad" x1="0%" y1="0%" x2="0%" y2="100%">
                                    <stop offset="0%" style="stop-color:#60a5fa;stop-opacity:1" />
                                    <stop offset="100%" style="stop-color:#dbeafe;stop-opacity:1" />
                                </linearGradient>
                            </defs>
                            <rect width="300" height="400" fill="url(#skyGrad)" />
                            <path d="M-50 400 L150 100 L350 400 Z" fill="#78350f" stroke="#451a03" stroke-width="2" />
                            <path d="M150 100 L120 160 L180 160 Z" fill="white" opacity="0.8" />
                        </svg>
                        <div class="climber-position" style="bottom: 20px; left: 10%;" onclick="selectClimberPosition(1, this)">😃</div>
                        <div class="mountain-level" style="bottom: 30px;">Level 1: Mudah</div>
                        <div class="climber-position" style="bottom: 90px; left: 25%;" onclick="selectClimberPosition(2, this)">🙂</div>
                        <div class="mountain-level" style="bottom: 100px;">Level 2</div>
                        <div class="climber-position" style="bottom: 160px; left: 35%;" onclick="selectClimberPosition(3, this)">😐</div>
                        <div class="mountain-level" style="bottom: 170px;">Level 3</div>
                        <div class="climber-position" style="bottom: 230px; left: 40%;" onclick="selectClimberPosition(4, this)">😓</div>
                        <div class="mountain-level" style="bottom: 240px;">Level 4</div>
                        <div class="climber-position" style="bottom: 300px; left: 45%;" onclick="selectClimberPosition(5, this)">🏔️</div>
                        <div class="mountain-level" style="bottom: 310px;">Level 5: Sangat Sulit</div>
                    </div>
                    <textarea id="mountain-input" class="input-field mt-4" rows="3" placeholder="Ceritakan sedikit tantangan yang kamu hadapi..."></textarea>
                    <div class="mt-4 text-center">
                        <button onclick="nextSession(3)" class="primary-btn w-auto px-8">Lanjut ke Harapan ➡️</button>
                    </div>
                </div>

                <!-- CONTENT SESSION 3: Hope Jars (Magic Teapot) -->
                <div id="session-3-content" class="session-content hidden flex flex-col items-center">
                    <h3 class="text-2xl font-bold mb-4 text-center">Usap Teko Ajaib Ini! ✨</h3>
                    
                    <!-- Teapot Interaction -->
                    <div id="teapot-container" class="cursor-pointer transition-transform duration-300 hover:scale-105 mb-6" onclick="rubTeapot()">
                        <div class="text-9xl filter drop-shadow-xl" id="teapot-emoji" style="font-size: 8rem;">🫖</div>
                        <p id="rub-text" class="text-sm text-center mt-2 animate-pulse text-yellow-200">Klik / Usap untuk memunculkan harapan</p>
                    </div>
                    
                    <!-- Hidden Forms Reveal -->
                    <div id="hope-forms" class="hidden w-full space-y-4 opacity-0 transition-opacity duration-1000">
                        <div class="hope-box genie-effect" style="animation-delay: 0.1s;">
                            <label class="block text-yellow-300 font-bold mb-1">📚 Harapan Akademik</label>
                            <input type="text" id="hope-1" class="input-field bg-transparent border-b border-white" placeholder="Contoh: Nilai Matematika naik...">
                        </div>
                        <div class="hope-box genie-effect" style="animation-delay: 0.3s;">
                            <label class="block text-pink-300 font-bold mb-1">🤝 Harapan Sosial</label>
                            <input type="text" id="hope-2" class="input-field bg-transparent border-b border-white" placeholder="Contoh: Lebih aktif bergaul...">
                        </div>
                        <div class="hope-box genie-effect" style="animation-delay: 0.5s;">
                            <label class="block text-blue-300 font-bold mb-1">🌟 Harapan Pribadi</label>
                            <input type="text" id="hope-3" class="input-field bg-transparent border-b border-white" placeholder="Contoh: Lebih percaya diri...">
                        </div>
                        
                        <div class="mt-8 text-center pt-4">
                            <button onclick="showMoodSelector()" class="primary-btn bg-gradient-to-r from-emerald-500 to-teal-600 text-white hover:from-emerald-600 hover:to-teal-700 shadow-lg scale-100 hover:scale-105 transition-all">
                                Saya Sudah Selesai! 🎉
                            </button>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>

    <!-- Mood Selector Modal (New) -->
    <div id="mood-modal" class="summary-modal" style="display: none;">
        <div class="summary-content text-center max-w-2xl bg-white text-gray-800">
            <div class="text-4xl mb-4">🤔</div>
            <h2 class="text-2xl font-bold mb-6">Bagaimana perasaanmu mengikuti kelas hari ini?</h2>
            
            <div class="grid grid-cols-2 sm:grid-cols-4 gap-4 mb-6">
                <button onclick="selectMood('😁', 'Sangat Senang')" class="mood-btn flex flex-col items-center">
                    <div>😁</div><span class="text-xs font-bold mt-2">Sangat Senang</span>
                </button>
                <button onclick="selectMood('😊', 'Senang')" class="mood-btn flex flex-col items-center">
                    <div>😊</div><span class="text-xs font-bold mt-2">Senang</span>
                </button>
                <button onclick="selectMood('😌', 'Lega')" class="mood-btn flex flex-col items-center">
                    <div>😌</div><span class="text-xs font-bold mt-2">Lega</span>
                </button>
                <button onclick="selectMood('😐', 'Biasa Saja')" class="mood-btn flex flex-col items-center">
                    <div>😐</div><span class="text-xs font-bold mt-2">Biasa Saja</span>
                </button>
                <button onclick="selectMood('😔', 'Sedih')" class="mood-btn flex flex-col items-center">
                    <div>😔</div><span class="text-xs font-bold mt-2">Sedih</span>
                </button>
                <button onclick="selectMood('🥱', 'Lelah')" class="mood-btn flex flex-col items-center">
                    <div>🥱</div><span class="text-xs font-bold mt-2">Lelah</span>
                </button>
                <button onclick="selectMood('🤯', 'Bingung')" class="mood-btn flex flex-col items-center">
                    <div>🤯</div><span class="text-xs font-bold mt-2">Bingung</span>
                </button>
                <button onclick="selectMood('🤩', 'Bersemangat')" class="mood-btn flex flex-col items-center">
                    <div>🤩</div><span class="text-xs font-bold mt-2">Bersemangat</span>
                </button>
            </div>
            <p class="text-sm text-gray-500">Pilih satu yang paling menggambarkan perasaanmu.</p>
        </div>
    </div>

    <!-- Finish / Menu Modal -->
    <div id="finish-modal" class="summary-modal" style="display: none;">
        <div class="summary-content text-center max-w-lg">
            <div class="text-6xl mb-4">🎉</div>
            <h2 class="text-3xl font-bold text-gray-800 mb-2">Terima Kasih!</h2>
            <p class="text-gray-600 mb-8">Jawaban dan perasaanmu sudah tersimpan.</p>
            
            <div class="grid gap-3">
                <button onclick="openPasswordModal('view')" class="primary-btn bg-indigo-600 text-white flex items-center justify-center gap-2">
                    <span>📈</span> Lihat Rangkuman (Guru)
                </button>
                <button onclick="openPasswordModal('download')" class="primary-btn bg-green-600 text-white flex items-center justify-center gap-2">
                    <span>📊</span> Download Excel (Guru)
                </button>
                <button onclick="resetForm(true)" class="primary-btn bg-gray-500 text-white flex items-center justify-center gap-2">
                    <span>🏠</span> Kembali ke Menu Utama
                </button>
            </div>
            
            <button class="close-summary-btn bg-gray-300 text-gray-700 mt-4" onclick="document.getElementById('finish-modal').style.display='none'">Tutup Menu</button>
        </div>
    </div>

    <!-- Password Prompt Modal -->
    <div id="password-modal" class="summary-modal" style="display: none;">
        <div class="glass p-8 max-w-sm w-full text-center">
            <h3 class="text-2xl font-bold mb-4">🔐 Akses Guru</h3>
            <p class="mb-4 text-sm opacity-80">Masukkan password untuk melanjutkan.</p>
            <input type="password" id="password-input" class="input-field text-center mb-4" placeholder="Password..." style="background: rgba(0,0,0,0.5);">
            <div class="flex gap-2">
                <button onclick="closePasswordModal()" class="primary-btn bg-gray-500 text-white">Batal</button>
                <button onclick="checkPassword()" class="primary-btn bg-blue-600 text-white">Masuk</button>
            </div>
        </div>
    </div>

    <!-- Summary Display Modal -->
    <div id="view-summary-modal" class="summary-modal" style="display: none;">
        <div class="summary-content">
            <h2 class="text-2xl font-bold text-gray-800 mb-4 text-center">📋 Rangkuman Data Kelas</h2>
            <div class="overflow-x-auto">
                <table class="data-table">
                    <thead>
                        <tr>
                            <th>No</th>
                            <th>Nama</th>
                            <th>Kelas</th>
                            <th>Kesiapan</th>
                            <th>Tantangan</th>
                            <th>Harapan</th>
                            <th>Perasaan</th>
                        </tr>
                    </thead>
                    <tbody id="summary-table-body">
                        <!-- Data injected here -->
                    </tbody>
                </table>
            </div>
            <p id="empty-msg" class="text-center text-gray-500 mt-4 hidden">Belum ada data yang masuk.</p>
            <button class="close-summary-btn" onclick="document.getElementById('view-summary-modal').style.display='none'">Tutup</button>
        </div>
    </div>

  </div> <!-- End App Wrapper -->

  <script>
    // --- STATE MANAGEMENT ---
    let appState = {
        class: '',
        name: '',
        trafficLight: '', 
        mountainLevel: '', 
        mountainText: '',
        hopes: { academic: '', social: '', personal: '' },
        feeling: { emoji: '', label: '' } // New state for mood
    };

    let pendingAction = null; 
    const DB_KEY = 'asesmen_bk_data_v2'; 
    
    function getStoredData() {
        const data = localStorage.getItem(DB_KEY);
        return data ? JSON.parse(data) : [];
    }

    function saveToStorage(newEntry) {
        const currentData = getStoredData();
        currentData.push(newEntry);
        localStorage.setItem(DB_KEY, JSON.stringify(currentData));
    }

    // --- MUSIC & START ---
    const music = document.getElementById('bg-music');
    const musicBtn = document.getElementById('music-toggle');
    let isMusicPlaying = false;

    function startApp() {
        showClassSelection();
        toggleMusic(true); 
    }

    function toggleMusic(forcePlay = false) {
        if (isMusicPlaying && !forcePlay) {
            music.pause();
            musicBtn.classList.remove('playing');
            isMusicPlaying = false;
        } else {
            music.play().then(() => {
                musicBtn.classList.add('playing');
                isMusicPlaying = true;
            }).catch(e => console.log("Autoplay blocked"));
        }
    }

    function stopMusic() {
        music.pause();
        musicBtn.classList.remove('playing');
        isMusicPlaying = false;
    }

    // --- NAVIGATION FUNCTIONS ---
    function showClassSelection() {
        document.getElementById('welcome-screen').classList.add('hidden');
        document.getElementById('class-selection-screen').classList.remove('hidden');
    }

    function backToWelcome() {
        document.getElementById('class-selection-screen').classList.add('hidden');
        document.getElementById('welcome-screen').classList.remove('hidden');
    }

    function selectClass(className) {
        appState.class = className;
        document.getElementById('class-selection-screen').classList.add('hidden');
        document.getElementById('main-screen').classList.remove('hidden');
        resetInputFields();
    }

    function resetInputFields() {
        document.getElementById('step-name').classList.remove('hidden');
        document.getElementById('step-interaction').classList.add('hidden');
        document.getElementById('nav-container').classList.add('hidden'); 
        
        document.getElementById('student-name').value = '';
        document.getElementById('current-user-display').textContent = 'Siswa: ...';
        
        // Reset Logic Teapot
        document.getElementById('teapot-container').style.display = 'block';
        document.getElementById('hope-forms').classList.add('hidden');
        document.getElementById('hope-forms').classList.remove('opacity-100');
        document.getElementById('hope-forms').classList.add('opacity-0');
        
        appState = {
            class: appState.class, // Keep class
            name: '',
            trafficLight: '', 
            mountainLevel: '', 
            mountainText: '',
            hopes: { academic: '', social: '', personal: '' },
            feeling: { emoji: '', label: '' }
        };

        document.querySelectorAll('.traffic-light').forEach(el => el.classList.remove('selected'));
        document.querySelectorAll('.climber-position').forEach(el => el.classList.remove('selected'));
        document.getElementById('mountain-input').value = '';
        document.getElementById('hope-1').value = '';
        document.getElementById('hope-2').value = '';
        document.getElementById('hope-3').value = '';
        document.getElementById('traffic-desc').textContent = '';
        document.getElementById('btn-next-1').classList.add('hidden');
        
        switchSession(1);
    }

    function startInput() {
        const nameInput = document.getElementById('student-name').value.trim();
        if (!nameInput) {
            alert("Harap isi namamu dulu ya! 😊");
            return;
        }
        appState.name = nameInput;
        document.getElementById('current-user-display').textContent = `Siswa: ${appState.name} (${appState.class})`;
        document.getElementById('step-name').classList.add('hidden');
        document.getElementById('step-interaction').classList.remove('hidden');
        document.getElementById('nav-container').classList.remove('hidden');
        document.getElementById('step-interaction').style.display = 'flex';
        switchSession(1);
    }

    // --- NEW VALIDATION HELPERS ---
    
    function validateStep(step) {
        if (step === 1) {
            if (!appState.trafficLight) {
                alert("Silakan pilih warna lampu lalu lintas dulu ya! 🚦");
                return false;
            }
        }
        if (step === 2) {
            const text = document.getElementById('mountain-input').value.trim();
            if (!appState.mountainLevel) {
                alert("Silakan pilih posisi pendaki dulu ya! 🧗");
                return false;
            }
            if (!text) {
                alert("Ceritakan sedikit tantanganmu ya! ✍️");
                return false;
            }
        }
        // Step 3 validasi ada di tombol selesai
        return true;
    }

    function switchSession(sessionId) {
        // Validasi sebelum pindah tab
        // Jika mau ke sesi 2, cek sesi 1
        if (sessionId === 2 && !validateStep(1)) return;
        
        // Jika mau ke sesi 3, cek sesi 1 dan 2
        if (sessionId === 3) {
            if (!validateStep(1)) return;
            if (!validateStep(2)) return;
        }

        // Logic normal
        document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('active'));
        document.getElementById(`nav-${sessionId}`).classList.add('active');
        document.querySelectorAll('.session-content').forEach(content => content.classList.add('hidden'));
        document.getElementById(`session-${sessionId}-content`).classList.remove('hidden');
        const titles = ["Kesiapan Belajar", "Tantangan Belajar", "Harapan & Impian"];
        document.getElementById('session-title').innerText = titles[sessionId-1];
    }
    
    function nextSession(targetId) {
        // nextSession juga menggunakan switchSession, jadi validasi otomatis berjalan
        switchSession(targetId);
    }

    // --- INTERACTION LOGIC ---

    function selectTrafficLight(color, element) {
        appState.trafficLight = color;
        document.querySelectorAll('.traffic-light').forEach(el => el.classList.remove('selected'));
        element.classList.add('selected');
        const descriptions = { 'Merah': 'Belum siap, butuh bantuan.', 'Kuning': 'Ragu-ragu, butuh motivasi.', 'Hijau': 'Siap semangat belajar!' };
        document.getElementById('traffic-desc').textContent = descriptions[color];
        document.getElementById('btn-next-1').classList.remove('hidden');
    }

    function selectClimberPosition(level, element) {
        appState.mountainLevel = level;
        document.querySelectorAll('.climber-position').forEach(el => el.classList.remove('selected'));
        element.classList.add('selected');
    }
    
    function rubTeapot() {
        const teapot = document.getElementById('teapot-emoji');
        const container = document.getElementById('teapot-container');
        const rubText = document.getElementById('rub-text');
        
        // Shake animation
        teapot.classList.add('shake-animation');
        rubText.textContent = "Sedang memanggil harapan... ✨";
        
        setTimeout(() => {
            teapot.classList.remove('shake-animation');
            container.style.transform = "scale(0.8) translateY(-20px) opacity(0)";
            setTimeout(() => {
                container.style.display = "none";
                const forms = document.getElementById('hope-forms');
                forms.classList.remove('hidden');
                // Force reflow
                void forms.offsetWidth;
                forms.classList.remove('opacity-0');
                forms.classList.add('opacity-100');
            }, 300);
        }, 1000);
    }

    // NEW: Flow Control
    function showMoodSelector() {
        // Ambil data terbaru dari input
        appState.mountainText = document.getElementById('mountain-input').value;
        appState.hopes.academic = document.getElementById('hope-1').value;
        appState.hopes.social = document.getElementById('hope-2').value;
        appState.hopes.personal = document.getElementById('hope-3').value;

        if (!appState.name) { alert("Nama belum diisi!"); return; }

        // Validasi Sesi 3 sebelum selesai
        if (!appState.hopes.academic || !appState.hopes.social || !appState.hopes.personal) {
            alert("Lengkapi ketiga harapanmu dulu ya! ✨");
            return;
        }
        
        // Show Mood Modal
        document.getElementById('mood-modal').style.display = 'flex';
    }

    function selectMood(emoji, label) {
        appState.feeling = { emoji, label };
        finishAssessment();
    }

    function finishAssessment() {
        document.getElementById('mood-modal').style.display = 'none';
        
        const record = {
            timestamp: new Date().toISOString(),
            ...appState
        };

        saveToStorage(record);
        document.getElementById('finish-modal').style.display = 'flex';
    }

    function resetForm(fullReset = false) {
        document.getElementById('finish-modal').style.display = 'none';
        resetInputFields();
        if (fullReset) {
            document.getElementById('main-screen').classList.add('hidden');
            document.getElementById('welcome-screen').classList.remove('hidden');
        }
    }

    // --- PASSWORD & GURU LOGIC ---

    function openPasswordModal(action) {
        pendingAction = action;
        document.getElementById('password-input').value = '';
        document.getElementById('password-modal').style.display = 'flex';
        document.getElementById('password-input').focus();
    }

    function closePasswordModal() {
        document.getElementById('password-modal').style.display = 'none';
        pendingAction = null;
    }

    function checkPassword() {
        const input = document.getElementById('password-input').value;
        
        if (pendingAction === 'view') {
            if (input === 'lihatrangkuman') {
                closePasswordModal();
                showSummaryTable();
            } else { alert("Password Salah! (Hint: lihatrangkuman)"); }
        } else if (pendingAction === 'download') {
            if (input === 'downloadrangkuman') {
                closePasswordModal();
                downloadCSV();
            } else { alert("Password Salah! (Hint: downloadrangkuman)"); }
        }
    }

    function showSummaryTable() {
        const data = getStoredData();
        const tbody = document.getElementById('summary-table-body');
        const emptyMsg = document.getElementById('empty-msg');
        
        tbody.innerHTML = '';
        if (data.length === 0) { emptyMsg.classList.remove('hidden'); } 
        else {
            emptyMsg.classList.add('hidden');
            data.forEach((item, index) => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td>${index + 1}</td>
                    <td><strong>${item.name}</strong></td>
                    <td>${item.class}</td>
                    <td>${item.trafficLight || '-'}</td>
                    <td>${item.mountainText || '-'} (Lvl ${item.mountainLevel})</td>
                    <td><small>A:${item.hopes.academic}<br>S:${item.hopes.social}<br>P:${item.hopes.personal}</small></td>
                    <td>${item.feeling?.emoji || '-'} ${item.feeling?.label || ''}</td>
                `;
                tbody.appendChild(tr);
            });
        }
        document.getElementById('finish-modal').style.display = 'none';
        document.getElementById('view-summary-modal').style.display = 'flex';
    }

    function downloadCSV() {
        const data = getStoredData();
        if (data.length === 0) { alert("Belum ada data."); return; }

        let csvContent = "data:text/csv;charset=utf-8,";
        csvContent += "No,Waktu,Nama,Kelas,Kesiapan,Level Tantangan,Deskripsi Tantangan,Harapan Akademik,Harapan Sosial,Harapan Pribadi,Perasaan\n";

        data.forEach((row, index) => {
            const clean = (text) => text ? `"${text.replace(/"/g, '""')}"` : "";
            const date = new Date(row.timestamp).toLocaleString('id-ID');
            let rowString = [
                index + 1, date, clean(row.name), clean(row.class),
                clean(row.trafficLight), row.mountainLevel, clean(row.mountainText),
                clean(row.hopes.academic), clean(row.hopes.social), clean(row.hopes.personal),
                clean(`${row.feeling?.emoji || ''} ${row.feeling?.label || ''}`)
            ].join(",");
            csvContent += rowString + "\n";
        });

        const encodedUri = encodeURI(csvContent);
        const link = document.createElement("a");
        link.setAttribute("href", encodedUri);
        link.setAttribute("download", `Rekap_Asesmen_BK_${new Date().toLocaleDateString('id-ID')}.csv`);
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }

    // --- BACKGROUND ---
    function initBackground() {
        const canvas = document.getElementById('bg-canvas');
        const renderer = new THREE.WebGLRenderer({ canvas: canvas, alpha: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 20;

        const particlesCount = 150;
        const geometry = new THREE.BufferGeometry();
        const positions = new Float32Array(particlesCount * 3);
        const velocities = [];

        for(let i = 0; i < particlesCount; i++) {
            positions[i * 3] = (Math.random() - 0.5) * 50;
            positions[i * 3 + 1] = (Math.random() - 0.5) * 50;
            positions[i * 3 + 2] = (Math.random() - 0.5) * 50;
            velocities.push({ x: (Math.random() - 0.5) * 0.05, y: (Math.random() - 0.5) * 0.05, z: (Math.random() - 0.5) * 0.05 });
        }
        geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
        const material = new THREE.PointsMaterial({ size: 0.2, color: 0x818cf8, transparent: true, opacity: 0.8 });
        const particlesMesh = new THREE.Points(geometry, material);
        scene.add(particlesMesh);

        const lineMaterial = new THREE.LineBasicMaterial({ color: 0x818cf8, transparent: true, opacity: 0.15 });
        const lineGeometry = new THREE.BufferGeometry();
        const linesMesh = new THREE.LineSegments(lineGeometry, lineMaterial);
        scene.add(linesMesh);

        function animate() {
            requestAnimationFrame(animate);
            const positionsAttribute = particlesMesh.geometry.attributes.position;
            const positions = positionsAttribute.array;

            for(let i = 0; i < particlesCount; i++) {
                positions[i * 3] += velocities[i].x;
                positions[i * 3 + 1] += velocities[i].y;
                positions[i * 3 + 2] += velocities[i].z;
                if(Math.abs(positions[i * 3]) > 25) velocities[i].x *= -1;
                if(Math.abs(positions[i * 3 + 1]) > 25) velocities[i].y *= -1;
                if(Math.abs(positions[i * 3 + 2]) > 25) velocities[i].z *= -1;
            }
            positionsAttribute.needsUpdate = true;

            const linePositions = [];
            for(let i = 0; i < particlesCount; i++) {
                for(let j = i + 1; j < particlesCount; j++) {
                    const dx = positions[i*3] - positions[j*3];
                    const dy = positions[i*3+1] - positions[j*3+1];
                    const dz = positions[i*3+2] - positions[j*3+2];
                    if (Math.sqrt(dx*dx + dy*dy + dz*dz) < 8) {
                        linePositions.push(positions[i*3], positions[i*3+1], positions[i*3+2], positions[j*3], positions[j*3+1], positions[j*3+2]);
                    }
                }
            }
            linesMesh.geometry.setAttribute('position', new THREE.Float32BufferAttribute(linePositions, 3));
            particlesMesh.rotation.y += 0.001; linesMesh.rotation.y += 0.001;
            renderer.render(scene, camera);
        }
        animate();
        window.addEventListener('resize', () => { camera.aspect = window.innerWidth / window.innerHeight; camera.updateProjectionMatrix(); renderer.setSize(window.innerWidth, window.innerHeight); });
    }
    window.onload = function() { initBackground(); };
  </script>
 </body>
</html>
