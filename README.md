<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>X-PRESS - Suivi de Livraison</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        .step-active { color: #009E60; }
        .step-line-active { background-color: #009E60; }
    </style>
</head>
<body class="bg-gray-50 min-height-screen flex flex-col justify-between">

<div class="p-4 flex-grow flex items-center justify-center">
    <div class="w-full max-w-md bg-white rounded-2xl shadow-xl border border-gray-100 p-6 space-y-6">
        
        <div class="text-center space-y-2">
        <img id="splash-logo" src="https://res.cloudinary.com/dyxob1wcj/image/upload/v1781134790/oeqlbzjhuvhbpmrzdpgh.jpg" alt="Logo" class="absolute w-28 h-20">
            </div>
            </div>
            <h1 class="text-2xl font-extrabold text-gray-900 tracking-tight">Suivre mon colis</h1>
            <p class="text-sm text-gray-500">Entrez l'identifiant à 4 chiffres fourni lors de votre commande.</p>
        </div>

        <div class="space-y-3">
            <div class="relative">
                <span class="absolute inset-y-0 left-0 flex items-center pl-4 text-xl text-gray-400">#</span>
                <input type="number" id="searchId" pattern="[0-9]*" inputmode="numeric" placeholder="Ex: 4345" 
                       class="w-full p-4 pl-9 pr-24 border-2 border-gray-200 rounded-xl font-bold text-lg focus:border-green-600 focus:outline-none transition-all">
                <button onclick="rechercherColis()" 
                        class="absolute right-2 top-2 bottom-2 bg-green-600 hover:bg-green-700 text-white font-bold px-4 rounded-lg text-sm transition-colors">
                    SUIVRE
                </button>
            </div>
        </div>

        <div id="resultZone" class="hidden space-y-6 pt-4 border-t border-gray-100 animate-fade-in">
            
            <div class="bg-gray-50 p-4 rounded-xl border border-gray-100">
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
                    <span class="text-gray-500 font-medium">Heure d'enregistrement</span>
                    <span id="trackHeure" class="text-gray-900 font-bold">-</span>
                </div>
                <div id="instructionBlock" class="bg-yellow-50 p-3 rounded-lg border border-yellow-100 text-xs text-yellow-800 font-medium hidden">
                    💡 <span id="trackInstructions"></span>
                </div>
            </div>

        </div>

        <div id="statusMessage" class="hidden text-center p-4 bg-red-50 text-red-700 font-bold text-sm rounded-xl border border-red-100"></div>

    </div>
</div>

<footer class="text-center p-4 text-xs font-semibold text-gray-400">
    &copy; 2026 X-PRESS LOGISTIQUE GABON — Tous droits réservés.
</footer>

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
    
    // Aligne un numéro classique (ex: 062550026 -> 062****26)
    function masquerTelephone(telString) {
        if (!telString) return "Inconnu";
        let clean = telString.trim().replace(/\s+/g, ''); // Enlever les espaces blancs
        if (clean.length >= 9) {
            return clean.substring(0, 3) + "****" + clean.substring(7);
        }
        // Sécurité générique si le format change
        return clean.substring(0, 2) + "***" + clean.substring(clean.length - 2);
    }

    // Aligne la chaîne de l'expéditeur (ex: zarashop241-066457172 -> zarashop241 (066****72))
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

        try {
            // Requête ciblée Firebase sur le noeud "id" de la table "missions"
            const q = query(ref(db, 'missions'), orderByChild('id'), equalTo(parseInt(inputId)));
            const snapshot = await get(q);

            if (!snapshot.exists()) {
                statusMessage.innerText = "❌ Aucun colis trouvé avec l'identifiant #" + inputId;
                statusMessage.classList.remove('hidden');
                return;
            }

            // Extraction des données trouvées
            let missionData = null;
            snapshot.forEach(childSnapshot => {
                missionData = childSnapshot.val();
            });

            // --- AFICHAGE ET INJECTION SÉCURISÉE DES DONNÉES CLIENTS ---
            document.getElementById('trackNom').innerText = missionData.nom || "Client X-Press";
            document.getElementById('trackTel').innerText = masquerTelephone(missionData.tel);
            document.getElementById('trackExp').innerText = masquerExpediteur(missionData.expediteur);
            document.getElementById('trackLieu').innerText = missionData.livraison || missionData.lieu || "Non spécifié";
            document.getElementById('trackNature').innerText = missionData.nature || "Marchandise";
            document.getElementById('trackHeure').innerText = missionData.heureSeule || "En cours de traitement";

            // Bloc instructions particulières
            const instBlock = document.getElementById('instructionBlock');
            if (missionData.instructions) {
                document.getElementById('trackInstructions').innerText = missionData.instructions;
                instBlock.classList.remove('hidden');
            } else {
                instBlock.classList.add('hidden');
            }

            // --- GESTION VISUELLE DU STATUT (ÉTAPES DE LIVRAISON) ---
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

    // Ajuste dynamiquement la barre graphique de progression
    function actualiserTimeline(etape, nomLivreur) {
        const c1 = document.getElementById('circle-1');
        const c2 = document.getElementById('circle-2');
        const c3 = document.getElementById('circle-3');
        const line = document.getElementById('progressLine');
        const txt = document.getElementById('statusText');

        // Réinitialisation par défaut
        [c1, c2, c3].forEach(c => {
            c.className = "w-9 h-9 rounded-full bg-gray-200 flex items-center justify-center font-bold text-sm text-gray-600 transition-colors";
        });

        if (etape === 1) {
            // Étape 1 : Commande en ligne / Recherche de coursier
            c1.className = "w-9 h-9 rounded-full bg-green-600 text-white flex items-center justify-center font-bold text-sm shadow-md";
            line.style.width = "0%";
            txt.innerHTML = "🕒 Commande reçue : En attente d'un livreur";
            txt.className = "text-center font-extrabold text-sm text-amber-700 mt-5 bg-amber-50 py-2 rounded-lg border border-amber-100 shadow-sm";
        } 
        else if (etape === 2 || (etape === 1 && nomLivreur && nomLivreur !== "Libre")) {
            // Étape 2 : Le coursier a accepté la course et est en transit
            c1.className = "w-9 h-9 rounded-full bg-green-600 text-white flex items-center justify-center font-bold text-sm";
            c2.className = "w-9 h-9 rounded-full bg-green-600 text-white flex items-center justify-center font-bold text-sm shadow-md";
            line.style.width = "50%";
            txt.innerHTML = `🚴 Colis récupéré ! Livreur en route`;
            txt.className = "text-center font-extrabold text-sm text-blue-700 mt-5 bg-blue-50 py-2 rounded-lg border border-blue-100 shadow-sm";
        } 
        else if (etape >= 3) {
            // Étape 3 : Finalisé (Archivé ou validé par signature/code)
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
