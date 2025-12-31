<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Mudanças - Estrutura Base</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            /* Variáveis de Tema (Editáveis via Admin) */
            --primary-color: #2563eb;
            --bg-color: #f8fafc;
            --card-bg: #ffffff;
            --text-main: #1e293b;
            --text-muted: #64748b;
            --border-color: #e2e8f0;
            --success: #22c55e;
            --warning: #f59e0b;
        }

        [data-theme="dark"] {
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --text-main: #f1f5f9;
            --text-muted: #94a3b8;
            --border-color: #334155;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', system-ui, sans-serif; }

        body { background-color: var(--bg-color); color: var(--text-main); transition: 0.3s; padding-bottom: 50px; }

        .container { max-width: 1000px; margin: 0 auto; padding: 20px; }

        /* HEADER */
        header { 
            background: var(--card-bg); padding: 1rem; border-bottom: 1px solid var(--border-color);
            display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100;
        }

        .btn { padding: 10px 20px; border-radius: 8px; border: none; cursor: pointer; font-weight: 600; transition: 0.2s; display: inline-flex; align-items: center; gap: 8px; }
        .btn-primary { background: var(--primary-color); color: white; }
        .btn-admin { background: #64748b; color: white; font-size: 0.8rem; }

        /* ÁREAS PRINCIPAIS */
        .view-section { display: none; }
        .active { display: block; }

        /* FORMULÁRIO E RESUMO */
        .main-layout { display: grid; grid-template-columns: 1fr 300px; gap: 20px; }
        @media (max-width: 768px) { .main-layout { grid-template-columns: 1fr; } .order-summary-box { position: static; } }

        .card { background: var(--card-bg); padding: 25px; border-radius: 12px; border: 1px solid var(--border-color); margin-bottom: 20px; }

        .order-summary-box { position: sticky; top: 80px; height: fit-content; background: var(--primary-color); color: white; border-radius: 12px; padding: 20px; }

        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-weight: 600; font-size: 0.9rem; }
        input, select, textarea { width: 100%; padding: 12px; border: 1px solid var(--border-color); border-radius: 8px; background: var(--bg-color); color: var(--text-main); }

        /* REVISÃO E ALERTAS */
        .alert-box { background: #fffbeb; border-left: 4px solid var(--warning); padding: 15px; margin: 10px 0; border-radius: 4px; color: #92400e; font-size: 0.9rem; }
        
        /* ADMIN TABLES */
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { padding: 12px; text-align: left; border-bottom: 1px solid var(--border-color); font-size: 0.9rem; }

        .badge { padding: 4px 8px; border-radius: 4px; font-size: 0.7rem; font-weight: bold; }
        .status-pendente { background: var(--warning); color: white; }

        .hidden { display: none; }
    </style>
</head>
<body data-theme="light">

    <header>
        <div class="logo"><h2 id="conf-nome-prestador">Transportes Pro</h2></div>
        <div>
            <button class="btn btn-admin" onclick="showAdminLogin()"><i class="fa-solid fa-lock"></i> Painel Admin</button>
        </div>
    </header>

    <div class="container">
        <section id="client-view" class="view-section active">
            <div class="card">
                <h1 id="conf-titulo-site">Solicite sua Mudança</h1>
                <p id="conf-desc-servico" style="color: var(--text-muted); margin-bottom: 20px;">Carretos rápidos e seguros.</p>
                <div class="alert-box" id="conf-aviso-responsabilidade">Valores sujeitos à confirmação.</div>
            </div>

            <div class="main-layout" id="form-step">
                <form id="mudanca-form">
                    <div class="card">
                        <h3>Dados Básicos</h3><br>
                        <div class="form-group">
                            <label>Nome Completo</label>
                            <input type="text" id="cli-nome" required>
                        </div>
                        <div class="form-group">
                            <label>Telefone (Opcional)</label>
                            <input type="tel" id="cli-tel">
                        </div>
                        <div class="form-group">
                            <label>Tipo de Serviço</label>
                            <select id="cli-servico" onchange="updateSummary()">
                                <option value="Mudança">Mudança</option>
                                <option value="Carreto">Carreto</option>
                            </select>
                        </div>
                    </div>

                    <div class="card">
                        <h3>O que vamos levar?</h3><br>
                        <div class="form-group">
                            <label id="label-itens">Quantidade de itens</label>
                            <select id="cli-itens" onchange="updateSummary()">
                                <option value="Pouca coisa">Pouca coisa</option>
                                <option value="Média quantidade">Média quantidade</option>
                                <option value="Muita coisa">Muita coisa</option>
                            </select>
                            <small id="conf-itens-desc" style="display:block; margin-top:5px; color:var(--text-muted)"></small>
                        </div>
                        <div class="form-group">
                            <label>Precisa de ajudante?</label>
                            <select id="cli-ajudante">
                                <option value="Não">Não</option>
                                <option value="Sim">Sim</option>
                            </select>
                        </div>
                    </div>

                    <div class="card">
                        <h3>Logística</h3><br>
                        <div class="form-grid" style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
                            <div class="form-group"><label>Origem</label><input type="text" id="cli-origem" required></div>
                            <div class="form-group"><label>Destino</label><input type="text" id="cli-destino" required></div>
                            <div class="form-group"><label>Data</label><input type="date" id="cli-data" onchange="updateSummary()" required></div>
                            <div class="form-group"><label>Hora (Apenas hora)</label><input type="number" id="cli-hora" min="0" max="23" placeholder="Ex: 14" onchange="updateSummary()" required></div>
                        </div>
                    </div>

                    <div class="card">
                        <h3>Pagamento e Obs</h3><br>
                        <div class="form-group">
                            <label>Forma de Pagamento</label>
                            <select id="cli-pgto">
                                <option value="Pix">Pix</option>
                                <option value="Dinheiro">Dinheiro</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label>Quando pagar?</label>
                            <select id="cli-quando">
                                <option value="No dia da entrega">No dia da entrega</option>
                                <option value="Antes">Antes</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label>Observações</label>
                            <textarea id="cli-obs" rows="3"></textarea>
                        </div>
                        <button type="button" class="btn btn-primary" onclick="goToReview()" style="width: 100%; justify-content: center;">Revisar Pedido</button>
                    </div>
                </form>

                <div class="order-summary-box">
                    <h4>Resumo da Solicitação</h4><hr style="margin: 10px 0; opacity: 0.3;">
                    <p>Serviço: <span id="res-servico">-</span></p>
                    <p>Itens: <span id="res-itens">-</span></p>
                    <p>Data: <span id="res-data">-</span></p>
                    <p>Hora: <span id="res-hora">-</span>h</p>
                </div>
            </div>

            <div id="review-step" class="hidden">
                <div class="card">
                    <h2>Revise seu Pedido <span id="review-id" style="color: var(--primary-color)"></span></h2>
                    <div id="review-content" style="margin: 20px 0; line-height: 1.8;"></div>
                    <div id="smart-alerts"></div>
                    <div style="display: flex; gap: 10px; margin-top: 20px;">
                        <button class="btn btn-admin" onclick="backToForm()">Voltar e Editar</button>
                        <button class="btn btn-primary" onclick="confirmAndSend()" style="background: var(--success);">Confirmar e Enviar WhatsApp</button>
                    </div>
                </div>
            </div>
        </section>

        <section id="admin-view" class="view-section">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
                <h2>Painel Administrativo</h2>
                <button class="btn btn-admin" onclick="logoutAdmin()">Sair</button>
            </div>

            <div class="card">
                <h3>Configurações de Exibição</h3><br>
                <div class="form-grid" style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px;">
                    <div class="form-group"><label>Nome do Prestador</label><input type="text" id="adm-nome-p"></div>
                    <div class="form-group"><label>Título do Site</label><input type="text" id="adm-titulo"></div>
                    <div class="form-group"><label>Anos de Exp.</label><input type="text" id="adm-exp"></div>
                    <div class="form-group"><label>Tema</label>
                        <select id="adm-tema">
                            <option value="light">Claro</option>
                            <option value="dark">Escuro</option>
                        </select>
                    </div>
                </div>
                <div class="form-group"><label>Descrição do Serviço</label><textarea id="adm-desc"></textarea></div>
                <div class="form-group"><label>Aviso de Responsabilidade</label><input type="text" id="adm-aviso"></div>
                <button class="btn btn-primary" onclick="saveSettings()">Salvar Configurações</button>
            </div>

            <div class="card">
                <h3>Histórico de Pedidos</h3>
                <div style="overflow-x: auto;">
                    <table id="pedidos-table">
                        <thead>
                            <tr>
                                <th>ID</th>
                                <th>Cliente</th>
                                <th>Serviço</th>
                                <th>Data</th>
                                <th>Status</th>
                                <th>Ações</th>
                            </tr>
                        </thead>
                        <tbody id="pedidos-body"></tbody>
                    </table>
                </div>
            </div>
        </section>
    </div>

    <div id="modal-senha" class="hidden" style="position: fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.8); z-index:200; display:flex; align-items:center; justify-content:center;">
        <div class="card" style="width: 300px; text-align: center;">
            <h3>Senha do Admin</h3><br>
            <input type="password" id="admin-pass-input" placeholder="Digite a senha"><br><br>
            <button class="btn btn-primary" onclick="loginAdmin()">Acessar</button>
            <button class="btn btn-admin" onclick="hideAdminLogin()">Cancelar</button>
        </div>
    </div>

    <script>
        // --- DATA DE BASE ---
        let settings = JSON.parse(localStorage.getItem('move_settings')) || {
            nomePrestador: "Transportes Pro",
            tituloSite: "Solicite sua Mudança",
            descServico: "Carretos rápidos e seguros em toda região.",
            aviso: "Valores e disponibilidade sujeitos à confirmação após análise.",
            tema: "light",
            exp: "10 anos",
            whatsapp: "5511953503735"
        };

        let pedidos = JSON.parse(localStorage.getItem('move_pedidos')) || [];

        // --- INICIALIZAÇÃO ---
        function init() {
            // Aplicar Configurações no DOM
            document.getElementById('conf-nome-prestador').innerText = settings.nomePrestador;
            document.getElementById('conf-titulo-site').innerText = settings.tituloSite;
            document.getElementById('conf-desc-servico').innerText = settings.descServico;
            document.getElementById('conf-aviso-responsabilidade').innerText = settings.aviso;
            document.body.setAttribute('data-theme', settings.tema);
            
            // Preencher campos do Admin com os valores atuais
            document.getElementById('adm-nome-p').value = settings.nomePrestador;
            document.getElementById('adm-titulo').value = settings.tituloSite;
            document.getElementById('adm-exp').value = settings.exp;
            document.getElementById('adm-tema').value = settings.tema;
            document.getElementById('adm-desc').value = settings.descServico;
            document.getElementById('adm-aviso').value = settings.aviso;

            // Bloquear datas passadas
            document.getElementById('cli-data').min = new Date().toISOString().split("T")[0];
            
            renderPedidos();
        }

        // --- LÓGICA DO CLIENTE ---
        function updateSummary() {
            document.getElementById('res-servico').innerText = document.getElementById('cli-servico').value;
            document.getElementById('res-itens').innerText = document.getElementById('cli-itens').value;
            document.getElementById('res-data').innerText = document.getElementById('cli-data').value || '-';
            document.getElementById('res-hora').innerText = document.getElementById('cli-hora').value || '-';
        }

        function generateID() {
            return "#MD-" + Math.floor(1000 + Math.random() * 9000);
        }

        let currentOrder = {};

        function goToReview() {
            const form = document.getElementById('mudanca-form');
            if(!form.checkValidity()) { alert("Preencha todos os campos obrigatórios."); return; }

            currentOrder = {
                id: generateID(),
                nome: document.getElementById('cli-nome').value,
                tel: document.getElementById('cli-tel').value,
                servico: document.getElementById('cli-servico').value,
                itens: document.getElementById('cli-itens').value,
                ajudante: document.getElementById('cli-ajudante').value,
                origem: document.getElementById('cli-origem').value,
                destino: document.getElementById('cli-destino').value,
                data: document.getElementById('cli-data').value,
                hora: document.getElementById('cli-hora').value,
                pgto: document.getElementById('cli-pgto').value,
                quando: document.getElementById('cli-quando').value,
                obs: document.getElementById('cli-obs').value,
                status: 'Pendente'
            };

            document.getElementById('review-id').innerText = currentOrder.id;
            document.getElementById('review-content').innerHTML = `
                <strong>Nome:</strong> ${currentOrder.nome}<br>
                <strong>Serviço:</strong> ${currentOrder.servico}<br>
                <strong>Volume:</strong> ${currentOrder.itens} (${currentOrder.ajudante === 'Sim' ? 'Com' : 'Sem'} ajudante)<br>
                <strong>Trajeto:</strong> ${currentOrder.origem} -> ${currentOrder.destino}<br>
                <strong>Agendamento:</strong> Dia ${currentOrder.data} às ${currentOrder.hora}h<br>
                <strong>Pagamento:</strong> ${currentOrder.pgto} (${currentOrder.quando})
            `;

            // Sugestões Inteligentes
            let alerts = "";
            if(currentOrder.itens === "Muita coisa" && currentOrder.ajudante === "Não") {
                alerts += `<div class="alert-box">💡 Sugestão: Para grandes volumes, recomendamos contratar um ajudante para agilizar o processo.</div>`;
            }
            if(currentOrder.pgto === "Dinheiro") {
                alerts += `<div class="alert-box">💡 Aviso: Pagamentos em dinheiro costumam ser realizados no ato da entrega.</div>`;
            }
            document.getElementById('smart-alerts').innerHTML = alerts;

            document.getElementById('form-step').classList.add('hidden');
            document.getElementById('review-step').classList.remove('hidden');
        }

        function backToForm() {
            document.getElementById('form-step').classList.remove('hidden');
            document.getElementById('review-step').classList.add('hidden');
        }

        function confirmAndSend() {
            // Salvar no Histórico
            pedidos.push(currentOrder);
            localStorage.setItem('move_pedidos', JSON.stringify(pedidos));

            // Formatar Mensagem WhatsApp
            const msg = `Olá, tudo bem?
Meu nome é ${currentOrder.nome}.

Preciso fazer uma ${currentOrder.servico.toUpperCase()}.

Quantidade de itens: ${currentOrder.itens.toUpperCase()}
Vai precisar de ajudante: ${currentOrder.ajudante.toUpperCase()}

Endereço de origem: ${currentOrder.origem}
Endereço de destino: ${currentOrder.destino}

Data desejada: ${currentOrder.data}
Horário desejado: ${currentOrder.hora}h

Forma de pagamento: ${currentOrder.pgto.toUpperCase()}
Pagamento: ${currentOrder.quando.toUpperCase()}

Observações:
${currentOrder.obs || 'Nenhuma'}

Pedido: ${currentOrder.id}`;

            const url = `https://wa.me/${settings.whatsapp}?text=${encodeURIComponent(msg)}`;
            window.open(url, '_blank');
            location.reload(); // Reinicia fluxo
        }

        // --- LÓGICA DO ADMIN ---
        function showAdminLogin() { document.getElementById('modal-senha').classList.remove('hidden'); }
        function hideAdminLogin() { document.getElementById('modal-senha').classList.add('hidden'); }

        function loginAdmin() {
            const now = new Date();
            const senhaCorreta = (now.getDate() + (now.getMonth() + 1)).toString() + (now.getHours() + 10).toString();
            const input = document.getElementById('admin-pass-input').value;

            if(input === senhaCorreta) {
                document.getElementById('client-view').classList.remove('active');
                document.getElementById('admin-view').classList.add('active');
                hideAdminLogin();
            } else {
                alert("Senha incorreta! (Dica do Desenvolvedor: Calcule Dia+Mês e Hora+10)");
            }
        }

        function logoutAdmin() {
            document.getElementById('admin-view').classList.remove('active');
            document.getElementById('client-view').classList.add('active');
        }

        function saveSettings() {
            settings.nomePrestador = document.getElementById('adm-nome-p').value;
            settings.tituloSite = document.getElementById('adm-titulo').value;
            settings.exp = document.getElementById('adm-exp').value;
            settings.tema = document.getElementById('adm-tema').value;
            settings.descServico = document.getElementById('adm-desc').value;
            settings.aviso = document.getElementById('adm-aviso').value;

            localStorage.setItem('move_settings', JSON.stringify(settings));
            alert("Configurações atualizadas!");
            init();
        }

        function renderPedidos() {
            const body = document.getElementById('pedidos-body');
            body.innerHTML = "";
            pedidos.reverse().forEach((p, index) => {
                body.innerHTML += `
                    <tr>
                        <td>${p.id}</td>
                        <td>${p.nome}</td>
                        <td>${p.servico}</td>
                        <td>${p.data}</td>
                        <td><span class="badge status-pendente">${p.status}</span></td>
                        <td><button onclick="alert('Funcionalidade de edição de status em expansão...')">Ver</button></td>
                    </tr>
                `;
            });
        }

        init();
    </script>
</body>
</html>
