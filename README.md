# Game-Hafalan-Al-Quran
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Game Hafalan Al-Qur'an</title>
    <style>
        /* --- Font dan Tampilan Dasar --- */
        @import url('https://fonts.googleapis.com/css2?family=Scheherazade+New:wght@400;700&family=Noto+Sans:wght@400;700&display=swap');

        :root {
            --primary-color: #00796b; /* Hijau Tua */
            --primary-light: #e0f2f1; /* Hijau Sangat Muda */
            --secondary-color: #005c4b; /* Hijau Lebih Tua */
            --correct-color: #00796b;
            --correct-bg: #e0f2f1;
            --wrong-color: #c62828;
            --wrong-bg: #ffebee;
            --text-dark: #333;
            --text-light: #555;
            --border-color: #ddd;
            --bg-light: #f0ffef; /* Warna latar belakang yang lebih cerah/islami */
            --bg-white: #ffffff;
            --bg-loading: rgba(255, 255, 255, 0.8);
            --timer-color: #d32f2f; /* Merah untuk timer */
        }

        body {
            font-family: 'Noto Sans', sans-serif;
            background-color: var(--bg-light);
            color: var(--text-dark);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            min-height: 100vh;
        }

        .container {
            width: 100%;
            max-width: 800px;
            background-color: var(--bg-white);
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }

        /* --- Layar (Screen) --- */
        .screen {
            padding: 25px 30px;
        }

        #game-screen, #results-screen {
            display: none;
        }

        /* --- Tombol --- */
        .btn {
            display: inline-block;
            padding: 12px 25px;
            font-size: 1rem;
            font-weight: 700;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s, transform 0.2s;
            text-align: center;
        }
        
        .btn-primary {
            background-color: var(--primary-color);
            color: white;
            width: 100%;
            margin-top: 20px;
        }
        .btn-primary:hover {
            background-color: var(--secondary-color);
            transform: translateY(-2px);
        }
        .btn-primary:disabled {
            background-color: #ccc;
            cursor: not-allowed;
            transform: none;
        }

        .btn-secondary {
            background-color: var(--bg-light);
            color: var(--text-dark);
            border: 1px solid var(--border-color);
        }
        .btn-secondary:hover {
            background-color: #e0e6ec;
        }
        
        /* --- Timer Kuis --- */
        #quiz-timer {
            text-align: center;
            padding: 10px;
            background-color: var(--timer-color);
            color: white;
            font-weight: 700;
            font-size: 1.2rem;
            border-radius: 8px;
            margin-bottom: 20px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        #timer-countdown.danger {
            animation: pulse 1s infinite;
        }
        @keyframes pulse {
            0% { background-color: var(--timer-color); }
            50% { background-color: #f44336; }
            100% { background-color: var(--timer-color); }
        }

        /* --- Teks Arab --- */
        .arabic-text {
            font-family: 'Scheherazade New', serif; 
            font-size: 2.2rem;
            line-height: 2;
            text-align: right;
            direction: rtl;
            margin: 20px 0;
            color: #000;
        }
        
        /* --- Fitur 1: Pilihan Juz --- */
        .juz-controls {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }
        .juz-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(50px, 1fr));
            gap: 10px;
            max-height: 200px;
            overflow-y: auto;
            border: 1px solid var(--border-color);
            padding: 15px;
            border-radius: 8px;
        }
        .juz-item {
            display: flex;
            align-items: center;
            justify-content: center;
            border: 1px solid #ccc;
            border-radius: 5px;
            padding: 0; 
            cursor: pointer;
            transition: background-color 0.2s;
        }
        .juz-item input[type="checkbox"] {
            display: none;
        }
        .juz-item label {
            cursor: pointer;
            padding: 8px 10px;
            width: 100%;
            height: 100%;
            text-align: center;
            user-select: none;
            border-radius: 5px;
        }
        .juz-item input[type="checkbox"]:checked + label {
            background-color: var(--primary-color);
            color: white;
        }
        .juz-item input[type="checkbox"]:not(:checked) + label:hover {
            background-color: var(--primary-light);
        }
        /* --- Error Message --- */
        .error-message {
            background-color: var(--wrong-bg);
            color: var(--wrong-color);
            border: 1px solid var(--wrong-color);
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 15px;
            text-align: center;
        }

        #question-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 1.1rem;
            font-weight: 700;
            color: var(--text-light);
            margin-bottom: 20px;
            text-align: right;
        }
        
        #question-text {
            border-bottom: 1px solid #eee;
            padding-bottom: 20px;
        }

        .options-container {
            display: flex;
            flex-direction: column;
            gap: 15px;
            margin-top: 20px;
        }
        .option-label {
            display: block;
            padding: 15px;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.2s, border-color 0.2s;
        }
        .option-label:hover {
            background-color: #f9f9f9;
        }
        /* State saat menjawab */
        .option-label.selected {
            background-color: var(--primary-light);
            border-color: var(--primary-color);
        }
        .option-label.correct {
            background-color: var(--correct-bg);
            border-color: var(--correct-color);
            font-weight: 700;
        }
        .option-label.wrong {
            background-color: var(--wrong-bg);
            border-color: var(--wrong-color);
            text-decoration: line-through;
        }

        .option-label input[type="radio"] {
            display: none;
        }
        .option-label .radio-btn {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 2px solid #ccc;
            border-radius: 50%;
            margin-right: 15px;
            vertical-align: middle;
            transition: border-color 0.2s;
        }
        .option-label .option-text {
            vertical-align: middle;
            font-family: 'Scheherazade New', serif;
            font-size: 1.8rem;
            direction: rtl;
            text-align: right;
            width: calc(100% - 40px);
            display: inline-block;
        }
        /* Penyesuaian font untuk jawaban non-Arab (Nama Surah/Nomor Ayat/Urutan) */
        .option-label .option-text.non-arabic {
            font-family: 'Noto Sans', sans-serif;
            direction: ltr;
            text-align: left;
            font-size: 1.2rem;
            font-weight: 600;
        }

        .option-label input[type="radio"]:checked + .radio-btn {
            border-color: var(--primary-color);
            background-color: var(--primary-color);
            box-shadow: 0 0 0 3px white inset, 0 0 0 2px var(--primary-color);
        }

        /* --- Area Feedback (Benar/Salah) --- */
        #feedback-area {
            margin-top: 20px;
            padding: 15px;
            border-radius: 8px;
            display: none;
        }
        #feedback-area.correct {
            background-color: var(--correct-bg);
            border: 1px solid var(--correct-color);
        }
        #feedback-area.wrong {
            background-color: var(--wrong-bg);
            border: 1px solid var(--wrong-color);
        }
        #feedback-text {
            font-size: 1.5rem;
            font-weight: 700;
            margin: 0;
        }
        #feedback-text.correct { color: var(--correct-color); }
        #feedback-text.wrong { color: var(--wrong-color); }

        #correction-text {
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px dashed var(--wrong-color);
        }
        #correction-explanation {
            font-size: 1rem;
            font-family: 'Noto Sans', sans-serif;
            direction: ltr;
            text-align: left;
            margin-top: 10px;
        }
        .arabic-correction {
            font-family: 'Scheherazade New', serif;
            font-size: 1.8rem;
            direction: rtl;
            text-align: right;
            color: var(--secondary-color);
        }


        /* --- Fitur 6: Layar Hasil Akhir --- */
        #summary {
            background-color: var(--bg-light);
            padding: 20px;
            border-radius: 8px;
            text-align: center;
            margin-bottom: 30px;
        }
        #summary h3 {
            margin-top: 0;
        }
        #summary p {
            font-size: 1.2rem;
            margin: 10px 0;
        }
        #final-score {
            font-size: 3rem;
            font-weight: 700;
            color: var(--primary-color);
        }

        #review-container {
            display: flex;
            flex-direction: column;
            gap: 20px;
            max-height: 400px;
            overflow-y: auto;
            padding-right: 10px; 
        }
        .review-item {
            border: 1px solid var(--border-color);
            padding: 15px;
            border-radius: 8px;
        }
        .review-item strong {
            font-size: 1.1rem;
            color: var(--text-dark);
        }
        .review-question {
            font-size: 1.5rem;
            margin-bottom: 10px;
        }
        .review-answer {
            margin: 5px 0;
            padding: 8px;
            border-radius: 5px;
            font-family: 'Scheherazade New', serif;
            font-size: 1.5rem;
            direction: rtl;
            text-align: right;
        }
        /* Penyesuaian font untuk jawaban non-Arab di review */
        .review-answer.non-arabic {
            font-family: 'Noto Sans', sans-serif;
            direction: ltr;
            text-align: left;
            font-size: 1.2rem;
        }
        
        .user-wrong {
            background-color: var(--wrong-bg);
            color: var(--wrong-color);
            border: 1px solid #ffcdd2;
        }
        .user-correct {
            background-color: var(--correct-bg);
            color: var(--correct-color);
            border: 1px solid #b2dfdb;
        }
        .correct-ans {
            font-weight: 700;
            background-color: var(--primary-light);
            color: var(--secondary-color);
        }
        .review-correction {
            font-size: 1rem;
            font-family: 'Noto Sans', sans-serif;
            direction: ltr;
            text-align: left;
            background-color: #f5f5f5;
            padding: 10px;
            border-top: 1px solid #eee;
            margin-top: 10px;
        }
        
        /* --- Loading Screen --- */
        #loading-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: var(--bg-loading);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            text-align: center;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s;
        }
        #loading-screen.visible {
            opacity: 1;
            visibility: visible;
        }
        .spinner {
            border: 8px solid #f3f3f3;
            border-top: 8px solid var(--primary-color);
            border-radius: 50%;
            width: 60px;
            height: 60px;
            animation: spin 1s linear infinite;
            margin-bottom: 20px;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

    </style>
</head>
<body>

    <main class="container">

        <section id="setup-screen" class="screen">
            <h1>Game Hafalan Al-Qur'an</h1>
            <p>Uji hafalan Anda dengan kuis pilihan ganda yang disesuaikan berdasarkan Juz dan tingkat kesulitan.</p>
            <form id="setup-form">

                <h3>1. Pilih Juz yang Ingin Diuji</h3>
                <div class="juz-controls">
                    <button type="button" id="select-all-juz" class="btn btn-secondary" style="flex: 1;">Pilih Semua</button>
                    <button type="button" id="clear-all-juz" class="btn btn-secondary" style="flex: 1;">Kosongkan Pilihan</button>
                </div>
                <div class="juz-container" id="juz-container"> 
                </div>
                <p id="juz-error" class="error-message" style="display: none;">Silakan pilih minimal satu Juz.</p>
                <div id="general-error" class="error-message" style="display: none;"></div>

                <h3>2. Pilih Jenis Game</h3>
                <div class="option-group" style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="gameType" id="game-sambung" value="Sambung Ayat (Lanjutan Ayat)" checked>
                        <label for="game-sambung">1. Sambung Ayat (Lanjutan)</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="gameType" id="game-tebak-lanjutan" value="Tebak Lanjutan Ayat (Potongan)">
                        <label for="game-tebak-lanjutan">2. Tebak Potongan Lanjutan</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="gameType" id="game-tebak-surat" value="Tebak Asal Surah / Juz">
                        <label for="game-tebak-surat">3. Tebak Asal Surah/Juz</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="gameType" id="game-ayat-pertama" value="Tebak Ayat Pertama Surah">
                        <label for="game-ayat-pertama">4. Tebak Ayat Pertama Surah</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="gameType" id="game-ayat-penutup" value="Tebak Ayat Penutup Surah">
                        <label for="game-ayat-penutup">5. Tebak Ayat Penutup Surah</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="gameType" id="game-nomor-ayat" value="Tebak Nomor Ayat">
                        <label for="game-nomor-ayat">6. Tebak Nomor Ayat</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="gameType" id="game-urutkan-ayat" value="Urutkan Potongan Ayat">
                        <label for="game-urutkan-ayat">7. Urutkan Potongan Ayat</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="gameType" id="game-mutasyabihat" value="Mutasyabihat (Kontekstual)">
                        <label for="game-mutasyabihat">8. Mutasyabihat (Kontekstual)</label>
                    </div>
                </div>

                <h3>3. Pilih Tingkat Kesulitan</h3>
                <div class="option-group" style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="difficulty" id="level-mudah" value="mudah" checked>
                        <label for="level-mudah">Mudah (25 detik/soal)</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="difficulty" id="level-sedang" value="sedang">
                        <label for="level-sedang">Sedang (20 detik/soal)</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="difficulty" id="level-sulit" value="sulit">
                        <label for="level-sulit">Sulit (15 detik/soal) - Pengecoh Mutasyabihat</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="difficulty" id="level-sangat-sulit" value="sangat sulit">
                        <label for="level-sangat-sulit">Sangat Sulit (10 detik/soal) - Mutasyabihat Ekstrem</label>
                    </div>
                </div>

                <h3>4. Pilih Jumlah Soal</h3>
                <div class="option-group" style="display: flex; flex-wrap: wrap; gap: 10px;">
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="numQuestions" id="num-5" value="5" checked>
                        <label for="num-5">5 Soal</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="numQuestions" id="num-10" value="10">
                        <label for="num-10">10 Soal</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="numQuestions" id="num-15" value="15">
                        <label for="num-15">15 Soal</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="numQuestions" id="num-20" value="20">
                        <label for="num-20">20 Soal</label>
                    </div>
                    <div class="radio-option" style="border: 1px solid #ddd; padding: 10px; border-radius: 8px;">
                        <input type="radio" name="numQ on
