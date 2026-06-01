<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suivi CT241 - Production</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide"></script>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">

    <div class="w-full max-w-sm bg-white p-6 rounded-3xl shadow-xl border-t-4 border-[#007fff]">
        <div class="text-center mb-6">
            <h1 class="text-xl font-bold text-gray-800">CT241 - Suivi</h1>
            <p class="text-xs text-gray-400">Recherchez votre statut par ID de commande</p>
        </div>

        <div class="space-y-4">
            <input type="text" id="orderId" placeholder="Entrez votre ID" 
                   class="w-full p-4 border border-gray-200 rounded-xl focus:ring-2 focus:ring-[#007fff] outline-none">
            
            <button onclick="rechercher()" class="w-full bg-[#007fff] text-white py-4 rounded-xl font-bold flex items-center justify-center gap-2 hover:bg-blue-600 transition">
                <i data-lucide="search" class="w-5 h-5 text-white" style="stroke: white;"></i>
                Consulter
            </button>
        </div>

        <div id="resultat" class="mt-6 hidden"></div>
    </div>

    <script>
        lucide.createIcons();

        async function rechercher() {
            // REMPLACEZ CECI PAR VOTRE LIEN CSV PUBLIÉ
            const csvUrl = "VOTRE_LIEN_CSV_PUBLIÉ_ICI"; 
            const orderId = document.getElementById('orderId').value.trim().toUpperCase();
            const divResult = document.getElementById('resultat');

            if(!orderId) return alert("Veuillez entrer un ID de commande.");

            try {
                const response = await fetch(csvUrl);
                const data = await response.text();
                const lignes = data.split('\n');
                
                const match = lignes.find(l => l.split(',')[0].trim().toUpperCase() === orderId);

                if (match) {
                    const c = match.split(',');
                    // MAPPING DES COLONNES (Index 0 = Col A)
                    divResult.classList.remove('hidden');
                    divResult.innerHTML = `
                        <div class="p-4 rounded-xl bg-blue-50 border border-blue-100 space-y-3">
                            <h2 class="font-bold text-[#007fff] text-center border-b border-blue-200 pb-2">Détails de la commande</h2>
                            <p class="text-sm"><strong>Nom :</strong> ${c[3] || 'N/A'}</p>
                            <p class="text-sm"><strong>Tél :</strong> ${c[4] || 'N/A'}</p>
                            <p class="text-sm"><strong>Paiement :</strong> ${c[6] || 'N/A'}</p>
                            <p class="text-sm"><strong>Statut :</strong> <span class="font-bold text-gray-800">${c[14] || 'En cours'}</span></p>
                            <p class="text-[10px] text-gray-400 text-center pt-2">Mise à jour : ${c[2] || 'N/A'}</p>
                        </div>`;
                } else {
                    alert("Aucune commande trouvée pour cet ID.");
                }
            } catch (err) {
                alert("Erreur de connexion.");
            }
        }
    </script>
</body>
</html>
