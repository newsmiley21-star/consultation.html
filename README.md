<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suivi Colis CT241</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide"></script>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">

    <div class="w-full max-w-md bg-white p-8 rounded-3xl shadow-2xl border-t-8 border-[#007fff]">
        
        <div class="text-center mb-8">
            <h1 class="text-3xl font-extrabold text-[#007fff]">CT241</h1>
            <p class="text-gray-500 mt-2">Suivi de commande en temps réel</p>
        </div>

        <div class="space-y-4">
            <input type="text" id="orderId" placeholder="Entrez votre ID de commande" 
                   class="w-full p-4 border-2 border-gray-200 rounded-xl focus:border-[#007fff] focus:ring-0 outline-none transition">
            
            <button onclick="rechercher()" class="w-full bg-[#007fff] hover:bg-blue-700 text-white py-4 rounded-xl font-bold flex items-center justify-center gap-2 transition-all transform hover:scale-[1.02]">
                <i data-lucide="search" class="w-5 h-5"></i>
                Rechercher
            </button>
        </div>

        <div id="resultat" class="mt-8 hidden"></div>
    </div>

    <script>
        lucide.createIcons();

        async function rechercher() {
            const csvUrl = "https://docs.google.com/spreadsheets/d/1CugnnRUwLlUzlPOUbYfjcaocHHDiyhYLWsy_Fv3vPgQ/edit?usp=sharing"; 
            const orderId = document.getElementById('orderId').value.trim().toUpperCase();
            const divResult = document.getElementById('resultat');

            if(!orderId) return alert("Veuillez entrer un ID.");

            try {
                const response = await fetch(csvUrl);
                const data = await response.text();
                // .slice(1) enlève la ligne des titres
                const lignes = data.split('\n').slice(1);
                
                const match = lignes.find(l => l.split(',')[0].trim().toUpperCase() === orderId);

                if (match) {
                    const c = match.split(',');
                    divResult.classList.remove('hidden');
                    divResult.innerHTML = `
                        <div class="p-6 rounded-2xl bg-gradient-to-br from-[#FFD700]/20 to-white border border-[#FFD700]">
                            <h2 class="font-bold text-[#007fff] text-lg mb-4 text-center border-b border-[#FFD700] pb-2">Informations Commande</h2>
                            <div class="space-y-3">
                                <p class="text-sm"><strong>Nom :</strong> ${c[3] || 'N/A'}</p>
                                <p class="text-sm"><strong>Tél :</strong> ${c[4] || 'N/A'}</p>
                                <p class="text-sm"><strong>Paiement :</strong> ${c[6] || 'N/A'}</p>
                                <div class="bg-white p-3 rounded-lg border border-[#007fff]">
                                    <p class="text-xs text-gray-500 uppercase font-bold">Statut Actuel</p>
                                    <p class="text-lg font-black text-[#007fff]">${c[6] || 'En cours'}</p>
                                </div>
                                <p class="text-[10px] text-gray-400 text-center pt-2">Date d'enregistrement : ${c[1] || 'N/A'}</p>
                            </div>
                        </div>`;
                } else {
                    alert("ID non trouvé.");
                }
            } catch (err) {
                alert("Erreur de connexion.");
            }
        }
    </script>
</body>
</html>
