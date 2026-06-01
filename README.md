<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suivi CT241</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 p-4">

    <div class="max-w-md mx-auto bg-white p-8 rounded-3xl shadow-xl border-t-8 border-[#007fff]">
        <h1 class="text-2xl font-bold text-center text-[#007fff] mb-6">CT241 - Suivi</h1>
        
        <input type="text" id="orderId" placeholder="Entrez votre ID de commande" class="w-full p-4 border-2 rounded-xl mb-4">
        <button onclick="rechercher()" class="w-full bg-[#007fff] text-white py-4 rounded-xl font-bold hover:bg-blue-700 transition">
            Consulter
        </button>
        
        <div id="resultat" class="mt-6 hidden"></div>
    </div>

    <script>
        async function rechercher() {
            // Remplacez par votre lien GIST RAW ou lien CSV fonctionnel
            const csvUrl = "https://docs.google.com/spreadsheets/d/e/2PACX-1vTUjpOIQdmYrCKhsbhb2j3tqh7nuIZKh_kxwNYntWS0GLC64U5e2ydzlvJNvcERQ9TdJefUgYFupt46/pub?output=csv"; 
            const orderId = document.getElementById('orderId').value.trim().toUpperCase();
            const divResult = document.getElementById('resultat');

            if(!orderId) {
                alert("Veuillez entrer un ID.");
                return;
            }

            try {
                const response = await fetch(csvUrl);
                if (!response.ok) throw new Error("Erreur de chargement");
                
                const data = await response.text();
                // On sépare les lignes et on enlève l'entête
                const lignes = data.split('\n').slice(1);
                
                const match = lignes.find(l => l.split(',')[0].trim().toUpperCase() === orderId);

                if (match) {
                    const c = match.split(',');
                    const statut = c[15] ? c[15].trim().toLowerCase() : "";
                    const telBrut = c[4] ? c[4].trim() : "";
    const telMasque = (telBrut.length >= 7) ? telBrut.substring(0, 3) + "****" + telBrut.substring(7) : telBrut;
                    
                    let couleur = "bg-gray-100 text-gray-600";
                    if (statut === "validé") couleur = "bg-green-100 text-green-700";
                    else if (statut === "en attente") couleur = "bg-orange-100 text-orange-700";
                    else if (statut === "erreur de paiement") couleur = "bg-red-100 text-red-700";

                    divResult.classList.remove('hidden');
                    divResult.innerHTML = `
                        <div class="p-6 rounded-2xl bg-yellow-50 border border-yellow-200 text-center">
                            <p class="mb-2"><strong>Nom :</strong> ${c[3] || 'N/A'}</p>
                            <p class="mb-2"><strong>Tél :</strong> ${c[4] || 'N/A'}</p>
                             <p class="mb-2"><strong>Date :</strong> ${c[1] || 'N/A'}</p>
                              <p class="mb-2"><strong>facturation :</strong> ${c[6] || 'N/A'}</p>
                            <div class="mt-4 px-4 py-2 rounded-full font-black uppercase ${couleur}">
                                ${c[15] || 'Statut inconnu'}
                            </div>
                        </div>`;
                } else {
                    alert("ID non trouvé dans le fichier.");
                }
            } catch (err) {
                console.error(err);
                alert("Erreur de connexion : " + err.message);
            }
        }
    </script>
</body>
</html>
