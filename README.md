<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Impossible Levels List | Pro Panel</title>
    <style>
        :root {
            --bg: #030303; --sidebar: #080808; --card: #111;
            --accent: #5865f2; --border: #1f1f1f; --text: #eee;
            --text-dim: #777; --gold: #f1c40f; --delete: #e74c3c;
            --success: #43b581; --owner: #ff4747;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', system-ui, sans-serif; }
        body { background: var(--bg); color: var(--text); display: flex; min-height: 100vh; overflow-x: hidden; }

        /* --- Sidebar & Nav --- */
        .sidebar { width: 280px; background: var(--sidebar); border-right: 1px solid var(--border); padding: 30px; display: flex; flex-direction: column; position: fixed; height: 100vh; overflow-y: auto; }
        .nav-label { font-size: 0.7rem; color: var(--text-dim); text-transform: uppercase; letter-spacing: 1px; margin: 20px 0 10px; }
        .nav-item { padding: 12px 15px; cursor: pointer; border-radius: 8px; color: var(--text-dim); transition: 0.2s; margin-bottom: 5px; font-weight: 500; font-size: 0.9rem; }
        .nav-item:hover, .nav-item.active { background: #1a1a1a; color: white; }
        .nav-item.active { border-left: 4px solid var(--accent); background: linear-gradient(90deg, #1a1a1a, transparent); }

        /* --- Main Content --- */
        main { flex-grow: 1; margin-left: 280px; padding: 50px; max-width: 1300px; }
        .view { display: none; animation: fadeIn 0.4s ease; }
        .view.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        /* --- Level Cards --- */
        .level-card { background: var(--card); border: 1px solid var(--border); border-radius: 12px; margin-bottom: 20px; display: flex; height: 160px; overflow: hidden; position: relative; }
        .level-img { width: 280px; object-fit: cover; border-right: 1px solid var(--border); }
        .level-main { flex: 1; padding: 25px; display: flex; flex-direction: column; justify-content: center; }
        .rank-num { font-size: 1.8rem; font-weight: 900; color: var(--accent); margin-right: 15px; font-style: italic; }
        .wr-badge { background: rgba(241, 196, 15, 0.1); color: var(--gold); padding: 4px 10px; border-radius: 5px; font-size: 0.75rem; border: 1px solid rgba(241, 196, 15, 0.3); display: inline-block; margin-top: 8px; }
        .completed-tag { color: var(--success); font-size: 0.8rem; font-weight: bold; margin-left: 10px; letter-spacing: 1px; vertical-align: middle; background: rgba(67, 181, 129, 0.1); padding: 3px 8px; border-radius: 4px; }
        
        .admin-tools { display: flex; flex-direction: column; gap: 5px; background: #000; padding: 10px; border-left: 1px solid var(--border); }
        .tool-btn { padding: 5px; background: #222; border: none; color: white; cursor: pointer; border-radius: 4px; font-size: 10px; }
        .tool-btn:hover { background: var(--accent); }

        /* --- UI Elements --- */
        table { width: 100%; border-collapse: collapse; background: var(--card); border-radius: 12px; overflow: hidden; margin-top: 20px; }
        th { text-align: left; padding: 15px 20px; background: #1a1a1a; color: var(--text-dim); font-size: 0.8rem; }
        td { padding: 15px 20px; border-bottom: 1px solid var(--border); }

        input, select, textarea { width: 100%; padding: 14px; background: #0a0a0a; border: 1px solid var(--border); color: white; border-radius: 8px; margin-top: 10px; outline: none; }
        input:focus { border-color: var(--accent); }
        button { padding: 12px 24px; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; background: var(--accent); color: white; margin-top: 15px; }

        .role-badge { padding: 3px 8px; border-radius: 4px; font-size: 0.7rem; font-weight: bold; }
        .r-owner { background: rgba(255,71,71,0.1); color: var(--owner); border: 1px solid var(--owner); }
        .r-admin { background: rgba(88,101,242,0.1); color: var(--accent); border: 1px solid var(--accent); }
        .r-user { background: #222; color: var(--text-dim); }

        /* --- Modals --- */
        .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.9); z-index: 3000; justify-content: center; align-items: center; }
        .modal-card { background: var(--card); padding: 30px; border-radius: 15px; border: 1px solid var(--border); width: 400px; text-align: center; }
    </style>
</head>
<body>

    <div id="authOverlay" class="modal" style="display:flex;">
        <div class="modal-card">
            <h2 style="margin-bottom: 10px;">ILL Panel</h2>
            <input type="text" id="au" placeholder="Username">
            <input type="password" id="ap" placeholder="Password">
            <button style="width:100%" onclick="login()">Enter List</button>
            <p onclick="signup()" style="color:var(--accent); cursor:pointer; font-size:0.8rem; margin-top:20px;">New? Create Account</p>
        </div>
    </div>

    <div id="placementModal" class="modal">
        <div class="modal-card">
            <h3 style="margin-bottom: 10px;">Assign Rank</h3>
            <p style="color:var(--text-dim); font-size:0.8rem; margin-bottom:15px;">Where should this level be placed? (1 to unlimited)</p>
            <input type="number" id="acceptRank" placeholder="Rank (e.g., 1 for Top 1)" min="1">
            <button style="width:100%; background:var(--success);" onclick="confirmAcceptLvl()">Accept & Place</button>
            <button style="width:100%; background:#222; margin-top:10px;" onclick="closeModals()">Cancel</button>
        </div>
    </div>

    <div class="sidebar">
        <h1 style="font-size: 1.4rem; letter-spacing: -1px;">ILL <span style="color:var(--accent)">100</span></h1>
        
        <div class="nav-label">General</div>
        <div class="nav-item active" id="n-list" onclick="setView('list')">Main List</div>
        <div class="nav-item" id="n-leader" onclick="setView('leader')">Leaderboard</div>
        <div class="nav-item" id="n-search" onclick="setView('search')">User Directory</div>

        <div class="nav-label">Interact</div>
        <div class="nav-item" id="n-submit" onclick="setView('submit')">Submit Record</div>
        <div class="nav-item" id="n-submitlvl" onclick="setView('submitlvl')">Submit New Level</div>

        <div class="nav-label">Personal</div>
        <div class="nav-item" id="n-profile" onclick="setView('profile')">Profile</div>
        
        <div id="adminPanel" style="display:none">
            <div class="nav-label">Administration</div>
            <div class="nav-item" onclick="setView('pendrec')" style="color:var(--gold)">Pending Records (<span id="pc">0</span>)</div>
            <div class="nav-item" onclick="setView('pendlvl')" style="color:var(--accent)">Pending Levels (<span id="plc">0</span>)</div>
        </div>

        <div style="margin-top:auto; font-size:0.8rem; border-top:1px solid var(--border); padding-top:20px;">
            <p id="un" style="font-weight:bold;"></p>
            <p onclick="logout()" style="color:var(--delete); cursor:pointer; margin-top:5px;">Sign Out</p>
        </div>
    </div>

    <main>
        <div id="v-list" class="view active">
            <h2>The List</h2><p style="color:var(--text-dim); margin-bottom:30px;">Automated ranking and physics verification.</p>
            <div id="listCont"></div>
        </div>

        <div id="v-leader" class="view">
            <h2>World Leaderboard</h2>
            <table><thead><tr><th>#</th><th>PLAYER</th><th>POINTS</th><th>RECORDS</th></tr></thead><tbody id="leaderCont"></tbody></table>
        </div>

        <div id="v-search" class="view">
            <h2>User Directory</h2>
            <input type="text" id="userSearchBox" onkeyup="renderUsers()" placeholder="Search by username..." style="max-width:400px; margin-bottom:20px;">
            <table><thead><tr><th>USER</th><th>ROLE</th><th>ACTIONS</th></tr></thead><tbody id="userDirCont"></tbody></table>
        </div>

        <div id="v-submit" class="view">
            <h2>Submit Record</h2>
            <div style="max-width:500px; margin-top:20px;">
                <label>Select Level</label><select id="sl"></select>
                <input type="text" id="sv" placeholder="Video Link (YouTube/Streamable)">
                <input type="text" id="sp" placeholder="Percentage (e.g. 100 or 98%)">
                <button onclick="sendSub()">Submit for Review</button>
            </div>
        </div>

        <div id="v-submitlvl" class="view">
            <h2>Submit a New Level</h2>
            <p style="color:var(--text-dim)">Send a level to the admins to be added to the official list.</p>
            <div style="max-width:500px; margin-top:20px; background:var(--card); padding:25px; border-radius:10px; border:1px solid var(--border);">
                <input type="text" id="sl_n" placeholder="Level Name">
                <input type="text" id="sl_c" placeholder="Creator">
                <input type="text" id="sl_f" placeholder="Required FPS">
                <input type="text" id="sl_i" placeholder="Banner Image URL">
                <button onclick="sendLvlSub()" style="width:100%">Send to Approvals</button>
            </div>

            <h3 style="margin-top:40px; border-bottom:1px solid var(--border); padding-bottom:10px;">Your Level Submissions</h3>
            <div id="myLvlSubsCont" style="margin-top:20px;"></div>
        </div>

        <div id="v-pendrec" class="view"><h2>Pending Records</h2><div id="pendingCont" style="margin-top:20px;"></div></div>

        <div id="v-pendlvl" class="view"><h2>Pending Levels</h2><div id="pendingLvlCont" style="margin-top:20px;"></div></div>

        <div id="v-profile" class="view"><h2>My Stats</h2><div id="profBox"></div></div>
    </main>

    <script>
        // --- DATA INITIALIZATION ---
        let users = JSON.parse(localStorage.getItem('il_u')) || [{u:"Nova", p:"Password", r:"owner"}];
        
        // Force Nova to be owner (in case of old cache)
        let novaIdx = users.findIndex(x => x.u === "Nova");
        if(novaIdx > -1) { users[novaIdx].r = "owner"; localStorage.setItem('il_u', JSON.stringify(users)); }
        else { users.push({u:"Nova", p:"Password", r:"owner"}); localStorage.setItem('il_u', JSON.stringify(users)); }

        let levels = JSON.parse(localStorage.getItem('il_l')) || [];
        let records = JSON.parse(localStorage.getItem('il_r')) || [];
        let lvlSubs = JSON.parse(localStorage.getItem('il_ls')) || []; // Level Submissions Array
        let session = JSON.parse(sessionStorage.getItem('il_s'));
        
        let pendingLvlIdToAccept = null;

        // --- AUTH ---
        function login() {
            const user = users.find(x => x.u === document.getElementById('au').value && x.p === document.getElementById('ap').value);
            if(user) { session = user; sessionStorage.setItem('il_s', JSON.stringify(user)); init(); }
            else alert("Invalid credentials");
        }
        function signup() {
            const u = document.getElementById('au').value, p = document.getElementById('ap').value;
            if(!u || !p) return;
            if(users.find(x => x.u === u)) return alert("Username taken!");
            users.push({u, p, r:"user"}); localStorage.setItem('il_u', JSON.stringify(users));
            alert("Account created! You can now log in.");
        }
        function logout() { sessionStorage.removeItem('il_s'); location.reload(); }

        // --- NAVIGATION ---
        function setView(id) {
            document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
            document.querySelectorAll('.nav-item').forEach(v => v.classList.remove('active'));
            document.getElementById('v-'+id).classList.add('active');
            const navBtn = document.getElementById('n-'+id);
            if(navBtn) navBtn.classList.add('active');
            render();
            if(id === 'search') renderUsers();
        }

        function closeModals() {
            document.querySelectorAll('.modal').forEach(m => m.style.display = 'none');
            pendingLvlIdToAccept = null;
        }

        // --- LEVEL SUBMISSIONS (User side) ---
        function sendLvlSub() {
            const sub = {
                id: Date.now(), sender: session.u,
                n: document.getElementById('sl_n').value, c: document.getElementById('sl_c').value,
                f: document.getElementById('sl_f').value, i: document.getElementById('sl_i').value,
                s: 'pending'
            };
            if(!sub.n || !sub.c) return alert("Fill out Name and Creator");
            lvlSubs.push(sub); localStorage.setItem('il_ls', JSON.stringify(lvlSubs));
            alert("Level submitted for admin review!");
            
            document.getElementById('sl_n').value = ''; document.getElementById('sl_c').value = '';
            document.getElementById('sl_f').value = ''; document.getElementById('sl_i').value = '';
            render();
        }

        // --- LEVEL REVIEW (Admin side) ---
        function promptAcceptLvl(id) {
            pendingLvlIdToAccept = id;
            document.getElementById('placementModal').style.display = 'flex';
        }

        function confirmAcceptLvl() {
            const rank = parseInt(document.getElementById('acceptRank').value);
            if(!rank || rank < 1) return alert("Enter a valid rank number.");
            
            let targetSub = lvlSubs.find(x => x.id === pendingLvlIdToAccept);
            if(!targetSub) return;

            // Mark as accepted in subs list
            targetSub.s = 'accepted';
            localStorage.setItem('il_ls', JSON.stringify(lvlSubs));

            // Splice into actual level list
            levels.splice(rank - 1, 0, { id: targetSub.id, n: targetSub.n, c: targetSub.c, f: targetSub.f, i: targetSub.i });
            localStorage.setItem('il_l', JSON.stringify(levels));

            closeModals();
            alert(`Level added to Top ${rank}!`);
            setView('list');
        }

        function denyLvlSub(id) {
            let targetSub = lvlSubs.find(x => x.id === id);
            targetSub.s = 'denied';
            localStorage.setItem('il_ls', JSON.stringify(lvlSubs));
            render();
        }

        // --- USER SEARCH & DIRECTORY ---
        function renderUsers() {
            const query = document.getElementById('userSearchBox').value.toLowerCase();
            const filtered = users.filter(u => u.u.toLowerCase().includes(query));
            
            document.getElementById('userDirCont').innerHTML = filtered.map(u => {
                let badgeClass = u.r === 'owner' ? 'r-owner' : (u.r === 'admin' ? 'r-admin' : 'r-user');
                let actionBtn = '';
                
                // Only Nova (owner) can make admins, and only if the target is a normal user
                if(session.r === 'owner' && u.r === 'user') {
                    actionBtn = `<button style="padding:4px 10px; margin:0; font-size:0.7rem; background:var(--accent)" onclick="promoteUser('${u.u}')">Make Admin</button>`;
                }

                return `<tr>
                    <td><b>${u.u}</b></td>
                    <td><span class="role-badge ${badgeClass}">${u.r.toUpperCase()}</span></td>
                    <td>${actionBtn}</td>
                </tr>`;
            }).join('');
        }

        function promoteUser(username) {
            if(confirm(`Promote ${username} to Admin?`)) {
                let uIdx = users.findIndex(x => x.u === username);
                users[uIdx].r = 'admin';
                localStorage.setItem('il_u', JSON.stringify(users));
                renderUsers();
            }
        }

        // --- RECORDS ---
        function sendSub() {
            records.push({id:Date.now(), u:session.u, l:document.getElementById('sl').value, v:document.getElementById('sv').value, p:document.getElementById('sp').value, s:'pending'});
            localStorage.setItem('il_r', JSON.stringify(records)); alert("Sent!"); setView('list');
        }
        function actRec(id, s) { records.find(x => x.id === id).s = s; localStorage.setItem('il_r', JSON.stringify(records)); render(); }
        
        function getTopRecord(lvlName) {
            const approved = records.filter(x => x.l === lvlName && x.s === 'approved');
            if(!approved.length) return null;
            return approved.sort((a,b) => parseInt(b.p) - parseInt(a.p))[0];
        }

        // --- LIST MANAGEMENT ---
        function move(idx, dir) {
            let target = idx + dir; if(target < 0 || target >= levels.length) return;
            let temp = levels[idx]; levels[idx] = levels[target]; levels[target] = temp;
            localStorage.setItem('il_l', JSON.stringify(levels)); render();
        }
        function del(idx) { if(confirm("Delete level?")) { levels.splice(idx,1); localStorage.setItem('il_l', JSON.stringify(levels)); render(); } }

        // --- RENDER EVERYTHING ---
        function render() {
            // Main List
            document.getElementById('listCont').innerHTML = levels.map((l, i) => {
                const wr = getTopRecord(l.n);
                const isCompleted = wr && parseInt(wr.p) === 100;
                return `
                <div class="level-card">
                    <img src="${l.i || 'https://via.placeholder.com/280x160/111/333'}" class="level-img">
                    <div class="level-main">
                        <div><span class="rank-num">#${i+1}</span><span style="font-size:1.6rem; font-weight:800;">${l.n}</span>${isCompleted ? '<span class="completed-tag">[COMPLETED]</span>' : ''}</div>
                        <p style="color:var(--text-dim)">by ${l.c} | ${l.f} FPS</p>
                        <div class="wr-badge">🏆 World Record: ${wr ? `${wr.u} (${parseInt(wr.p)}%)` : "No Records"}</div>
                    </div>
                    ${['owner', 'admin'].includes(session.r) ? `
                    <div class="admin-tools">
                        <button class="tool-btn" onclick="move(${i},-1)">▲</button>
                        <button class="tool-btn" onclick="move(${i},1)">▼</button>
                        <button class="tool-btn" onclick="del(${i})" style="background:var(--delete)">✕</button>
                    </div>` : ''}
                </div>`;
            }).join('');

            // Leaderboard
            const appRecs = records.filter(x => x.s === 'approved');
            const lb = users.map(u => {
                let p = 0; const ur = appRecs.filter(x => x.u === u.u);
                ur.forEach(r => { let lIdx = levels.findIndex(lvl => lvl.n === r.l); if(lIdx !== -1) p += Math.max(10, 1000 - (lIdx * 20)); });
                return {u: u.u, p, c: ur.length};
            }).sort((a,b) => b.p - a.p);
            document.getElementById('leaderCont').innerHTML = lb.map((x, i) => `<tr><td>#${i+1}</td><td><b>${x.u}</b></td><td>${x.p}</td><td>${x.c}</td></tr>`).join('');

            // Profile
            const myStats = lb.find(x => x.u === session.u) || {p:0, c:0};
            document.getElementById('profBox').innerHTML = `
                <div style="background:var(--card); padding:40px; border-radius:15px; border:1px solid var(--border);">
                    <h1>${session.u}</h1><p class="role-badge ${session.r === 'owner' ? 'r-owner' : (session.r === 'admin' ? 'r-admin' : 'r-user')}" style="display:inline-block; margin-top:5px;">${session.r.toUpperCase()}</p>
                    <div style="display:flex; gap:20px; margin-top:30px;">
                        <div style="background:#000; padding:20px; flex:1; border-radius:10px; text-align:center;"><small>POINTS</small><br><h2>${myStats.p}</h2></div>
                        <div style="background:#000; padding:20px; flex:1; border-radius:10px; text-align:center;"><small>COMPLETIONS</small><br><h2>${myStats.c}</h2></div>
                    </div>
                </div>`;

            // User's own level submissions tab
            const mySubs = lvlSubs.filter(x => x.sender === session.u).reverse();
            document.getElementById('myLvlSubsCont').innerHTML = mySubs.length ? mySubs.map(s => {
                let color = s.s === 'accepted' ? 'var(--success)' : (s.s === 'denied' ? 'var(--delete)' : 'var(--gold)');
                return `<div style="padding:15px; background:#000; border:1px solid var(--border); border-radius:8px; margin-bottom:10px; display:flex; justify-content:space-between;">
                    <div><b>${s.n}</b> by ${s.c}</div>
                    <div style="color:${color}; font-weight:bold; font-size:0.8rem; text-transform:uppercase;">${s.s}</div>
                </div>`;
            }).join('') : '<p style="color:var(--text-dim)">You haven\'t submitted any levels yet.</p>';

            // Admin: Pending Records
            const pRecs = records.filter(x => x.s === 'pending');
            document.getElementById('pc').innerText = pRecs.length;
            document.getElementById('pendingCont').innerHTML = pRecs.map(r => `
                <div style="background:var(--card); border:1px solid var(--border); padding:20px; border-radius:10px; margin-bottom:10px; display:flex; justify-content:space-between;">
                    <div><b>${r.u}</b> on ${r.l} (${r.p})<br><a href="${r.v}" target="_blank" style="color:var(--accent)">Video</a></div>
                    <div><button onclick="actRec(${r.id},'approved')">Approve</button> <button onclick="actRec(${r.id},'denied')" style="background:var(--delete)">Deny</button></div>
                </div>
            `).join('');

            // Admin: Pending Levels
            const pLvls = lvlSubs.filter(x => x.s === 'pending');
            document.getElementById('plc').innerText = pLvls.length;
            document.getElementById('pendingLvlCont').innerHTML = pLvls.map(l => `
                <div style="background:var(--card); border:1px solid var(--border); padding:20px; border-radius:10px; margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;">
                    <div>
                        <span style="color:var(--text-dim); font-size:0.7rem;">Sent by ${l.sender}</span><br>
                        <b style="font-size:1.2rem;">${l.n}</b> by ${l.c} <br>
                        <span style="font-size:0.8rem; color:var(--text-dim);">${l.f} FPS</span>
                    </div>
                    <div>
                        <button style="background:var(--success);" onclick="promptAcceptLvl(${l.id})">Accept & Place</button> 
                        <button style="background:var(--delete);" onclick="denyLvlSub(${l.id})">Deny</button>
                    </div>
                </div>
            `).join('');

            // Dropdowns
            document.getElementById('sl').innerHTML = levels.map(l => `<option>${l.n}</option>`).join('');
        }

        // --- INIT ---
        function init() {
            if(!session) { document.getElementById('authOverlay').style.display='flex'; return; }
            document.getElementById('authOverlay').style.display='none';
            document.getElementById('un').innerText = session.u;
            
            // Show Admin Panel if owner or admin
            if(['owner', 'admin'].includes(session.r)) {
                document.getElementById('adminPanel').style.display = 'block';
            }
            render();
        }

        init();
    </script>
</body>
</html>
