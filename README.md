<html lang="nl">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Dossier Beheer Systeem (Lokaal)</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f7f7f9; }
    </style>
</head>
<body>
    <div id="app" class="min-h-screen flex items-center justify-center p-4">
        <div class="w-full max-w-xl bg-white p-6 md:p-10 rounded-xl shadow-2xl transition-all duration-300">

            <!-- Header -->
            <header class="text-center mb-8">
                <div class="flex items-center justify-center mb-3">
                    <span class="text-4xl text-blue-600 mr-2"><i data-lucide="folder-open"></i></span>
                    <h1 class="text-3xl font-extrabold text-gray-800">Dossier Beheer Systeem</h1>
                </div>
            </header>

            <!-- Login kaart -->
            <div id="login-card" class="bg-red-50 p-6 rounded-xl shadow-xl border border-red-200">
                <h2 class="text-2xl font-bold text-red-700 mb-4 flex items-center justify-center">
                    <i data-lucide="lock" class="w-6 h-6 mr-3"></i> Systeem Toegang Vereist
                </h2>
                <p class="mb-5 text-gray-600 text-center">Voer de beveiligingscode in om het dossierbeheer systeem te ontgrendelen.</p>
                <div class="mb-4">
                    <label for="login-code-input" class="block text-sm font-medium text-gray-700 mb-1">Toegangscode:</label>
                    <input type="password" id="login-code-input" placeholder="Voer beveiligingscode in"
                           class="w-full p-3 border border-gray-300 rounded-lg focus:ring-red-500 focus:border-red-500 shadow-inner text-gray-700 transition duration-150 text-center tracking-widest"
                           onkeydown="if(event.key === 'Enter') checkLoginCode()">
                </div>
                <button onclick="checkLoginCode()"
                        class="w-full px-4 py-3 bg-red-600 text-white font-bold rounded-lg hover:bg-red-700 transition duration-200 shadow-md flex items-center justify-center text-sm">
                    <i data-lucide="log-in" class="w-4 h-4 mr-2"></i> Inloggen
                </button>
                <p id="login-message" class="mt-4 text-center text-red-600 font-semibold hidden"></p>
            </div>

            <!-- Main controls (verborgen tot login) -->
            <div id="main-controls" class="hidden mt-6">
                <div class="mb-4">
                    <label for="naam-input" class="block text-sm font-medium text-gray-700 mb-1">Volledige Naam:</label>
                    <input type="text" id="naam-input" placeholder="Naam van het te zoeken/creëren dossier"
                           class="w-full p-3 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500 shadow-inner text-gray-700 transition duration-150"
                           onkeydown="if(event.key === 'Enter') zoekBestaand()">
                </div>

                <div class="mb-4">
                    <label for="notitie-input" class="block text-sm font-medium text-gray-700 mb-1">NIEUWE Notitie / Geheime Hint Toevoegen:</label>
                    <textarea id="notitie-input" placeholder="Voer hier de NIEUWE notitie in die aan de bestaande hint zal worden toegevoegd."
                              rows="3"
                              class="w-full p-3 border border-gray-300 rounded-lg focus:ring-green-500 focus:border-green-500 shadow-inner text-gray-700 transition duration-150"></textarea>
                </div>

                <input type="hidden" id="current-doc-id" value="">

                <div class="grid grid-cols-2 sm:grid-cols-4 gap-3">
                    <button id="search-btn" onclick="zoekBestaand()"
                            class="col-span-2 sm:col-span-1 px-4 py-3 bg-blue-600 text-white font-bold rounded-lg hover:bg-blue-700 transition duration-200 shadow-md flex items-center justify-center text-sm">
                        <i data-lucide="search" class="w-4 h-4 mr-2"></i> Zoek
                    </button>

                    <button id="add-btn" onclick="maakNieuwAan()"
                            class="px-4 py-3 bg-green-600 text-white font-bold rounded-lg hover:bg-green-700 transition duration-200 shadow-md flex items-center justify-center text-sm">
                        <i data-lucide="folder-plus" class="w-4 h-4 mr-2"></i> Nieuw
                    </button>

                    <button id="update-btn" onclick="updateDossier()"
                            class="px-4 py-3 bg-yellow-600 text-white font-bold rounded-lg hover:bg-yellow-700 transition duration-200 shadow-md flex items-center justify-center text-sm" disabled>
                        <i data-lucide="save" class="w-4 h-4 mr-2"></i> Notitie Toevoegen
                    </button>

                    <button id="delete-btn" onclick="verwijderDossierPrompt()"
                            class="px-4 py-3 bg-red-600 text-white font-bold rounded-lg hover:bg-red-700 transition duration-200 shadow-md flex items-center justify-center text-sm" disabled>
                        <i data-lucide="trash-2" class="w-4 h-4 mr-2"></i> Verwijderen (Dossier)
                    </button>
                </div>
            </div>

            <!-- Output -->
            <div id="dossier-output" class="mt-8"></div>

            <!-- Confirmatie modal -->
            <div id="confirmation-modal" class="hidden fixed inset-0 bg-gray-900 bg-opacity-75 flex items-center justify-center z-50 p-4">
                <div class="bg-white p-6 rounded-xl shadow-2xl w-full max-w-sm">
                    <h3 id="modal-title" class="text-xl font-bold text-red-700 mb-4 flex items-center">
                         <i data-lucide="alert-triangle" class="w-6 h-6 mr-3 text-red-500"></i> Bevestig Verwijdering
                    </h3>
                    <p id="modal-message" class="text-gray-600 mb-6"></p>
                    <div class="flex justify-end gap-3">
                        <button id="modal-cancel" class="px-4 py-2 bg-gray-300 text-gray-800 font-semibold rounded-lg hover:bg-gray-400 transition">Annuleren</button>
                        <button id="modal-confirm" class="px-4 py-2 bg-red-600 text-white font-semibold rounded-lg hover:bg-red-700 transition">Verwijder</button>
                    </div>
                </div>
            </div>

        </div>
    </div>

    <script>
        const REQUIRED_CODE = "1237151213";
        const NOTE_DELIMITER = '\n\n--- [Notitie op '; 
        let currentDossierId = null;

        // --- Login ---
        function checkLoginCode() {
            const input = document.getElementById('login-code-input');
            const msg = document.getElementById('login-message');
            msg.classList.add('hidden');

            if(input.value === REQUIRED_CODE){
                document.getElementById('login-card').classList.add('hidden');
                document.getElementById('main-controls').classList.remove('hidden');
                msg.textContent = "Succesvol ingelogd!";
                msg.classList.remove('hidden','text-red-600');
                msg.classList.add('text-green-600');
            } else {
                msg.textContent = "Foutieve code ingevoerd. Probeer opnieuw.";
                msg.classList.remove('hidden','text-green-600');
                msg.classList.add('text-red-600');
            }
        }

        // --- Helpers ---
        function escapeHTML(str) {
            return str.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
        }

        function getDossiers() {
            return JSON.parse(localStorage.getItem('dossiers')||'{}');
        }

        function saveDossiers(dossiers){
            localStorage.setItem('dossiers', JSON.stringify(dossiers));
        }

        function generateId(){
            return crypto.randomUUID();
        }

        function generateDossierNummer(){
            const num = Math.floor(Math.random()*900)+100;
            const year = new Date().getFullYear();
            return `XX-${year}-${num}`;
        }

        function displayMessage(msg,type='blue'){
            const out = document.getElementById('dossier-output');
            const colors = {
                blue: ['bg-blue-50','border-blue-200','text-blue-700','info'],
                red: ['bg-red-50','border-red-200','text-red-700','x-circle'],
                yellow: ['bg-yellow-50','border-yellow-200','text-yellow-700','alert-triangle'],
                green: ['bg-green-50','border-green-200','text-green-700','check-circle']
            }
            const [bg,border,text,icon] = colors[type] || colors.blue;
            out.innerHTML = `<div class="border ${bg} ${border} p-4 rounded-xl shadow-md flex items-center"><i data-lucide="${icon}" class="w-5 h-5 mr-3"></i><p class="font-medium">${msg}</p></div>`;
            lucide.createIcons();
        }

        function setActionButtons(mode){
            const addBtn = document.getElementById('add-btn');
            const updateBtn = document.getElementById('update-btn');
            const deleteBtn = document.getElementById('delete-btn');
            addBtn.disabled = updateBtn.disabled = deleteBtn.disabled = false;
            if(mode==='found'){
                addBtn.disabled = true;
            } else if(mode==='not-found'){
                updateBtn.disabled = deleteBtn.disabled = true;
            }
        }

        // --- Zoek dossier ---
        function zoekBestaand(){
            const naamInput = document.getElementById('naam-input').value.trim().toLowerCase();
            const dossiers = getDossiers();
            if(!naamInput){ displayMessage("Voer een naam in om te zoeken.","yellow"); setActionButtons('not-found'); return;}
            let found = null;
            for(const id in dossiers){
                if(dossiers[id].naam.toLowerCase()===naamInput){ found = dossiers[id]; currentDossierId=id; break;}
            }
            if(found){
                displayDossier(found);
                setActionButtons('found');
            } else {
                currentDossierId=null;
                displayMessage(`Geen dossier gevonden voor '${naamInput}'.`,'red');
                setActionButtons('not-found');
            }
        }

        function displayDossier(dossier){
            let notesHtml='';
            const notes = dossier.geheimeHint.split(NOTE_DELIMITER).filter(n=>n);
            notes.forEach((note,i)=>{
                notesHtml+=`<div class="p-3 mt-2 border border-gray-300 rounded-lg bg-white shadow-sm">
                <strong class="text-xs font-semibold text-blue-700">Notitie ${i+1}</strong>
                <p class="text-gray-800 text-sm whitespace-pre-wrap mt-1">${escapeHTML(note)}</p></div>`;
            });
            document.getElementById('dossier-output').innerHTML=`
            <div class="bg-green-50 border border-green-200 p-6 rounded-xl shadow-lg">
                <h2 class="text-2xl font-bold text-green-700 mb-4 flex items-center"><i data-lucide="file-check" class="w-6 h-6 mr-2"></i> Dossier Gevonden</h2>
                <p><strong>Naam:</strong> ${dossier.naam}</p>
                <p><strong>Dossier Nr.:</strong> ${dossier.dossierNummer}</p>
                <div class="mt-4">${notesHtml}</div>
            </div>`;
            lucide.createIcons();
        }

        // --- Maak nieuw dossier ---
        function maakNieuwAan(){
            const naam = document.getElementById('naam-input').value.trim();
            const notitie = document.getElementById('notitie-input').value.trim()||'Leeg dossier';
            if(!naam){ displayMessage("Voer een naam in.","red"); return;}
            const dossiers = getDossiers();
            for(const id in dossiers){ if(dossiers[id].naam.toLowerCase()===naam.toLowerCase()){ displayMessage("Dossier bestaat al.","yellow"); return;}}
            const id = generateId();
            dossiers[id]={naam:naam,dossierNummer:generateDossierNummer(),geheimeHint:notitie};
            saveDossiers(dossiers);
            currentDossierId=id;
            displayDossier(dossiers[id]);
            setActionButtons('found');
            document.getElementById('naam-input').value='';
            document.getElementById('notitie-input').value='';
        }

        // --- Update dossier ---
        function updateDossier(){
            if(!currentDossierId){ displayMessage("Geen dossier geselecteerd.","red"); return;}
            const newNote = document.getElementById('notitie-input').value.trim();
            if(!newNote){ displayMessage("Voer een notitie in.","yellow"); return;}
            const dossiers = getDossiers();
            dossiers[currentDossierId].geheimeHint += NOTE_DELIMITER + newNote;
            saveDossiers(dossiers);
            displayDossier(dossiers[currentDossierId]);
            document.getElementById('notitie-input').value='';
        }

        // --- Verwijder dossier ---
        function verwijderDossierPrompt(){
            if(!currentDossierId){ displayMessage("Geen dossier geselecteerd.","red"); return;}
            const modal = document.getElementById('confirmation-modal');
            const msg = document.getElementById('modal-message');
            msg.textContent = `Weet je zeker dat je dossier '${getDossiers()[currentDossierId].naam}' wilt verwijderen?`;
            modal.classList.remove('hidden');
            document.getElementById('modal-confirm').onclick=()=>{
                const dossiers = getDossiers();
                delete dossiers[currentDossierId];
                saveDossiers(dossiers);
                currentDossierId=null;
                modal.classList.add('hidden');
                document.getElementById('naam-input').value='';
                document.getElementById('notitie-input').value='';
                displayMessage("Dossier verwijderd.","red");
                setActionButtons('not-found');
            }
            document.getElementById('modal-cancel').onclick=()=>{modal.classList.add('hidden'); displayMessage("Verwijderen geannuleerd.","blue");};
        }

        document.addEventListener('DOMContentLoaded',()=>{lucide.createIcons();});
    </script>
</body>
</html>
