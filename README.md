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

    <div class="w-full max-w-md bg-white p-8 rounded-3xl shadow-2xl border-t-8 border-[#007fff]">
        <div class="text-center mb-8">
            <h1 class="text-3xl font-extrabold text-[#007fff]">CT241</h1>
            <p class="text-gray-500 mt-2">Suivi de commande personnalisé</p>
        </div>

        <div class="space-y-4">
            <input type="text" id="orderId" placeholder="Entrez votre ID de commande" 
                   class="w-full p-4 border-2 border-gray-200 rounded-xl focus:border-[#007fff] outline-none">
            
            <button onclick="rechercher()" class="w-full bg-[#007fff] hover:bg-blue-700 text-white py-4 rounded-xl font-bold transition-all">
                Consulter ma commande
            </button>
        </div>

        <div id="resultat" class="mt-8 hidden"></div>
    </div>

    <script>
        async function rechercher() {
            // Remplacez par votre lien de publication CSV
            const csvUrl = "https://docs.google.com/spreadsheets/d/e/2PACX-1vTUjpOIQdmYrCKhsbhb2j3tqh7nuIZKh_kxwNYntWS0GLC64U5e2ydzlvJNvcERQ9TdJefUgYFupt46/pub?output=csv"; 
            const orderId = document.getElementById('orderId').value.trim().toUpperCase();
            const divResult = document.getElementById('resultat');

            try {
                const response = await fetch(csvUrl);
                const data = await response.text();
                const lignes = data.split('\n').slice(1);
                
                const match = lignes.find(l => l.split(',')[16].trim().toUpperCase() === orderId);

                if (match)  
               const match = lignes.find(l => {
               const colonnes = l.split(',');
    // On vérifie les colonnes 0 à 4 (A, B, C, D, E) pour voir si l'ID s'y trouve
    return colonnes.slice(0, 17).some(col => col.trim().toUpperCase() === orderId);
});
                    let couleur = "bg-gray-100 text-gray-600";
                    if (statutBrut === "valide") couleur = "bg-green-100 text-green-700";
                    else if (statutBrut === "en attente") couleur = "bg-orange-100 text-orange-700";
                    else if (statutBrut === "erreur de paiement") couleur = "bg-red-100 text-red-700";

                    divResult.classList.remove('hidden');
                    divResult.innerHTML = `
                        <div class="p-6 rounded-2xl bg-yellow-50 border border-yellow-200 shadow-inner">
                            <h2 class="font-bold text-[#007fff] mb-4 border-b border-yellow-200 pb-2">Détails de la commande</h2>
                            <p class="mb-2"><strong>Nom :</strong> ${c[3] || 'N/A'}</p>
                            <p class="mb-4"><strong>Tél :</strong> ${c[4] || 'N/A'}</p>
                            <p class="mb-4"><strong>paiement :</strong> ${c[6] || 'N/A'}</p>
                            <div class="text-center mt-4">
                                <span class="px-6 py-2 rounded-full font-black uppercase text-sm ${couleur}">
                                    ${c[6] || 'Statut inconnu'}
                                </span>
                            </div>
                        </div>`;
                } else {
                    alert("ID non trouvé. Vérifiez votre saisie.");
                }
            } catch (err) {
                alert("Erreur de connexion au fichier de données.");
            }
        }
    </script>
</body>
</html>
