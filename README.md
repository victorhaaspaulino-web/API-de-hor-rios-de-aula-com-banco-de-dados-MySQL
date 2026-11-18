<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grade Escolar • API Dashboard</title>
    <style>
        :root {
            --bg-body: #0f172a;
            --bg-card: #1e293b;
            --bg-input: #0f172a;
            --border: #334155;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
            --primary: #8b5cf6;
            --primary-hover: #7c3aed;
            --danger: #ef4444;
            --danger-hover: #dc2626;
            --success: #10b981;
        }
        * {
            box-sizing: border-box;
            font-family: 'Segoe UI', system-ui, sans-serif;
        }
        body {
            background-color: var(--bg-body);
            color: var(--text-main);
            margin: 0;
            padding: 25px;
        }
        header {
            max-width: 960px;
            margin: 0 auto 25px auto;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 15px;
            border-bottom: 1px solid var(--border);
            padding-bottom: 15px;
        }
        header h1 {
            margin: 0;
            font-size: 22px;
            letter-spacing: 0.5px;
        }
        .tabs {
            display: flex;
            gap: 10px;
        }
        .tab-btn {
            background: var(--bg-card);
            color: var(--text-muted);
            border: 1px solid var(--border);
            padding: 10px 18px;
            border-radius: 20px;
            cursor: pointer;
            font-weight: 600;
            transition: 0.2s;
        }
        .tab-btn.active {
            background: var(--primary);
            color: #fff;
            border-color: var(--primary);
        }
        .wrapper {
            max-width: 960px;
            margin: 0 auto;
        }
        .secao {
            display: none;
        }
        .secao.ativa {
            display: grid;
            grid-template-columns: 340px 1fr;
            gap: 24px;
        }
        .painel {
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 12px;
            padding: 20px;
        }
        .painel h2 {
            margin-top: 0;
            font-size: 17px;
            color: #e2e8f0;
            margin-bottom: 15px;
        }
        .campo {
            margin-bottom: 12px;
        }
        .campo label {
            display: block;
            font-size: 12px;
            text-transform: uppercase;
            color: var(--text-muted);
            margin-bottom: 5px;
            font-weight: bold;
        }
        input, select {
            width: 100%;
            padding: 10px;
            background: var(--bg-input);
            border: 1px solid var(--border);
            border-radius: 8px;
            color: var(--text-main);
            font-size: 14px;
        }
        input:focus, select:focus {
            outline: none;
            border-color: var(--primary);
        }
        .btn-acao {
            width: 100%;
            padding: 11px;
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 8px;
            transition: 0.2s;
        }
        .btn-acao:hover {
            background: var(--primary-hover);
        }
        .dias-nav {
            display: flex;
            gap: 8px;
            margin-bottom: 18px;
            flex-wrap: wrap;
        }
        .dia-pill {
            background: var(--bg-input);
            border: 1px solid var(--border);
            color: var(--text-muted);
            padding: 7px 14px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 13px;
            font-weight: 600;
        }
        .dia-pill.ativo {
            border-color: var(--primary);
            color: #fff;
            background: rgba(139, 92, 246, 0.2);
        }
        .lista-cards {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }
        .item-card {
            background: var(--bg-input);
            border: 1px solid var(--border);
            border-radius: 10px;
            padding: 14px 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .item-info h3 {
            margin: 0 0 4px 0;
            font-size: 16px;
        }
        .item-info p {
            margin: 0;
            font-size: 13px;
            color: var(--text-muted);
        }
        .badge {
            display: inline-block;
            background: rgba(139, 92, 246, 0.2);
            color: #c4b5fd;
            padding: 2px 8px;
            border-radius: 6px;
            font-size: 12px;
            font-weight: bold;
            margin-right: 6px;
        }
        .btn-del {
            background: transparent;
            border: 1px solid var(--danger);
            color: var(--danger);
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 12px;
            font-weight: bold;
        }
        .btn-del:hover {
            background: var(--danger);
            color: #fff;
        }
        #toast {
            position: fixed;
            bottom: 20px;
            right: 20px;
            padding: 14px 20px;
            border-radius: 8px;
            font-weight: 600;
            font-size: 14px;
            display: none;
            box-shadow: 0 10px 25px rgba(0,0,0,0.4);
            z-index: 999;
        }
        #toast.ok { background: var(--success); color: #fff; }
        #toast.err { background: var(--danger); color: #fff; }
        .vazio {
            text-align: center;
            color: var(--text-muted);
            padding: 30px 10px;
            font-size: 14px;
        }
        @media (max-width: 768px) {
            .secao.ativa { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>

    <header>
        <h1>Gestão de Grade Escolar</h1>
        <div class="tabs">
            <button class="tab-btn active" onclick="trocarAba('horarios', this)">Grade de Horários</button>
            <button class="tab-btn" onclick="trocarAba('aulas', this)">Cadastro de Aulas</button>
        </div>
    </header>

    <div class="wrapper">
        <!-- ABA 1: HORÁRIOS -->
        <section id="aba-horarios" class="secao ativa">
            <div class="painel">
                <h2>Alocar Horário</h2>
                <form id="formHorario">
                    <div class="campo">
                        <label>ID da Aula (idaula)</label>
                        <input type="number" id="idaula" required placeholder="Ex: 1 (ou 99 para testar FK)">
                    </div>
                    <div class="campo">
                        <label>Dia da Semana</label>
                        <select id="dia_semana" required>
                            <option value="Segunda">Segunda</option>
                            <option value="Terça">Terça</option>
                            <option value="Quarta">Quarta</option>
                            <option value="Quinta">Quinta</option>
                            <option value="Sexta">Sexta</option>
                        </select>
                    </div>
                    <div class="campo">
                        <label>Ordem da Aula</label>
                        <input type="number" id="ordem" min="1" max="10" required placeholder="1">
                    </div>
                    <button type="submit" class="btn-acao">Salvar na Grade</button>
                </form>
            </div>

            <div class="painel">
                <h2>Horários do Dia</h2>
                <div class="dias-nav" id="botoesDias">
                    <button class="dia-pill ativo" onclick="selecionarDia('Segunda', this)">Segunda</button>
                    <button class="dia-pill" onclick="selecionarDia('Terça', this)">Terça</button>
                    <button class="dia-pill" onclick="selecionarDia('Quarta', this)">Quarta</button>
                    <button class="dia-pill" onclick="selecionarDia('Quinta', this)">Quinta</button>
                    <button class="dia-pill" onclick="selecionarDia('Sexta', this)">Sexta</button>
                </div>
                <div id="listaHorarios" class="lista-cards"></div>
            </div>
        </section>

        <!-- ABA 2: AULAS -->
        <section id="aba-aulas" class="secao">
            <div class="painel">
                <h2>Nova Aula</h2>
                <form id="formAula">
                    <div class="campo">
                        <label>Série</label>
                        <input type="text" id="serie" maxlength="2" required placeholder="Ex: 2º">
                    </div>
                    <div class="campo">
                        <label>Turma</label>
                        <input type="text" id="turma" maxlength="6" required placeholder="Ex: DSB">
                    </div>
                    <div class="campo">
                        <label>Componente Curricular</label>
                        <input type="text" id="componente" maxlength="100" required placeholder="Ex: Back End">
                    </div>
                    <div class="campo">
                        <label>Professor(a)</label>
                        <input type="text" id="professor" maxlength="100" required placeholder="Ex: Ronaldo">
                    </div>
                    <button type="submit" class="btn-acao">Cadastrar Aula</button>
                </form>
            </div>

            <div class="painel">
                <h2>Aulas Cadastradas</h2>
                <div id="listaAulas" class="lista-cards"></div>
            </div>

