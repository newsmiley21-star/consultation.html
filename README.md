<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>X-PRESS - Suivi de Livraison</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        body { 
            font-family: 'Inter', sans-serif; 
            overflow-x: hidden;
        }
        /* Effet de flou de verre renforcé pour contraster avec le fond énergique */
        .glass-card {
            background: rgba(255, 255, 255, 0.85);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.6);
        }
        canvas#bg-animation {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
            pointer-events: auto; /* Permet les interactions tactiles et souris */
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between relative bg-slate-100">

    <!-- Canvas interactif réactif aux mouvements et clics -->
    <canvas id="bg-animation"></canvas>

    <!-- En-tête de la page -->
    <header class="p-4 flex justify-between items-center max-w-5xl mx-auto w-full z-10 pointer-events-none">
        <div class="flex items-center space-x-2 pointer-events-auto">
            <img id="splash-logo" src="https://res.cloudinary.com/dyxob1wcj/image/upload/v1781134790/oeqlbzjhuvhbpmrzdpgh.jpg" alt="Logo X-PRESS" class="w-24 h-16 object-contain rounded-lg shadow-md">
        </div>
        <span class="text-xs font-bold text-green-700 bg-green-100 px-3 py-1.5 rounded-full uppercase tracking-wider shadow-sm">Suivi en direct</span>
    </header>

    <!-- Zone principale contenant le widget de recherche -->
    <div class="p-4 flex-grow flex items-center justify-center z-10">
        <div class="w-full max-w-md glass-card rounded-3xl shadow-2xl p-6 space-y-6 transition-all duration-300 hover:shadow-green-200/40">
            
            <div class="text-center space-y-2">
                <h1 class="text-2xl font-extrabold text-gray-900 tracking-tight">Suivre mon colis</h1>
                <p class="text-sm text-gray-500">Entrez l'identifiant à 4 chiffres fourni lors de votre commande.</p>
            </div>

            <div class="space-y-3">
                <div class="relative">
                    <span class="absolute inset-y-0 left-0 flex items-center pl-4 text-xl text-gray-400">#</span>
                    <input type="number" id="searchId" pattern="[0-9]*" inputmode="numeric" placeholder="Ex: 4345" 
                           class="w-full p-4 pl-9 pr-24 border-2 border-gray-200 rounded-xl font-bold text-lg focus:border-green-600 focus:outline-none transition-all bg-white/80">
                    <button onclick="rechercherColis()" 
                            class="absolute right-2 top-2 bottom-2 bg-green-600 hover:bg-green-700 text-white font-bold px-4 rounded-lg text-sm transition-all duration-200 active:scale-95 shadow-md shadow-green-600/30">
                        SUIVRE
                    </button>
                </div>
            </div>

            <!-- Résultats du suivi -->
            <div id="resultZone" class="hidden space-y-6 pt-4 border-t border-gray-100">
                
                <div class="bg-gray-50/90 p-4 rounded-xl border border-gray-100 shadow-inner">
                    <div class="text-xs font-bold text-gray-400 uppercase tracking-wider mb-4 text-center">État de la livraison</div>
                    
                    <div class="flex items-center justify-between relative px-4">
                        <div class="absolute left-8 right-8 top-4 h-1 bg-gray-200 -z-10"></div>
                        <div id="progressLine" class="absolute left-8 top-4 h-1 bg-green-600 -z-10 transition-all duration-500" style="width: 0%;"></div>

                        <div class="flex flex-col items-center space-y-1">
                            <div id="circle-1" class="w-9 h-9 rounded-full bg-gray-200 flex items-center justify-center font-bold text-sm text-gray-600 transition-colors">1</div>
                            <span class="text-[11px] font-bold text-gray-500">Pris en charge</span>
                        </div>

                        <div class="flex flex-col items-center space-y-1">
                            <div id="circle-2" class="w-9 h-9 rounded-full bg-gray-200 flex items-center justify-center font-bold text-sm text-gray-600 transition-colors">2</div>
                            <span class="text-[11px] font-bold text-gray-500">En cours</span>
                        </div>

                        <div class="flex flex-col items-center space-y-1">
                            <div id="circle-3" class="w-9 h-9 rounded-full bg-gray-200 flex items-center justify-center font-bold text-sm text-gray-600 transition-colors">3</div>
                            <span class="text-[11px] font-bold text-gray-500">Livré</span>
                        </div>
                    </div>
                    
                    <div id="statusText" class="text-center font-extrabold text-sm text-green-700 mt-5 bg-white py-2 rounded-lg border border-green-100 shadow-sm"></div>
                </div>

                <!-- Détails textuels du colis -->
                <div class="space-y-3">
                    <div class="flex justify-between items-center text-sm border-b pb-2">
                        <span class="text-gray-500 font-medium">Destinataire</span>
                        <span id="trackNom" class="font-bold text-gray-900">-</span>
                    </div>
                    <div class="flex justify-between items-center text-sm border-b pb-2">
                        <span class="text-gray-500 font-medium">Téléphone</span>
                        <span id="trackTel" class="font-mono bg-gray-100 text-gray-700 px-2 py-0.5 rounded font-bold tracking-wider">-</span>
                    </div>
                    <div class="flex justify-between items-center text-sm border-b pb-2">
                        <span class="text-gray-500 font-medium">Expéditeur</span>
                        <span id="trackExp" class="font-bold text-gray-800">-</span>
                    </div>
                    <div class="flex justify-between items-center text-sm border-b pb-2">
                        <span class="text-gray-500 font-medium">Destination</span>
                        <span id="trackLieu" class="font-bold text-gray-900">-</span>
                    </div>
                    <div class="flex justify-between items-center text-sm border-b pb-2">
                        <span class="text-gray-500 font-medium">Contenu</span>
                        <span id="trackNature" class="text-gray-600 text-xs italic font-semibold">-</span>
                    </div>
                    <div class="flex justify-between items-center text-sm border-b pb-2">
                        <span class="text-gray-500 font-medium">Date d'expédition</span>
                        <span id="trackDate" class="text-gray-900 font-bold">-</span>
                    </div>
                    <div class="flex justify-between items-center text-sm border-b pb-2">
                        <span class="text-gray-500 font-medium">Heure d'enregistrement</span>
                        <span id="trackHeure" class="text-gray-900 font-bold">-</span>
                    </div>
                    <div id="instructionBlock" class="bg-yellow-50 p-3 rounded-lg border border-yellow-100 text-xs text-yellow-800 font-medium hidden">
                        💡 <span id="trackInstructions"></span>
                    </div>
                </div>

            </div>

            <div id="statusMessage" class="hidden text-center p-4 bg-red-50 text-red-700 font-bold text-sm rounded-xl border border-red-100 animate-pulse"></div>

        </div>
    </div>

    <!-- Pied de page -->
    <footer class="text-center p-4 text-xs font-semibold text-gray-400 z-10 pointer-events-none">
        &copy; 2026 X-PRESS LOGISTIQUE GABON — Tous droits réservés.
    </footer>

    <script>
        const canvas = document.getElementById('bg-animation');
        const ctx = canvas.getContext('2d');

        let particles = [];
        const particleCount = 65; // Densité augmentée pour être plus visible
        const connectionDistance = 140; // Distance des lignes de connexion étendue
        
        // Souris / Toucher interactif
        const mouse = {
            x: null,
            y: null,
            radius: 160 // Zone d'influence d'évitement
        };

        // Système d'ondes de choc suite à un clic ou toucher
        let shockwaves = [];

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Réception des événements souris/toucher
        window.addEventListener('mousemove', (e) => {
            mouse.x = e.clientX;
            mouse.y = e.clientY;
        });

        window.addEventListener('mouseleave', () => {
            mouse.x = null;
            mouse.y = null;
        });

        // Détection de toucher tactile sur mobile
        window.addEventListener('touchmove', (e) => {
            if (e.touches.length > 0) {
                mouse.x = e.touches[0].clientX;
                mouse.y = e.touches[0].clientY;
            }
        }, { passive: true });

        window.addEventListener('touchend', () => {
            mouse.x = null;
            mouse.y = null;
        });

        // Génération d'onde de choc lors d'un clic/tap
        function triggerShockwave(x, y) {
            shockwaves.push({
                x: x,
                y: y,
                radius: 0,
                maxRadius: 280,
                speed: 10,
                alpha: 1
            });
        }

        window.addEventListener('mousedown', (e) => {
            triggerShockwave(e.clientX, e.clientY);
        });

        window.addEventListener('touchstart', (e) => {
            if (e.touches.length > 0) {
                triggerShockwave(e.touches[0].clientX, e.touches[0].clientY);
            }
        }, { passive: true });

        // Classe définissant un point interactif et véloce
        class Particle {
            constructor() {
                this.x = Math.random() * canvas.width;
                this.y = Math.random() * canvas.height;
                // Vitesse de base modérée
                this.baseVx = (Math.random() - 0.5) * 0.8;
                this.baseVy = (Math.random() - 0.5) * 0.8;
                this.vx = this.baseVx;
                this.vy = this.baseVy;
                this.radius = Math.random() * 3 + 2.5; // Légèrement plus grand pour la visibilité
                this.color = 'rgba(0, 158, 96, 0.65)'; // Vert émeraude visible
            }

            update() {
                // Interaction d'évitement de la souris
                if (mouse.x !== null && mouse.y !== null) {
                    const dx = this.x - mouse.x;
                    const dy = this.y - mouse.y;
                    const dist = Math.sqrt(dx * dx + dy * dy);
                    if (dist < mouse.radius) {
                        const force = (mouse.radius - dist) / mouse.radius;
                        // Vecteur unitaire directionnel de fuite
                        const forceX = (dx / dist) * force * 5;
                        const forceY = (dy / dist) * force * 5;
                        
                        // Accélération vers l'extérieur
                        this.vx += forceX;
                        this.vy += forceY;
                    }
                }

                // Interaction avec les ondes de choc actives
                shockwaves.forEach(wave => {
                    const dx = this.x - wave.x;
                    const dy = this.y - wave.y;
                    const dist = Math.sqrt(dx * dx + dy * dy);
                    if (Math.abs(dist - wave.radius) < 25) {
                        const pushForce = 8 * wave.alpha;
                        this.vx += (dx / dist) * pushForce;
                        this.vy += (dy / dist) * pushForce;
                    }
                });

                // Friction naturelle pour stabiliser la vitesse après impulsion
                this.vx *= 0.94;
                this.vy *= 0.94;

                // Ajout de la vitesse de dérive de base
                this.vx += this.baseVx * 0.08;
                this.vy += this.baseVy * 0.08;

                // Mise à jour de la position
                this.x += this.vx;
                this.y += this.vy;

                // Rebond/Frottement souple sur les bords de l'écran
                if (this.x < 0) { this.x = 0; this.vx *= -1; this.baseVx *= -1; }
                if (this.x > canvas.width) { this.x = canvas.width; this.vx *= -1; this.baseVx *= -1; }
                if (this.y < 0) { this.y = 0; this.vy *= -1; this.baseVy *= -1; }
                if (this.y > canvas.height) { this.y = canvas.height; this.vy *= -1; this.baseVy *= -1; }
            }

            draw() {
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                ctx.fillStyle = this.color;
                ctx.shadowColor = 'rgba(0, 158, 96, 0.3)';
                ctx.shadowBlur = 8;
                ctx.fill();
                ctx.shadowBlur = 0; // Réinitialiser l'effet de flou
            }
        }

        // Créer les particules de départ
        function initParticles() {
            particles = [];
            for (let i = 0; i < particleCount; i++) {
                particles.push(new Particle());
            }
        }
        initParticles();

        // Boucle d'animation principale
        function animate() {
            // Effet de trainée douce pour un rendu cinétique premium
            ctx.fillStyle = 'rgba(241, 245, 249, 0.45)'; // Assorti au gris clair de la page
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 1. Mise à jour et dessin des ondes de choc
            shockwaves = shockwaves.filter(wave => {
                wave.radius += wave.speed;
                wave.alpha = 1 - (wave.radius / wave.maxRadius);
                
                ctx.beginPath();
                ctx.arc(wave.x, wave.y, wave.radius, 0, Math.PI * 2);
                ctx.strokeStyle = `rgba(0, 158, 96, ${wave.alpha * 0.45})`;
                ctx.lineWidth = 3;
                ctx.stroke();

                return wave.radius < wave.maxRadius;
            });

            // 2. Traitement des particules et des connexions réseau
            for (let i = 0; i < particles.length; i++) {
                particles[i].update();
                particles[i].draw();

                for (let j = i + 1; j < particles.length; j++) {
                    const dx = particles[i].x - particles[j].x;
                    const dy = particles[i].y - particles[j].y;
                    const distance = Math.sqrt(dx * dx + dy * dy);

                    if (distance < connectionDistance) {
                        // Lignes plus intenses et lumineuses
                        const alpha = (1 - (distance / connectionDistance)) * 0.35;
                        ctx.beginPath();
                        ctx.moveTo(particles[i].x, particles[i].y);
                        ctx.lineTo(particles[j].x, particles[j].y);
                        ctx.strokeStyle = `rgba(0, 158, 96, ${alpha})`;
                        ctx.lineWidth = alpha * 2; // Épaisseur adaptative
                        ctx.stroke();
                    }
                }
            }
            requestAnimationFrame(animate);
        }

        window.onload = function() {
            animate();
        };
    </script>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
        import { getDatabase, ref, query, orderByChild, equalTo, get } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyAPCKRy9NTo4X8nn8YpxAbPtX8SlKj-7sQ",
            authDomain: "cashtransfert-21.firebaseapp.com",
            databaseURL: "https://cashtransfert-21-default-rtdb.firebaseio.com",
            projectId: "cashtransfert-21",
            storageBucket: "cashtransfert-21.firebasestorage.app",
            messagingSenderId: "564831743134",
            appId: "1:564831743134:web:c22a1f53707f0f1dd9df8f"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);

        // ==========================================
        // FONCTIONS DE MASQUAGE SÉCURISÉ DES NUMÉROS
        // ==========================================
        function masquerTelephone(telString) {
            if (!telString) return "Inconnu";
            let clean = telString.trim().replace(/\s+/g, ''); 
            if (clean.length >= 9) {
                return clean.substring(0, 3) + "****" + clean.substring(7);
            }
            return clean.substring(0, 2) + "***" + clean.substring(clean.length - 2);
        }

        function masquerExpediteur(expString) {
            if (!expString) return "Inconnu";
            if (expString.includes('-')) {
                const fragments = expString.split('-');
                const nomBoutique = fragments[0];
                const numTel = fragments[1];
                return nomBoutique + " (" + masquerTelephone(numTel) + ")";
            }
            return expString;
        }

        // ==========================================
        // MOTEUR DE RECHERCHE ET DE TRACKING
        // ==========================================
        window.rechercherColis = async () => {
            const inputId = document.getElementById('searchId').value.trim();
            const resultZone = document.getElementById('resultZone');
            const statusMessage = document.getElementById('statusMessage');

            // Réinitialisation
            resultZone.classList.add('hidden');
            statusMessage.classList.add('hidden');

            if (!inputId) {
                statusMessage.innerText = "⚠️ Veuillez saisir un numéro de suivi valide.";
                statusMessage.classList.remove('hidden');
                return;
            }

            // Générer une onde de choc à l'écran lors de la recherche
            triggerShockwave(window.innerWidth / 2, window.innerHeight / 2);

            try {
                const q = query(ref(db, 'missions'), orderByChild('id'), equalTo(parseInt(inputId)));
                const snapshot = await get(q);

                if (!snapshot.exists()) {
                    statusMessage.innerText = "❌ Aucun colis trouvé avec l'identifiant #" + inputId;
                    statusMessage.classList.remove('hidden');
                    return;
                }

                let missionData = null;
                snapshot.forEach(childSnapshot => {
                    missionData = childSnapshot.val();
                });

                // --- AFFICHAGE ET INJECTION SÉCURISÉE DES DONNÉES ---
                document.getElementById('trackNom').innerText = missionData.nom || "Client X-Press";
                document.getElementById('trackTel').innerText = masquerTelephone(missionData.tel);
                document.getElementById('trackExp').innerText = masquerExpediteur(missionData.expediteur);
                document.getElementById('trackLieu').innerText = missionData.livraison || missionData.lieu || "Non spécifié";
                document.getElementById('trackNature').innerText = missionData.nature || "Marchandise";
                
                // Injection de la date de création de la mission
                document.getElementById('trackDate').innerText = missionData.date || "Non renseignée";
                document.getElementById('trackHeure').innerText = missionData.heureSeule || "En cours de traitement";

                // Bloc instructions particulières
                const instBlock = document.getElementById('instructionBlock');
                if (missionData.instructions) {
                    document.getElementById('trackInstructions').innerText = missionData.instructions;
                    instBlock.classList.remove('hidden');
                } else {
                    instBlock.classList.add('hidden');
                }

                // --- GESTION VISUELLE DU STATUT ---
                const etape = parseInt(missionData.etape) || 0;
                actualiserTimeline(etape, missionData.livreur);

                // Rendre visible la zone de résultat
                resultZone.classList.remove('hidden');

            } catch (error) {
                console.error(error);
                statusMessage.innerText = "💥 Erreur réseau. Veuillez réessayer.";
                statusMessage.classList.remove('hidden');
            }
        };

        function actualiserTimeline(etape, nomLivreur) {
            const c1 = document.getElementById('circle-1');
            const c2 = document.getElementById('circle-2');
            const c3 = document.getElementById('circle-3');
            const line = document.getElementById('progressLine');
            const txt = document.getElementById('statusText');

            [c1, c2, c3].forEach(c => {
                c.className = "w-9 h-9 rounded-full bg-gray-200 flex items-center justify-center font-bold text-sm text-gray-600 transition-colors";
            });

            if (etape === 1) {
                c1.className = "w-9 h-9 rounded-full bg-green-600 text-white flex items-center justify-center font-bold text-sm shadow-md";
                line.style.width = "0%";
                txt.innerHTML = "🕒 Commande reçue : En attente d'un livreur";
                txt.className = "text-center font-extrabold text-sm text-amber-700 mt-5 bg-amber-50 py-2 rounded-lg border border-amber-100 shadow-sm";
            } 
            else if (etape === 2 || (etape === 1 && nomLivreur && nomLivreur !== "Libre")) {
                c1.className = "w-9 h-9 rounded-full bg-green-600 text-white flex items-center justify-center font-bold text-sm";
                c2.className = "w-9 h-9 rounded-full bg-green-600 text-white flex items-center justify-center font-bold text-sm shadow-md";
                line.style.width = "50%";
                txt.innerHTML = `🚴 Colis récupéré ! Livreur en route`;
                txt.className = "text-center font-extrabold text-sm text-blue-700 mt-5 bg-blue-50 py-2 rounded-lg border border-blue-100 shadow-sm";
            } 
            else if (etape >= 3) {
                c1.className = "w-9 h-9 rounded-full bg-green-600 text-white flex items-center justify-center font-bold text-sm";
                c2.className = "w-9 h-9 rounded-full bg-green-600 text-white flex items-center justify-center font-bold text-sm";
                c3.className = "w-9 h-9 rounded-full bg-green-700 text-white flex items-center justify-center font-bold text-sm shadow-md";
                line.style.width = "100%";
                txt.innerHTML = "✅ Colis livré avec succès ! Merci pour votre confiance.";
                txt.className = "text-center font-extrabold text-sm text-green-700 mt-5 bg-green-50 py-2 rounded-lg border border-green-100 shadow-sm";
            }
        }
    </script>
</body>
</html>
