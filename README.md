<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suivi CT241</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.0.0/firebase-app.js";
        import { getDatabase, ref, set, get } from "https://www.gstatic.com/firebasejs/9.0.0/firebase-database.js";

        // --- ZONE DE CONFIGURATION (À REMPLIR) ---
        const firebaseConfig = {
            apiKey: "VOTRE_API_KEY",
            authDomain: "VOTRE_PROJET.firebaseapp.com",
            databaseURL: "https://VOTRE_PROJET-default-rtdb.firebaseio.com",
            projectId: "VOTRE_PROJET",
            storageBucket: "VOTRE_PROJET.appspot.com",
            messagingSenderId: "VOTRE_ID",
            appId: "VOTRE_APP_ID"
        };
        const ADMIN_CODE = "12901564";
        // -----------------------------------------

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);

        window.consulter = async () => {
            const id = document.getElementById('searchId').value.toUpperCase();
            const res = await get(ref(db, 'missions/' + id));
            if (res.exists()) {
                const d = res.val();
                let color = d.statut === "Livré" ? "green" : (d.statut === "Erreur de paiement" ? "red" : "blue");
                document.getElementById('resultat').innerHTML = `
                    <div class="p-4 rounded-xl border-l-4 border-${color}-500 bg-gray-50">
                        <p class="font-bold">Statut : <span class="text-${color}-600">${d.statut}</span></p>
                        <p class="text-sm text-gray-500">Mise à jour : ${d.date}</p>
                    </div>`;
            } else { alert("Mission introuvable."); }
        };

        window.loginAdmin = () => {
            if (prompt("Code Admin:") === ADMIN_CODE) {
                document.getElementById('adminZone').classList.remove('hidden');
            } else { alert("Accès refusé"); }
        };

        window.sauvegarder = () => {
            const id = document.getElementById('adminId').value.toUpperCase();
            const status = document.getElementById('adminStatus').value;
            set(ref(db, 'missions/' + id), { statut: status, date: new Date().toLocaleDateString() })
            .then(() => alert("Mission mise à jour dans la base !"));
        };
    </script>
</head>
<body class="bg-gray-100 p-4">

    <div class="max-w-md mx-auto bg-white p-6 rounded-2xl shadow-lg border-t-4 border-blue-600">
        <h1 class="text-2xl font-bold mb-5 text-gray-800">Suivi Logistique</h1>
        <input id="searchId" type="text" placeholder="Entrez le n° de mission" class="w-full p-3 border rounded-xl mb-3">
        <button onclick="consulter()" class="w-full bg-blue-600 text-white py-3 rounded-xl font-bold">Consulter</button>
        <div id="resultat" class="mt-6"></div>
    </div>

    <div id="adminZone" class="hidden max-w-md mx-auto mt-8 bg-red-50 p-6 rounded-2xl shadow border border-red-200">
        <h2 class="font-bold text-red-700 mb-4 uppercase">Panneau Administrateur</h2>
        <input id="adminId" type="text" placeholder="N° Mission" class="w-full p-2 border mb-2 rounded">
        <select id="adminStatus" class="w-full p-2 border mb-2 rounded">
            <option>En cours</option>
            <option>Livré</option>
            <option>Erreur de paiement</option>
        </select>
        <button onclick="sauvegarder()" class="w-full bg-red-600 text-white py-2 rounded font-bold">Mettre à jour</button>
    </div>

    <button onclick="loginAdmin()" class="fixed bottom-4 right-4 bg-gray-800 text-white p-2 rounded-full shadow-lg">⚙️</button>
</body>
</html>
