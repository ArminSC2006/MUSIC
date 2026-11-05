[HOLLOW KINGHT 2.html](https://github.com/user-attachments/files/34448126/HOLLOW.KINGHT.2.html)
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sinfonía de Hallownest - Reproductor de Música</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome para Iconos -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Fuentes de Google -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@400;600;700;900&family=Metamorphous&family=Montserrat:wght@300;400;500;600&display=swap" rel="stylesheet">

    <style>
        /* Estilos Personalizados Temática Hollow Knight */
        :root {
            --hk-bg: #080b12;
            --hk-card: #0e1525;
            --hk-accent: #6ee7b7; /* Resplandor Lumélula / Almas */
            --hk-soul: #e0f2fe;
            --hk-dark-border: #1e293b;
            --hk-glow: rgba(110, 231, 183, 0.25);
        }

        body {
            background-color: var(--hk-bg);
            color: #d1d5db;
            font-family: 'Montserrat', sans-serif;
            background-image: 
                radial-gradient(circle at 50% 20%, rgba(14, 116, 144, 0.15) 0%, transparent 50%),
                radial-gradient(circle at 80% 80%, rgba(16, 185, 129, 0.08) 0%, transparent 40%);
            background-attachment: fixed;
        }

        .hk-title {
            font-family: 'Cinzel', serif;
            letter-spacing: 2px;
        }

        .hk-ornament-title {
            font-family: 'Metamorphous', cursive;
        }

        /* Marcos Ornamentados */
        .hk-frame {
            border: 2px solid #2d3748;
            box-shadow: 0 0 15px rgba(0,0,0,0.8), inset 0 0 15px rgba(0,0,0,0.6);
            position: relative;
        }

        .hk-frame::before {
            content: '';
            position: absolute;
            top: -5px; left: -5px; right: -5px; bottom: -5px;
            border: 1px solid rgba(110, 231, 183, 0.2);
            pointer-events: none;
            border-radius: inherit;
        }

        /* Lumélulas flotantes animadas en el fondo */
        .lumafly {
            position: fixed;
            width: 4px;
            height: 4px;
            background-color: #a7f3d0;
            border-radius: 50%;
            box-shadow: 0 0 10px #6ee7b7, 0 0 20px #6ee7b7;
            pointer-events: none;
            animation: float 8s infinite ease-in-out;
            z-index: 0;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0) translateX(0) scale(1); opacity: 0.3; }
            50% { transform: translateY(-40px) translateX(20px) scale(1.5); opacity: 0.8; }
        }

        /* Personalización de Barras de Desplazamiento y Progreso */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #080b12;
        }
        ::-webkit-scrollbar-thumb {
            background: #1e293b;
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #334155;
        }

        input[type=range] {
            accent-color: #6ee7b7;
        }

        /* Resplandor de selección activo */
        .album-active {
            border-color: #6ee7b7 !important;
            box-shadow: 0 0 20px rgba(110, 231, 183, 0.3);
        }

        .song-active {
            background-color: rgba(110, 231, 183, 0.1);
            color: #6ee7b7;
            border-left: 3px solid #6ee7b7;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between relative overflow-x-hidden">

    <!-- Efecto de partículas (Lumélulas) -->
    <div id="lumafly-container"></div>

    <!-- Encabezado Principal -->
    <header class="relative z-10 border-b border-slate-800 bg-slate-950/80 backdrop-blur-md px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-3">
            <i class="fa-solid fa-spaghetti-monster-flying text-2xl text-emerald-300"></i>
            <h1 class="hk-title text-xl md:text-2xl font-bold tracking-widest text-slate-100">
                HALLOWNEST <span class="text-emerald-400 font-normal">AUDIO PLAYER</span>
            </h1>
        </div>
        <div class="text-xs text-slate-400 hk-ornament-title hidden sm:block">
            Eco de los Insectos & Documentos del Abismo
        </div>
    </header>

    <!-- Contenido Principal -->
    <main class="relative z-10 max-w-7xl mx-auto w-full p-4 md:p-6 grid grid-cols-1 lg:grid-cols-12 gap-6 my-auto">

        <!-- Lista de Álbumes (4 Columnas en Desktop) -->
        <section class="lg:col-span-5 flex flex-col space-y-4">
            <div class="flex justify-between items-center border-b border-slate-800 pb-2">
                <h2 class="hk-title text-lg font-semibold text-slate-200 flex items-center gap-2">
                    <i class="fa-solid fa-record-vinyl text-emerald-400"></i> Colección de Álbumes
                </h2>
                <span id="album-count" class="text-xs text-slate-400 bg-slate-900 px-2 py-1 rounded border border-slate-800">10 Álbumes</span>
            </div>

            <!-- Grid Scrollable de Álbumes -->
            <div id="albums-grid" class="grid grid-cols-2 sm:grid-cols-3 gap-3 max-h-[500px] overflow-y-auto pr-1">
                <!-- Se llena dinámicamente desde JavaScript -->
            </div>
        </section>

        <!-- Panel del Reproductor Activo + Lista de Canciones (7 Columnas en Desktop) -->
        <section class="lg:col-span-7 flex flex-col space-y-4">
            
            <!-- Tarjeta del Reproductor -->
            <div class="hk-frame bg-slate-900/90 rounded-xl p-5 border border-slate-800 flex flex-col md:flex-row gap-6 items-center">
                <!-- Portada Grande -->
                <div class="relative group w-40 h-40 md:w-48 md:h-48 flex-shrink-0">
                    <img id="player-cover" src="" alt="Portada" class="w-full h-full object-cover rounded-lg border border-slate-700 shadow-2xl">
                    <div id="vinyl-animation" class="absolute -inset-1 bg-gradient-to-r from-emerald-500 to-cyan-500 rounded-lg blur opacity-20 group-hover:opacity-40 transition duration-1000"></div>
                </div>

                <!-- Detalles e Información -->
                <div class="flex-1 w-full text-center md:text-left flex flex-col justify-between h-full">
                    <div>
                        <span id="player-album-title" class="text-xs uppercase tracking-widest text-emerald-400 font-semibold">ÁLBUM</span>
                        <h3 id="player-song-title" class="hk-title text-xl md:text-2xl text-slate-100 font-bold truncate mt-1">Selecciona una canción</h3>
                        <p id="player-artist" class="text-sm text-slate-400 mt-1">Artista de Hallownest</p>
                    </div>

                    <!-- Progreso y Tiempo -->
                    <div class="mt-4 w-full">
                        <input type="range" id="progress-bar" value="0" min="0" max="100" class="w-full h-1.5 bg-slate-800 rounded-lg appearance-none cursor-pointer">
                        <div class="flex justify-between text-xs text-slate-400 mt-1 font-mono">
                            <span id="current-time">00:00</span>
                            <span id="duration-time">00:00</span>
                        </div>
                    </div>

                    <!-- Botones de Control -->
                    <div class="flex items-center justify-center md:justify-start space-x-4 mt-4">
                        <button id="btn-random" class="text-slate-400 hover:text-emerald-400 transition" title="Modo Aleatorio">
                            <i class="fa-solid fa-shuffle text-sm"></i>
                        </button>
                        <button id="btn-prev" class="text-slate-200 hover:text-emerald-300 text-lg transition">
                            <i class="fa-solid fa-backward-step"></i>
                        </button>
                        <button id="btn-play" class="w-12 h-12 bg-emerald-500 hover:bg-emerald-400 text-slate-950 rounded-full flex items-center justify-center text-lg shadow-lg shadow-emerald-500/20 transition transform active:scale-95">
                            <i id="play-icon" class="fa-solid fa-play ml-0.5"></i>
                        </button>
                        <button id="btn-next" class="text-slate-200 hover:text-emerald-300 text-lg transition">
                            <i class="fa-solid fa-forward-step"></i>
                        </button>
                        <button id="btn-repeat" class="text-slate-400 hover:text-emerald-400 transition" title="Repetir">
                            <i class="fa-solid fa-repeat text-sm"></i>
                        </button>

                        <!-- Volumen -->
                        <div class="flex items-center space-x-2 ml-auto hidden sm:flex">
                            <i id="volume-icon" class="fa-solid fa-volume-high text-xs text-slate-400"></i>
                            <input type="range" id="volume-bar" value="80" min="0" max="100" class="w-16 h-1 bg-slate-800 rounded-lg appearance-none cursor-pointer">
                        </div>
                    </div>
                </div>
            </div>

            <!-- Lista de Canciones del Álbum Seleccionado -->
            <div class="bg-slate-900/60 rounded-xl border border-slate-800 p-4 flex-1 flex flex-col">
                <div class="flex justify-between items-center mb-3 pb-2 border-b border-slate-800/80">
                    <h3 class="hk-title text-sm text-slate-300 font-semibold flex items-center gap-2">
                        <i class="fa-solid fa-list-ul text-emerald-400"></i> Lista de Pistas
                    </h3>
                    <span id="song-count" class="text-xs text-slate-400">0 canciones</span>
                </div>
                <div id="songs-list" class="space-y-1 max-h-[220px] overflow-y-auto pr-1">
                    <!-- Las canciones del álbum activo se renderizan aquí -->
                </div>
            </div>

        </section>
    </main>

    <!-- Pie de página -->
    <footer class="relative z-10 border-t border-slate-800/80 bg-slate-950/80 text-center py-3 text-xs text-slate-400">
        Inspirado en el universo de Hollow Knight &copy; Team Cherry | Configurable para Sublime Text
    </footer>

    <!-- Elemento Audio oculto -->
    <audio id="audio-player"></audio>

    <!-- LÓGICA Y DATOS CONFIGURABLES EN JAVASCRIPT -->
    <script>
        /* =====================================================================
           CONFIGURACIÓN DE ÁLBUMES Y CANCIONES (FÁCILMENTE EDITABLE EN SUBLIME)
           
           Puedes agregar, eliminar o modificar las canciones de cada álbum.
           Cada objeto de álbum incluye:
           - id: Identificador único
           - title: Título del Álbum
           - artist: Nombre del Artista / Compositor
           - cover: URL de la imagen de portada
           - songs: Arreglo/Lista con canciones { title, duration, src }
           ===================================================================== */
        const albumsData = [
            {
                id: 1,
                title: "HATSUNE MIKU ",
                artist: "HATSUNE MIKU",
                cover: "HATSUNE MIKU.png",
                songs: [
                    { title: "いますぐ輪廻 _ 初音ミク(MP3_128K).mp3", duration: "03:54", src: "いますぐ輪廻 _ 初音ミク(MP3_128K).mp3" },
                    { title: "オーバーライド - 重音テトSV[吉田夜世]", duration: "02:20", src: "オーバーライド - 重音テトSV[吉田夜世](MP3_128K).mp3" },
                    { title: "スプリットダンス _ 初音ミク・重音テト(MP3_128K).mp3", duration: "03:05", src: "スプリットダンス _ 初音ミク・重音テト(MP3_128K).mp3" },
                    { title: "ダダダダダル _ 雨良 feat.初音ミクVS重音テト(Dadadadadaru _ Amala ft.Miku VS Teto)(MP3_128K).mp3", duration: "03:05", src: "ダダダダダル _ 雨良 feat.初音ミクVS重音テト(Dadadadadaru _ Amala ft.Miku VS Teto)(MP3_128K).mp3" },
                    { title: "テトリス _ 重音テトSV(MP3_128K).mp3", duration: "03:05", src: "テトリス _ 重音テトSV(MP3_128K).mp3" },
                    { title: "バゥムクゥヘン・エンドロゥル _ 雨良 feat.初音ミクVS重音テトVS亞北ネル(Baumkuchen End Credits _ Amala ft.Miku vs Teto vs Ner(MP3_1.mp3", duration: "03:05", src: "バゥムクゥヘン・エンドロゥル _ 雨良 feat.初音ミクVS重音テトVS亞北ネル(Baumkuchen End Credits _ Amala ft.Miku vs Teto vs Ner(MP3_1.mp3" },
                    { title: "メズマライザー _ 初音ミク・重音テトSV - Mesmerizer(MP3_128K).mp3", duration: "03:05", src: "メズマライザー _ 初音ミク・重音テトSV - Mesmerizer(MP3_128K).mp3" },
                    { title: "ライアーダンサー_重音テトSV(MP3_128K).mp3", duration: "03:05", src: "ライアーダンサー_重音テトSV(MP3_128K).mp3" },
                    { title: "弱者のマーチ _ 重音テト(MP3_128K).mp3", duration: "03:05", src: "弱者のマーチ _ 重音テト(MP3_128K).mp3" },
                    { title: "混沌ブギ _ jon-YAKITORY_ 初音ミク -Konton Boogie _ jon-YAKITORY_ Hatsune Miku-(MP3_128K).mp3", duration: "03:05", src: "混沌ブギ _ jon-YAKITORY_ 初音ミク -Konton Boogie _ jon-YAKITORY_ Hatsune Miku-(MP3_128K).mp3" },
                    { title: "目撃_テト31世🎉🎃🧟雨衣・重音テト(MP3_128K).mp3", duration: "03:05", src: "目撃_テト31世🎉🎃🧟雨衣・重音テト(MP3_128K).mp3" }
                ]
            },
            {
                id: 2,
                title: "CARAVAN PALACE",
                artist: "CARAVAN PALACE",
                cover: "139-1397249_caravan-palace-albums.png",
                songs: [
                    { title: "Caravan Palace -  Reverse (Official Audio_Visualizer)(MP3_128K).mp3", duration: "03:30", src: "Caravan Palace -  Reverse (Official Audio_Visualizer)(MP3_128K).mp3" },
                    { title: "Caravan Palace - 81 Special (Official Audio)(MP3_128K).mp3", duration: "02:45", src: "Caravan Caravan Palace - 81 Special (Official Audio)(MP3_128K).mp3" },
                    { title: "Caravan Palace - Brotherswing(MP3_128K).mp3", duration: "02:10", src: "Caravan Palace - Brotherswing(MP3_128K).mp3" },
                    { title: "Caravan Palace - Wonderland(MP3_128K).mp3", duration: "04:12", src: "Caravan Palace - Wonderland(MP3_128K).mp3" },
                    { title: "Caravan Palace - Comics (Official MV)(MP3_128K).mp3", duration: "02:10", src: "Caravan Palace - Comics (Official MV)(MP3_128K).mp3" },
                    { title: "Caravan Palace - Lone Digger (Official MV)(MP3_128K).mp3", duration: "02:10", src: "Caravan Palace - Lone Digger (Official MV)(MP3_128K).mp3" },
                    { title: "Caravan Palace - Panic(MP3_128K).mp3", duration: "02:10", src: "Caravan Palace - Panic(MP3_128K).mp3" },
                    { title: "Caravan Palace - Mirrors (Official MV)(MP3_128K).mp3", duration: "02:10", src: "Caravan Palace - Mirrors (Official MV)(MP3_128K).mp3" },
                    { title: "Caravan Palace - Moonshine (Official Video)(MP3_128K).mp3", duration: "02:10", src: "Caravan Palace - Moonshine (Official Video)(MP3_128K).mp3" },
                    { title: "Caravan Palace - Dramophone (Official MV)(MP3_128K).mp3", duration: "02:10", src: "Caravan Palace - Dramophone (Official MV)(MP3_128K).mp3" }
                ]
            },
            {
                id: 3,
                title: "ENIMEM",
                artist: "ENIMEM",
                cover: "250px-Eminem_(cropped).jpg_utm_source=en.wikipedia.png",
                songs: [
                    { title: "Eminem - My Name Is (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Eminem - My Name Is (Official Music Video)(MP3_128K).mp3" },
                    { title: "Eminem ft. Rihanna - The Monster (Explicit) [Official Video](MP3_128K).mp3", duration: "03:40", src: "Eminem ft. Rihanna - The Monster (Explicit) [Official Video](MP3_128K).mp3" },
                    { title: "Eminem - Without Me (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Eminem - Without Me (Official Music Video)(MP3_128K).mp3" },
                    { title: "Eminem - Rap God (Explicit)(MP3_128K).mp3", duration: "02:50", src: "Eminem - Rap God (Explicit)(MP3_128K).mp3" },
                    { title: "Eminem - Houdini [Official Music Video](MP3_128K).mp3", duration: "02:50", src: "Eminem - Houdini [Official Music Video](MP3_128K).mp3" },
                    { title: "Eminem - Shake That (Official Music Video) ft. Nate Dogg(MP3_128K).mp3", duration: "02:50", src: "Eminem - Shake That (Official Music Video) ft. Nate Dogg(MP3_128K).mp3" },
                    { title: "Eminem - Mockingbird [Official Music Video](MP3_128K).mp3", duration: "02:50", src: "Eminem - Mockingbird [Official Music Video](MP3_128K).mp3" }
                ]
            },
            {
                id: 4,
                title: "ALAN WALKER",
                artist: "ALAN WALKER",
                cover: "ALAN WALKER .PNG.png",
                songs: [
                    { title: "Alan Walker - Alone(MP3_128K).mp3", duration: "02:15", src: "Alan Walker - Alone(MP3_128K).mp3" },
                    { title: "Alan Walker - Faded(MP3_128K).mp3", duration: "03:10", src: "Alan Walker - Faded(MP3_128K).mp3" },
                    { title: "Alan Walker - Force(MP3_128K).mp3", duration: "02:15", src: "Alan Walker - Force(MP3_128K).mp3" },
                    { title: "Alan Walker - Sing Me To Sleep(MP3_128K).mp3", duration: "02:15", src: "Alan Walker - Sing Me To Sleep(MP3_128K).mp3" },
                    { title: "ALAN Alan Walker - Darkside (feat. Au_Ra and Tomine Harket)(MP3_128K).mp3", duration: "02:15", src: "Alan Walker - Darkside (feat. Au_Ra and Tomine Harket)(MP3_128K).mp3" },
                    { title: "ALAN Alan Walker - Diamond Heart (feat. Sophia Somajo)(MP3_128K).mp3", duration: "02:15", src: "Alan Walker - Diamond Heart (feat. Sophia Somajo)(MP3_128K).mp3" },
                    { title: "Alan Walker _ Ava Max - Alone_ Pt. II(MP3_128K).mp3", duration: "02:15", src: "Alan Walker _ Ava Max - Alone_ Pt. II(MP3_128K).mp3" },
                    { title: "Alan Walker - The Drum (Official Music Video)(MP3_128K).mp3", duration: "02:15", src: "Alan Walker - The Drum (Official Music Video)(MP3_128K).mp3" },
                    { title: "Alan x Walkers feat. SAPPHIRE - Unity(MP3_128K).mp3", duration: "02:15", src: "Alan x Walkers feat. SAPPHIRE - Unity(MP3_128K).mp3" },
                    { title: "Alan Walker_ K-391_ Tungevaag_ Mangoo - PLAY (Alan Walker_s Video)(MP3_128K).mp3", duration: "02:15", src: "Alan Walker_ K-391_ Tungevaag_ Mangoo - PLAY (Alan Walker_s Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 5,
                title: "MARSHMELLO",
                artist: "MARSHMELLO",
                cover: "marshmello-helmet-portrait-9ow4cm2wjtgt4pwf.png",
                songs: [
                    { title: "Marshmello - Alone (Official Music Video)(MP3_128K).mp3", duration: "04:00", src: "Marshmello - Alone (Official Music Video)(MP3_128K).mp3" },
                    { title: "Marshmello - Blocks (Official Music Video)(MP3_128K).mp3", duration: "02:30", src: "Marshmello - Blocks (Official Music Video)(MP3_128K).mp3" },
                    { title: "Marshmello - FLY (Official Music Video)(MP3_128K).mp3", duration: "03:15", src: "Marshmello - FLY (Official Music Video)(MP3_128K).mp3" },
                    { title: "Marshmello _ Anne-Marie - FRIENDS (Music Video) _OFFICIAL FRIENDZONE ANTHEM_(MP3_128K).mp3", duration: "04:00", src: "Marshmello _ Anne-Marie - FRIENDS (Music Video) _OFFICIAL FRIENDZONE ANTHEM_(MP3_128K).mp3" },
                    { title: "Marshmello - Moving On (Official Music Video)(MP3_128K).mp3", duration: "04:00", src: "Marshmello - Moving On (Official Music Video)(MP3_128K).mp3" },
                    { title: "Marshmello _ Logic - EVERYDAY (Official Music Video)(MP3_128K).mp3", duration: "04:00", src: "Marshmello _ Logic - EVERYDAY (Official Music Video)(MP3_128K).mp3" },
                    { title: "Marshmello - Keep it Mello ft. Omar LinX (Official Music Video)(MP3_128K).mp3", duration: "04:00", src: "Marshmello - Keep it Mello ft. Omar LinX (Official Music Video)(MP3_128K).mp3" },
                    { title: "Marshmello - Silence Ft. Khalid (Official Music Video)(MP3_128K).mp3", duration: "04:00", src: "Marshmello - Silence Ft. Khalid (Official Music Video)(MP3_128K).mp3" },
                    { title: "Marshmello - LoVe U (Official Music Video)(MP3_128K).mp3", duration: "04:00", src: "Marshmello - LoVe U (Official Music Video)(MP3_128K).mp3" },
                    { title: "Marshmello - Take It Back (Official Music Video)(MP3_128K).mp3", duration: "04:00", src: "Marshmello - Take It Back (Official Music Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 6,
                title: "NICOLO PAGANINI",
                artist: "NICOLO PAGANINI",
                cover: "NICOLO PAGANINI.png",
                songs: [
                    { title: "Niccolò Paganini - Caprice No. 24(MP3_128K).mp3", duration: "03:22", src: "Niccolò Paganini - Caprice No. 24(MP3_128K).mp3" },
                    { title: "Niccolo Paganini -  La campanella(MP3_128K).mp3", duration: "02:05", src: "Niccolo Paganini -  La campanella(MP3_128K).mp3" },
                    { title: "Niccolo Paganini - Caprice N° 5 - The Devil_s Violinist(MP3_128K).mp3", duration: "03:22", src: "Niccolo Paganini - Caprice N° 5 - The Devil_s Violinist(MP3_128K).mp3" },
                    { title: "Vivaldi - Violin Concerto in A Minor RV356(MP3_128K).mp3", duration: "03:22", src: "Vivaldi - Violin Concerto in A Minor RV356(MP3_128K).mp3" },
                    { title: "Vivaldi Four Seasons_ Winter (L_Inverno)_ original version. Freivogel _ Voices of Music  RV 297 4K(MP3_128K).mp3", duration: "03:22", src: "Vivaldi Four Seasons_ Winter (L_Inverno)_ original version. Freivogel _ Voices of Music  RV 297 4K(MP3_128K).mp3" },
                    { title: "Beethoven - Symphony No.7 in A major op.92 - II_ Allegretto(MP3_128K).mp3", duration: "03:22", src: "Beethoven - Symphony No.7 in A major op.92 - II_ Allegretto(MP3_128K).mp3" }
                ]
            },
            {
                id: 7,
                title: "REPULSIVE",
                artist: "REPULSIVE",
                cover: "REPULSIVE. PNG.png",
                songs: [
                    { title: "REPULSIVE - Aftersome [COPYRIGHT FREE DARK MUSIC](M4A_128K).m4a", duration: "03:45", src: "REPULSIVE - Aftersome [COPYRIGHT FREE DARK MUSIC](M4A_128K).m4a" },
                    { title: "REPULSIVE - 1908 Vol. II [COPYRIGHT FREE DARK MUSIC](M4A_128K).m4a", duration: "02:55", src: "REPULSIVE - 1908 Vol. II [COPYRIGHT FREE DARK MUSIC](M4A_128K) (2).m4a" },
                    { title: "REPULSIVE - Akane_s  Regret [COPYRIGHT FREE DARK MUSIC](M4A_128K).m4a", duration: "04:20", src: "REPULSIVE - Akane_s  Regret [COPYRIGHT FREE DARK MUSIC](M4A_128K).m4a" },
                    { title: "REPULSIVE - I_ll Hide My Scars [COPYRIGHT FREE DARK MUSIC](M4A_128K).m4a", duration: "02:55", src: "REPULSIVE - I_ll Hide My Scars [COPYRIGHT FREE DARK MUSIC](M4A_128K) (2).m4a" },
                    { title: "IN In The Dark(M4A_128K).m4a", duration: "02:55", src: "IN In The Dark(M4A_128K).m4a" },
                    { title: "i was only temporary (Super Slowed)(M4A_128K).m4a", duration: "02:55", src: "i was only temporary (Super Slowed)(M4A_128K).m4a" }
                ]
            },
            {
                id: 8,
                title: "STEVE AOKI",
                artist: "STEVE AOKI",
                cover: "steve-aoki-logo-png_seeklogo-324328.png",
                songs: [
                    { title: "Steve Aoki _ Laidback Luke ft. Lil Jon - Turbulence(MP3_128K).mp3", duration: "03:10", src: "Steve Aoki _ Laidback Luke ft. Lil Jon - Turbulence(MP3_128K).mp3" },
                    { title: "Steve Aoki_ Chris Lake _ Tujamo - Boneless (Official Video)(MP3_128K).mp3", duration: "03:50", src: "Steve Aoki_ Chris Lake _ Tujamo - Boneless (Official Video)(MP3_128K).mp3" },
                    { title: "Dimitri Vegas _ Like Mike_ Steve Aoki vs Ummet Ozcan - Melody(MP3_128K).mp3", duration: "03:50", src: "Dimitri Vegas _ Like Mike_ Steve Aoki vs Ummet Ozcan - Melody(MP3_128K).mp3" },
                    { title: "Armin van Buuren - Blah Blah Blah (Official Lyric Video)(MP3_128K).mp3", duration: "03:50", src: "Armin van Buuren - Blah Blah Blah (Official Lyric Video)(MP3_128K).mp3" },
                    { title: "Deorro - Five Hours (Static Video) [LE7ELS](MP3_128K).mp3", duration: "03:50", src: "Deorro - Five Hours (Static Video) [LE7ELS](MP3_128K).mp3" }
                ]
            },
            {
                id: 9,
                title: "LEAGUE OF LEGENDS",
                artist: "LEAGUE OF LEGENDS",
                cover: "League-of-Legends-PNG-HD-256x225.png",
                songs: [
                    { title: "Legends Never Die (ft. Against The Current) [OFFICIAL AUDIO] _ Worlds 2017 - League of Legends(MP3_128K).mp3", duration: "03:30", src: "Legends Never Die (ft. Against The Current) [OFFICIAL AUDIO] _ Worlds 2017 - League of Legends(MP3_128K).mp3" },
                    { title: "Warriors (ft. Imagine Dragons) _ Worlds 2014 - League of Legends(MP3_128K).mp3", duration: "04:05", src: "Warriors (ft. Imagine Dragons) _ Worlds 2014 - League of Legends(MP3_128K).mp3" },
                    { title: "Worlds Collide (ft. Nicki Taylor) _ Worlds 2015 - League of Legends(MP3_128K).mp3", duration: "04:05", src: "Worlds Collide (ft. Nicki Taylor) _ Worlds 2015 - League of Legends(MP3_128K).mp3" },
                    { title: "Know My Name ft. True Damage (Official Music Video) _ League of Legends Worlds 2026 Anthem(MP3_128K).mp3", duration: "04:05", src: "Know My Name ft. True Damage (Official Music Video) _ League of Legends Worlds 2026 Anthem(MP3_128K).mp3" }

                ]
            },
            {
                id: 10,
                title: "PHNOK",
                artist: "PONK",
                cover: "https://images.unsplash.com/photo-1514525253161-7a46d19cd819?q=80&w=400&auto=format&fit=crop",
                songs: [
                    { title: "KORDHELL - KILLERS FROM THE NORTHSIDE(MP3_128K).mp3", duration: "02:40", src: "KORDHELL - KILLERS FROM THE NORTHSIDE(MP3_128K).mp3" },
                    { title: "KORDHELL - MURDER IN MY MIND(MP3_128K).mp3", duration: "03:15", src: "KORDHELL - MURDER IN MY MIND(MP3_128K).mp3" },
                    { title: "MIDNIGHT(MP3_128K).mp3", duration: "04:30", src: "MIDNIGHT(MP3_128K).mp3" },
                    { title: "NEON BLADE(MP3_128K).mp3", duration: "04:30", src: "NEON BLADE(MP3_128K).mp3" },
                    { title: "Crystals(MP3_128K).mp3", duration: "04:30", src: "Crystals(MP3_128K).mp3" },
                    { title: "", duration: "04:30", src: "NEON BLADE(MP3_128K).mp3" }
                ]
            },
            {
                id: 11,
                title: "GHOST",
                artist: "GHOST",
                cover: "KnuckleBonz-Papa-IV-Robes-19.png",
                songs: [
                    { title: "Ghost - Umbra (Official Music Video)(MP3_160K).mp3", duration: "02:40", src: "Ghost - Umbra (Official Music Video)(MP3_160K).mp3" },
                    { title: "Ghost - Zenith (Official Audio)(MP3_160K)_1.mp3", duration: "03:15", src: "Ghost - Zenith (Official Audio)(MP3_160K)_1.mp3" },
                    { title: "Ghost - Cirice (Official Music Video)(MP3_160K) (2).mp3", duration: "04:30", src: "Ghost - Cirice (Official Music Video)(MP3_160K) (2).mp3" },
                    { title: "Ghost - Dance Macabre (Official Music Video)(MP3_160K).mp3", duration: "04:30", src: "Ghost - Dance Macabre (Official Music Video)(MP3_160K).mp3" },
                    { title: "Ghost - Lachryma (Official Music Video)(MP3_160K) (2).mp3", duration: "04:30", src: "Ghost - Lachryma (Official Music Video)(MP3_160K) (2).mp3" },
                    { title: "Ghost - Rats (Official Music Video)(M4A_128K).m4a", duration: "04:30", src: "Ghost - Rats (Official Music Video)(M4A_128K).m4a" },
                    { title: "Ghost - Satanized (Official Music Video)(MP3_160K) (2).mp3", duration: "04:30", src: "Ghost - Satanized (Official Music Video)(MP3_160K) (2).mp3" },
                    { title: "Ghost - Spillways (Official Music Video)(MP3_160K) (2).mp3", duration: "04:30", src: "Ghost - Satanized (Official Music Video)(MP3_160K) (2).mp3" },
                    { title: "Ghost - Square Hammer (Official Music Video)(MP3_160K) (2).mp3", duration: "04:30", src: "Ghost - Square Hammer (Official Music Video)(MP3_160K) (2).mp3" },
                    { title: "Ghost - Jigolo Har Megiddo (Subtitulado en Español)(MP3_160K) (2).mp3", duration: "04:30", src: "Ghost - Jigolo Har Megiddo (Subtitulado en Español)(MP3_160K) (2).mp3" },
                    { title: "Ghost - The Future Is A Foreign Land (Official Music Video)(MP3_160K) (2).mp3", duration: "04:30", src: "Ghost - The Future Is A Foreign Land (Official Music Video)(MP3_160K) (2).mp3" }

                ]
            },
            {
                id: 12,
                title: "STAR WARS",
                artist: "STAR WARS",
                cover: "star-wars-galaxy-of-sounds-disneyplys-featured_63dcac0c.png",
                songs: [
                    { title: "Star Wars- The Imperial March (Darth Vader_s Theme)(MP3_128K).mp3", duration: "02:50", src: "Star Wars- The Imperial March (Darth Vader_s Theme)(MP3_128K).mp3" },
                    { title: "Jedi temple march loop [marching sound edit](MP3_128K).mp3", duration: "02:50", src: "Jedi temple march loop [marching sound edit](MP3_128K).mp3" },
                    { title: "Separatist droids march loop [marching sound edit](MP3_128K).mp3", duration: "02:50", src: "Separatist droids march loop [marching sound edit](MP3_128K).mp3" },
                    { title: "Battle of the Heroes(MP3_128K).mp3", duration: "02:50", src: "Battle of the Heroes(MP3_128K).mp3" },
                    { title: "The Empire Strikes Back - Lando_s Palace(MP3_128K).mp3", duration: "02:50", src: "The Empire Strikes Back - Lando_s Palace(MP3_128K).mp3" },
                    { title: "General Grievous theme [marching sound edit](MP3_128K).mp3", duration: "02:50", src: "General Grievous theme [marching sound edit](MP3_128K).mp3" },
                    { title: "Yoda_s Theme(MP3_128K).mp3", duration: "02:50", src: "Yoda_s Theme(MP3_128K).mp3" }
                ]
            },
            {
                id: 13,
                title: "MORENADA",
                artist: "MORENADA",
                cover: "Morenada-BOLIVIA-1.png",
                songs: [
                    { title: "JACH_A MALLKU - _Cecilia_  (Morenada)(MP3_128K).mp3", duration: "02:50", src: "JACH_A MALLKU - _Cecilia_  (Morenada)(MP3_128K).mp3" },
                    { title: "TODO SE PAGA - MARA - PODEROSA Y ÚNICA MORENADA CENTRAL JULIACA(MP3_128K).mp3", duration: "03:40", src: "TODO SE PAGA - MARA - PODEROSA Y ÚNICA MORENADA CENTRAL JULIACA(MP3_128K).mp3" },
                    { title: "MARIA JUANA - IDILIO (MORENADA)(MP3_128K).mp3", duration: "02:50", src: "MARIA JUANA - IDILIO (MORENADA)(MP3_128K).mp3" },
                    { title: "MARIA JUANA - VETE POR DONDE VINISTE (MORENADA)(MP3_128K).mp3", duration: "02:50", src: "MARIA JUANA - VETE POR DONDE VINISTE (MORENADA)(MP3_128K).mp3" },
                    { title: "LLAJTAYMANTA - _Brujita_  (Morenada)(MP3_128K).mp3", duration: "02:50", src: "LLAJTAYMANTA - _Brujita_  (Morenada)(MP3_128K).mp3" },
                    { title: "Aullagas  - Linda Morenita (Morenada) L y M. Wilson Aguirre V.(MP3_128K).mp3", duration: "02:50", src: "Aullagas  - Linda Morenita (Morenada) L y M. Wilson Aguirre V.(MP3_128K).mp3" },
                    { title: "ETERNO. suena bien  ETERNO AMOR  Morenada 2026 video clip oficial(MP3_128K).mp3", duration: "02:50", src: "ETERNO. suena bien  ETERNO AMOR  Morenada 2026 video clip oficial(MP3_128K).mp3" }
                ]
            },
            {
                id: 14,
                title: "WALTZ",
                artist: "WALTZ",
                cover: "magical-ballroom-dance-stockcake.png",
                songs: [
                    { title: "Dmitri Shostakovich - Waltz No. 2(MP3_128K).mp3", duration: "02:50", src: "Dmitri Shostakovich - Waltz No. 2(MP3_128K).mp3" },
                    { title: "Tchaikovsky - Valse Sentimentale(MP3_128K).mp3", duration: "03:40", src: "Tchaikovsky - Valse Sentimentale(MP3_128K).mp3" },
                    { title: "Arthur Rubinstein - Frederic Chopin_ Waltz Op. 34 No.2 in A minor(MP3_128K).mp3", duration: "02:50", src: "Arthur Rubinstein - Frederic Chopin_ Waltz Op. 34 No.2 in A minor(MP3_128K).mp3" },
                    { title: "Piotr Ilich Tchaikovsky - Waltz of the Flowers from _The Nutcracker_(MP3_128K).mp3", duration: "02:50", src: "Piotr Ilich Tchaikovsky - Waltz of the Flowers from _The Nutcracker_(MP3_128K).mp3" },
                    { title: "Viennese Waltz (original song)(MP3_128K).mp3", duration: "02:50", src: "Viennese Waltz (original song)(MP3_128K).mp3" },
                    { title: "Johannes Brahms - Waltz in A-flat Major_ Op. 39_ No. 15(MP3_128K).mp3", duration: "02:50", src: "Johannes Brahms - Waltz in A-flat Major_ Op. 39_ No. 15(MP3_128K).mp3" },
                    { title: "Chopin- Waltz no. 7 in C sharp minor_ Op. 64 no. 2(MP3_128K).mp3", duration: "02:50", src: "Chopin- Waltz no. 7 in C sharp minor_ Op. 64 no. 2(MP3_128K).mp3" }
                ]
            },
            {
                id: 15,
                title: "LADY GAGA",
                artist: "LADY GAGA",
                cover: "lady-gaga-768-x-1005-picture-n554nl1t4grw7nxw.png",
                songs: [
                    { title: "Lady Gaga - Applause(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - Applause(MP3_128K).mp3" },
                    { title: "Lady Gaga - Abracadabra (Official Music Video)(MP3_128K).mp3", duration: "03:40", src: "" },
                    { title: "Abracadabra(MP3_128K).mp3", duration: "02:50", src: "Abracadabra(MP3_128K).mp3" },
                    { title: "Lady Gaga - Alejandro (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - Alejandro (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lady Gaga - Bloody Mary (Official Audio)(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - Bloody Mary (Official Audio)(MP3_128K).mp3" },
                    { title: "Lady Gaga - Judas (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - Judas (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lady Gaga - Paparazzi (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - Paparazzi (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lady Gaga - Bad Romance (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - Bad Romance (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lady Gaga - LoveGame (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - LoveGame (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lady Gaga - Poker Face (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - Poker Face (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lady Lady Gaga - Just Dance (Official Music Video) ft. Colby O_Donis(MP3_128K).mp3", duration: "02:50", src: "Lady Gaga - Just Dance (Official Music Video) ft. Colby O_Donis(MP3_128K).mp3" }
                ]
            },
            {
                id: 16,
                title: "EPICAS BATALLAS DEL RAP",
                artist: "EPICAS BATALLAS DEL RAP",
                cover: "EPICAS BATALLAS.png",
                songs: [
                    { title: "Finn vs Mordecai. Épicas Batallas de Rap del Frikismo _ Keyblade ft. Kinox y Vau Boy(MP3_128K).mp3", duration: "02:50", src: "Finn vs Mordecai. Épicas Batallas de Rap del Frikismo _ Keyblade ft. Kinox y Vau Boy(MP3_128K).mp3" },
                    { title: "Mickey Mouse vs Bugs Bunny. Épicas Batallas de Rap del Frikismo S2 _ Keyblade(MP3_128K).mp3", duration: "03:40", src: "Mickey Mouse vs Bugs Bunny. Épicas Batallas de Rap del Frikismo S2 _ Keyblade(MP3_128K).mp3" },
                    { title: "DEADPOOL VS SPIDERMAN RAP _ Kronno Zomber_ Cyclo _ Zarcort (Video Oficial)(MP3_128K).mp3", duration: "02:50", src: "DEADPOOL VS SPIDERMAN RAP _ Kronno Zomber_ Cyclo _ Zarcort (Video Oficial)(MP3_128K).mp3" },
                    { title: "Deadpool vs Deathstroke. Épicas Batallas de Rap del Frikismo _ Keyblade(MP3_128K).mp3", duration: "02:50", src: "Deadpool vs Deathstroke. Épicas Batallas de Rap del Frikismo _ Keyblade(MP3_128K).mp3" },
                    { title: "Eren vs Kirito. Épicas Batallas de Rap del Frikismo _ Keyblade(MP3_128K).mp3", duration: "02:50", src: "Eren vs Kirito. Épicas Batallas de Rap del Frikismo _ Keyblade(MP3_128K).mp3" },
                    { title: "Freddy Krueger vs Jason Voorhees. Batalla de Rap _ Keyblade(MP3_128K).mp3", duration: "02:50", src: "Freddy Krueger vs Jason Voorhees. Batalla de Rap _ Keyblade(MP3_128K).mp3" },
                    { title: "Light Yagami vs Lelouch Lamperouge. Épicas Batallas de Rap del Frikismo S2 _ Keyblade(MP3_128K).mp3", duration: "02:50", src: "Light Yagami vs Lelouch Lamperouge. Épicas Batallas de Rap del Frikismo S2 _ Keyblade(MP3_128K).mp3" },
                    { title: "CREEPER VS ZOMBIE _ ESPECIAL 1 MILLÓN _ PARTE 2(MP3_128K).mp3", duration: "02:50", src: "CREEPER VS ZOMBIE _ ESPECIAL 1 MILLÓN _ PARTE 2(MP3_128K).mp3" },
                    { title: "Saitama vs Izuku Midoriya. Épicas Batallas de Rap del Frikismo S2 _ Keyblade(MP3_128K).mp3", duration: "02:50", src: "Saitama vs Izuku Midoriya. Épicas Batallas de Rap del Frikismo S2 _ Keyblade(MP3_128K).mp3" },
                    { title: "DRAGON BALL SUPER BROLY RAP _ PORTA _ VIDEO OFICIAL RESUBIDO(MP3_128K).mp3", duration: "02:50", src: "DRAGON BALL SUPER BROLY RAP _ PORTA _ VIDEO OFICIAL RESUBIDO(MP3_128K).mp3" }
                ]
            },
            {
                id: 17,
                title: "IRONMOUSE",
                artist: "IRONMOSUE",
                cover: "IRONMOSE.png",
                songs: [
                    { title: "Cry For Me WA WA WA(MP3_128K).mp3", duration: "02:50", src: "Cry For Me WA WA WA(MP3_128K).mp3" },
                    { title: "Hell Again(MP3_128K).mp3", duration: "03:40", src: "Hell Again(MP3_128K).mp3" },
                    { title: "Sour Taste(MP3_128K).mp3", duration: "02:50", src: "Sour Taste(MP3_128K).mp3" },
                    { title: "Abracadabra(MP3_128K).mp3", duration: "02:50", src: "Abracadabra(MP3_128K).mp3" },
                    { title: "Time To Feast(MP3_128K).mp3", duration: "02:50", src: "Time To Feast(MP3_128K).mp3" },
                    { title: "Meow(MP3_128K).mp3", duration: "02:50", src: "Meow(MP3_128K).mp3" },
                    { title: "Umbrella(MP3_128K).mp3", duration: "02:50", src: "Umbrella(MP3_128K).mp3" },
                    { title: "Aishite Aishite Aishite (愛して愛して愛して) - Kikuo (Cover)(MP3_128K).mp3", duration: "02:50", src: "Aishite Aishite Aishite (愛して愛して愛して) - Kikuo (Cover)(MP3_128K).mp3" },
                    { title: "Stay Focused - Ironmouse(MP3_128K).mp3", duration: "02:50", src: "Stay Focused - Ironmouse(MP3_128K).mp3" },
                    { title: "HOLLOW HUNGER(MP3_128K).mp3", duration: "02:50", src: "HOLLOW HUNGER(MP3_128K).mp3" }
                ]
            },
            {
                id: 18,
                title: "TITANES DEL PACIFICO",
                artist: "TITANES DEL PACIFICO",
                cover: "TITANES.png",
                songs: [
                    { title: "PACIFIC RIM - _My Demons_ - Starset (Music Video)(MP3_128K).mp3", duration: "02:50", src: "PACIFIC RIM - _My Demons_ - Starset (Music Video)(MP3_128K).mp3" },
                    { title: "Pacific Rim Courtesy Call(MP3_128K).mp3", duration: "03:40", src: "Pacific Rim Courtesy Call(MP3_128K).mp3" },
                    { title: "Pacific Rim _ Skillet - Rise(MP3_128K).mp3", duration: "02:50", src: "Pacific Rim _ Skillet - Rise(MP3_128K).mp3" },
                    { title: "Pacific Rim - A light that never comes(MP3_128K).mp3", duration: "02:50", src: "Pacific Rim - A light that never comes(MP3_128K).mp3" },
                    { title: "Pacific Rim Skillet Whispers In The Dark(MP3_128K).mp3", duration: "02:50", src: "Pacific Rim Skillet Whispers In The Dark(MP3_128K).mp3" },
                    { title: "Pacific Rim _ Music Video _ Feel Invincible _ SKILLET(MP3_128K).mp3", duration: "02:50", src: "Pacific Rim _ Music Video _ Feel Invincible _ SKILLET(MP3_128K).mp3" },
                    { title: "Pacific Rim Tribute _ Believer - Imagine Dragons _ Music Video(MP3_128K).mp3", duration: "02:50", src: "Pacific Rim Tribute _ Believer - Imagine Dragons _ Music Video(MP3_128K).mp3" },
                    { title: "Pacific Rim_ The Black [AMV] Stronger (The Score) _ Marshall Studios(MP3_128K).mp3", duration: "02:50", src: "Pacific Rim_ The Black [AMV] Stronger (The Score) _ Marshall Studios(MP3_128K).mp3" },
                    { title: "__Pacific Rim 2__「HERO」(MP3_128K).mp3", duration: "02:50", src: "__Pacific Rim 2__「HERO」(MP3_128K).mp3" },
                    { title: "Titanes del Pacifico_Immortals(MP3_128K).mp3", duration: "02:50", src: "Titanes del Pacifico_Immortals(MP3_128K).mp3" },
                    { title: "", duration: "02:50", src: "" }
                ]
            },
            {
                id: 19,
                title: "RAMMSTEIN",
                artist: "RAMMSTEIN",
                cover: "RAMMSTEIN.png",
                songs: [
                    { title: "Rammstein - Feuer Frei_ (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Rammstein - Feuer Frei_ (Official Video)(MP3_128K).mp3" },
                    { title: "Rammstein - Ich Will (Official Video)(MP3_128K).mp3", duration: "03:40", src: "Rammstein - Ich Will (Official Video)(MP3_128K).mp3" },
                    { title: "Rammstein - Amerika (Official 4K Video)(MP3_128K).mp3", duration: "02:50", src: "Rammstein - Amerika (Official 4K Video)(MP3_128K).mp3" },
                    { title: "Rammstein - Mein Herz Brennt (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Rammstein - Mein Herz Brennt (Official Video)(MP3_128K).mp3" },
                    { title: "Rammstein - Du Hast (Official 4K Video)(MP3_128K).mp3", duration: "02:50", src: "Rammstein - Du Hast (Official 4K Video)(MP3_128K).mp3" },
                    { title: "Rammstein - Du Riechst So Gut _98 (Official 4K Video)(MP3_128K).mp3", duration: "02:50", src: "Rammstein - Du Riechst So Gut _98 (Official 4K Video)(MP3_128K).mp3" },
                    { title: "Sonne(MP3_128K).mp3", duration: "02:50", src: "Sonne(MP3_128K).mp3" },
                    { title: "Deutschland(MP3_128K).mp3", duration: "02:50", src: "Deutschland(MP3_128K).mp3" },
                    { title: "Rein raus(MP3_128K).mp3", duration: "02:50", src: "Rein raus(MP3_128K).mp3" },
                    { title: "Engel(MP3_128K).mp3", duration: "02:50", src: "Engel(MP3_128K).mp3" }
                ]
            },
            {
                id: 20,
                title: "JUANES",
                artist: "JUANES",
                cover: "JUANES.png",
                songs: [
                    { title: "Juanes - Es Por Ti(MP3_128K).mp3", duration: "02:50", src: "Juanes - Es Por Ti(MP3_128K).mp3" },
                    { title: "Juanes - La Camisa Negra(MP3_128K).mp3", duration: "03:40", src: "Juanes - La Camisa Negra(MP3_128K).mp3" },
                    { title: "Juanes - Para Tu Amor(MP3_128K).mp3", duration: "02:50", src: "Juanes - Para Tu Amor(MP3_128K).mp3" },
                    { title: "Juanes - A Dios Le Pido(MP3_128K).mp3", duration: "02:50", src: "Juanes - A Dios Le Pido(MP3_128K).mp3" },
                    { title: "Juanes - Nada Valgo Sin Tu Amor(MP3_128K).mp3", duration: "02:50", src: "Juanes - Nada Valgo Sin Tu Amor(MP3_128K).mp3" },
                    { title: "Juanes - Me Enamora (Official Music Video)(MP3_128K).mp3", duration: "02:50", src: "Juanes - Me Enamora (Official Music Video)(MP3_128K).mp3" },
                    { title: "Mala Gente(MP3_128K).mp3", duration: "02:50", src: "Mala Gente(MP3_128K).mp3" },
                    { title: "Café Tacvba - Eres (Video Oficial)(MP3_128K).mp3", duration: "02:50", src: "Café Tacvba - Eres (Video Oficial)(MP3_128K).mp3" },
                    { title: "La Paga(MP3_128K).mp3", duration: "02:50", src: "La Paga(MP3_128K).mp3" }
                ]
            },
            {
                id: 21,
                title: "MODER TALKING",
                artist: "moder talking",
                cover: "moder talking.png",
                songs: [
                    { title: "Modern Talking - Brother Louie (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Modern Talking - Brother Louie (Official Video)(MP3_128K).mp3" },
                    { title: "Modern Talking - Geronimo_s Cadillac (Video)(MP3_128K).mp3", duration: "03:40", src: "Modern Talking - Geronimo_s Cadillac (Video)(MP3_128K).mp3" },
                    { title: "Modern Talking - Cheri Cheri Lady (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Modern Talking - Cheri Cheri Lady (Official Video)(MP3_128K).mp3" },
                    { title: "Modern Talking - Atlantis Is Calling (S.O.S. For Love) (Video)(MP3_128K).mp3", duration: "02:50", src: "Modern Talking - Atlantis Is Calling (S.O.S. For Love) (Video)(MP3_128K).mp3" },
                    { title: "Modern Talking - You_re My Heart_ You_re My Soul (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Modern Talking - You_re My Heart_ You_re My Soul (Official Video)(MP3_128K).mp3" },
                    { title: "No Face_ No Name_ No Number (ZDF-Fernsehgarten 4.6.2000) (Live from ZDF-Fernsehgarten_ 2000)(MP3_128K).mp3", duration: "02:50", src: "No Face_ No Name_ No Number (ZDF-Fernsehgarten 4.6.2000) (Live from ZDF-Fernsehgarten_ 2000)(MP3_128K).mp3" },
                    { title: "Eurythmics_ Annie Lennox_ Dave Stewart - Sweet Dreams (Are Made Of This) (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Eurythmics_ Annie Lennox_ Dave Stewart - Sweet Dreams (Are Made Of This) (Official Video)(MP3_128K).mp3" },
                    { title: "Atlantis Is Calling (Die Hundertausend-PS-Show 06.09.1986) (Live from Die Hundertausend PS(MP3_128K).mp3", duration: "02:50", src: "Atlantis Is Calling (Die Hundertausend-PS-Show 06.09.1986) (Live from Die Hundertausend PS(MP3_128K).mp3" },
                    { title: "You Can Win If You Want (Wetten_ dass..._ 18.05.1985) (Live from Wetten_ dass...__ 1985)(MP3_128K).mp3", duration: "02:50", src: "You Can Win If You Want (Wetten_ dass..._ 18.05.1985) (Live from Wetten_ dass...__ 1985)(MP3_128K).mp3" }
                ]
            },
            {
                id: 22,
                title: "MICHAEL JACKSON",
                artist: "MICHAEL JACKSON",
                cover: "MICHAEL JACKSON.png",
                songs: [
                    { title: "Michael Jackson - Bad (Shortened Version)(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - Bad (Shortened Version)(MP3_128K).mp3" },
                    { title: "Michael Jackson - Chicago (Official Audio)(MP3_128K).mp3", duration: "03:40", src: "Michael Jackson - Chicago (Official Audio)(MP3_128K).mp3" },
                    { title: "Michael Jackson - Billie Jean (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - Billie Jean (Official Video)(MP3_128K).mp3" },
                    { title: "Michael Jackson - Earth Song (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - Earth Song (Official Video)(MP3_128K).mp3" },
                    { title: "Michael Jackson - Smooth Criminal (Official Video)(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - Smooth Criminal (Official Video)(MP3_128K).mp3" },
                    { title: "Michael Jackson - Heaven Can Wait (Official Audio)(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - Heaven Can Wait (Official Audio)(MP3_128K).mp3" },
                    { title: "Michael Jackson - They Don_t Care About Us(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - They Don_t Care About Us(MP3_128K).mp3" },
                    { title: "Michael Jackson - Thriller (Official Video - Shortened Version)(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - Thriller (Official Video - Shortened Version)(MP3_128K).mp3" },
                    { title: "Michael Jackson - Beat It (Official 4K Video)(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - Beat It (Official 4K Video)(MP3_128K).mp3" },
                    { title: "Michael Jackson - Remember the Time (Official Video - Upscaled)(MP3_128K).mp3", duration: "02:50", src: "Michael Jackson - Remember the Time (Official Video - Upscaled)(MP3_128K).mp3" }
                ]
            },
            {
                id: 23,
                title: "RE: ZERO",
                artist: "RE: ZERO",
                cover: "re-zero-pictures-b7a8ovol1bb09gc0.png",
                songs: [
                    { title: "Re_ZERO -Starting Life in Another World- Opening 2 _ Paradisus-Paradoxum(MP3_128K).mp3", duration: "02:50", src: "Re_ZERO -Starting Life in Another World- Opening 2 _ Paradisus-Paradoxum(MP3_128K).mp3" },
                    { title: "Re_Zero Kara Hajimeru Isekai Seikatsu OP Full(MP3_128K).mp3", duration: "03:40", src: "Re_Zero Kara Hajimeru Isekai Seikatsu OP Full(MP3_128K).mp3" },
                    { title: "Re_Zero Season 2 - Ending Full『Memento』by nonoc(MP3_128K).mp3", duration: "02:50", src: "Re_Zero Season 2 - Ending Full『Memento』by nonoc(MP3_128K).mp3" },
                    { title: "Re_Zero Season 2 - Opening Full『Realize』by Konomi Suzuki(MP3_128K).mp3", duration: "02:50", src: "Re_Zero Season 2 - Opening Full『Realize』by Konomi Suzuki(MP3_128K).mp3" },
                    { title: "Re_ZERO -Starting Life in Another World- Season 2 - Ending 2 _ Believe In You(MP3_128K).mp3", duration: "02:50", src: "Re_ZERO -Starting Life in Another World- Season 2 - Ending 2 _ Believe In You(MP3_128K).mp3" },
                    { title: "Paradisus-Paradoxum(MP3_128K).mp3", duration: "02:50", src: "" },
                    { title: "Paradisus-Paradoxum(MP3_128K).mp3", duration: "02:50", src: "" },
                    { title: "Recollect(MP3_128K).mp3", duration: "02:50", src: "Recollect(MP3_128K).mp3" },
                    { title: "Re_ZERO -Starting Re_ZERO -Starting Life in Another World- Season 2 - Opening _ Realize v2(MP3_128K).mp3", duration: "02:50", src: "" },
                    { title: "Ender Ember(MP3_128K).mp3", duration: "02:50", src: "Ender Ember(MP3_128K).mp3" }

                ]
            },
            {
                id: 24,
                title: "HARRY POTTER",
                artist: "HARRY POTTER",
                cover: "harry potter.png",
                songs: [
                    { title: "Prologue(MP3_128K).mp3", duration: "02:50", src: "Prologue(MP3_128K).mp3" },
                    { title: "Visit to the Zoo and Letters from Hogwarts(MP3_128K).mp3", duration: "03:40", src: "Visit to the Zoo and Letters from Hogwarts(MP3_128K).mp3" },
                    { title: "The Arrival of Baby Harry(MP3_128K).mp3", duration: "02:50", src: "The Arrival of Baby Harry(MP3_128K).mp3" },
                    { title: "Entry into the Great Hall and the Banquet(MP3_128K).mp3", duration: "02:50", src: "Entry into the Great Hall and the Banquet(MP3_128K).mp3" },
                    { title: "Diagon Alley and the Gringotts Vault(MP3_128K).mp3", duration: "02:50", src: "Diagon Alley and the Gringotts Vault(MP3_128K).mp3" },
                    { title: "Harry_s Wondrous World (Extended Version)(MP3_128K).mp3", duration: "02:50", src: "Harry_s Wondrous World (Extended Version)(MP3_128K).mp3" },
                    { title: "Hogwarts Forever_ and the Moving Stairs(MP3_128K).mp3", duration: "02:50", src: "Hogwarts Forever_ and the Moving Stairs(MP3_128K).mp3" },
                    { title: "Mr. Longbottom Flies(MP3_128K).mp3", duration: "02:50", src: "Mr. Longbottom Flies(MP3_128K).mp3" },
                    { title: "Platform Nine-and-Three-Quarters and the Journey to Hogwarts(MP3_128K).mp3", duration: "02:50", src: "Platform Nine-and-Three-Quarters and the Journey to Hogwarts(MP3_128K).mp3" }
                ]
            },
            {
                id: 25,
                title: "CABALLEROS DEL ZODIACO",
                artist: "CABALLEROS DEL ZODIACO",
                cover: "caballeros-del-zodiaco610_hzbr.png",
                songs: [
                    { title: "Caballeros del Zodiaco (saint seiya) ost 10(MP3_128K).mp3", duration: "02:50", src: "Caballeros del Zodiaco (saint seiya) ost 10(MP3_128K).mp3" },
                    { title: "Caballeros del Zodiaco (saint seiya) ost 4(MP3_128K).mp3", duration: "03:40", src: "Caballeros del Zodiaco (saint seiya) ost 4(MP3_128K).mp3" },
                    { title: "Caballeros del Zodiaco (saint seiya) ost 6(MP3_128K).mp3", duration: "02:50", src: "Caballeros del Zodiaco (saint seiya) ost 6(MP3_128K).mp3" },
                    { title: "Caballeros del Zodiaco (saint seiya) ost 8(MP3_128K).mp3", duration: "02:50", src: "Caballeros del Zodiaco (saint seiya) ost 8(MP3_128K).mp3" },
                    { title: "Caballeros del Zodiaco (saint seiya) ost 1 - Galactian War(MP3_128K).mp3", duration: "02:50", src: "Caballeros del Zodiaco (saint seiya) ost 1 - Galactian War(MP3_128K).mp3" },
                    { title: "Caballeros del Zodiaco (saint seiya) ost 2 - Shiru theme(MP3_128K).mp3", duration: "02:50", src: "Caballeros del Zodiaco (saint seiya) ost 2 - Shiru theme(MP3_128K).mp3" },
                    { title: "Caballeros del Zodiaco (saint seiya) ost 5 - Cosmos Theme(MP3_128K).mp3", duration: "02:50", src: "Caballeros del Zodiaco (saint seiya) ost 5 - Cosmos Theme(MP3_128K).mp3" },
                    { title: "Caballeros del Zodiaco (saint seiya) ost 11 - Seiya Pegasus theme(MP3_128K).mp3", duration: "02:50", src: "Caballeros del Zodiaco (saint seiya) ost 11 - Seiya Pegasus theme(MP3_128K).mp3" },
                    { title: "Los Caballeros del Zodiaco (saint seiya) ost 7(MP3_128K).mp3", duration: "02:50", src: "Los Caballeros del Zodiaco (saint seiya) ost 7(MP3_128K).mp3" },
                    { title: "Saint Seiya Sad Brothers  [Original Soundtrack](MP3_128K).mp3", duration: "02:50", src: "Saint Seiya Sad Brothers  [Original Soundtrack](MP3_128K).mp3" }
                ]
            },
            {
                id: 26,
                title: "KASANE TETO",
                artist: "KASANE TETO",
                cover: "teto.png",
                songs: [
                    { title: "처형박수 (Execution Clap) _ 카사네 테토(MP3_128K).mp3", duration: "03:22", src: "처형박수 (Execution Clap) _ 카사네 테토(MP3_128K).mp3" },
                    { title: "Kasane Teto - Teto territory(MP3_128K).mp3", duration: "02:05", src: "Kasane Teto - Teto territory(MP3_128K).mp3" },
                    { title: "[MV] ABM - _ヤラララ_ (YARARARA) feat. 重音テト(MP3_128K).mp3", duration: "03:22", src: "[MV] ABM - _ヤラララ_ (YARARARA) feat. 重音テト(MP3_128K).mp3" },
                    { title: "東京真中 -  ブレインロット feat. 重音テト(MP3_128K).mp3", duration: "03:22", src: "東京真中 -  ブレインロット feat. 重音テト(MP3_128K).mp3" },
                    { title: "イガク - 重音テト(MP3_128K).mp3", duration: "03:22", src: "イガク - 重音テト(MP3_128K).mp3" },
                    { title: "人マニア - 重音テト(MP3_128K).mp3", duration: "03:22", src: "人マニア - 重音テト(MP3_128K).mp3" }
                ]
            },
            {
                id: 27,
                title: "ADO",
                artist: "ADO",
                cover: "ADO.png",
                songs: [
                    { title: "Kura Kura(MP3_128K).mp3", duration: "03:22", src: "Kura Kura(MP3_128K).mp3" },
                    { title: "KokoroToIuNaNoFukakai(MP3_128K).mp3", duration: "02:05", src: "KokoroToIuNaNoFukakai(MP3_128K).mp3" },
                    { title: "Backlight (UTA from ONE PIECE FILM RED)(MP3_128K).mp3", duration: "03:22", src: "Backlight (UTA from ONE PIECE FILM RED)(MP3_128K).mp3" },
                    { title: "Show(MP3_128K).mp3", duration: "03:22", src: "Show(MP3_128K).mp3" },
                    { title: "Readymade(MP3_128K).mp3", duration: "03:22", src: "Readymade(MP3_128K).mp3" },
                    { title: "Ashura-chan(MP3_128K).mp3", duration: "03:22", src: "Ashura-chan(MP3_128K).mp3" },
                    { title: "Usseewa(MP3_128K).mp3", duration: "03:22", src: "Usseewa(MP3_128K).mp3" },
                    { title: "Gira Gira(MP3_128K).mp3", duration: "03:22", src: "Gira Gira(MP3_128K).mp3" }
                ]
            },
            {
                id: 28,
                title: "JOSE JOSE",
                artist: "JOSE JOSE",
                cover: "josejose.png",
                songs: [
                    { title: "José José - La Nave del Olvido (Revisitado [Lyric Video])(MP3_128K).mp3", duration: "03:22", src: "José José - La Nave del Olvido (Revisitado [Lyric Video])(MP3_128K).mp3" },
                    { title: "JOSE José José - El Triste (Revisitado [Lyric Video])(MP3_128K).mp3", duration: "02:05", src: "EL José José - El Triste (Revisitado [Lyric Video])(MP3_128K).mp3" },
                    { title: "José José - Quiero Perderme Contigo (Cover Audio)(MP3_128K).mp3", duration: "03:22", src: "José José - Quiero Perderme Contigo (Cover Audio)(MP3_128K).mp3" },
                    { title: "José José - Piel de Azúcar (Letra _ Lyrics)(MP3_128K).mp3", duration: "03:22", src: "José José - Piel de Azúcar (Letra _ Lyrics)(MP3_128K).mp3" },
                    { title: "LO José José - Lo Dudo (Revisitado [Lyric Video])(MP3_128K).mp3", duration: "03:22", src: "José José - Lo Dudo (Revisitado [Lyric Video])(MP3_128K).mp3" },
                    { title: "Y José José - ¿Y Quién Puede Ser_ (Video Oficial)(MP3_128K).mp3", duration: "03:22", src: "José José - ¿Y Quién Puede Ser_ (Video Oficial)(MP3_128K).mp3" },
                    { title: "José José - El Amar y el Querer (Letra Oficial)(MP3_128K).mp3", duration: "03:22", src: "José José - El Amar y el Querer (Letra Oficial)(MP3_128K).mp3" },
                    { title: "José José - Almohada (Letra Oficial)(MP3_128K).mp3", duration: "03:22", src: "José José - Almohada (Letra Oficial)(MP3_128K).mp3" }
                ]
            },
            {
                id: 29,
                title: "DIABLADA",
                artist: "DIABLADA",
                cover: "diablada.png",
                songs: [
                    { title: "Fraterno Soy - Los Masis(MP3_128K).mp3", duration: "03:22", src: "Fraterno Soy - Los Masis(MP3_128K).mp3" },
                    { title: "CUANDO ERA NINO - RAYMI BOLIVIA(MP3_128K).mp3", duration: "02:05", src: "CUANDO ERA NINO - RAYMI BOLIVIA(MP3_128K).mp3" },
                    { title: "SEMILLA - _Los Auténticos_  (Diablada)(MP3_128K).mp3", duration: "03:22", src: "SEMILLA - _Los Auténticos_  (Diablada)(MP3_128K).mp3" },
                    { title: "SEMILLA - _Los 7 Pecados Capitales_   (Diablada)(MP3_128K).mp3", duration: "03:22", src: "SEMILLA - _Los 7 Pecados Capitales_   (Diablada)(MP3_128K).mp3" },
                    { title: "YARA - _Las Cuatro Plagas_  (Diablada)(MP3_128K).mp3", duration: "03:22", src: "YARA - _Las Cuatro Plagas_  (Diablada)(MP3_128K).mp3" },
                    { title: "LLAJTAYMANTA - _Tío Mañazo_  (Diablada)(MP3_128K).mp3", duration: "03:22", src: "LLAJTAYMANTA - _Tío Mañazo_  (Diablada)(MP3_128K).mp3" },
                    { title: "LLAJTAYMANTA - _El Chiru Chiru_  (Diablada)(MP3_128K).mp3", duration: "03:22", src: "LLAJTAYMANTA - _El Chiru Chiru_  (Diablada)(MP3_128K).mp3" },
                    { title: "ARRAIGO  - DIABLADA URUS(MP3_128K).mp3", duration: "03:22", src: "ARRAIGO  - DIABLADA URUS(MP3_128K).mp3" },
                    { title: "DIABLOS LOCOS -  GRUPO ARRAIGO(MP3_128K).mp3", duration: "03:22", src: "DIABLOS LOCOS -  GRUPO ARRAIGO(MP3_128K).mp3" }
                ]
            },
            {
                id: 30,
                title: "KDA",
                artist: "KDA",
                cover: "kda.png",
                songs: [
                    { title: "K_DA - FIRESTARTER [Dolby Atmos Stems _ Hidden and Background Vocals](MP3_128K).mp3", duration: "03:22", src: "K_DA - FIRESTARTER [Dolby Atmos Stems _ Hidden and Background Vocals](MP3_128K).mp3" },
                    { title: "BLACKPINK - _How You Like That_ DANCE PERFORMANCE VIDEO(MP3_128K).mp3", duration: "02:05", src: "BLACKPINK - _How You Like That_ DANCE PERFORMANCE VIDEO(MP3_128K).mp3" },
                    { title: "K_DA - THE BADDEST Dance - Official Choreography Video _ League of Legends(MP3_128K).mp3", duration: "03:22", src: "K_DA - THE BADDEST Dance - Official Choreography Video _ League of Legends(MP3_128K).mp3" },
                    { title: "K_DA - MORE ft. Madison Beer_ (G)I-DLE_ Lexie Liu_ Jaira Burns_ Seraphine (Official Music Video)(MP3_128K).mp3", duration: "03:22", src: "K_DA - MORE ft. Madison Beer_ (G)I-DLE_ Lexie Liu_ Jaira Burns_ Seraphine (Official Music Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 31,
                title: "OSCAR D_LEON",
                artist: "OSCAR D_LEON",
                cover: "oscar leon.png",
                songs: [
                    { title: "Lloraras(MP3_128K).mp3", duration: "03:22", src: "Lloraras(MP3_128K).mp3" },
                    { title: "Oscar D_León - Traicionera (Visualizer)(MP3_128K).mp3", duration: "02:05", src: "Oscar D_León - Traicionera (Visualizer)(MP3_128K).mp3" },
                    { title: "Oscar D_León - La Carta (Visualizer)(MP3_128K).mp3", duration: "03:22", src: "Oscar D_León - La Carta (Visualizer)(MP3_128K).mp3" },
                    { title: "Oscar D_León - Mujer De Arena (Visualizer)(MP3_128K).mp3", duration: "03:22", src: "Oscar D_León - Mujer De Arena (Visualizer)(MP3_128K).mp3" },
                    { title: "Oscar D_León - Mírala Como Se Menea (Visualizer)(MP3_128K).mp3", duration: "03:22", src: "Oscar D_León - Mírala Como Se Menea (Visualizer)(MP3_128K).mp3" },
                    { title: "Oscar D_León_ Yorman - Padre E Hijo (Visualizer)(MP3_128K).mp3", duration: "03:22", src: "Oscar D_León_ Yorman - Padre E Hijo (Visualizer)(MP3_128K).mp3" },
                    { title: "Oscar D_León - Ni Frío Ni Calor (Video)(MP3_128K).mp3", duration: "03:22", src: "Oscar D_León - Ni Frío Ni Calor (Video)(MP3_128K).mp3" },
                    { title: "Oscar D_León - Me Voy Pa_Cali (Visualizer)(MP3_128K).mp3", duration: "03:22", src: "Oscar D_León - Me Voy Pa_Cali (Visualizer)(MP3_128K).mp3" }
                ]
            },
            {
                id: 32,
                title: "PROYECTO UNO",
                artist: "PROYECTO UNO",
                cover: "proyecto uno.png",
                songs: [
                    { title: "NO HAY PESOS - LOS CANTANTES (video oficial) _ramonorlando(MP3_128K).mp3", duration: "03:22", src: "NO HAY PESOS - LOS CANTANTES (video oficial) _ramonorlando(MP3_128K).mp3" },
                    { title: "La Dueña del Swing(MP3_128K).mp3", duration: "02:05", src: "La Dueña del Swing(MP3_128K).mp3" },
                    { title: "Tu Eres AJENA 🎤 - Eddy Herrera [Video Oficial](MP3_128K).mp3", duration: "03:22", src: "Tu Eres AJENA 🎤 - Eddy Herrera [Video Oficial](MP3_128K).mp3" },
                    { title: "Elvis Crespo - Suavemente(MP3_128K).mp3", duration: "03:22", src: "Elvis Crespo - Suavemente(MP3_128K).mp3" },
                    { title: "Tiburón 🦈 - Proyecto Uno [Video Oficial](MP3_128K).mp3", duration: "03:22", src: "Tiburón 🦈 - Proyecto Uno [Video Oficial](MP3_128K).mp3" },
                    { title: "Don Omar - Taboo(MP3_128K).mp3", duration: "03:22", src: "Don Omar - Taboo(MP3_128K).mp3" },
                    { title: "Juan Luis Guerra 4.40 - Las Avispas (Video Oficial)(MP3_128K).mp3", duration: "03:22", src: "Juan Luis Guerra 4.40 - Las Avispas (Video Oficial)(MP3_128K).mp3" }
                ]
            },
            {
                id: 33,
                title: "SKILLET",
                artist: "SKILLET",
                cover: "Skillet-JimmyFontaine_1956-x-1956.png",
                songs: [
                    { title: "Skillet - Hero (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Skillet - Hero (Official Video)(MP3_128K).mp3" },
                    { title: "Skillet - Monster (Official Video)(MP3_128K).mp3", duration: "02:05", src: "Skillet - Monster (Official Video)(MP3_128K).mp3" },
                    { title: "Skillet - _Lions_ [Official Lyric Video](MP3_128K).mp3", duration: "03:22", src: "Skillet - _Lions_ [Official Lyric Video](MP3_128K).mp3" },
                    { title: "Skillet - American Noise (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Skillet - American Noise (Official Video)(MP3_128K).mp3" },
                    { title: "Skillet - _The Resistance_ [Official Lyric Video](MP3_128K).mp3", duration: "03:22", src: "Skillet - _The Resistance_ [Official Lyric Video](MP3_128K).mp3" },
                    { title: "Skillet - Awake and Alive (Official Audio)(MP3_128K).mp3", duration: "03:22", src: "Skillet - Awake and Alive (Official Audio)(MP3_128K).mp3" },
                    { title: "Skillet - _Feel Invincible_ [Official Music Video](MP3_128K).mp3", duration: "03:22", src: "Skillet - _Feel Invincible_ [Official Music Video](MP3_128K).mp3" },
                    { title: "Skillet - Dead Man Walking (Official Lyric Video)(MP3_128K).mp3", duration: "03:22", src: "Skillet - Dead Man Walking (Official Lyric Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 34,
                title: "RICKY MARTIN",
                artist: "RICKY MARTIN",
                cover: "RICKY.png",
                songs: [
                    { title: "Ricky Martin - Pégate(MP3_128K).mp3", duration: "03:22", src: "Ricky Martin - Pégate(MP3_128K).mp3" },
                    { title: "Ricky Martin - Tal Vez (Official Video)(MP3_128K).mp3", duration: "02:05", src: "Ricky Martin - Tal Vez (Official Video)(MP3_128K).mp3" },
                    { title: "Ricky Martin - Vuelve (Live Black _ White Tour)(MP3_128K).mp3", duration: "03:22", src: "Ricky Martin - Vuelve (Live Black _ White Tour)(MP3_128K).mp3" },
                    { title: "Ricky Martin_ Farruko - Tiburones (Remix - Official Video)(MP3_128K).mp3", duration: "03:22", src: "Ricky Martin_ Farruko - Tiburones (Remix - Official Video)(MP3_128K).mp3" },
                    { title: "Ricky Martin - La Mordidita (Official Video) ft. Yotuel(MP3_128K).mp3", duration: "03:22", src: "Ricky Martin - La Mordidita (Official Video) ft. Yotuel(MP3_128K).mp3" },
                    { title: "Ricky Martin - Adiós (Spanish_French) (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Ricky Martin - Adiós (Spanish_French) (Official Video)(MP3_128K).mp3" },
                    { title: "Ricky Martin - Adrenalina ft. Jennifer Lopez_ Wisin (Spanglish Version)(MP3_128K).mp3", duration: "03:22", src: "Ricky Martin - Adrenalina ft. Jennifer Lopez_ Wisin (Spanglish Version)(MP3_128K).mp3" },
                    { title: "Ricky Martin - Livin_ La Vida Loca (Official HD Video)(MP3_128K).mp3", duration: "03:22", src: "Ricky Martin - Livin_ La Vida Loca (Official HD Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 35,
                title: "RAUW ALEJANDRO",
                artist: "RAUW ALEJANDRO",
                cover: "ALEJANDRO.png",
                songs: [
                    { title: "Rauw Alejandro❌Chencho Corleone FT. Kevvo ❌ Bryant Myers❌Lyanno ❌Dalex - El Efecto RMX (VIDEO LYRIC)(MP3_128K).mp3", duration: "03:22", src: "Rauw Alejandro❌Chencho Corleone FT. Kevvo ❌ Bryant Myers❌Lyanno ❌Dalex - El Efecto RMX (VIDEO LYRIC)(MP3_128K).mp3" },
                    { title: "Rauw Alejandro _ Perreo Pesau_ (Video Oficial)(MP3_128K).mp3", duration: "02:05", src: "Rauw Alejandro _ Perreo Pesau_ (Video Oficial)(MP3_128K).mp3" },
                    { title: "Rauw Alejandro - Algo Mágico (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Rauw Alejandro - Algo Mágico (Official Video)(MP3_128K).mp3" },
                    { title: "Rauw Alejandro - Dile a El (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Rauw Alejandro - Dile a El (Official Video)(MP3_128K).mp3" },
                    { title: "Rauw Alejandro_ Romeo Santos - Khé_ (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Rauw Alejandro_ Romeo Santos - Khé_ (Official Video)(MP3_128K).mp3" },
                    { title: "Rauw Alejandro_ Chencho Corleone - Desesperados (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Rauw Alejandro_ Chencho Corleone - Desesperados (Official Video)(MP3_128K).mp3" },
                    { title: "Rauw Alejandro - Todo De Ti (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Rauw Alejandro - Todo De Ti (Official Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 36,
                title: "CALLE 13",
                artist: "CALLE 13",
                cover: "CALLE 13.png",
                songs: [
                    { title: "Calle 13 - Latinoamérica(MP3_128K).mp3", duration: "03:22", src: "Calle 13 - Latinoamérica(MP3_128K).mp3" },
                    { title: "Calle 13 - Suave(MP3_128K).mp3", duration: "02:05", src: "Calle 13 - Suave(MP3_128K).mp3" },
                    { title: "Calle 13 - El Aguante(MP3_128K).mp3", duration: "03:22", src: "Calle 13 - El Aguante(MP3_128K).mp3" },
                    { title: "Calle 13 - Muerte En Hawaii (Audio)(MP3_128K).mp3", duration: "03:22", src: "Calle 13 - Muerte En Hawaii (Audio)(MP3_128K).mp3" },
                    { title: "Calle 13 - Atrevete te te (Explicit)(MP3_128K).mp3", duration: "03:22", src: "Calle 13 - Atrevete te te (Explicit)(MP3_128K).mp3" },
                    { title: "Calle 13 - La Perla (Long Version) ft. Rubén Blades_ La Chilinga(MP3_128K).mp3", duration: "03:22", src: "Calle 13 - La Perla (Long Version) ft. Rubén Blades_ La Chilinga(MP3_128K).mp3" },
                    { title: "Calle 13 - Algo Con-Sentido (Cover Audio Video) ft. pg-13(MP3_128K).mp3", duration: "03:22", src: "Calle 13 - Algo Con-Sentido (Cover Audio Video) ft. pg-13(MP3_128K).mp3" },
                    { title: "Calle 13 - No Hay Nadie Como Tú (Video) ft. Café Tacuba(MP3_128K).mp3", duration: "03:22", src: "Calle 13 - No Hay Nadie Como Tú (Video) ft. Café Tacuba(MP3_128K).mp3" }
                ]
            },
            {
                id: 37,
                title: "CAPORALES",
                artist: "CAPORALES",
                cover: "caporales.png",
                songs: [
                    { title: "CHILA JATUN  Bolivia - Ñawpaq Warmisita (Video Oficial HD)(MP3_128K).mp3", duration: "03:22", src: "CHILA JATUN  Bolivia - Ñawpaq Warmisita (Video Oficial HD)(MP3_128K).mp3" },
                    { title: "Soy Caporal(MP3_128K).mp3", duration: "02:05", src: "Soy Caporal(MP3_128K).mp3" },
                    { title: "La Saya(MP3_128K).mp3", duration: "03:22", src: "La Saya(MP3_128K).mp3" },
                    { title: "Negrita(MP3_128K).mp3", duration: "03:22", src: "Negrita(MP3_128K).mp3" },
                    { title: "Bella Mujer(MP3_128K).mp3", duration: "03:22", src: "Bella Mujer(MP3_128K).mp3" },
                    { title: "PASION ANDINA - MORENA(MP3_128K).mp3", duration: "03:22", src: "PASION ANDINA - MORENA(MP3_128K).mp3" },
                    { title: "Tupay - Cholero (Videolyric Oficial)(MP3_128K).mp3", duration: "03:22", src: "Tupay - Cholero (Videolyric Oficial)(MP3_128K).mp3" }
                ]
            },
            {
                id: 38,
                title: "MARCHAS MILITARES",
                artist: "MARCHAS MILITARES",
                cover: "marchas.png",
                songs: [
                    { title: "Marcha de los Colorados de Bolivia»(MP3_128K).mp3", duration: "03:22", src: "Marcha de los Colorados de Bolivia»(MP3_128K).mp3" },
                    { title: "Talacocha(MP3_128K).mp3", duration: "02:05", src: "Talacocha(MP3_128K).mp3" },
                    { title: "Las Canteras(MP3_128K).mp3", duration: "03:22", src: "Las Canteras(MP3_128K).mp3" },
                    { title: "VICTORIA DE AROMA(MP3_128K).mp3", duration: "03:22", src: "VICTORIA DE AROMA(MP3_128K).mp3" },
                    { title: "Primera Compañía(MP3_128K).mp3", duration: "03:22", src: "Primera Compañía(MP3_128K).mp3" },
                    { title: "Marcha _Waidmannsheil_ Bolivia(MP3_128K).mp3", duration: "03:22", src: "Marcha _Waidmannsheil_ Bolivia(MP3_128K).mp3" },
                    { title: "HIMNO  DEL EJÉRCITO DE BOLIVIA 2013(MP3_128K).mp3", duration: "03:22", src: "HIMNO  DEL EJÉRCITO DE BOLIVIA 2013(MP3_128K).mp3" },
                    { title: "Marcha militar _Canterías_(MP3_128K).mp3", duration: "03:22", src: "Marcha militar _Canterías_(MP3_128K).mp3" }
                ]
            },
            {
                id: 39,
                title: "PLAN B",
                artist: "PLAN B",
                cover: "CANDY.png",
                songs: [
                    { title: "Plan B - Fronteo(MP3_128K).mp3", duration: "03:22", src: "Plan B - Fronteo(MP3_128K).mp3" },
                    { title: "Plan B - Mi Vecinita(MP3_128K).mp3", duration: "02:05", src: "Plan B - Mi Vecinita(MP3_128K).mp3" },
                    { title: "Plan B - Soy y Sere(MP3_128K).mp3", duration: "03:22", src: "Plan B - Soy y Sere(MP3_128K).mp3" },
                    { title: "Plan B - Fanatica Sensual Lyric Video(MP3_128K).mp3", duration: "03:22", src: "Plan B - Fanatica Sensual Lyric Video(MP3_128K).mp3" },
                    { title: "Plan B - Candy [Official Audio](MP3_128K).mp3", duration: "03:22", src: "Plan B - Candy [Official Audio](MP3_128K).mp3" },
                    { title: "Plan B - Es Un Secreto [Official Video](MP3_128K).mp3", duration: "03:22", src: "Plan B - Es Un Secreto [Official Video](MP3_128K).mp3" },
                    { title: "Plan B - Por Que Te Demoras_ [Official Audio](MP3_128K).mp3", duration: "03:22", src: "Plan B - Por Que Te Demoras_ [Official Audio](MP3_128K).mp3" }
                ]
            },
            {
                id: 40,
                title: "SEBASTIAN YATRA",
                artist: "SEBASTIAN YATRA",
                cover: "SEBASTIAN.png",
                songs: [
                    { title: "Sebastián Yatra - Traicionera (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Sebastián Yatra - Traicionera (Official Video)(MP3_128K).mp3" },
                    { title: "Sebastián Yatra - No Hay Nadie Más (Official Video)(MP3_128K).mp3", duration: "02:05", src: "Sebastián Yatra - No Hay Nadie Más (Official Video)(MP3_128K).mp3" },
                    { title: "Sebastián Yatra_ Álvaro Díaz - A Dónde Van (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Sebastián Yatra_ Álvaro Díaz - A Dónde Van (Official Video)(MP3_128K).mp3" },
                    { title: "Sebastián Yatra_ Manuel Turizo_ Beéle - VAGABUNDO (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Sebastián Yatra_ Manuel Turizo_ Beéle - VAGABUNDO (Official Video)(MP3_128K).mp3" },
                    { title: "Sebastián Yatra - Básicamente (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Sebastián Yatra - Básicamente (Official Video)(MP3_128K).mp3" },
                    { title: "Sebastián Yatra - Tacones Rojos (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Sebastián Yatra - Tacones Rojos (Official Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 41,
                title: "J. BALVIN",
                artist: "J. BALVIN",
                cover: "J BALVIN.png",
                songs: [
                    { title: "J Balvin - 6 AM ft. Farruko(MP3_128K).mp3", duration: "03:22", src: "J Balvin - 6 AM ft. Farruko(MP3_128K).mp3" },
                    { title: "J Balvin - Morado (Official Video)(MP3_128K).mp3", duration: "02:05", src: "J Balvin - Morado (Official Video)(MP3_128K).mp3" },
                    { title: "J Balvin - Amarillo (Official Video)(MP3_128K).mp3", duration: "03:22", src: "J Balvin - Amarillo (Official Video)(MP3_128K).mp3" },
                    { title: "J Balvin - Azul (Official Animated Video)(MP3_128K).mp3", duration: "03:22", src: "J Balvin - Azul (Official Animated Video)(MP3_128K).mp3" },
                    { title: "J. Balvin - Ay Vamos (Official Video)(MP3_128K) (2).mp3", duration: "03:22", src: "J. Balvin - Ay Vamos (Official Video)(MP3_128K).mp3" },
                    { title: "J Balvin - Safari ft. Pharrell Williams_ BIA_ Sky (Official Video)(MP3_128K).mp3", duration: "03:22", src: "J Balvin - Safari ft. Pharrell Williams_ BIA_ Sky (Official Video)(MP3_128K).mp3" },
                    { title: "COLORES - Album Completo - J_balvin - sky -ronalelkilla(MP3_128K).mp3", duration: "03:22", src: "" }
                ]
            },
            {
                id: 42,
                title: "OZUNA",
                artist: "OZUNA",
                cover: "OZUNA.png",
                songs: [
                    { title: "Ozuna - Coméntale Feat. Akon (Video Oficial)(MP3_128K).mp3", duration: "03:22", src: "Ozuna - Coméntale Feat. Akon (Video Oficial)(MP3_128K).mp3" },
                    { title: "Ozuna - Se Preparó (Video Oficial) _ Odisea(MP3_128K).mp3", duration: "02:05", src: "Ozuna - Se Preparó (Video Oficial) _ Odisea(MP3_128K).mp3" },
                    { title: "Ozuna x Omar Courtz - ZIZI  (Video Oficial)(MP3_128K).mp3", duration: "03:22", src: "Ozuna x Omar Courtz - ZIZI  (Video Oficial)(MP3_128K).mp3" },
                    { title: "Ozuna - Quiero Mas Feat. Wisin y Yandel (Video Oficial)(MP3_128K).mp3", duration: "03:22", src: "Ozuna - Quiero Mas Feat. Wisin y Yandel (Video Oficial)(MP3_128K).mp3" },
                    { title: "Ozuna - Dile Que Tu Me Quieres (Video Oficial) _ Odisea(MP3_128K).mp3", duration: "03:22", src: "Ozuna - Dile Que Tu Me Quieres (Video Oficial) _ Odisea(MP3_128K).mp3" },
                    { title: "Ozuna x Romeo Santos - El Farsante (Remix) (Video Oficial)(MP3_128K).mp3", duration: "03:22", src: "Ozuna x Romeo Santos - El Farsante (Remix) (Video Oficial)(MP3_128K) (2).mp3" },
                    { title: "Ozuna x J Balvin x Chencho Corleone - Una Locura (Video Oficial)(MP3_128K).mp3", duration: "03:22", src: "Ozuna x J Balvin x Chencho Corleone - Una Locura (Video Oficial)(MP3_128K).mp3" },
                    { title: "Natti Natasha ❌ Ozuna - Criminal [Official Video](MP3_128K).mp3", duration: "03:22", src: "Natti Natasha ❌ Ozuna - Criminal [Official Video](MP3_128K).mp3" }
                ]
            },
            {
                id: 43,
                title: "GORILLAZ",
                artist: "GORILLAZ",
                cover: "Gorillaz.png",
                songs: [
                    { title: "Gorillaz - Humility (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Gorillaz - Humility (Official Video)(MP3_128K).mp3" },
                    { title: "Gorillaz - DARE (Official Video)(MP3_128K).mp3", duration: "02:05", src: "Gorillaz - DARE (Official Video)(MP3_128K).mp3" },
                    { title: "Gorillaz - 19-2000 (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Gorillaz - 19-2000 (Official Video)(MP3_128K).mp3" },
                    { title: "Gorillaz - Dirty Harry (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Gorillaz - Dirty Harry (Official Video)(MP3_128K).mp3" },
                    { title: "Gorillaz - Clint Eastwood (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Gorillaz - Clint Eastwood (Official Video)(MP3_128K).mp3" },
                    { title: "Gorillaz - Feel Good Inc. (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Gorillaz - Feel Good Inc. (Official Video)(MP3_128K).mp3" },
                    { title: "Gorillaz - On Melancholy Hill (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Gorillaz - On Melancholy Hill (Official Video)(MP3_128K).mp3" },
                    { title: "Gorillaz - Cracker Island ft. Thundercat (Official Video)(MP3_128K).mp3", duration: "03:22", src: "Gorillaz - Cracker Island ft. Thundercat (Official Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 44,
                title: "BIG DAD VOODOO DADDY",
                artist: "BIG BAD VOODOO DADDY",
                cover: "Big-Bad-Voodoo-Daddy-fun-2.png",
                songs: [
                    { title: "Big Bad Voodoo Daddy- Mambo Swing(MP3_128K).mp3", duration: "03:22", src: "Big Bad Voodoo Daddy- Mambo Swing(MP3_128K).mp3" },
                    { title: "Big Bad Voodoo Daddy - Save My Soul(MP3_128K).mp3", duration: "02:05", src: "Big Bad Voodoo Daddy- Mambo Swing(MP3_128K).mp3" },
                    { title: "Big Bad Voodoo Daddy - Mr. Pinstripe Suit(MP3_128K).mp3", duration: "03:22", src: "Big Bad Voodoo Daddy - Mr. Pinstripe Suit(MP3_128K).mp3" },
                    { title: "Big Bad Voodoo Daddy - Go Daddy-O(MP3_128K).mp3", duration: "03:22", src: "Big Bad Voodoo Daddy - Go Daddy-O(MP3_128K).mp3" },
                    { title: "Sing sing sing_(MP3_128K).mp3", duration: "03:22", src: "Sing sing sing_(MP3_128K).mp3" },
                    { title: "Minnie The Moocher - Big Bad Voodoo Daddy(MP3_128K).mp3", duration: "03:22", src: "Minnie The Moocher - Big Bad Voodoo Daddy(MP3_128K).mp3" }
                ]
            },
            {
                id: 45,
                title: "METRELLA Y LIRICA",
                artist: "METRALLA Y LIRICA",
                cover: "METRALLA.png",
                songs: [
                    { title: "El Ratón y el Queso(MP3_128K).mp3", duration: "03:22", src: "El Ratón y el Queso(MP3_128K).mp3" },
                    { title: "_a la ruleta rusa vine a jugar_ 「AMV anime mix」la locura esta en mi (version extendida) - porta(MP3_128K).mp3", duration: "02:05", src: "_a la ruleta rusa vine a jugar_ 「AMV anime mix」la locura esta en mi (version extendida) - porta(MP3_128K).mp3" },
                    { title: "Santa Estilo ft. Cartel de Santa _ Millonario - Pesao _ACTIVADOSYPRENDIDOS(MP3_128K).mp3", duration: "03:22", src: "Santa Estilo ft. Cartel de Santa _ Millonario - Pesao _ACTIVADOSYPRENDIDOS(MP3_128K).mp3" },
                    { title: "_La Locura Está En Mí_ _ SantaFlow_ Porta_ Eneyser _ Letra _ Kimetsu No Yaiba(MP3_128K).mp3", duration: "03:22", src: "_La Locura Está En Mí_ _ SantaFlow_ Porta_ Eneyser _ Letra _ Kimetsu No Yaiba(MP3_128K).mp3" },
                    { title: "EXTASIS(MP3_128K).mp3", duration: "03:22", src: "EXTASIS(MP3_128K).mp3" },
                    { title: "MAS FLOW MAS CASH(MP3_128K).mp3", duration: "03:22", src: "MAS FLOW MAS CASH(MP3_128K).mp3" },
                    { title: "Culón Culito(MP3_128K).mp3", duration: "03:22", src: "Culón Culito(MP3_128K).mp3" },
                    { title: "LIRICA Y METRALLA(MP3_128K).mp3", duration: "03:22", src: "LIRICA Y METRALLA(MP3_128K).mp3" }
                ]
            },
            {
                id: 46,
                title: "DRAGON BALL",
                artist: "DRAGON BALL",
                cover: "DRAGON.png",
                songs: [
                    { title: "DRAGON BALL SUPER BROLY RAP _ PORTA _ VIDEO OFICIAL RESUBIDO(MP3_128K).mp3", duration: "03:22", src: "DRAGON BALL SUPER BROLY RAP _ PORTA _ VIDEO OFICIAL RESUBIDO(MP3_128K).mp3" },
                    { title: "Dragon Ball Opening Latino HD 720p(MP3_128K).mp3", duration: "02:05", src: "Dragon Ball Opening Latino HD 720p(MP3_128K).mp3" },
                    { title: "Dragon Ball Z Ending 2 - Audio Latino(MP3_128K).mp3", duration: "03:22", src: "Dragon Ball Z Ending 2 - Audio Latino(MP3_128K).mp3" },
                    { title: "Dragon Ball Z opening 2 Latino (1080p HD)(MP3_128K).mp3", duration: "03:22", src: "Dragon Ball Z opening 2 Latino (1080p HD)(MP3_128K).mp3" },
                    { title: "Dragon Ball Z - Chala Head Chala (opening Latino) Full HD 1080p(MP3_128K).mp3", duration: "03:22", src: "Dragon Ball Z - Chala Head Chala (opening Latino) Full HD 1080p(MP3_128K).mp3" },
                    { title: "Ending 1 Dragon Ball Z (Español Latino) con letra _ Pandatio(MP3_128K).mp3", duration: "03:22", src: "Ending 1 Dragon Ball Z (Español Latino) con letra _ Pandatio(MP3_128K).mp3" }
                ]
            },
            {
                id: 47,
                title: "JAZZ",
                artist: "JAZZ",
                cover: "jazz-art-vector.png",
                songs: [
                    { title: "Los Aristogatos - Todos Quieren ser ya Gatos Jazz [Audio Latino](MP3_128K).mp3", duration: "03:22", src: "Los Aristogatos - Todos Quieren ser ya Gatos Jazz [Audio Latino](MP3_128K).mp3" },
                    { title: "Diana Krall - Autumn In New York (Official Video)(MP3_128K).mp3", duration: "02:05", src: "Diana Krall - Autumn In New York (Official Video)(MP3_128K).mp3" },
                    { title: "Dave Brubeck_ The Dave Brubeck Quartet - Take Five (Audio)(MP3_128K).mp3", duration: "03:22", src: "Dave Brubeck_ The Dave Brubeck Quartet - Take Five (Audio)(MP3_128K).mp3" },
                    { title: "Stacey Kent - How Insensitive (Lyrics video)(MP3_128K).mp3", duration: "03:22", src: "Stacey Kent - How Insensitive (Lyrics video)(MP3_128K).mp3" },
                    { title: "Ain_t no sunshine (Bossa Mix) - Karen Souza  - INSTANT LOVE(MP3_128K).mp3", duration: "03:22", src: "Ain_t no sunshine (Bossa Mix) - Karen Souza  - INSTANT LOVE(MP3_128K).mp3" },
                    { title: "Beegie Adair - Misty (Visualizer)(MP3_128K).mp3", duration: "03:22", src: "Beegie Adair - Misty (Visualizer)(MP3_128K).mp3" },
                    { title: "Billie Holiday - Solitude (Audio)(MP3_128K).mp3", duration: "03:22", src: "Billie Holiday - Solitude (Audio)(MP3_128K).mp3" },
                    { title: "Jack Jezzro - The Shadow Of Your Smile (Visualizer) ft. Beegie Adair(MP3_128K).mp3", duration: "03:22", src: "Jack Jezzro - The Shadow Of Your Smile (Visualizer) ft. Beegie Adair(MP3_128K).mp3" }
                ]
            },
            {
                id: 48,
                title: "LINDSEY STIRLING",
                artist: "LINDSEY STIRLING",
                cover: "LINDSEY.png",
                songs: [
                    { title: "Lindsey Stirling - Shadows (Official Music Video)(MP3_128K).mp3", duration: "03:22", src: "Lindsey Stirling - Shadows (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lindsey Stirling - Moon Trance (Official Music Video)(MP3_128K).mp3", duration: "02:05", src: "Lindsey Stirling - Moon Trance (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lindsey Stirling - Roundtable Rival (Official Music Video)(MP3_128K).mp3", duration: "03:22", src: "Lindsey Stirling - Roundtable Rival (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lindsey Stirling - Crystallize (Dubstep Violin Original Song)(MP3_128K).mp3", duration: "03:22", src: "Lindsey Stirling - Crystallize (Dubstep Violin Original Song)(MP3_128K).mp3" },
                    { title: "Lindsey Stirling - Elements (Dubstep Violin Original Song)(MP3_128K).mp3", duration: "03:22", src: "Lindsey Stirling - Elements (Dubstep Violin Original Song)(MP3_128K).mp3" },
                    { title: "Lindsey Stirling - Take Flight (Official Music Video) _YTMA(MP3_128K).mp3", duration: "03:22", src: "Lindsey Stirling - Take Flight (Official Music Video) _YTMA(MP3_128K).mp3" },
                    { title: "Lindsey Stirling - Electric Daisy Violin (Official Music Video)(MP3_128K).mp3", duration: "03:22", src: "Lindsey Stirling - Electric Daisy Violin (Official Music Video)(MP3_128K).mp3" },
                    { title: "Lindsey Stirling - Beyond The Veil (Official Music Video)(MP3_128K).mp3", duration: "03:22", src: "Lindsey Stirling - Beyond The Veil (Official Music Video)(MP3_128K).mp3" }
                ]
            },
            {
                id: 49,
                title: "FANFARE CIOCARLIA",
                artist: "FANFARE CIOCARLIA",
                cover: "MOLIENDO.png",
                songs: [
                    { title: "Moliendo Café(MP3_128K).mp3", duration: "03:22", src: "Moliendo Café(MP3_128K).mp3" },
                    { title: "Hora Andalusia(MP3_128K).mp3", duration: "02:05", src: "Hora Andalusia(MP3_128K).mp3" },
                    { title: "Casablanca(MP3_128K).mp3", duration: "03:22", src: "Casablanca(MP3_128K).mp3" },
                    { title: "Iag Bari(MP3_128K).mp3", duration: "03:22", src: "Iag Bari(MP3_128K).mp3" },
                    { title: "Lume_ lume si Hora(MP3_128K).mp3", duration: "03:22", src: "Lume_ lume si Hora(MP3_128K).mp3" },
                    { title: "Asfalt Tango(MP3_128K).mp3", duration: "03:22", src: "Asfalt Tango(MP3_128K).mp3" },
                    { title: "Baro Biao(MP3_128K).mp3", duration: "03:22", src: "Baro Biao(MP3_128K).mp3" },
                    { title: "Mariana(MP3_128K).mp3", duration: "03:22", src: "Mariana(MP3_128K).mp3" }
                ]
            },
            {
                id: 50,
                title: "DEATH NOTE",
                artist: "DEATH NOTE",
                cover: "DEAD NOTE.png",
                songs: [
                    { title: "Death Note - (Notebook Theme) Music(MP3_128K).mp3", duration: "03:22", src: "Death Note - (Notebook Theme) Music(MP3_128K).mp3" },
                    { title: "Death Note - (Light_s Theme E) Music(MP3_128K).mp3", duration: "02:05", src: "Death Note - (Light_s Theme E) Music(MP3_128K).mp3" },
                    { title: "Death Note - (Kira_s Theme A) Music(MP3_128K).mp3", duration: "03:22", src: "Death Note - (Kira_s Theme A) Music(MP3_128K).mp3" },
                    { title: "Death Note - (Light_s Theme F) Music(MP3_128K).mp3", duration: "03:22", src: "Death Note - (Light_s Theme F) Music(MP3_128K).mp3" },
                    { title: "Death Note - (Light_s Theme B) Music(MP3_128K).mp3", duration: "03:22", src: "Death Note - (Light_s Theme B) Music(MP3_128K).mp3" },
                    { title: "Death Note - (Light_s Theme D) Music(MP3_128K).mp3", duration: "03:22", src: "Death Note - (Light_s Theme D) Music(MP3_128K).mp3" },
                    { title: "Death Note - (Light_s Theme A) Music(MP3_128K).mp3", duration: "03:22", src: "Death Note - (Light_s Theme A) Music(MP3_128K).mp3" },
                    { title: "Death Note - (Light_s Theme C) Music(MP3_128K).mp3", duration: "03:22", src: "Death Note - (Light_s Theme C) Music(MP3_128K).mp3" }
                ]
            },
        ];

        /* =====================================================================
           VARIABLES DE ESTADO DEL REPRODUCTOR
           ===================================================================== */
        let currentAlbumIndex = 0;
        let currentSongIndex = 0;
        let isPlaying = false;
        let isRandom = false;
        let isRepeat = false;

        // Elementos DOM
        const audio = document.getElementById('audio-player');
        const albumsGrid = document.getElementById('albums-grid');
        const songsList = document.getElementById('songs-list');
        
        const playerCover = document.getElementById('player-cover');
        const playerAlbumTitle = document.getElementById('player-album-title');
        const playerSongTitle = document.getElementById('player-song-title');
        const playerArtist = document.getElementById('player-artist');
        
        const btnPlay = document.getElementById('btn-play');
        const playIcon = document.getElementById('play-icon');
        const btnPrev = document.getElementById('btn-prev');
        const btnNext = document.getElementById('btn-next');
        const btnRandom = document.getElementById('btn-random');
        const btnRepeat = document.getElementById('btn-repeat');
        
        const progressBar = document.getElementById('progress-bar');
        const currentTimeEl = document.getElementById('current-time');
        const durationTimeEl = document.getElementById('duration-time');
        const volumeBar = document.getElementById('volume-bar');
        const volumeIcon = document.getElementById('volume-icon');

        /* =====================================================================
           INICIALIZACIÓN Y RENDERIZADO
           ===================================================================== */
        window.onload = () => {
            createLumaflices();
            renderAlbums();
            loadAlbum(0, 0, false);
        };

        // Crear animación de Lumélulas flotantes
        function createLumaflices() {
            const container = document.getElementById('lumafly-container');
            for (let i = 0; i < 15; i++) {
                const lumafly = document.createElement('div');
                lumafly.className = 'lumafly';
                lumafly.style.left = `${Math.random() * 100}vw`;
                lumafly.style.top = `${Math.random() * 100}vh`;
                lumafly.style.animationDelay = `${Math.random() * 5}s`;
                lumafly.style.animationDuration = `${6 + Math.random() * 6}s`;
                container.appendChild(lumafly);
            }
        }

        // Cargar y mostrar la lista de 10 Álbumes
        function renderAlbums() {
            albumsGrid.innerHTML = '';
            albumsData.forEach((album, index) => {
                const card = document.createElement('div');
                card.className = `hk-frame bg-slate-900/80 rounded-lg p-2 cursor-pointer transition transform hover:-translate-y-1 hover:border-emerald-400 ${index === currentAlbumIndex ? 'album-active' : ''}`;
                card.onclick = () => selectAlbum(index);

                card.innerHTML = `
                    <div class="aspect-square w-full rounded overflow-hidden mb-2 bg-slate-800">
                        <img src="${album.cover}" alt="${album.title}" class="w-full h-full object-cover">
                    </div>
                    <h4 class="hk-title text-xs text-slate-200 font-bold truncate">${album.title}</h4>
                    <p class="text-[10px] text-slate-400 truncate">${album.songs.length} canciones</p>
                `;
                albumsGrid.appendChild(card);
            });
        }

        // Cargar un álbum en la lista de reproducción de la derecha
        function selectAlbum(index) {
            currentAlbumIndex = index;
            currentSongIndex = 0;
            renderAlbums();
            loadAlbum(currentAlbumIndex, currentSongIndex, true);
        }

        // Cargar canción seleccionada en el reproductor principal
        function loadAlbum(albumIdx, songIdx, autoPlay = false) {
            const album = albumsData[albumIdx];
            const song = album.songs[songIdx];

            playerCover.src = album.cover;
            playerAlbumTitle.textContent = album.title;
            playerSongTitle.textContent = song.title;
            playerArtist.textContent = album.artist;

            audio.src = song.src;

            renderSongsList();

            if (autoPlay) {
                playAudio();
            } else {
                pauseAudio();
            }
        }

        // Muestra la lista de canciones del álbum actual
        function renderSongsList() {
            const album = albumsData[currentAlbumIndex];
            document.getElementById('song-count').textContent = `${album.songs.length} canciones`;
            songsList.innerHTML = '';

            album.songs.forEach((song, idx) => {
                const item = document.createElement('div');
                const isActive = idx === currentSongIndex;
                item.className = `flex justify-between items-center p-2 rounded text-xs cursor-pointer transition hover:bg-slate-800/80 ${isActive ? 'song-active' : 'text-slate-300'}`;
                item.onclick = () => {
                    currentSongIndex = idx;
                    loadAlbum(currentAlbumIndex, currentSongIndex, true);
                };

                item.innerHTML = `
                    <div class="flex items-center space-x-2 truncate">
                        <i class="fa-solid ${isActive && isPlaying ? 'fa-volume-high text-emerald-400 animate-pulse' : 'fa-music text-slate-500'} text-[10px]"></i>
                        <span class="truncate font-medium">${song.title}</span>
                    </div>
                    <span class="text-[10px] text-slate-400 font-mono ml-2">${song.duration}</span>
                `;
                songsList.appendChild(item);
            });
        }

        /* =====================================================================
           CONTROLES DE AUDIO
           ===================================================================== */
        function playAudio() {
            isPlaying = true;
            audio.play().catch(() => { isPlaying = false; updatePlayIcon(); });
            updatePlayIcon();
            renderSongsList();
        }

        function pauseAudio() {
            isPlaying = false;
            audio.pause();
            updatePlayIcon();
            renderSongsList();
        }

        function updatePlayIcon() {
            if (isPlaying) {
                playIcon.className = "fa-solid fa-pause";
            } else {
                playIcon.className = "fa-solid fa-play ml-0.5";
            }
        }

        btnPlay.onclick = () => {
            if (isPlaying) {
                pauseAudio();
            } else {
                playAudio();
            }
        };

        btnNext.onclick = () => {
            nextSong();
        };

        btnPrev.onclick = () => {
            const album = albumsData[currentAlbumIndex];
            if (currentSongIndex > 0) {
                currentSongIndex--;
            } else {
                currentSongIndex = album.songs.length - 1;
            }
            loadAlbum(currentAlbumIndex, currentSongIndex, true);
        };

        function nextSong() {
            const album = albumsData[currentAlbumIndex];
            if (isRandom) {
                currentSongIndex = Math.floor(Math.random() * album.songs.length);
            } else {
                if (currentSongIndex < album.songs.length - 1) {
                    currentSongIndex++;
                } else {
                    // Pasar al siguiente álbum si termina
                    currentAlbumIndex = (currentAlbumIndex + 1) % albumsData.length;
                    currentSongIndex = 0;
                    renderAlbums();
                }
            }
            loadAlbum(currentAlbumIndex, currentSongIndex, true);
        }

        // Bucle y Aleatorio
        btnRandom.onclick = () => {
            isRandom = !isRandom;
            btnRandom.classList.toggle('text-emerald-400', isRandom);
            btnRandom.classList.toggle('text-slate-400', !isRandom);
        };

        btnRepeat.onclick = () => {
            isRepeat = !isRepeat;
            btnRepeat.classList.toggle('text-emerald-400', isRepeat);
            btnRepeat.classList.toggle('text-slate-400', !isRepeat);
        };

        // Evento fin de canción
        audio.onended = () => {
            if (isRepeat) {
                playAudio();
            } else {
                nextSong();
            }
        };

        /* =====================================================================
           BARRA DE PROGRESO Y TIEMPO
           ===================================================================== */
        audio.ontimeupdate = () => {
            if (audio.duration) {
                const progress = (audio.currentTime / audio.duration) * 100;
                progressBar.value = progress;
                currentTimeEl.textContent = formatTime(audio.currentTime);
                durationTimeEl.textContent = formatTime(audio.duration);
            }
        };

        progressBar.oninput = () => {
            if (audio.duration) {
                audio.currentTime = (progressBar.value / 100) * audio.duration;
            }
        };

        function formatTime(seconds) {
            const mins = Math.floor(seconds / 60);
            const secs = Math.floor(seconds % 60);
            return `${mins < 10 ? '0' : ''}${mins}:${secs < 10 ? '0' : ''}${secs}`;
        }

        /* =====================================================================
           CONTROL DE VOLUMEN
           ===================================================================== */
        volumeBar.oninput = () => {
            const val = volumeBar.value / 100;
            audio.volume = val;
            if (val === 0) {
                volumeIcon.className = "fa-solid fa-volume-xmark text-xs text-slate-400";
            } else if (val < 0.5) {
                volumeIcon.className = "fa-solid fa-volume-low text-xs text-slate-400";
            } else {
                volumeIcon.className = "fa-solid fa-volume-high text-xs text-slate-400";
            }
        };
    </script>
</body>
</html>
