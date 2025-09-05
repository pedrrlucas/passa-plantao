<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Passa Plantão</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Fontes do Google -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">

    <!--Calendário da Flatpickr-->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
    <script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
    <link rel="stylesheet" type="text/css" href="https://npmcdn.com/flatpickr/dist/themes/light.css">
    <script src="https://npmcdn.com/flatpickr/dist/l10n/pt.js"></script>

    <!--Bibliioteca de Gráficos-->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <style>


        .info-tag {
            display: inline-flex;
            align-items: center;
            padding: 0.2rem 0.6rem;
            border-radius: 9999px;
            font-size: 0.8rem;
            font-weight: 500;
            line-height: 1.2;
            background-color: #f9fafb; /* Cor MAIS CLARA (bg-gray-50) */
            color: #4b5563; /* text-gray-600 */
            border: 1px solid #f3f4f6; /* Borda sutil (border-gray-100) */
            white-space: normal; 
            text-align: left;
            max-width: 100%;
        }

        /* Container para as tags (Sua excelente nova classe) */
        .tags-display-area {
            display: flex;
            flex-wrap: wrap;
            gap: 0.3rem; /* Aumentei um pouco o gap para melhor leitura */
            min-height: 24px;
            padding-top: 0.25rem;
        }

        /* 1. Botão de remover DENTRO da tag. Por padrão, ele fica ESCONDIDO. */
        .info-tag .remove-item-btn {
            display: none; /* COMEÇA ESCONDIDO */
            margin-left: 0.4rem;
            cursor: pointer;
            border-radius: 50%;
            width: 16px;
            height: 16px;
            align-items: center;
            justify-content: center;
            font-size: 14px;
            color: #6b7280; /* text-gray-500 */
            transition: all 0.2s ease;
        }

        /* 2. SÓ MOSTRAMOS o botão de remover quando um ancestral tem a classe '.module-editing' */
        .module-editing .info-tag .remove-item-btn {
            display: flex; /* SÓ APARECE NO MODO DE EDIÇÃO */
        }

        /* 3. O efeito hover continua o mesmo */
        .info-tag .remove-item-btn:hover {
            background-color: #fca5a5; /* bg-red-300 */
            color: #b91c1c; /* text-red-700 */
            transform: scale(1.1);
        }

        /* Estilo para os containers dos itens de texto */
        .item-text-container {
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            gap: 0.1rem; /* Espaço mínimo entre os spans */
            line-height: 1.5; /* Melhora a legibilidade se quebrar a linha */
            color: #374151; /* Cor do texto (text-gray-700) */
        }

        /* Suaviza a paginação */
        .pagination-transition {
            transition: all 0.3s ease;
        }

        .pagination-transition:hover {
            transform: translateY(-1px);
        }

        /* Estilos do modal de visualização de registros de passagem de plantão */
        #view-handover-modal {
            z-index: 60 !important;
        }
        #view-handover-modal .bg-gray-900 {
            background-color: rgba(0, 0, 0, 0.85) !important;
        }

        /* Estilos personalizados */
        .overflow-visible-important {
            overflow: visible !important;
        }

        /* Estilo base para a aplicação */
        body {
            font-family: 'Inter', sans-serif;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }
        /* Estilo para a barra de rolagem */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        ::-webkit-scrollbar-thumb {
            background: #888;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #555;
        }
        /* Estilo para transição do acordeão */
        .accordion-content { max-height: 0; overflow: hidden; transition: max-height 0.4s ease-in-out; }
        .autocomplete-active { background-color: #e0f2fe; }
        .tag { display: inline-flex; align-items: center; padding: 0.25rem 0.75rem; margin: 0.25rem; border-radius: 9999px; font-weight: 500; font-size: 0.875rem; line-height: 1.25rem; }
        .tag-remove { margin-left: 0.5rem; cursor: pointer; border-radius: 50%; padding: 0.1rem; display: flex; align-items: center; justify-content: center; }
        .tag-remove:hover { background-color: rgba(0,0,0,0.1); }
        .time-tag { background-color: #e0e7ff; color: #3730a3; border: 1px solid #c7d2fe; }
        .medication-block { border: 1px solid #e5e7eb; border-radius: 0.5rem; padding: 0.75rem; margin-bottom: 0.75rem; }
        #print-view {
            display: none;
        }

        @media print {
            /* --- NOVAS REGRAS PARA CORRIGIR A IMPRESSÃO DE MÚLTIPLAS PÁGINAS --- */
            html, body {
                height: auto !important;      /* Permite que a altura cresça com o conteúdo */
                overflow: visible !important;  /* Garante que nada seja cortado */
                background-color: #fff;        /* Fundo branco para impressão */
            }
            /* ----------------------------------------------------------------- */

            /* Esconde o container principal da aplicação e tudo que não deve ser impresso */
            body > #app {
                display: none !important;
            }

            /* Garante que o body não tenha configurações que possam atrapalhar a impressão (redundante, mas seguro) */
            body {
                overflow: visible;
            }

            /* Mostra e formata a área de impressão */
            #print-view {
                display: block !important;
                position: static !important; /* Remove qualquer posicionamento fixo/absoluto */
                width: 100% !important;
                border: none !important;
                box-shadow: none !important;
                max-height: none !important; /* Remove qualquer limite de altura */
                overflow: visible !important;
            }

            .status-badge {
                color: black !important;
                background-color: transparent !important;
                border: 1px solid #333 !important; /* Adiciona uma borda sutil para manter o formato */
                font-weight: 600 !important; /* Deixa o texto um pouco mais forte */
                padding-left: 8px !important;
                padding-right: 8px !important;
            }
        }
        
        .accordion-form-content {
            transition: max-height 0.4s ease-in-out;
        }

        .module-editing {
            border: 2px solid #60a5fa; /* Cor azul (blue-400) */
            box-shadow: 0 4px 14px 0 rgba(96, 165, 250, 0.5); /* Sombra azul */
            transition: all 0.3s ease-in-out;
        }
        .module-static-filled {
            opacity: 0.7;
            transition: opacity 0.3s ease;
        }


        /* Garante que os inputs e textareas no formulário sejam mais compactos */
        #add-handovers-form input[type="text"],
        #add-handovers-form input[type="time"],
        #add-handovers-form textarea {
            padding-top: 0.3rem;
            padding-bottom: 0.3rem;
            padding-left: 0.5rem;
            padding-right: 0.5rem;
            font-size: 0.875rem; /* text-sm */
        }

        /* Reduz o tamanho da fonte dos itens de lista nos módulos de riscos/precauções */
        #module-riscos .item-text-container .item-text,
        #module-precaucoes .item-text-container .item-text {
            font-size: 0.8rem; /* Um pouco menor que text-sm */
        }

        .clickable-item-area {
            padding: 0.5rem;
            border-radius: 0.5rem; /* rounded-lg */
            background-color: transparent;
            cursor: pointer;
            transition: all 0.2s ease-in-out;
            border: 2px dashed #e5e7eb; /* Borda tracejada (border-gray-200) */
            min-height: 54px; /* Garante uma altura mínima clicável */
        }

        .clickable-item-area:hover {
            background-color: #f9fafb; /* bg-gray-50 */
            border-color: #d1d5db; /* border-gray-300 */
        }

        /* Estilo para o contêiner que agrupa toda a seção de alergia */
        .allergy-module-box {
            border: 2px dashed #e5e7eb; /* Borda tracejada (border-gray-200) */
            border-radius: 0.5rem; /* rounded-lg */
            padding: 0.75rem; /* p-3 */
            transition: all 0.2s ease-in-out;
        }

        /* --- OTIMIZAÇÃO DO MÓDULO DE EXAMES --- */
        /* 1. Transforma o card do módulo em um container flexível vertical */
        #module-exames {
            display: flex;
            flex-direction: column;
            /* Garante que o módulo tente ocupar a altura disponível na sua coluna */
            min-height: 400px;
            position: relative;
        }

        /* 2. Faz a área das listas crescer para preencher o espaço vazio */
        #exam-lists-container {
            flex-grow: 1;
            min-height: 0; 
            gap: 1rem;
            margin-bottom: 0.75rem;
        }

        /* 3. Faz as listas individuais terem scroll se o conteúdo for maior que o espaço */
        #scheduled-exams-list,
        #pending-exams-list {
            flex-grow: 1; /* Faz a lista crescer para preencher o espaço vertical disponível */
            overflow-y: auto; /* Agora a rolagem funcionará corretamente */
            padding-right: 4px; /* Espaço para a barra de rolagem não colar no texto */
            /* A propriedade 'height' foi removida */
            height: 150px; 

        }

        /* 4. Garante que a área do botão fique sempre no final */
        #exam-main-action-area {
            /* A propriedade 'margin-top: auto' em um container flex empurra o elemento para o final */
            margin-top: auto; 
            padding-top: 0.75rem; /* Adiciona um respiro acima do botão */
            border-top: 1px solid #e5e7eb; /* border-gray-200 */
        }

        /* --- CORREÇÃO DE LAYOUT DO EDITOR DE EXAMES --- */
        #exam-editor-area {
            position: absolute;   /* Tira o elemento do fluxo normal */
            bottom: 0;            /* Alinha na base do card */
            left: 0;              /* Alinha na esquerda do card */
            right: 0;             /* Alinha na direita do card */
            background-color: white;
            border-top: 1px solid #e5e7eb;
            padding: 1rem;
            box-shadow: 0 -4px 12px rgba(0, 0, 0, 0.05);
            transition: all 0.3s ease-in-out;
            /* A linha abaixo pode ou não já existir. O importante é que position: absolute já está lá, o que é ainda melhor que relative. Então, não precisa mudar nada aqui! */
        }

        #exam-lists-container > div {
            display: flex;
            flex-direction: column;
            min-height: 0; /* Crucial para rolagem em containers flex aninhados */
        }

        /* --- OTIMIZAÇÃO DO MÓDULO DE MEDICAÇÕES --- */
        /* 1. Transforma o card principal em um container flexível com altura fixa */
        #module-medicacoes {
            display: flex;
            flex-direction: column;
            min-height: 120px; /* Altura mínima para mostrar o botão "+ Nova Medicação" */
            max-height: 220px; /* Metade da altura anterior, para a lista */
            transition: all 0.3s ease-in-out; /* Suaviza a mudança de tamanho */
            position: relative;
        }

        /* 2. Faz a lista de medicações crescer e ter rolagem */
        #medications-list-container {
            flex-grow: 1;      /* Faz a lista ocupar todo o espaço vertical disponível */
            overflow-y: auto;  /* Ativa a rolagem quando o conteúdo transbordar */
            min-height: 0;     /* Correção essencial para rolagem em containers flex */
            padding-right: 4px; /* Espaço para a barra de rolagem */
        }

        /* 3. Estilo para o novo botão de adicionar horário com emoji */
        .add-med-time-btn-emoji {
            background: none;
            border: none;
            cursor: pointer;
            font-size: 1.25rem; /* Tamanho do emoji */
            line-height: 1;
            padding: 0.25rem;
            border-radius: 9999px;
            transition: background-color 0.2s;
        }

        .add-med-time-btn-emoji:hover {
            background-color: #eef2ff; /* indigo-50 */
        }
        
        /* Estilo para o card de medicação, espelhado no .exam-item */
        .medication-block {
            background-color: white;
            padding: 0.75rem; /* p-3 */
            border-radius: 0.25rem; /* rounded */
            border: 1px solid #e5e7eb; /* border-gray-200 */
            box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05); /* shadow-sm */
            font-size: 0.875rem; /* text-sm */
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
        }

        /* Container para os botões de ação (emojis) */
        .med-actions {
            display: flex;
            gap: 0.5rem;
            font-size: 1.125rem; /* text-lg */
        }

        #medication-editor-area {
            background-color: #f9fafb; /* bg-gray-50 */
            padding: 0.75rem; /* p-3 */
            border-radius: 0.375rem; /* rounded-md */
            border: 1px solid #e5e7eb; /* border-gray-200 */
        }

        /* Estilo para o container do calendário inline */
        #inline-time-picker-container .flatpickr-calendar {
            /* Remove sombras, bordas e posicionamento absoluto do modo popup */
            position: static !important;
            box-shadow: none !important;
            border: 1px solid #d1d5db; /* border-gray-300 */
            width: 100% !important;
            max-width: 280px; /* Impede que fique largo demais */
            margin: 0 auto; /* Centraliza */
        }

        .news2-low { background-color: #dcfce7; color: #166534; } /* bg-green-100 text-green-800 */
        .news2-low-medium { background-color: #fef9c3; color: #854d0e; } /* bg-yellow-100 text-yellow-800 */
        .news2-medium { background-color: #ffedd5; color: #9a3412; } /* bg-orange-100 text-orange-800 */
        .news2-high { background-color: #fee2e2; color: #510909; } /* bg-red-100 text-red-800 */

        .fugulin-minimos { background-color: #dcfce7; color: #166534; } /* Verde (bg-green-100) */
        .fugulin-intermediarios { background-color: #fef9c3; color: #854d0e; } /* Amarelo (bg-yellow-100) */
        .fugulin-alta-dependencia { background-color: #ffedd5; color: #9a3412; } /* LARANJA (bg-orange-100) */
        .fugulin-semi-intensivos { background-color: #fee2e2; color: #960e0e; } /* Vermelho Claro (bg-red-100) */
        .fugulin-intensivos { background-color: #fecaca; color: #510909; } /* Vermelho Escuro (bg-red-200) */


        /* O contêiner do menu de seleção */
        .custom-select-options {
            position: absolute; /* Para flutuar sobre o conteúdo */
            background-color: white;
            border: 1px solid #d1d5db; /* border-gray-300 */
            border-radius: 0.5rem; /* rounded-lg */
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
            z-index: 100; /* Garante que fique na frente */
            max-height: 200px;
            overflow-y: auto;
            transition: all 0.2s ease-out;
            padding: 0.25rem; /* p-1 */
        }

        /* Cada item individual dentro do menu */
        .custom-select-option {
            padding: 0.5rem 0.75rem; /* p-2 px-3 */
            cursor: pointer;
            border-radius: 0.375rem; /* rounded-md */
            font-size: 0.875rem; /* text-sm */
            color: #374151; /* text-gray-700 */
            transition: background-color 0.15s ease-in-out;
            white-space: normal;
            text-align: left;
        }

        /* Efeito hover para os itens */
        .custom-select-option:hover {
            background-color: #f3f4f6; /* bg-gray-100 */
            color: #111827; /* text-gray-900 */
        }

        /* Estilo para a área clicável do módulo de monitoramento */
        .monitoring-item-area {
            min-height: 54px; /* Garante altura mínima consistente */
        }

        /* Estilo para a área que exibe o valor inserido */
        .monitoring-display-area {
            font-size: 0.95rem; /* Aumenta um pouco o texto do valor */
            font-weight: 600; /* Deixa em negrito */
            color: #1e40af; /* Cor azul (text-blue-800) */
            min-height: 24px; /* Altura mínima para não "pular" quando vazio */
            padding-top: 0.1rem;
        }

        /* Estilo para os inputs de monitoramento, para serem mais compactos */
        #module-monitoramento .monitoring-input {
            padding: 0.3rem 0.5rem; /* Menos preenchimento vertical */
            font-size: 0.875rem; /* text-sm */
            border-radius: 0.375rem; /* rounded-md */
            border: 1px solid #d1d5db; /* border-gray-300 */
            box-shadow: inset 0 1px 2px rgba(0,0,0,0.05);
        }

        /* Remove a borda padrão do input quando ele está focado */
        #module-monitoramento .monitoring-input:focus {
            outline: none;
            border-color: #3b82f6; /* Cor da borda azul ao focar */
            box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.4); /* Sombra externa azul */
        }

        /* Esta regra remove a altura e margem do container de tags de alergia APENAS quando ele estiver vazio */
        #allergies-tags-container:empty {
            min-height: 0;
            margin-bottom: 0.25rem; /* Reduz a margem inferior quando vazio */
            padding-top: 0;
        }

        /* Estilos GENÉRICOS para o conteúdo dos modais de handover */
        .summary-section {
            background-color: #f9fafb; /* bg-gray-50 */
            border: 1px solid #e5e7eb; /* border-gray-200 */
            border-radius: 0.5rem; /* rounded-lg */
            padding: 1rem;
            margin-bottom: 1rem; /* Espaçamento entre as seções */
        }
        .summary-section:last-child {
            margin-bottom: 0;
        }
        .summary-section-header {
            font-size: 0.875rem; /* text-sm */
            font-weight: 600; /* font-semibold */
            color: #111827; /* text-gray-900 - Título mais escuro */
            border-bottom: 1px solid #e5e7eb;
            padding-bottom: 0.5rem;
            margin-bottom: 0.75rem;
            display: flex;
            align-items: center;
        }
        .summary-item {
            padding-bottom: 0.5rem; /* Pequeno espaço entre os itens */
        }
        .summary-item-label {
            font-weight: 600; /* font-semibold */
            color: #1f2937; /* text-gray-800 - Rótulo preto */
            display: block; /* Rótulo em sua própria linha */
        }
        .summary-item-value {
            color: #374151; /* text-gray-700 */
            padding-left: 0.25rem; /* Leve indentação do valor */
        }
        .summary-item-value ul {
            list-style-type: none;
            padding-left: 0;
            margin-top: 0.25rem;
        }
        .summary-item-value li {
            padding-bottom: 0.25rem;
        }

        /* --- ESTILO PARA ALERGIA ATIVA E CLICÁVEL --- */
        .allergy-module-box.is-active {
            border-color: #d1d5db; /* border-gray-300 */
        }

        .allergy-module-box.is-active:hover {
            cursor: pointer;
            border-color: #9ca3af; /* border-gray-400 */
        }

        .history-item-button {
            display: flex;
            justify-content: space-between;
            align-items: center;
            width: 100%;
            padding: 0.75rem 1rem; /* 12px 16px */
            border-bottom: 1px solid #e5e7eb; /* border-gray-200 */
            text-align: left;
            transition: background-color 0.2s ease-in-out;
        }

        .history-item-button:hover {
            background-color: #f9fafb; /* bg-gray-50 */
            cursor: pointer;
        }

        .history-item-button:last-child {
            border-bottom: none;
        }

        .history-item-info {
            display: flex;
            align-items: center;
            gap: 0.75rem; /* 12px */
        }

        .history-item-icon {
            flex-shrink: 0;
            width: 2.5rem; /* 40px */
            height: 2.5rem; /* 40px */
            border-radius: 9999px; /* rounded-full */
            background-color: #eef2ff; /* bg-indigo-100 */
            color: #4338ca; /* text-indigo-700 */
            display: flex;
            align-items: center;
            justify-content: center;
        }

        /* --- ESTILO PARA O NOVO BOTÃO DE VOLTAR DOS MODAIS --- */
        .modal-back-button {
            flex-shrink: 0;
            padding: 0.5rem; /* 8px */
            border-radius: 9999px; /* rounded-full */
            color: #4b5563; /* text-gray-600 */
            background-color: transparent;
            transition: background-color 0.2s, color 0.2s;
        }

        .modal-back-button:hover {
            background-color: #f3f4f6; /* bg-gray-100 */
            color: #1f2937; /* text-gray-800 */
            cursor: pointer;
        }

        /* --- CSS para o Indicador de Conectividade --- */
        .status-dot {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            margin-right: 6px;
            transition: background-color 0.3s ease;
        }
        .status-online {
            background-color: #22c55e; /* green-500 */
        }

        .status-connecting {
            background-color: #facc15; /* yellow-400 */
            animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        }
        .status-offline {
            background-color: #a1a1aa; /* zinc-400 */
            animation: pulse 1.5s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        }

        @keyframes pulse {
        50% {
            opacity: .5;
        }
        }

        /* 1. Estilo base para todos os cards nos módulos para permitir o alinhamento */
        .module-card {
            display: flex;
            flex-direction: column;
            height: 100%; /* Faz o card tentar ocupar toda a altura da célula do grid */
        }



        /* 3. Modifica o módulo de Medicações para remover as restrições de altura */
        #module-medicacoes {
            /* Remove as alturas mínimas e máximas para permitir o crescimento dinâmico */
            min-height: initial; 
            max-height: initial;
        }

        /* Garante que o conteúdo interno dos módulos de Exames e Medicações cresça */
        #exam-lists-container,
        #medications-list-container {
            flex-grow: 1;
        }

        /* --- ESTILO PARA ITEM DE DISPOSITIVO --- */
        .device-item-box {
            display: flex;
            align-items: center;
            justify-content: flex-start; /* Alinha o conteúdo à esquerda */
            padding: 0.70rem; /* p-3 */
            border: 2px dashed #e5e7eb; /* border-2 border-dashed border-gray-200 */
            border-radius: 0.5rem; /* rounded-lg */
            transition: all 0.2s ease-in-out;
        }

        .device-item-box:hover {
            background-color: #f9fafb; /* bg-gray-50 */
            border-color: #d1d5db; /* border-gray-300 */
        }

        /* --- OTIMIZAÇÃO DO MÓDULO DE DIAGNÓSTICO --- */

        /* 1. O contêiner principal dos itens do módulo */
        #module-diagnostico .module-content-area {
            display: flex;          /* Ativa o layout flexível */
            flex-direction: column; /* Organiza os itens em uma coluna (verticalmente) */
            height: 100%;           /* Garante que o contêiner ocupe toda a altura do card */
            gap: 0.75rem;           /* (Tailwind: gap-3) Adiciona um espaçamento consistente e igual entre todos os itens */
        }

        /* 2. A seção específica da Evolução/Plano */
        #module-diagnostico .evolution-section {
            display: flex;          /* Também se torna um contêiner flexível */
            flex-direction: column; /* Para o label e o textarea ficarem um em cima do outro */
            flex-grow: 1;           /* ESSENCIAL: Faz esta seção crescer para ocupar todo o espaço vertical que sobrar */
            min-height: 0;          /* Necessário para garantir que o flex-grow funcione corretamente em todos os navegadores */
        }

        /* 3. O próprio campo de texto da evolução */
        #module-diagnostico .evolution-section textarea {
            flex-grow: 1;           /* Faz o textarea preencher o espaço disponível dentro da sua seção */
            resize: none;           /* Impede que o usuário redimensione o textarea, o que quebraria o layout */
        }

        /* --- OTIMIZAÇÃO DOS MÓVEIS DE PRECAUÇÕES E DISPOSITIVOS --- */

        /* 1. Transforma os cards dos módulos em containers flexíveis verticais */
        #module-precaucoes,
        #module-dispositivos {
            display: flex;
            flex-direction: column;
            height: 100%; /* Garante que o card ocupe toda a altura da célula do grid */
        }

        /* 2. Faz as áreas de conteúdo (a lista de tags/dispositivos) crescerem para ocupar o espaço vazio */
        #precaucoes-container,
        #dispositivos-grid {
            flex-grow: 1;
            /* Boa prática para evitar problemas de overflow em flex containers */
            min-height: 0; 
            padding-bottom: 0.75rem;
        }

        /* 3. Empurra as áreas de ação (botão "+ Novo" e "Cancelar") para o final do card */
        #module-precaucoes .trigger-wrapper,
        #module-precaucoes .cancel-action-wrapper,
        #module-dispositivos .trigger-wrapper,
        #module-dispositivos .cancel-action-wrapper {
            margin-top: auto; /* A mágica do flexbox para empurrar para o fundo */
        }

        /* --- AJUSTE DE LAYOUT PARA O MÓDULO DE OBSERVAÇÕES GERAIS --- */

        /* 1. Transforma o card principal em um container flexível vertical. */
        #module-observacoes {
            display: flex;
            flex-direction: column;
        }

        /* 2. Faz o container que envolve a caixa de texto crescer para ocupar o espaço. */
        /* Usamos "> div:last-child" para selecionar o div que contém o textarea. */
        #module-observacoes > div:last-child {
            display: flex; /* Para que o filho (textarea) possa esticar */
            flex-grow: 1;
        }

        /* 3. Faz a caixa de texto (textarea) esticar para preencher 100% da altura do seu container pai. */
        #form-pending-obs {
            height: 100%;
            resize: none; /* Opcional: Desativa o redimensionamento manual pelo usuário, o que melhora a estética. */
        }

        /* --- CORREÇÃO DE ALINHAMENTO INTERNO PARA MÓDULOS FLEXÍVEIS --- */

        #precaucoes-container,
        #dispositivos-grid {
            /*
            * Impede que os itens internos (tags e checkboxes) se estiquem ou se espalhem verticalmente.
            * 'align-content: flex-start' alinha todo o conteúdo ao topo do container,
            * deixando o espaço extra vazio na parte inferior, exatamente onde queremos.
            */
            align-content: flex-start;
        }

        /* --- ESTILOS PARA O NOVO LAYOUT DE ADENDOS --- */
        
        /* 1. Transforma o container principal do modal em um container flexível vertical */
        .modal-flex-container {
            display: flex;
            flex-direction: column;
            max-height: 85vh; /* Define a altura máxima como 85% da altura da tela */
            width: 90%; /* Ocupa 90% da largura da tela */
            max-width: 1024px; /* Define uma largura máxima de 1024px para telas grandes */
        }

        /* 2. Faz a área de conteúdo principal crescer e ter sua própria rolagem (sem alteração) */
        .modal-scrollable-content {
            flex-grow: 1;
            overflow-y: auto;
            padding-right: 0.5rem;
        }
        
        /* 3. CORREÇÃO: Container de adendos agora tem fundo branco e apenas uma borda superior */
        .adendos-container {
            flex-shrink: 0;
            padding-top: 0.75rem;
            margin-top: 0.75rem;
            border-top: 1px solid #e5e7eb; /* Mantém apenas a linha divisória */
            background-color: #ffffff; /* Fundo branco */
            position: relative; /* Necessário para posicionar o botão de adicionar */
        }

        /* 4. A lista de adendos (sem alteração) */
        .adendos-list {
            max-height: 150px;
            overflow-y: auto;
            padding-right: 0.5rem;
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
        }

        /* 5. Estilo da "bolha" de cada adendo (sem alteração) */
        .adendo-bubble {
            background-color: #eef2ff;
            padding: 0.5rem 0.75rem;
            border-radius: 0.5rem;
            font-size: 0.875rem;
            line-height: 1.4;
        }

        /* 6. Estilo dos metadados (quem e quando) do adendo (sem alteração) */
        .adendo-meta {
            font-size: 0.75rem;
            color: #6b7280;
            text-align: right;
            margin-top: 0.25rem;
        }

        /* Botão de expandir/recolher com ícone */
        .adendo-toggle-icon-btn {
            background-color: #f3f4f6; /* bg-gray-100 */
            color: #4b5563; /* text-gray-600 */
            border-radius: 9999px; /* rounded-full */
            width: 28px;
            height: 28px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            transition: background-color 0.2s;
        }
        .adendo-toggle-icon-btn:hover {
            background-color: #e5e7eb; /* bg-gray-200 */
        }

        /* --- ESTILOS PARA O PAINEL DE NOTIFICAÇÕES --- */
        #notification-list .notification-item {
            padding: 0.75rem; /* 12px */
            border-bottom: 1px solid #f3f4f6; /* border-gray-100 */
            transition: background-color 0.2s ease-in-out;
            cursor: pointer;
        }

        #notification-list .notification-item:hover {
            background-color: #f9fafb; /* bg-gray-50 */
        }

        #notification-list .notification-item:last-child {
            border-bottom: none;
        }

        /* Estilo para notificações NÃO LIDAS */
        #notification-list .notification-item.unread {
            background-color: #eef2ff; /* bg-indigo-50 */
            position: relative;
        }

        /* Pequeno ponto azul para indicar não lida */
        #notification-list .notification-item.unread::before {
            content: '';
            position: absolute;
            right: 12px;  /* Alinhado com o padding direito (0.75rem) */
            bottom: 15px; /* Posicionado para alinhar verticalmente com a linha da data */
            width: 8px;   /* Aumentado para melhor visibilidade */
            height: 8px;
            background-color: #4f46e5; /* bg-indigo-600 */
            border-radius: 50%;
            /* A propriedade 'transform' foi removida pois não é mais necessária */
        }

        /* Esconde permanentemente o botão 'x' das tags nos módulos de Riscos e Cuidados */
        #module-riscos.module-editing .info-tag .remove-item-btn,
        #module-cuidados-enfermagem.module-editing .info-tag .remove-item-btn {
            display: none;
        }

        /* 1. Adiciona uma transição suave para os cards de paciente */
        .patient-card, .patient-list-item {
            transition: all 0.35s ease-in-out;
        }

        /* 2. Define o estado "arquivando" que será ativado via JavaScript */
        .is-archiving {
            opacity: 0;
            transform: scale(0.95);
            max-height: 0;
            padding-top: 0;
            padding-bottom: 0;
            margin-top: 0;
            margin-bottom: -1rem; /* Compensa o 'gap' para os outros itens subirem suavemente */
            overflow: hidden;
            border-width: 0;
        }

        /* --- Adiciona estilo às listas de autocomplete --- */
        #diagnosis-autocomplete-list,
        #medication-autocomplete-list {
            position: absolute; /* Garante o posicionamento flutuante */
            background-color: white;
            border: 1px solid #d1d5db; /* Tailwind: border-gray-300 */
            border-radius: 0.375rem; /* Tailwind: rounded-md */
            box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1); /* Tailwind: shadow-lg */
            z-index: 10000; /* Z-index alto para sobrepor tudo */
            max-height: 240px; /* A altura máxima continua limitando o tamanho */
            overflow-y: auto;
        }

        /* Melhora a aparência do item na lista de autocomplete */
        .autocomplete-item {
            padding: 0.5rem 0.75rem;
            font-size: 0.875rem;
            cursor: pointer;
        }

        .autocomplete-item:hover {
            background-color: #f3f4f6; /* Tailwind: bg-gray-100 */
        }

        #search-patient:not(:placeholder-shown) + #search-placeholder-container {
            display: none;
        }

        /* --- INÍCIO: NOVOS ESTILOS PARA MOBILE --- */
        @media (max-width: 768px) {
            /* Reduz o título do histórico do módulo no celular */
            #module-history-title {
                font-size: 1.125rem; /* Equivalente a text-lg do Tailwind */
                line-height: 1.75rem; /* Equivalente a text-lg do Tailwind */
            }

            /* Ajusta o popup do histórico para não transbordar no celular */
            #module-history-modal .relative {
                width: 95%; /* Ocupa 95% da tela, evitando transbordar */
                padding: 1rem; /* Reduz o padding interno no celular */
            }

            /* Opcional: Reduz o título do modal de detalhes do plantão também */
            #view-handover-main-title {
                font-size: 1.125rem;
                line-height: 1.75rem;
            }

            /*
             * Aumenta a fonte de inputs e textareas para 16px em telas menores
             * para prevenir o zoom automático em dispositivos iOS.
             * O !important garante que esta regra sobrescreva as outras em telas de celular.
            */
            input[type="text"],
            input[type="email"],
            input[type="password"],
            input[type="number"],
            input[type="time"],
            textarea {
                font-size: 16px !important;
            }

            /* Permite que o corpo e o app cresçam e rolem verticalmente */
            body, #app {
                height: auto;
                min-height: 100vh; /* Garante que o fundo ocupe a tela toda */
                overflow: visible;
            }

            /* Força a área principal a ser a responsável pela rolagem */
            main {
                overflow-y: visible;
            }
            
            /* Garante que os cabeçalhos fiquem fixos no topo ao rolar a tela */
            #main-content > header, #patient-detail-screen > header {
                position: sticky;
                top: 0;
                z-index: 50; /* Garante que fique sobre o conteúdo */
            }

            /* Adiciona um espaçamento no final da tela de detalhes do paciente */
            /* para que o botão "Salvar" não fique escondido pela barra de navegação */
            #patient-detail-screen main {
                padding-bottom: 6rem; /* 96px de espaço extra no final */
            }

            #trends-chart-container > div {
                /* Aumenta a altura do container do gráfico para 70% da altura da tela em dispositivos móveis */
                height: 70vh;
            }
            #patient-detail-name {
                /* Define uma largura máxima para o contêiner do nome,
                   evitando que ele empurre os botões para fora da tela.
                   O cálculo subtrai o espaço aproximado dos botões e margens. */
                max-width: calc(100vw - 150px);

                /* As três propriedades abaixo garantem que, se o nome ainda for
                   muito longo, ele será cortado com "..." no final. */
                white-space: nowrap;
                overflow: hidden;
                text-overflow: ellipsis;
            }

            /* 1. Remove o posicionamento fixo à direita do painel */
            #notification-panel {
                position: fixed; /* Garante que ele flutue sobre o conteúdo */
                right: auto;     /* Remove o alinhamento à direita */
                left: 50%;       /* Posiciona o lado esquerdo do painel no meio da tela */
                top: 6rem; /* Distância do topo */
                transform: translateX(-50%); /* Puxa o painel de volta pela metade de sua largura, centralizando-o */
                width: 95%;      /* Define a largura para 95% da tela, deixando pequenas margens */
                max-width: 400px; /* Define uma largura máxima para não ficar excessivamente largo em telas maiores */
            }
            
        }
        /* --- FIM: NOVOS ESTILOS PARA MOBILE --- */

    </style>
</head>
<body class="bg-gray-50 text-gray-800">
    <!-- Container Principal da Aplicação -->
    <div id="app" class="h-full flex flex-col overflow-hidden">

        <!-- Tela de Carregamento Inicial -->
        <div id="loading-screen" class="flex items-center justify-center min-h-screen">
            <div class="text-center">
                <svg class="animate-spin h-10 w-10 text-blue-600 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                <p class="mt-4 text-lg font-medium text-gray-600">Carregando Sistema...</p>
            </div>
        </div>

        <!-- Tela de Login -->
        <div id="login-screen" class="hidden flex-grow">
            <div class="flex min-h-full flex-col justify-center px-6 py-12 lg:px-8">
                <div class="sm:mx-auto sm:w-full sm:max-w-md">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="mx-auto h-12 w-auto text-blue-600"><rect x="2" y="3" width="20" height="14" rx="2" ry="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="17" x2="12" y2="21"/><path d="M8 7h8"/><path d="M8 11h4"/></svg>
                    <h2 class="mt-6 text-center text-2xl font-bold leading-9 tracking-tight text-gray-900">Acesse sua conta</h2>
                    <p class="mt-2 text-center text-sm text-gray-600">Sistema de Passagem de Plantão</p>
                </div>

                <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-md">
                    <form id="login-form" class="space-y-6">
                        <div>
                            <label for="login-email" class="block text-sm font-medium leading-6 text-gray-900">Email</label>
                            <div class="mt-2">
                                <input id="login-email" name="email" type="email" autocomplete="email" required class="block w-full rounded-md border-0 py-2 px-3 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-blue-600 sm:text-sm sm:leading-6">
                            </div>
                        </div>

                        <div>
                            <div class="flex items-center justify-between">
                                <label for="login-password" class="block text-sm font-medium leading-6 text-gray-900">Senha</label>
                            </div>
                            <div class="mt-2">
                                <input id="login-password" name="password" type="password" autocomplete="current-password" required class="block w-full rounded-md border-0 py-2 px-3 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-blue-600 sm:text-sm sm:leading-6">
                            </div>
                        </div>

                        <div>
                            <button type="submit" class="flex w-full justify-center rounded-md bg-blue-600 px-3 py-2 text-sm font-semibold leading-6 text-white shadow-sm hover:bg-blue-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600">Entrar</button>
                        </div>
                    </form>

                    <p class="mt-10 text-center text-sm text-gray-500">
                        Não tem uma conta?
                        <a href="#" id="go-to-register" class="font-semibold leading-6 text-blue-600 hover:text-blue-500">Cadastre-se</a>
                    </p>
                </div>
            </div>
        </div>

        <!-- Tela de Cadastro -->
        <div id="register-screen" class="hidden flex-grow">
            <div class="flex min-h-full flex-col justify-center px-6 py-12 lg:px-8">
                <div class="sm:mx-auto sm:w-full sm:max-w-md">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="mx-auto h-12 w-auto text-blue-600"><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><line x1="19" x2="19" y1="8" y2="14"/><line x1="22" x2="16" y1="11" y2="11"/></svg>
                    <h2 class="mt-6 text-center text-2xl font-bold leading-9 tracking-tight text-gray-900">Crie sua conta</h2>
                     <p class="mt-2 text-center text-sm text-gray-600">Utilize um e-mail fictício, que não precisa existir, mas ele obrigatoriamente deve terminar com @testeficticio.com (ex: seu.nome@testeficticio.com).</p>
                </div>

                <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-md">
                    <form id="register-form" class="space-y-6">
                        <div>
                            <label for="register-name" class="block text-sm font-medium leading-6 text-gray-900">Nome Completo</label>
                            <input id="register-name" type="text" required class="mt-2 block w-full rounded-md border-0 py-2 px-3 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:ring-2 focus:ring-inset focus:ring-blue-600">
                        </div>
                        <div>
                            <label for="register-email" class="block text-sm font-medium leading-6 text-gray-900">Email para o Teste</label>
                            <input id="register-email" type="email" required class="mt-2 block w-full rounded-md border-0 py-2 px-3 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:ring-2 focus:ring-inset focus:ring-blue-600">
                        </div>
                        <div>
                            <label for="register-password" class="block text-sm font-medium leading-6 text-gray-900">Senha</label>
                            <input id="register-password" type="password" required class="mt-2 block w-full rounded-md border-0 py-2 px-3 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:ring-2 focus:ring-inset focus:ring-blue-600">
                        </div>
                        <div>
                            <button type="submit" class="flex w-full justify-center rounded-md bg-blue-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-blue-500">Cadastrar</button>
                        </div>
                    </form>

                    <p class="mt-10 text-center text-sm text-gray-500">
                        Já tem uma conta?
                        <a href="#" id="go-to-login" class="font-semibold leading-6 text-blue-600 hover:text-blue-500">Faça o login</a>
                    </p>
                </div>
            </div>
        </div>
        
        <!-- Área Principal (Dashboard de Pacientes) -->
        <div id="main-content" class="hidden flex-grow flex flex-col h-full">
            <!-- Cabeçalho -->
            <header class="bg-white shadow-sm">
                <div class="mx-auto px-4 sm:px-6 lg:px-8">
                    <div class="flex h-16 items-center justify-between">
                    <div class="flex items-center">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-8 w-auto text-blue-600"><rect x="2" y="3" width="20" height="14" rx="2" ry="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="17" x2="12" y2="21"/><path d="M8 7h8"/><path d="M8 11h4"/></svg>
                        <span class="ml-3 hidden text-xl font-bold text-gray-800 sm:inline">Passa Plantão</span>
                    </div>
                        <div class="flex items-center space-x-4">
                            <div id="connection-status" class="flex items-center">
                            </div>
                            <span id="user-info" class="hidden sm:block text-sm text-gray-600"></span>
                            
                            <div class="relative">
                                <button id="notification-bell-btn" class="relative p-2 rounded-full text-gray-500 hover:bg-gray-100 hover:text-gray-700 focus:outline-none">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6 6 0 00-5-5.917V5a1 1 0 00-2 0v.083A6 6 0 006 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" />
                                    </svg>
                                    <span id="notification-indicator" class="hidden absolute top-1 right-1 block h-2.5 w-2.5 rounded-full bg-red-500 border-2 border-white"></span>
                                </button>

                                <div id="notification-panel" class="hidden absolute right-0 mt-2 w-80 origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 z-50">
                                    <div class="p-2 border-b">
                                        <p class="text-sm font-semibold text-gray-800">Notificações</p>
                                    </div>
                                    <div id="notification-list" class="max-h-96 overflow-y-auto">
                                        </div>
                                </div>
                            </div>
                            <button id="logout-button" class="rounded-md bg-red-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-red-500">
                                Sair
                            </button>
                        </div>
                    </div>
                </div>
            </header>

            <!-- Conteúdo do Dashboard -->
            <main class="flex-grow overflow-y-auto min-h-0">
                <div class="mx-auto py-6 sm:px-6 lg:px-8">

                    <!-- Título e Ações -->
                    <div class="px-4 sm:px-0 flex flex-col md:flex-row md:items-center md:justify-between gap-y-4">
                        
                        <div class="flex items-center justify-center md:justify-start gap-x-3 order-last md:order-first">
                            <h1 class="text-2xl font-bold tracking-tight text-gray-900">Painel de Pacientes</h1>
                            <button id="show-unit-summary-button" title="Ver Resumo da Unidade" class="p-2 rounded-full text-gray-500 hover:bg-gray-100 hover:text-indigo-600 transition-colors">
                                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-6">
                                  <path fill-rule="evenodd" d="M3 6a3 3 0 0 1 3-3h12a3 3 0 0 1 3 3v12a3 3 0 0 1-3 3H6a3 3 0 0 1-3-3V6Zm4.5 7.5a.75.75 0 0 1 .75.75v2.25a.75.75 0 0 1-1.5 0v-2.25a.75.75 0 0 1 .75-.75Zm3.75-1.5a.75.75 0 0 0-1.5 0v4.5a.75.75 0 0 0 1.5 0V12Zm2.25-3a.75.75 0 0 1 .75.75v6.75a.75.75 0 0 1-1.5 0V9.75A.75.75 0 0 1 13.5 9Zm3.75-1.5a.75.75 0 0 0-1.5 0v9a.75.75 0 0 0 1.5 0v-9Z" clip-rule="evenodd" />
                                </svg>
                            </button>
                        </div>
                        
                        <div class="flex items-center justify-between md:justify-end gap-x-2 order-first md:order-last">
                            <div class="relative">
                                <button id="view-toggle-button" class="rounded-md border border-gray-300 bg-white p-2 text-sm font-medium text-gray-700 shadow-sm hover:bg-gray-50">
                                    <svg id="view-toggle-icon-grid" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5"><rect width="7" height="7" x="3" y="3" rx="1"/><rect width="7" height="7" x="14" y="3" rx="1"/><rect width="7" height="7" x="14" y="14" rx="1"/><rect width="7" height="7" x="3" y="14" rx="1"/></svg>
                                    <svg id="view-toggle-icon-list" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5 hidden"><line x1="8" x2="21" y1="6" y2="6"/><line x1="8" x2="21" y1="12" y2="12"/><line x1="8" x2="21" y1="18" y2="18"/><line x1="3" x2="3.01" y1="6" y2="6"/><line x1="3" x2="3.01" y1="12" y2="12"/><line x1="3" x2="3.01" y1="18" y2="18"/></svg>
                                </button>
                                <div id="view-toggle-dropdown" class="hidden absolute left-0 sm:right-0 sm:left-auto mt-2 w-32 origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 z-20">
                                    <div class="py-1">
                                        <a href="#" data-view="grid" class="view-option-button text-gray-700 group flex items-center px-4 py-2 text-sm">
                                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="mr-3 h-5 w-5 text-gray-400 group-hover:text-gray-500"><rect width="7" height="7" x="3" y="3" rx="1"/><rect width="7" height="7" x="14" y="3" rx="1"/><rect width="7" height="7" x="14" y="14" rx="1"/><rect width="7" height="7" x="3" y="14" rx="1"/></svg>
                                            Grade
                                        </a>
                                        <a href="#" data-view="list" class="view-option-button text-gray-700 group flex items-center px-4 py-2 text-sm">
                                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="mr-3 h-5 w-5 text-gray-400 group-hover:text-gray-500"><line x1="8" x2="21" y1="6" y2="6"/><line x1="8" x2="21" y1="12" y2="12"/><line x1="8" x2="21" y1="18" y2="18"/><line x1="3" x2="3.01" y1="6" y2="6"/><line x1="3" x2="3.01" y1="12" y2="12"/><line x1="3" x2="3.01" y1="18" y2="18"/></svg>
                                            Lista
                                        </a>
                                    </div>
                                </div>
                            </div>
        
                            <div class="relative">
                                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none z-10">
                                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24"
                                        viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                                        stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5 text-gray-400">
                                        <circle cx="11" cy="11" r="8" />
                                        <path d="m21 21-4.3-4.3" />
                                    </svg>
                                </div>

                                <input id="search-patient" type="text" placeholder=" "
                                    class="block w-full rounded-md border-gray-300 py-2 pr-4 pl-10 shadow-sm focus:border-blue-500 focus:ring-blue-500 md:w-56"
                                    autocomplete="off">
                                
                                <div id="search-placeholder-container" class="absolute inset-y-0 left-10 flex items-center text-gray-400 pointer-events-none">
                                    <span class="md:hidden">Pacientes</span>
                                    <span class="hidden md:inline">Buscar paciente...</span>
                                </div>
                            </div>
                            <div class="relative" id="bed-filter-container">
                                <button id="bed-filter-button" class="flex items-center justify-between w-full rounded-md border border-gray-300 bg-white px-3 py-2 text-sm font-medium text-gray-700 shadow-sm hover:bg-gray-50 md:px-4 md:w-48">
                                    <span id="bed-filter-button-text" class="truncate">
                                        <span class="hidden md:inline">Buscar por </span><span>Leito</span>
                                    </span>
                                    <span id="bed-filter-clear-wrapper" class="hidden ml-2">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" class="h-4 w-4 text-red-500 hover:text-red-700"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                                    </span>
                                    <svg id="bed-filter-arrow-icon" class="h-5 w-5 ml-2 text-gray-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z" clip-rule="evenodd"></path></svg>
                                </button>
                                <div id="bed-filter-dropdown" class="hidden absolute right-0 mt-2 w-56 origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 z-20">
                                    <div class="p-2">
                                        <input id="bed-search-input" type="text" placeholder="Filtrar leitos..." class="w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 sm:text-sm" autocomplete="off">
                                    </div>
                                    <div id="bed-filter-list" class="max-h-60 overflow-y-auto p-1"></div>
                                    <div class="border-t border-gray-200 p-2">
                                        <button id="bed-filter-clear-button" class="w-full text-center text-sm text-blue-600 hover:underline">Limpar seleção</button>
                                    </div>
                                </div>
                            </div>

                            <button id="add-patient-button" class="flex items-center justify-center rounded-md bg-blue-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-blue-700 md:px-4">
                                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5 md:mr-2"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
                                <span class="hidden md:inline">Novo Paciente</span>
                                <span class="md:hidden">Novo</span>
                            </button>
                        </div>
                    </div>


                    <!-- Lista de Pacientes -->
                    <div id="patient-list" class="mt-6 px-4 sm:px-0 grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3">
                        <!-- Cards de pacientes serão inseridos aqui pelo JS -->
                    </div>
                    <div id="no-patients-message" class="hidden"></div>

                    <div id="load-more-trigger" class="w-full py-8 flex justify-center items-center hidden">
                        <svg class="animate-spin h-8 w-8 text-blue-600" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                        </svg>
                    </div>
                </div>
            </main>
        </div>

        <!-- Tela de Detalhes do Paciente -->
        <div id="patient-detail-screen" class="hidden h-full flex flex-col">
            <header class="bg-white shadow-sm sticky top-0 z-10">
                <div class="mx-auto px-4 sm:px-6 lg:px-8">
                    <div class="flex h-16 items-center justify-between">
                        <button id="back-to-dashboard" class="flex items-center text-blue-600 hover:text-blue-800">
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5 mr-2"><path d="m12 19-7-7 7-7"/><path d="M19 12H5"/></svg>
                            <span class="font-semibold">Voltar ao Painel</span>
                        </button>
                        <div class="flex items-center flex-wrap gap-2">
                            <button id="show-summary-button" title="Ver Resumo Atual do Paciente" class="rounded-md bg-blue-100 p-2 text-sm font-semibold text-blue-700 shadow-sm hover:bg-blue-200">
                                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                                  <path stroke-linecap="round" stroke-linejoin="round" d="M7.5 14.25v2.25m3-4.5v4.5m3-6.75v6.75m3-9v9M6 20.25h12A2.25 2.25 0 0 0 20.25 18V6A2.25 2.25 0 0 0 18 3.75H6A2.25 2.25 0 0 0 3.75 6v12A2.25 2.25 0 0 0 6 20.25Z" />
                                </svg>
                            </button>
                            <button id="delete-patient-button" title="Arquivar Paciente" class="rounded-md bg-red-100 p-2 text-sm font-semibold text-red-700 shadow-sm hover:bg-red-200">
                                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5"><path d="M3 6h18"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6"/><path d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg>
                            </button>
                        </div>
                    </div>
                </div>
            </header>

            <main class="flex-grow overflow-y-auto p-4 sm:p-6 lg:p-8">

                <div class="bg-white shadow-md rounded-lg p-4 mb-6">
                    <div class="flex justify-between items-start mb-2">
                        <div class="flex items-center">
                            <h1 id="patient-detail-name" class="text-2xl font-bold text-gray-900 truncate"></h1>
                            <button id="edit-patient-details-button" title="Editar dados cadastrais do paciente" class="ml-3 p-1 rounded-full text-gray-500 hover:bg-gray-200 hover:text-blue-600 transition-colors">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                    <path d="M17.414 2.586a2 2 0 00-2.828 0L7 10.172V13h2.828l7.586-7.586a2 2 0 000-2.828z" />
                                    <path fill-rule="evenodd" d="M2 6a2 2 0 012-2h4a1 1 0 010 2H4v10h10v-4a1 1 0 112 0v4a2 2 0 01-2 2H4a2 2 0 01-2-2V6z" clip-rule="evenodd" />
                                </svg>
                            </button>
                        </div>

                        <div class="flex items-center space-x-2 flex-shrink-0 ml-4">
                            <button id="show-last-handover-button" title="Ver Última Passagem" class="rounded-md bg-white sm:px-3 sm:py-2 p-2 text-sm font-semibold text-gray-700 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"> 
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 sm:hidden" viewBox="0 0 20 20" fill="currentColor">
                                    <path d="M8 3a1 1 0 011-1h2a1 1 0 110 2H9a1 1 0 01-1-1z" />
                                    <path d="M6 3a2 2 0 00-2 2v11a2 2 0 002 2h8a2 2 0 002-2V5a2 2 0 00-2-2 3 3 0 01-3 3H9a3 3 0 01-3-3z" />
                                </svg>
                                <span class="hidden sm:block">Última Passagem</span>
                            </button>
                        </div>
                    </div>

                    <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-y-2 text-sm text-gray-600 border-t pt-2">
                        <div class="flex flex-wrap items-center gap-x-4 gap-y-2">
                            <div><span class="font-semibold">Idade:</span> <span id="patient-detail-age"></span></div>
                            <div><span class="font-semibold">Internação:</span> <span id="patient-detail-admission-date"></span></div> 
                            <div><span class="font-semibold">Leito:</span> <span id="patient-detail-room" class="font-mono"></span></div>
                            <div><span class="font-semibold">Prontuário:</span> <span id="patient-detail-number" class="font-mono"></span></div>
                        </div>
                        
                        <div class="flex items-center flex-wrap gap-2">
                            <div id="patient-detail-news2"></div>
                            <div id="patient-detail-fugulin"></div>
                        </div>
                    </div>
                </div>

                <form id="add-handovers-form" class="flex flex-col">
                    <div id="modules-grid-container" class="flex-grow grid grid-cols-1 lg:grid-cols-3 gap-4">

                            <div id="module-diagnostico" class="bg-white rounded-lg shadow p-4 module-card">
                                <div class="flex justify-between items-center mb-2 border-b pb-2">
                                    <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                        Diagnóstico e Situação
                                    </h3>
                                    <div class="flex items-center space-x-2">
                                        <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="diagnostico">
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                            <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                            </svg>
                                        </button>
                                    </div>
                                </div>
                                <div class="module-content-area">
                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area">
                                            <label class="block text-xs font-semibold text-gray-8002mb-1">Diagnóstico(s) Principal(is)</label>
                                            <div id="diagnoses-tags-container" class="tags-display-area"></div>
                                            <div id="diagnosis-input-wrapper" class="input-wrapper hidden mt-2">
                                                <input type="text" id="form-diagnosis" autocomplete="off" placeholder="Buscar por CID ou nome..." class="w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 py-1 px-2 text-sm">
                                                <div id="diagnosis-autocomplete-container" class="relative">
                                                    <div id="diagnosis-autocomplete-list" class="absolute w-full bg-white border rounded-md shadow-lg z-10 hidden"></div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Comorbidades</label>
                                            <div id="comorbidities-tags-container" class="tags-display-area"></div>
                                            <div id="comorbidities-input-wrapper" class="input-wrapper hidden mt-2">
                                                <input type="text" id="form-comorbidities" autocomplete="off" placeholder="Adicionar e teclar Enter..." class="w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm">
                                            </div>
                                        </div>
                                    </div>
                                                        
                                    <div class="allergy-module-box clickable-item-area">
                                        <label class="block text-xs font-semibold text-gray-800">Alérgico?</label>
                                        <div class="flex items-center space-x-4 mt-2">
                                            <label class="flex items-center cursor-pointer">
                                                <input type="radio" id="allergy-radio-yes" name="allergy-known" class="h-4 w-4 text-blue-600 focus:ring-blue-500">
                                                <span class="ml-2 text-sm">Sim</span>
                                            </label>
                                            <label class="flex items-center cursor-pointer">
                                                <input type="radio" id="allergy-radio-no" name="allergy-known" class="h-4 w-4 text-blue-600 focus:ring-blue-500">
                                                <span class="ml-2 text-sm">Não</span>
                                            </label>
                                        </div>
                                        <div id="allergy-input-container" class="hidden mt-3 item-section-wrapper">
                                            <label id="allergy-description-label" class="block text-xs font-semibold text-gray-800 mb-1">Alergias Conhecidas:</label>
                                            <div id="allergies-tags-container" class="tags-display-area mb-2"></div>
                                            <p id="allergy-placeholder-message" class="text-gray-500 text-xs italic hidden pl-1">Nenhuma alergia adicionada</p>
                                            <div id="allergy-input-wrapper" class="input-wrapper hidden">
                                                <input type="text" id="form-allergies" autocomplete="off" placeholder="Descrever e teclar Enter..." class="w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm">
                                            </div>
                                        </div>
                                    </div>
                                    
                                    <div class="evolution-section">
                                        <label class="block text-xs font-semibold text-gray-800 mb-1">Evolução / Plano</label>
                                        <textarea id="form-evolution" autocomplete="off" rows="3" class="mt-1 w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm" placeholder="Evolução do paciente, intercorrências e plano."></textarea>
                                    </div>
                                </div>
                            </div>

                            <div id="module-cuidados-enfermagem" class="bg-white rounded-lg shadow p-4 module-card">
                                <div class="flex justify-between items-center mb-2 border-b pb-2">
                                    <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                        Cuidados de Enfermagem
                                    </h3>
                                    <span id="live-fugulin-score" class="ml-auto font-mono text-sm font-bold text-gray-600 mr-4"></span>
                                    <div class="flex items-center space-x-2">
                                        <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="cuidados">
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                            <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                            </svg>
                                        </button>
                                    </div>
                                </div>
                                
                                <div class="space-y-3">
                                    
                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-fugulin="cuidadoCorporal">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Cuidado Corporal / Pele</label>
                                            <div id="fugulin-cuidado-corporal-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="fugulin-cuidado-corporal-options" class="custom-select-options hidden"></div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-fugulin="motilidade">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Motilidade / Movimentação</label>
                                            <div id="fugulin-motilidade-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="fugulin-motilidade-options" class="custom-select-options hidden"></div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-fugulin="deambulacao">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Deambulação</label>
                                            <div id="fugulin-deambulacao-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="fugulin-deambulacao-options" class="custom-select-options hidden"></div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-fugulin="alimentacao">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Alimentação / Hidratação</label>
                                            <div id="fugulin-alimentacao-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="fugulin-alimentacao-options" class="custom-select-options hidden"></div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-fugulin="eliminacao">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Cuidado com Eliminações</label>
                                            <div id="fugulin-eliminacao-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="fugulin-eliminacao-options" class="custom-select-options hidden"></div>
                                    </div>
                                </div>
                            </div>
                            <div id="module-exames" class="bg-white rounded-lg shadow p-4 module-card">
                                <div class="flex justify-between items-center mb-2 border-b pb-2">
                                    <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                        Exames e Procedimentos
                                    </h3>
                                    <div class="flex items-center space-x-2">
                                        <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="exames">
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                            <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                            </svg>
                                        </button>
                                    </div>
                                </div>

                                <div id="exam-lists-container" class="grid grid-cols-1 md:grid-cols-2 gap-4 flex-grow">
                                    <div>
                                        <h4 class="text-xs font-semibold text-gray-700 mb-1 text-center border-b pb-1">A Fazer / Agendados</h4>
                                        <div id="scheduled-exams-list" class="space-y-1 bg-gray-50 p-1 rounded-md min-h-[150px]"></div>
                                    </div>
                                    <div>
                                        <h4 class="text-xs font-semibold text-gray-700 mb-1 text-center border-b pb-1">Pendentes de Resultado</h4>
                                        <div id="pending-exams-list" class="space-y-1 bg-gray-50 p-1 rounded-md min-h-[150px]"></div>
                                    </div>
                                </div>

                                <div id="exam-editor-area" class="hidden border-t pt-3 mt-3">
                                    <input type="hidden" id="exam-editor-id">
                                    <input type="hidden" id="exam-editor-mode">
                                    <button type="button" id="close-exam-editor-btn" class="absolute top-2 right-2 p-1 rounded-full hover:bg-gray-200">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-gray-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                                        </svg>
                                    </button>
                                    <h4 id="exam-editor-title" class="text-sm font-semibold text-gray-800 mb-2"></h4>
                                    <div class="space-y-3">
                                        <div id="exam-editor-name-input-wrapper">
                                            <label for="exam-editor-name-input" class="block text-xs font-medium text-gray-700">Nome do Exame</label>
                                            <input type="text" id="exam-editor-name-input" autocomplete="off" placeholder="Ex: Hemograma, TC de Crânio..." class="mt-1 w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm">
                                        </div>
                                        <div id="exam-editor-name-display-wrapper" class="hidden">
                                            <p class="text-xs font-medium text-gray-700">Exame</p>
                                            <p id="exam-editor-name-display" class="mt-1 text-sm font-semibold text-gray-900"></p>
                                        </div>
                                        <div id="exam-editor-flow-choice-wrapper" class="hidden">
                                            <div class="mt-1 flex space-x-2">
                                                <button type="button" id="exam-editor-flow-schedule-btn" class="flex-1 rounded-md bg-blue-100 text-blue-700 px-3 py-1 text-xs font-semibold hover:bg-blue-200">Agendar</button>
                                                <button type="button" id="exam-editor-flow-register-btn" class="flex-1 rounded-md bg-green-100 text-green-700 px-3 py-1 text-xs font-semibold hover:bg-green-200">Registrar Realização</button>
                                            </div>
                                        </div>
                                        <div id="exam-editor-datetime-wrapper" class="hidden">
                                            <label id="exam-editor-datetime-label" for="exam-editor-datetime-input" class="block text-xs font-medium text-gray-700">Data e Hora</label>
                                            <input type="text" id="exam-editor-datetime-input" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm">
                                        </div>
                                        <div id="exam-editor-result-choice-wrapper" class="hidden">
                                            <label class="block text-xs font-medium text-gray-700">Resultado disponível?</label>
                                            <div class="mt-1 flex space-x-2">
                                                <button type="button" id="exam-editor-result-yes-btn" class="flex-1 rounded-md border border-gray-300 px-3 py-1 text-xs font-semibold hover:bg-gray-100">Sim</button>
                                                <button type="button" id="exam-editor-result-no-btn" class="flex-1 rounded-md border border-gray-300 px-3 py-1 text-xs font-semibold hover:bg-gray-100">Não</button>
                                            </div>
                                        </div>
                                        <div id="exam-editor-result-textarea-wrapper" class="hidden">
                                            <label for="exam-editor-result-textarea" class="block text-xs font-medium text-gray-700">Descrição do Resultado</label>
                                            <textarea id="exam-editor-result-textarea" autocomplete="off" rows="2" class="mt-1 w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm" placeholder="Descreva os achados relevantes..."></textarea>
                                        </div>
                                        <div id="exam-editor-actions-wrapper" class="hidden flex justify-end space-x-2 pt-2 border-t mt-2">
                                            <button type="button" id="exam-editor-cancel-btn" class="rounded-md bg-white px-3 py-1 text-xs font-semibold text-gray-700 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50">Cancelar</button>
                                            <button type="button" id="exam-editor-save-btn" class="rounded-md bg-blue-600 px-3 py-1 text-xs font-semibold text-white shadow-sm hover:bg-blue-500">Confirmar</button>
                                        </div>
                                    </div>
                                </div>
                                <div id="exam-main-action-area" class="border-t pt-2 mt-auto"> <div class="flex justify-center">
                                    <button type="button" id="add-new-exam-btn" class="rounded-full bg-blue-50 hover:bg-blue-100 text-blue-700 font-semibold px-4 py-1 flex items-center space-x-2 text-xs">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd" /></svg>
                                        <span>Novo Exame</span>
                                    </button>
                                </div>
                                </div>
                            </div>

                            <div id="module-riscos" class="bg-white rounded-lg shadow p-4 module-card">
                                <div class="flex justify-between items-center mb-2 border-b pb-2">
                                    <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                        Riscos Assistenciais
                                    </h3>
                                    <div class="flex items-center space-x-2">
                                        <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="riscos">
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                            <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                            </svg>
                                        </button>
                                    </div>
                                </div>
                                <div class="grid grid-cols-1 gap-y-3">
                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-risk="lpp">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Risco de LPP</label>
                                            <div id="riscos-lpp-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="riscos-lpp-options" class="custom-select-options hidden"></div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-risk="quedas">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Risco de Quedas</label>
                                            <div id="riscos-quedas-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="riscos-quedas-options" class="custom-select-options hidden"></div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-risk="bronco">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Risco de Broncoaspiração</label>
                                            <div id="riscos-bronco-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="riscos-bronco-options" class="custom-select-options hidden"></div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area" data-risk="iras">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Risco de IRAS</label>
                                            <div id="riscos-iras-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="riscos-iras-options" class="custom-select-options hidden"></div>
                                    </div>
                                </div>
                            </div>

                            <div id="module-monitoramento" class="bg-white rounded-lg shadow p-4 module-card">
                                <div class="flex justify-between items-center mb-2 border-b pb-2">
                                    <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                        Monitoramento
                                    </h3>
                                    <span id="live-news2-score" class="ml-auto font-mono text-sm font-bold text-gray-600 mr-4"></span>
                                    <div class="flex items-center space-x-2">
                                        <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="monitoramento">
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                                <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                            </svg>
                                        </button>
                                    </div>
                                </div>
                                
                                <div class="grid grid-cols-2 lg:grid-cols-3 gap-x-4 gap-y-3">

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area monitoring-item-area" data-monitoring-item="true">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">PA (mmHg)</label>
                                            <div class="monitoring-display-area"></div>
                                            <input type="text" id="form-sv-pa" autocomplete="off" class="monitoring-input w-full hidden" placeholder="Ex: 120/80" data-validate="pressure">
                                        </div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area monitoring-item-area" data-monitoring-item="true">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">FC (bpm)</label>
                                            <div class="monitoring-display-area"></div>
                                            <input type="text" id="form-sv-fc" autocomplete="off" class="monitoring-input w-full hidden" placeholder="Ex: 75" data-validate="numeric">
                                        </div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area monitoring-item-area" data-monitoring-item="true">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">FR (irpm)</label>
                                            <div class="monitoring-display-area"></div>
                                            <input type="text" id="form-sv-fr" autocomplete="off" class="monitoring-input w-full hidden" placeholder="Ex: 16" data-validate="numeric">
                                        </div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area monitoring-item-area" data-monitoring-item="true">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">SatO₂ (%)</label>
                                            <div class="monitoring-display-area"></div>
                                            <input type="text" id="form-sv-sato2" autocomplete="off" class="monitoring-input w-full hidden" placeholder="Ex: 98" data-validate="numeric">
                                        </div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area monitoring-item-area" data-monitoring-item="true">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Temp (°C)</label>
                                            <div class="monitoring-display-area"></div>
                                            <input type="text" id="form-sv-temp" autocomplete="off" class="monitoring-input w-full hidden" placeholder="Ex: 36.5" data-validate="decimal">
                                        </div>
                                    </div>

                                    <div class="item-section-wrapper">
                                        <div class="clickable-item-area monitoring-item-area" data-monitoring-item="true">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">HGT (mg/dL)</label>
                                            <div class="monitoring-display-area"></div>
                                            <input type="text" id="form-sv-hgt" autocomplete="off" class="monitoring-input w-full hidden" placeholder="Ex: 95" data-validate="numeric">
                                        </div>
                                    </div>
                                    
                                    <div class="lg:col-span-2 item-section-wrapper">
                                        <div class="clickable-item-area" data-monitoring="consciencia">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Consciência</label>
                                            <div id="monitoring-consciencia-container" class="tags-display-area"></div>
                                        </div>
                                        <div id="monitoring-consciencia-options" class="custom-select-options hidden"></div>
                                    </div>

                                    <div class="flex items-center justify-center p-2 border-2 border-dashed border-gray-200 rounded-lg">
                                        <label class="flex items-center space-x-2 cursor-pointer">
                                            <input type="checkbox" id="form-sv-o2" class="h-4 w-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500">
                                            <span class="text-sm font-medium text-gray-800">Uso de O₂?</span>
                                        </label>
                                    </div>

                                    <div class="lg:col-span-3 item-section-wrapper">
                                        <div class="clickable-item-area monitoring-item-area" data-monitoring-item="true">
                                            <label class="block text-xs font-semibold text-gray-800 mb-1">Diurese / Drenos / Outros</label>
                                            <div class="monitoring-display-area"></div>
                                            <input type="text" id="form-sv-others" autocomplete="off" class="monitoring-input w-full hidden" placeholder="Descrever..." data-validate="text">
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <div id="module-dispositivos" class="bg-white rounded-lg shadow p-4 module-card">
                                <div class="flex justify-between items-center mb-2 border-b pb-2">
                                    <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                        Dispositivos
                                    </h3>
                                    <div class="flex items-center space-x-2">
                                        <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="dispositivos">
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                            <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                            </svg>
                                        </button>
                                    </div>
                                </div>

                                <div id="dispositivos-grid" class="grid grid-cols-2 gap-x-4 gap-y-2 text-sm min-h-[100px]">

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="AVP MSE" class="rounded">
                                            <span>AVP MSE</span>
                                        </label>
                                    </div>

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="AVP MSD" class="rounded">
                                            <span>AVP MSD</span>
                                        </label>
                                    </div>

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="PICC" class="rounded">
                                            <span>PICC</span>
                                        </label>
                                    </div>

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="CVC" class="rounded">
                                            <span>CVC</span>
                                        </label>
                                    </div>

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="CDL" class="rounded">
                                            <span>CDL</span>
                                        </label>
                                    </div>

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="SNE" class="rounded">
                                            <span>SNE</span>
                                        </label>
                                    </div>

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="GTT" class="rounded">
                                            <span>GTT</span>
                                        </label>
                                    </div>

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="SVD" class="rounded">
                                            <span>SVD</span>
                                        </label>
                                    </div>

                                    <div class="device-item-box">
                                        <label class="flex items-center space-x-2 w-full cursor-pointer">
                                            <input type="checkbox" name="dispositivo" value="Monitor" class="rounded">
                                            <span>Monitor</span>
                                        </label>
                                    </div>
                                    
                                    <div id="custom-dispositivos-container" class="contents"></div>

                                </div>

                                    <div id="outros-dispositivos-input-wrapper" class="input-wrapper hidden mt-2">
                                        <input type="text" id="dispositivo-outros-input" placeholder="Digite o nome e tecle Enter..." class="w-full text-sm rounded-md border-gray-300 shadow-sm p-1">
                                    </div>

                                <div class="border-t pt-2 mt-2 flex justify-center trigger-wrapper">
                                    <button type="button" id="add-custom-dispositivo-btn" class="add-item-trigger-btn rounded-full bg-blue-50 hover:bg-blue-100 text-blue-700 font-semibold px-4 py-1 flex items-center space-x-2 text-xs">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd"></path></svg>
                                        <span>Novo Dispositivo</span>
                                    </button>
                                </div>

                                <div class="cancel-action-wrapper hidden border-t pt-2 mt-2 flex justify-center">
                                    <button type="button" class="cancel-action-btn text-xs text-red-600 hover:underline mt-2">
                                        Cancelar
                                    </button>
                                </div>
                            </div>

                            <div id="module-precaucoes" class="bg-white rounded-lg shadow p-4 module-card">
                                <div class="flex justify-between items-center mb-2 border-b pb-2">
                                    <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                        Precauções
                                    </h3>
                                    <div class="flex items-center space-x-2">
                                        <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="precaucoes">
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                            <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                            </svg>
                                        </button>
                                    </div>
                                </div>
                                
                                <div id="precaucoes-container" class="tags-display-area min-h-[24px]"></div>

                                <div id="precaucoes-input-wrapper" class="input-wrapper hidden mt-2">
                                    <input type="text" id="form-precaucoes" placeholder="Adicionar precaução e teclar Enter..." class="w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm">
                                </div>

                                <div class="border-t pt-2 mt-2 flex justify-center trigger-wrapper">
                                    <button type="button" class="add-item-trigger-btn rounded-full bg-blue-50 hover:bg-blue-100 text-blue-700 font-semibold px-4 py-1 flex items-center space-x-2 text-xs" data-target-input="#precaucoes-input-wrapper">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd"></path></svg>
                                        <span>Nova Precaução</span>
                                    </button>
                                </div>

                                <div class="cancel-action-wrapper hidden border-t pt-2 mt-2 flex justify-center">
                                    <button type="button" class="cancel-action-btn text-xs text-red-600 hover:underline mt-2">
                                        Cancelar
                                    </button>
                                </div>
                            </div>

                            <div id="module-medicacoes" class="bg-white rounded-lg shadow p-4 module-card">
                                    <div class="flex justify-between items-center mb-2 border-b pb-2">
                                        <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                            Medicações do Plantão
                                        </h3>
                                        <div class="flex items-center space-x-2">
                                            <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="medicacoes">
                                                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                                <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                                </svg>
                                            </button>
                                        </div>
                                    </div>
                                <input type="text" id="medication-time-picker-input" class="hidden">
                                
                                <div id="medications-list-container" class="space-y-2 min-h-[24px]"></div>

                                <div class="mt-auto pt-3 border-t border-gray-200">
            
                                    <div id="medication-action-area" class="flex justify-center">
                                        <button type="button" id="add-new-medication-btn" class="rounded-full bg-blue-50 hover:bg-blue-100 text-blue-700 font-semibold px-4 py-1 flex items-center space-x-2 text-xs">
                                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd" /></svg>
                                            <span>Nova Medicação</span>
                                        </button>
                                    </div>

                                    <div id="medication-editor-area" class="hidden">
                                        <div id="medication-search-editor">
                                            <label for="form-medications" class="block text-xs font-medium text-gray-700 mb-1">Buscar ou Digitar Nova Medicação</label>
                                            <div class="relative mt-1 flex items-center gap-x-2">
                                                <div class="relative flex-grow">
                                                    <input type="text" id="form-medications" autocomplete="off" placeholder="Buscar ou digitar nome..." class="w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm">
                                                    <div id="medication-autocomplete-container" class="relative">
                                                        <div id="medication-autocomplete-list" class="absolute w-full bg-white border rounded-md shadow-lg z-10 hidden"></div>
                                                    </div>
                                                </div>
                                                <button type="button" id="confirm-med-name-btn" class="hidden flex-shrink-0 rounded-md bg-blue-600 px-3 py-1 text-xs font-semibold text-white shadow-sm hover:bg-blue-500">Confirmar</button>
                                            </div>
                                        </div>

                                        <div id="medication-time-editor" class="hidden">
                                            <label id="medication-time-editor-label" class="block text-xs font-medium text-gray-700">Selecione o horário</label>
                                            <div id="inline-time-picker-container" class="flex justify-center"></div>
                                            <div class="flex items-center space-x-2 mt-2">
                                                <button type="button" id="correct-med-time-btn" class="hidden flex-1 rounded-md bg-white px-3 py-1 text-xs font-semibold text-gray-700 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50">Corrigir Antigo</button>
                                                <button type="button" id="add-med-time-btn" class="flex-1 rounded-md bg-blue-600 px-3 py-1 text-xs font-semibold text-white shadow-sm hover:bg-blue-500">Adicionar Novo</button>
                                            </div>
                                        </div>

                                        <button type="button" id="cancel-editor-btn" class="w-full text-center text-xs text-red-600 hover:underline mt-2">Cancelar</button>
                                    </div>
                                </div> 
                            </div>

                            <div id="module-observacoes" class="bg-white rounded-lg shadow p-4 module-card">
                                <div class="flex justify-between items-center mb-2 border-b pb-2">
                                    <h3 class="text-base font-semibold text-gray-800 flex items-center">
                                        Observações Gerais
                                    </h3>
                                    <div class="flex items-center space-x-2">
                                        <button type="button" title="Ver Histórico do Módulo" class="module-history-btn text-blue-600 hover:text-blue-800 transition-colors" data-module="observacoes">
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                                            <path fill-rule="evenodd" d="M6.75 2.25A.75.75 0 017.5 3v1.5h9V3A.75.75 0 0118 2.25a.75.75 0 01.75.75v1.5h.75a3 3 0 013 3v11.25a3 3 0 01-3 3H5.25a3 3 0 01-3-3V7.5a3 3 0 013-3h.75V3a.75.75 0 01.75-.75zM5.25 6.75c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h13.5c.621 0 1.125-.504 1.125-1.125V7.875c0-.621-.504-1.125-1.125-1.125H5.25z" clip-rule="evenodd" />
                                            </svg>
                                        </button>
                                    </div>
                                </div>
                                <div>
                                    <label for="form-pending-obs" class="block text-xs font-medium text-gray-700 sr-only">Observações Gerais</label>
                                    <textarea id="form-pending-obs" autocomplete="off" rows="2" class="w-full rounded-md border-gray-300 shadow-sm py-1 px-2 text-sm" placeholder="Algo importante para o próximo plantonista?"></textarea>
                                </div>
                            </div>
                    </div>

                    <div class="flex justify-end mt-6">
                        <button type="submit" class="rounded-md bg-gray-400 cursor-not-allowed opacity-75 px-6 py-3 text-base font-semibold text-white shadow-sm focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600" disabled>
                            Salvar Passagem de Plantão
                        </button>
                    </div>
                </form>

            </main>
        </div>

        <!-- Modal para Histórico de Alterações do Módulo -->
        <div id="module-history-modal" class="hidden fixed inset-0 bg-gray-900 bg-opacity-75 overflow-y-auto h-full w-full z-50">
            <div class="relative top-10 mx-auto p-5 border w-full max-w-3xl shadow-lg rounded-md bg-white">
                <div class="flex justify-between items-center pb-3 border-b">
                    <p id="module-history-title" class="text-2xl font-bold text-gray-800">Histórico de Alterações</p>
                    <button id="close-module-history-modal" class="cursor-pointer z-50 p-2 rounded-full hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-6 w-6 text-gray-600"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                    </button>
                </div>
                <div id="module-history-content" class="mt-4 max-h-[70vh] overflow-y-auto pr-2 space-y-4">
                    </div>
            </div>
        </div>

        <!-- Modal para Visualizar Detalhes do Plantão -->
        <div id="view-handover-modal" class="hidden fixed inset-0 bg-gray-900 bg-opacity-75 overflow-y-auto h-full w-full z-50">
                <div class="relative top-10 mx-auto p-5 border w-full max-w-4xl shadow-lg rounded-md bg-white modal-flex-container">
                    <div class="flex-shrink-0 flex flex-wrap justify-between items-center pb-3 border-b gap-y-2">
                        <div class="flex items-center gap-x-4 order-2 sm:order-1">
                            <button id="back-to-history-list-btn" class="modal-back-button" title="Voltar">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M15 19l-7-7 7-7" /></svg>
                            </button>
                            <div>
                                <p id="view-handover-main-title" class="text-lg md:text-2xl font-bold text-gray-800">Detalhes do Plantão</p>
                                <p id="view-handover-subtitle" class="text-xs sm:text-sm text-gray-500 mt-1"></p>
                            </div>
                        </div>
                        <div class="flex items-center justify-end space-x-2 order-1 sm:order-2 w-full sm:w-auto">
                            <button id="print-handover-detail-button" title="Imprimir" class="rounded-md bg-gray-100 p-2 text-sm font-semibold text-gray-700 shadow-sm hover:bg-gray-200">
                                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5"><polyline points="6 9 6 2 18 2 18 9"/><path d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2"/><rect x="6" y="14" width="12" height="8"/></svg>
                            </button>
                            <button id="close-view-handover-modal" class="cursor-pointer z-50 p-2 rounded-full hover:bg-gray-200">
                                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-6 w-6 text-gray-600"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                            </button>
                        </div>
                    </div>

                    <div id="view-handover-content" class="mt-4 modal-scrollable-content">
                        </div>

                    <div id="view-handover-adendos-section" class="adendos-container">
                        <div id="adendos-visible-wrapper-view-handover" class="hidden">
                            <div class="flex justify-between items-center mb-2">
                                <h4 class="text-base font-semibold text-gray-700">Adendos</h4>
                                <button id="toggle-adendos-view-view-handover" class="toggle-adendos-view-btn adendo-toggle-icon-btn hidden" data-state="last" title="Fechar">
                                <svg class="h-5 w-5 icon-expand" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="m4.5 15.75 7.5-7.5 7.5 7.5" />
                                </svg>
                                <svg class="h-5 w-5 icon-collapse hidden" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="m19.5 8.25-7.5 7.5-7.5-7.5" />
                                </svg>
                            </button>
                            </div>
                            <div id="adendos-list-view-handover" class="adendos-list">
                                </div>
                        </div>

                        <div id="inline-adendo-form-wrapper-view-handover" class="inline-adendo-form-wrapper mt-2 hidden">
                            <textarea id="adendo-text-view-handover" rows="2" class="w-full rounded-md border-gray-300 shadow-sm text-sm" placeholder="Digite seu adendo..."></textarea>
                            <div class="flex justify-end space-x-2 mt-2">
                                <button type="button" class="cancel-adendo-btn px-3 py-1 text-sm font-semibold text-gray-700 rounded-md hover:bg-gray-100">Cancelar</button>
                                <button type="button" class="save-adendo-btn rounded-md bg-blue-600 px-4 py-1 text-sm font-semibold text-white shadow-sm hover:bg-blue-700">Salvar Adendo</button>
                            </div>
                        </div>

                        <div id="add-adendo-trigger-wrapper-view-handover" class="add-adendo-trigger-wrapper mt-2">
                            <button type="button" class="add-adendo-trigger-btn rounded-md bg-gray-200 hover:bg-gray-300 text-gray-800 font-semibold px-3 py-1 flex items-center space-x-2 text-sm">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd" /></svg>
                                <span>Adicionar Adendo</span>
                            </button>
                        </div>
                    </div>
                </div>
            </div>

        <!-- Modal para Resumo do Paciente -->
        <div id="patient-summary-modal" class="hidden fixed inset-0 bg-gray-900 bg-opacity-75 overflow-y-auto h-full w-full z-50">
            <div class="relative top-5 mx-auto p-5 border w-full max-w-6xl shadow-lg rounded-md bg-white">
                <div class="flex flex-col sm:flex-row sm:justify-between sm:items-center pb-3 border-b gap-y-2">
                    <div class="w-full flex items-center justify-between order-1 sm:w-auto sm:order-2 space-x-2">
                        <button id="print-patient-button" title="Imprimir Resumo" class="rounded-md bg-gray-100 p-2 text-sm font-semibold text-gray-700 shadow-sm hover:bg-gray-200">
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5"><polyline points="6 9 6 2 18 2 18 9"/><path d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2"/><rect x="6" y="14" width="12" height="8"/></svg>
                        </button>
                        <button id="close-summary-modal-button" class="cursor-pointer z-50 p-2 rounded-full hover:bg-gray-200">
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-6 w-6 text-gray-600"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                        </button>
                    </div>
                    <div class="w-full order-2 sm:order-1">
                        <p id="patient-summary-title" class="text-lg sm:text-2xl font-bold text-gray-800">Resumo Clínico da Semana</p>
                        <p id="patient-summary-subtitle" class="text-sm text-gray-500"></p>
                    </div>
                </div>
                
                <div id="patient-summary-content" class="mt-4 max-h-[80vh] overflow-y-auto pr-2">
                    
                    <div id="summary-loader" class="text-center p-10">
                        <svg class="animate-spin h-8 w-8 text-blue-600 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                        <p class="mt-3 text-gray-600">Analisando dados da semana...</p>
                    </div>

                    <div id="summary-main-container" class="hidden space-y-6">
                        
                        <div id="kpi-panel-container">
                            <h3 class="text-base font-semibold text-gray-700 mb-2">Destaques da Semana</h3>
                            <div id="kpi-panel" class="grid grid-cols-2 md:grid-cols-4 lg:grid-cols-6 gap-3">
                                </div>
                        </div>

                        <div id="trends-chart-container">
                            <h3 class="text-base font-semibold text-gray-700 mb-2">Tendências de Sinais Vitais e Scores</h3>
                            <div class="bg-gray-50 p-2 rounded-lg">
                                <canvas id="trends-chart"></canvas>
                            </div>
                        </div>

                        <div id="events-table-container">
                            <h3 class="text-base font-semibold text-gray-700 mb-2">Linha do Tempo de Eventos Relevantes</h3>
                            <div class="overflow-x-auto">
                                <table class="min-w-full bg-white border">
                                    <thead class="bg-gray-100">
                                        <tr>
                                            <th class="text-left text-xs font-semibold text-gray-600 uppercase p-2">Data/Hora</th>
                                            <th class="text-left text-xs font-semibold text-gray-600 uppercase p-2">Categoria</th>
                                            <th class="text-left text-xs font-semibold text-gray-600 uppercase p-2">Evento / Descrição</th>
                                            <th class="text-left text-xs font-semibold text-gray-600 uppercase p-2">Profissional</th>
                                        </tr>
                                    </thead>
                                    <tbody id="events-table-body">
                                        </tbody>
                                </table>
                            </div>
                        </div>

                    </div>
                </div>
            </div>
        </div>

        <!-- Modal para Adicionar Paciente -->
        <div id="add-patient-modal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50">
            <div class="relative top-20 mx-auto p-5 border w-full max-w-md shadow-lg rounded-md bg-white">
                <div class="flex justify-between items-center pb-3">
                    <p class="text-2xl font-bold">Cadastrar Novo Paciente</p>
                    <button id="close-modal-button" class="cursor-pointer z-50">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-6 w-6"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                    </button>
                </div>
                <form id="add-patient-form" class="space-y-5" autocomplete="off">
                    <div>
                        <label for="new-patient-name" class="block text-sm font-medium text-gray-700">Nome Completo</label>
                        <input id="new-patient-name" type="text" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500" placeholder="Digite o nome completo do paciente">
                    </div>
                    <div>
                        <label for="new-patient-dob" class="block text-sm font-medium text-gray-700">Data de Nascimento</label>
                        <input id="new-patient-dob" type="text" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500" placeholder="Selecione a data">
                    </div>
                    <div>
                        <label for="new-patient-number" class="block text-sm font-medium text-gray-700">Nº do Prontuário</label>
                        <input id="new-patient-number" type="number" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500" placeholder="Digite o número do prontuário">
                    </div>
                    <div>
                        <label for="new-patient-room" class="block text-sm font-medium text-gray-700">Nº do Leito/Sala</label>
                        <input id="new-patient-room" type="number" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500" placeholder="Digite o número do leito">
                    </div>
                    <div class="flex justify-end pt-4">
                        <button type="submit" class="rounded-md bg-blue-600 px-4 py-2 text-sm font-semibold text-white shadow-sm hover:bg-blue-700">Salvar Paciente</button>
                    </div>
                </form>
            </div>
        </div>

        <!-- Modal para Editar Paciente -->
        <div id="edit-patient-modal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50">
            <div class="relative top-10 mx-auto p-5 border w-full max-w-lg shadow-lg rounded-md bg-white">
                <div class="flex justify-between items-center pb-3">
                    <p class="text-2xl font-bold">Editar Dados Cadastrais do Paciente</p>
                    <button id="close-edit-modal-button" class="cursor-pointer z-50">&times;</button>
                </div>
                <div class="text-sm text-gray-600 mb-4">
                    As informações originais não serão apagadas. Uma nova entrada será criada no histórico com a sua justificativa.
                </div>
                <form id="edit-patient-form" class="space-y-4" autocomplete="off">
                    <input type="hidden" id="edit-patient-id">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div>
                            <label class="block text-sm font-medium">Nome Completo</label>
                            <input id="edit-patient-name" type="text" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                        </div>
                        <div>
                            <label class="block text-sm font-medium">Data de Nascimento</label>
                            <input id="edit-patient-dob" type="text" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="dd/mm/aaaa">
                        </div>
                        <div>
                            <label class="block text-sm font-medium">Nº do Prontuário</label>
                            <input id="edit-patient-number" type="number" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                        </div>
                        <div>
                            <label class="block text-sm font-medium">Nº do Leito/Sala</label>
                            <input id="edit-patient-room" type="number" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                        </div>
                    </div>
                    <div>
                        <label class="block text-sm font-medium">Justificativa da Correção / Adendo</label>
                        <textarea id="edit-patient-justification" required rows="3" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="Ex: Correção de erro de digitação no prontuário."></textarea>
                    </div>
                    <div class="flex justify-end pt-4">
                        <button type="submit" class="rounded-md bg-blue-600 px-4 py-2 text-sm font-semibold text-white shadow-sm hover:bg-blue-700">Salvar Correção</button>
                    </div>
                </form>
            </div>
        </div>

        <!-- Modal para Visualizar Histórico Completo -->
        <div id="full-history-modal" class="hidden fixed inset-0 bg-gray-900 bg-opacity-75 overflow-y-auto h-full w-full z-50">
            <div class="relative top-10 mx-auto p-5 border w-full max-w-4xl shadow-lg rounded-md bg-white modal-flex-container">
                <div class="flex justify-between items-center pb-3 border-b">
                    <div class="flex items-center gap-x-4">
                        <button id="back-to-last-handover-btn" class="modal-back-button" title="Voltar para Última Passagem">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M15 19l-7-7 7-7" />
                            </svg>
                        </button>
                        <p class="text-lg md:text-2xl font-bold text-gray-800">Histórico Completo de Passagens de Plantão</p>
                    </div>
                    <button id="close-full-history-modal" class="cursor-pointer z-50 p-2 rounded-full hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-6 w-6 text-gray-600"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                    </button>
                </div>

                <div id="full-history-content-wrapper" class="mt-4 max-h-[70vh] overflow-y-auto pr-2">
                    <div id="full-history-content" class="space-y-0">
                        </div>
                </div>
                <div id="full-history-pagination" class="flex justify-center items-center mt-4 pt-4 border-t"></div>
            </div>
        </div>

        <!-- Modal de Confirmação de Exclusão -->
        <div id="delete-confirm-modal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50">
            <div class="relative top-20 mx-auto p-5 border w-full max-w-md shadow-lg rounded-md bg-white">
                <div class="mt-3 text-center">
                    <div class="mx-auto flex items-center justify-center h-12 w-12 rounded-full bg-red-100">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-6 w-6 text-red-600"><path d="m21.73 18-8-14a2 2 0 0 0-3.46 0l-8 14A2 2 0 0 0 4 21h16a2 2 0 0 0 1.73-3Z"/><path d="M12 9v4"/><path d="M12 17h.01"/></svg>
                    </div>
                    <h3 class="text-lg leading-6 font-medium text-gray-900 mt-4">Arquivar Paciente</h3>
                    <div class="mt-2 px-7 py-3">
                        <p class="text-sm text-gray-500">
                            Tem certeza que deseja arquivar este paciente e todo o seu histórico?<br>Esta ação não pode ser desfeita.
                        </p>
                    </div>
                    <div class="items-center px-4 py-3 space-x-4">
                        <button id="confirm-delete-button" class="px-4 py-2 bg-red-600 text-white text-base font-medium rounded-md w-auto shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500">
                            Arquivar
                        </button>
                        <button id="cancel-delete-button" class="px-4 py-2 bg-gray-200 text-gray-800 text-base font-medium rounded-md w-auto shadow-sm hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-400">
                            Cancelar
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div id="generic-confirm-modal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50">
            <div class="relative top-20 mx-auto p-5 border w-full max-w-md shadow-lg rounded-md bg-white">
                <div class="mt-3 text-center">
                    <!-- Ícone e cores atualizados para o tema azul -->
                    <div class="mx-auto flex items-center justify-center h-12 w-12 rounded-full bg-blue-100">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                        </svg>
                    </div>
                    <h3 id="generic-confirm-title" class="text-lg leading-6 font-medium text-gray-900 mt-4">Título da Confirmação</h3>
                    <div class="mt-2 px-7 py-3">
                        <p id="generic-confirm-text" class="text-sm text-gray-500">
                            Texto da confirmação vai aqui.
                        </p>
                    </div>
                    <div class="items-center px-4 py-3 space-x-4">
                        <!-- Botão de confirmação atualizado para o tema azul -->
                        <button id="generic-confirm-button" class="px-4 py-2 bg-blue-600 text-white text-base font-medium rounded-md w-auto shadow-sm hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500">
                            Confirmar Ação
                        </button>
                        <button id="generic-cancel-button" class="px-4 py-2 bg-gray-200 text-gray-800 text-base font-medium rounded-md w-auto shadow-sm hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-400">
                            Cancelar
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div id="cancel-exam-confirm-modal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50">
            <div class="relative top-20 mx-auto p-5 border w-full max-w-md shadow-lg rounded-md bg-white">
                <div class="mt-3 text-center">
                    <div class="mx-auto flex items-center justify-center h-12 w-12 rounded-full bg-blue-100">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                        </svg>
                    </div>
                    <h3 class="text-lg leading-6 font-medium text-gray-900 mt-4">Cancelar Exame</h3>
                    <div class="mt-2 px-7 py-3">
                        <p id="cancel-exam-modal-text" class="text-sm text-gray-500">
                            Tem certeza de que deseja cancelar este exame? Ele será removido da lista a partir deste plantão.
                        </p>
                    </div>
                    <div class="items-center px-4 py-3 space-x-4">
                        <button id="confirm-cancel-exam-button" class="px-4 py-2 bg-blue-600 text-white text-base font-medium rounded-md w-auto shadow-sm hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500">
                            Sim, Cancelar
                        </button>
                        <button id="cancel-cancel-exam-button" class="px-4 py-2 bg-gray-200 text-gray-800 text-base font-medium rounded-md w-auto shadow-sm hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-400">
                            Voltar
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div id="clear-allergies-confirm-modal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50">
            <div class="relative top-20 mx-auto p-5 border w-full max-w-md shadow-lg rounded-md bg-white">
                <div class="mt-3 text-center">
                    <div class="mx-auto flex items-center justify-center h-12 w-12 rounded-full bg-blue-100">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                        </svg>
                    </div>
                    <h3 class="text-lg leading-6 font-medium text-gray-900 mt-4">Remover Alergias?</h3>
                    <div class="mt-2 px-7 py-3">
                        <p id="clear-allergies-modal-text" class="text-sm text-gray-500">
                            Mudar para "Não" irá remover todas as alergias listadas. Deseja continuar?
                        </p>
                    </div>
                    <div class="items-center px-4 py-3 space-x-4">
                        <button id="confirm-clear-allergies-button" class="px-4 py-2 bg-blue-600 text-white text-base font-medium rounded-md w-auto shadow-sm hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500">
                            Sim, Remover
                        </button>
                        <button id="cancel-clear-allergies-button" class="px-4 py-2 bg-gray-200 text-gray-800 text-base font-medium rounded-md w-auto shadow-sm hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-400">
                            Cancelar
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div id="last-handover-modal" class="hidden fixed inset-0 bg-gray-900 bg-opacity-80 overflow-y-auto h-full w-full z-50">
                <div class="relative top-10 mx-auto p-5 border w-full max-w-4xl shadow-lg rounded-md bg-white modal-flex-container">
                    <div class="flex-shrink-0 flex flex-col sm:flex-row sm:justify-between sm:items-center pb-3 border-b gap-y-2">
                        <div class="w-full flex items-center justify-between order-1 sm:w-auto sm:order-2 space-x-2">
                            <div class="flex items-center space-x-2">
                                <button id="print-last-handover-button" title="Imprimir" class="rounded-md bg-gray-100 p-2 text-sm font-semibold text-gray-700 shadow-sm hover:bg-gray-200">
                                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5"><polyline points="6 9 6 2 18 2 18 9"/><path d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2"/><rect x="6" y="14" width="12" height="8"/></svg>
                                </button>
                                <button id="open-full-history-from-last-handover-btn" class="rounded-md bg-white px-3 py-2 text-sm font-semibold text-blue-600 shadow-sm ring-1 ring-inset ring-blue-300 hover:bg-blue-50">
                                    Histórico
                                </button>
                            </div>
                            <button id="close-last-handover-modal" class="cursor-pointer z-50 p-2 rounded-full hover:bg-gray-200">
                                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-6 w-6 text-gray-600"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                            </button>
                        </div>
                        <div class="w-full order-2 sm:order-1">
                            <p id="last-handover-title" class="text-lg md:text-2xl font-bold text-gray-800">Última Passagem de Plantão</p>
                            <p id="last-handover-subtitle" class="text-sm text-gray-500 mt-1"></p>
                        </div>
                    </div>

                    <div id="last-handover-content" class="mt-4 text-sm modal-scrollable-content">
                        </div>

                    <div id="last-handover-adendos-section" class="adendos-container">
                        <div id="adendos-visible-wrapper-last-handover" class="hidden">
                            <div class="flex justify-between items-center mb-2">
                                <h4 class="text-base font-semibold text-gray-700">Adendos</h4>
                                <button id="toggle-adendos-view-last-handover" class="toggle-adendos-view-btn adendo-toggle-icon-btn hidden" data-state="last" title="Ver todos os adendos">
                                    <svg class="h-5 w-5 icon-expand" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="m4.5 15.75 7.5-7.5 7.5 7.5" />
                                    </svg>
                                    <svg class="h-5 w-5 icon-collapse hidden" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="m19.5 8.25-7.5 7.5-7.5-7.5" />
                                    </svg>
                                </button>
                            </div>
                            <div id="adendos-list-last-handover" class="adendos-list">
                                </div>
                        </div>

                        <div id="inline-adendo-form-wrapper-last-handover" class="inline-adendo-form-wrapper mt-2 hidden">
                            <textarea id="adendo-text-last-handover" rows="2" class="w-full rounded-md border-gray-300 shadow-sm text-sm" placeholder="Digite seu adendo..."></textarea>
                            <div class="flex justify-end space-x-2 mt-2">
                                <button type="button" class="cancel-adendo-btn px-3 py-1 text-sm font-semibold text-gray-700 rounded-md hover:bg-gray-100">Cancelar</button>
                                <button type="button" class="save-adendo-btn rounded-md bg-blue-600 px-4 py-1 text-sm font-semibold text-white shadow-sm hover:bg-blue-700">Salvar Adendo</button>
                            </div>
                        </div>
                        
                        <div id="add-adendo-trigger-wrapper-last-handover" class="add-adendo-trigger-wrapper mt-2">
                            <button type="button" class="add-adendo-trigger-btn rounded-md bg-gray-200 hover:bg-gray-300 text-gray-800 font-semibold px-3 py-1 flex items-center space-x-2 text-sm">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd" /></svg>
                                <span>Adicionar Adendo</span>
                            </button>
                        </div>
                    </div>
                </div>
            </div>

         <!-- Toast de Notificação -->
        <div id="toast" class="hidden fixed bottom-5 right-5 bg-gray-900 text-white py-3 px-6 rounded-lg shadow-lg transition-opacity duration-300">
            <p id="toast-message"></p>
        </div>
    </div>
    <div id="print-view"></div>

    <div id="unit-summary-modal" class="hidden fixed inset-0 bg-gray-900 bg-opacity-75 overflow-y-auto h-full w-full z-50">
        <div class="relative top-5 mx-auto p-5 border w-full max-w-7xl shadow-lg rounded-md bg-white">
            <div class="flex flex-col sm:flex-row sm:justify-between sm:items-center pb-3 border-b gap-y-2">
                <div class="w-full flex items-center justify-between order-1 sm:w-auto sm:order-2 space-x-2">
                    <button id="print-unit-summary-button" title="Imprimir Resumo da Unidade" class="rounded-md bg-gray-100 p-2 text-sm font-semibold text-gray-700 shadow-sm hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5"><polyline points="6 9 6 2 18 2 18 9"/><path d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2"/><rect x="6" y="14" width="12" height="8"/></svg>
                    </button>
                    <button id="close-unit-summary-modal-button" class="cursor-pointer z-50 p-2 rounded-full hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="h-6 w-6 text-gray-600"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                    </button>
                </div>
                <div class="w-full order-2 sm:order-1">
                    <p id="unit-summary-title" class="text-lg sm:text-2xl font-bold text-gray-800">Resumo Geral da Unidade</p>
                    <p id="unit-summary-subtitle" class="text-sm text-gray-600">Dados atualizados em tempo real</p>
                </div>
            </div>
            
            <div id="unit-summary-content" class="mt-4 max-h-[80vh] overflow-y-auto pr-2">
                
                <div id="unit-summary-loader" class="text-center p-10">
                    <svg class="animate-spin h-8 w-8 text-blue-600 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                    <p class="mt-3 text-gray-600">Analisando dados da unidade...</p>
                </div>

                <div id="unit-summary-main-container" class="hidden space-y-6">
                    <div>
                        <h3 class="text-base font-semibold text-gray-700 mb-2">Visão Geral da Unidade</h3>
                        <div id="unit-kpi-panel" class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4">
                            </div>
                    </div>

                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 pt-4 border-t">
                        <div>
                            <h3 class="text-base font-semibold text-gray-700 mb-2 text-center">Distribuição por Complexidade (Fugulin)</h3>
                            <div class="bg-gray-50 p-2 rounded-lg h-64 flex justify-center items-center">
                                <canvas id="fugulin-distribution-chart"></canvas>
                            </div>
                        </div>
                        <div>
                            <h3 class="text-base font-semibold text-gray-700 mb-2 text-center">5 Principais Medicações (Últimos 7 dias)</h3>
                            <div class="bg-gray-50 p-2 rounded-lg h-64 flex justify-center items-center">
                                <canvas id="medication-chart"></canvas>
                            </div>
                        </div>
                        <div class="lg:col-span-2">
                            <h3 class="text-base font-semibold text-gray-700 mb-2 text-center">Tendência Semanal: Complexidade (Fugulin) vs. Gravidade (NEWS)</h3>
                            <div class="bg-gray-50 p-2 rounded-lg h-72">
                                <canvas id="trends-chart-unit"></canvas>
                            </div>
                        </div>
                        <div class="lg:col-span-2">
                            <h3 class="text-base font-semibold text-gray-700 mb-2 text-center">Fluxo de Pacientes (Admissões vs. Saídas)</h3>
                            <div class="bg-gray-50 p-2 rounded-lg h-72">
                                <canvas id="flow-chart-unit"></canvas>
                            </div>
                        </div>
                    </div>

                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 pt-4 border-t">
                        <div>
                            <h3 class="text-base font-semibold text-gray-700 mb-2">5 Pacientes com Maior Risco de Deterioração</h3>
                            <div class="overflow-x-auto">
                                <table class="min-w-full bg-white border text-sm">
                                    <thead class="bg-gray-100">
                                        <tr>
                                            <th class="text-left font-semibold text-gray-600 p-2">Leito</th>
                                            <th class="text-left font-semibold text-gray-600 p-2">Paciente</th>
                                            <th class="text-left font-semibold text-gray-600 p-2">NEWS</th>
                                            <th class="text-left font-semibold text-gray-600 p-2">Tend.</th>
                                            <th class="text-left font-semibold text-gray-600 p-2">Último Profissional</th>
                                        </tr>
                                    </thead>
                                    <tbody id="high-risk-table-body">
                                        </tbody>
                                </table>
                            </div>
                        </div>
                        <div>
                            <h3 class="text-base font-semibold text-gray-700 mb-2">Diagnósticos Mais Frequentes (Últimos 7 dias)</h3>
                            <div class="overflow-x-auto">
                                <table class="min-w-full bg-white border text-sm">
                                    <thead class="bg-gray-100">
                                        <tr>
                                            <th class="text-left font-semibold text-gray-600 p-2">Diagnóstico</th>
                                            <th class="text-left font-semibold text-gray-600 p-2">Nº Pac.</th>
                                            <th class="text-left font-semibold text-gray-600 p-2">% Total</th>
                                        </tr>
                                    </thead>
                                    <tbody id="diagnosis-frequency-table-body">
                                        </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    
    <!-- Script Principal da Aplicação com Firebase -->
    <script type="module">
        // Importações do Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { 
            getAuth, 
            createUserWithEmailAndPassword, 
            signInWithEmailAndPassword, 
            signOut, 
            onAuthStateChanged,
            updateProfile
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { 
            getFirestore, 
            collection, 
            addDoc, 
            doc, 
            getDoc, 
            getDocs, 
            updateDoc, 
            deleteDoc, 
            query, 
            where, 
            onSnapshot, 
            orderBy, 
            serverTimestamp, 
            writeBatch, 
            Timestamp,
            arrayUnion,
            enableIndexedDbPersistence,
            limit,
            startAfter,
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { 
            getDatabase, 
            ref, 
            onValue 
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-database.js";

        // Configuração do Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyBYwizxU0ZH7Wgzq7zzbDUpr6U9SiTTrZw",
            authDomain: "passa-plantao-app.firebaseapp.com",
            projectId: "passa-plantao-app",
            storageBucket: "passa-plantao-app.firebasestorage.app",
            messagingSenderId: "136407748271",
            appId: "1:136407748271:web:9725b877e81eeb20100e5a"
        };

        // Inicialização do Firebase
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        window.db = db;


        // --- NOVO BLOCO DE CÓDIGO PARA MONITORAMENTO PRECISO DA CONEXÃO ---

        // Inicializa o Realtime Database
        const database = getDatabase(app);
        // Cria uma referência para o caminho especial ".info/connected"
        const connectedRef = ref(database, '.info/connected');

        // Adiciona um listener que dispara sempre que o status de conexão do Firebase muda
        onValue(connectedRef, (snap) => {
            // snap.val() retornará true se conectado, false se desconectado
            if (snap.val() === true) {
                console.log("Firebase Realtime Database: Conectado.");
                updateConnectionStatus('online');
            } else {
                // Se o Firebase diz que está offline, verificamos se o navegador concorda
                // Isso evita falsos negativos durante a inicialização
                if (navigator.onLine) {
                    console.log("Firebase Realtime Database: Desconectado, mas navegador está online. Reconectando...");
                    updateConnectionStatus('connecting');
                } else {
                    console.log("Firebase Realtime Database: Desconectado.");
                    updateConnectionStatus('offline');
                }
            }
        });

        // Tenta ativar a persistência. Isso armazena os dados localmente.
        // A ativação da persistência é envolvida em uma função assíncrona auto-executável
        // para que ela não bloqueie a renderização inicial da página.
        // Isso corrige o problema de carregamento infinito em navegadores como o Safari.
        (async () => {
            try {
                await enableIndexedDbPersistence(db);
                console.log("Persistência offline do Firestore ativada com sucesso!");
            } catch (err) {
                if (err.code == 'failed-precondition') {
                    console.warn("Falha ao ativar persistência: Múltiplas abas abertas podem causar este problema.");
                } else if (err.code == 'unimplemented') {
                    console.warn("Persistência offline não suportada neste navegador.");
                }
            }
        })(); // A função é chamada imediatamente, mas o script principal não espera por sua conclusão.

        // --- VARIÁVEIS GLOBAIS E ESTADO DA APLICAÇÃO ---
        let currentUser = null;
        let currentPatientId = null;
        let currentScreen = 'loading';
        let unsubscribePatients = null;
        let unsubscribeHandovers = null;
        let currentHistoryPage = 1;
        // ADICIONADAS:
        const PATIENTS_PER_PAGE = 9; // Carrega 9 por vez (bom para grids de 3 colunas)
        let lastVisiblePatientDoc = null; // Guarda o último documento para a paginação
        let isLoadingPatients = false;    // Impede carregamentos múltiplos
        let allPatientsLoaded = false;    // Indica se todos os pacientes já foram carregados
        let debounceTimer;
        let originalPatientState = { allergies: [] };
        let currentMedications = [];
        let resolvedPendingExams = {};
        let currentHandovers = [];
        let currentPatientData = {};
        let currentlyViewedHandover = null;
        let currentViewMode = 'grid'; // 'grid' ou 'list'
        let currentExamsDone = [];
        let hasUnsavedChanges = false;
        let patientDetailListenersAttached = false;
        let dispositivosListenersAttached = false;
        let currentCustomDevices = [];
        let originalPatientDevices = []; // Para calcular o delta ao salvar
        let activeEditingModule = null;
        let activeSelectorInfo = null;
        let flatpickrInstance = null;
        let currentShiftCompletedExams = [];
        let currentShiftRescheduledExams = [];
        let currentPatientList = [];
        let devicesAddedThisSession = []; 
        let unsubscribeNotifications = null; // Para o listener de notificações
        let allNotifications = [];           // Para guardar a lista de notificações
        let weeklySummaryChart = null;
        let fugulinChart = null;
        let medicationChart = null;
        let unitTrendsChart = null;
        let unitFlowChart = null;
        let currentUnitSummaryData = null;

        // Modal de Última Passagem de Plantão
        const showLastHandoverButton = document.getElementById('show-last-handover-button');
        const lastHandoverModal = document.getElementById('last-handover-modal');
        const closeLastHandoverModalBtn = document.getElementById('close-last-handover-modal');
        const lastHandoverContent = document.getElementById('last-handover-content');

        // Mapeamento para os tooltips dos dispositivos
        const deviceTooltips = {
            'AVP MSE': 'Acesso Venoso Periférico em Membro Superior Esquerdo',
            'AVP MSD': 'Acesso Venoso Periférico em Membro Superior Direito',
            'PICC': 'Cateter Central de Inserção Periférica',
            'CVC': 'Cateter Venoso Central',
            'CDL': 'Cateter de Duplo Lúmen para Hemodiálise',
            'SNE': 'Sonda Nasoenteral',
            'GTT': 'Gastrostomia',
            'SVD': 'Sonda Vesical de Demora',
            'Monitor': 'Monitorização Cardíaca Contínua'
        };

        // Ordem de severidade para a legenda do gráfico Fugulin
        const FUGULIN_CLASSIFICATION_ORDER = [
            'Cuidados Mínimos',
            'Cuidados Intermediários',
            'Cuidados de Alta Dependência',
            'Cuidados Semi-Intensivos',
            'Cuidados Intensivos',
            'Não Classificado'
        ];

        // Mapeamento de classificação Fugulin para as cores do gráfico, garantindo consistência.
        const FUGULIN_CHART_COLORS = {
            'Cuidados Mínimos': '#dcfce7',            // Verde (bg-green-100)
            'Cuidados Intermediários': '#fef9c3',     // Amarelo (bg-yellow-100)
            'Cuidados de Alta Dependência': '#ffedd5',// Laranja Claro (bg-orange-100)
            'Cuidados Semi-Intensivos': '#fee2e2',     // Vermelho Claro (bg-red-100)
            'Cuidados Intensivos': '#fecaca',          // Vermelho Escuro (bg-red-200)
            'Não Classificado': '#dbeafe'              // Azul (bg-blue-100)
        };

        const FUGULIN_CHART_BORDERS = {
            'Cuidados Mínimos': '#166534',            // text-green-800
            'Cuidados Intermediários': '#854d0e',     // text-yellow-800
            'Cuidados de Alta Dependência': '#9a3412',// text-orange-800
            'Cuidados Semi-Intensivos': '#960e0e',     // text-red-700
            'Cuidados Intensivos': '#510909',          // text-red-900
            'Não Classificado': '#1e40af'              // text-blue-800
        };

        // Objeto com as opções padronizadas para cada tipo de cuidado
        const fugulinOptions = {
            cuidadoCorporal: [
                { text: 'Autossuficiente', value: '1' },
                { text: 'Ajuda no banho / em partes do corpo', value: '2' },
                { text: 'Banho no leito, higiene oral', value: '3' },
                { text: 'Incontinente, com lesões, curativos complexos', value: '4' }
            ],
            motilidade: [
                { text: 'Ativo, movimenta-se sozinho', value: '1' },
                { text: 'Requer mudança de decúbito programada', value: '2' },
                { text: 'Necessita de ajuda para se movimentar', value: '3' },
                { text: 'Totalmente restrito ao leito', value: '4' }
            ],
            deambulacao: [
                { text: 'Deambula sozinho, sem ajuda', value: '1' },
                { text: 'Requer auxílio para deambular', value: '2' },
                { text: 'Ajuda para transferência (leito-cadeira)', value: '3' },
                { text: 'Totalmente acamado', value: '4' }
            ],
            alimentacao: [
                { text: 'Alimenta-se sozinho', value: '1' },
                { text: 'Requer ajuda parcial / estímulo', value: '2' },
                { text: 'Alimentação por sonda (SNE/GTT)', value: '3' },
                { text: 'Nutrição Parenteral Total (NPT)', value: '4' }
            ],
            eliminacao: [
                { text: 'Independente, controle esfincteriano', value: '1' },
                { text: 'Uso de comadre / auxílio no banheiro', value: '2' },
                { text: 'Sonda Vesical de Demora (SVD)', value: '3' },
                { text: 'Incontinência, evacuação no leito, ostomias', value: '4' }
            ]
        };

        // Objeto com as opções padronizadas para cada tipo de risco
        const riskOptions = {
            lpp: [
                "Sem Risco Aparente",
                "Risco Baixo (Braden 15-18)",
                "Risco Moderado (Braden 13-14)",
                "Risco Alto (Braden 10-12)",
                "Risco Muito Alto (Braden ≤9)"
            ],
            quedas: [
                "Sem Risco Aparente",
                "Risco Baixo (Morse 0-24)",
                "Risco Médio (Morse 25-44)",
                "Risco Alto (Morse ≥45)"
            ],
            bronco: [
                "Sem Risco Aparente",
                "Risco Baixo (Alerta, deambula, deglute bem)",
                "Risco Moderado (Sonolento, disfagia leve, tosse)",
                "Risco Alto (SNG/GTT, rebaixamento de consciência)"
            ],
            iras: [
                "Sem Fatores de Risco",
                "Uso de Dispositivo Invasivo (AVP, CVC, SVD, etc.)",
                "Paciente Imunossuprimido",
                "Colonização por MRO (Bactéria Multirresistente)",
                "Sítio Cirúrgico / Ferida Operatória"
            ]
        };

        // Novo objeto de opções
        const monitoringOptions = {
            consciencia: [
                { text: 'Alerta (A)', value: 'A' },
                { text: 'Voz (V)', value: 'V' },
                { text: 'Dor (P)', value: 'P' },
                { text: 'Não Responde (U)', value: 'U' }
            ]
        };

        // --- CONFIGURAÇÕES DO FLATPICKR ---

        // Configuração para o SELETOR DE TEMPO de medicações
        const configTimePicker = {
            enableTime: true,    // Habilita a seleção de tempo
            noCalendar: true,    // Esconde o calendário, mostrando apenas o relógio
            dateFormat: "H:i",   // Formato de 24h para o relógio
            time_24hr: true,
            minuteIncrement: 5,  // Incrementos de 5 em 5 minutos
            locale: "pt",
            allowInput: true // Permite a digitação manual
        };

        // Configuração para AGENDAR (não permite datas passadas)
        const configAgendamento = {
            enableTime: true,
            dateFormat: "d/m/Y H:i", // Formato brasileiro
            time_24hr: true,
            defaultDate: new Date(), // Padrão: data e hora atuais
            minDate: "today", // Não permite selecionar datas/horas passadas
            minuteIncrement: 30, // Incremento dos minutos de 30 em 30
            locale: "pt", // (Opcional, mas traduz o calendário - requer um script extra)
            allowInput: true, // Permite a digitação manual
            onKeyDown: (selectedDates, dateStr, instance, e) => {
                if (e.key === 'Enter') {
                    // Impede que o "Enter" se propague e acione outros listeners (como o de salvar)
                    e.stopPropagation();
                    e.preventDefault();
                    // Força o flatpickr a analisar e definir a data a partir do que foi digitado
                    instance.setDate(dateStr, true);
                }
            }
        };

        // Configuração para REGISTRAR (não permite datas futuras)
        const configRegistro = {
            enableTime: true,
            dateFormat: "d/m/Y H:i",
            time_24hr: true,
            defaultDate: new Date(),
            maxDate: new Date(), // Não permite selecionar datas/horas futuras
            minuteIncrement: 30,
            locale: "pt",
            allowInput: true, // Permite a digitação manual
            onKeyDown: (selectedDates, dateStr, instance, e) => {
                if (e.key === 'Enter') {
                    e.stopPropagation();
                    e.preventDefault();
                    instance.setDate(dateStr, true);
                }
            }
        };

        // Para data de nascimento
        const configDatePickerNascimento = {
            dateFormat: "Y-m-d",    // Formato que o banco de dados entende
            altInput: true,         // Mostra um formato amigável para o usuário
            altFormat: "d/m/Y",     // Formato brasileiro amigável
            locale: "pt",           // Usa a tradução para português
            maxDate: "today",       // Impede a seleção de datas futuras
            // Define uma data inicial padrão mais realista (30 anos atrás)
            defaultDate: new Date().setFullYear(new Date().getFullYear() - 30),
            allowInput: true // Permite a digitação manual
        };

        // --- ELEMENTOS DA UI ---

        // Modal de Dispositivos
        const dispositivosGrid = document.getElementById('dispositivos-grid');
        const dispositivoOutrosChk = document.getElementById('dispositivo-outros-chk');
        const outrosDispositivosInputWrapper = document.getElementById('outros-dispositivos-input-wrapper');
        const dispositivoOutrosInput = document.getElementById('dispositivo-outros-input');
        const customDispositivosContainer = document.getElementById('custom-dispositivos-container');
        const addCustomDispositivoBtn = document.getElementById('add-custom-dispositivo-btn');

        // Modal de Histórico de Módulo
        const moduleHistoryModal = document.getElementById('module-history-modal');
        const moduleHistoryTitle = document.getElementById('module-history-title');
        const moduleHistoryContent = document.getElementById('module-history-content');
        const closeModuleHistoryModalBtn = document.getElementById('close-module-history-modal');

        // Modal de Histórico Completo
        const fullHistoryModal = document.getElementById('full-history-modal');
        const closeFullHistoryModalBtn = document.getElementById('close-full-history-modal');
        const fullHistoryContentWrapper = document.getElementById('full-history-content-wrapper'); // Usaremos para os eventos

        // Modal de Exames Realizados
        const addDoneExamBtn = document.getElementById('add-done-exam-btn');
        const formExamDoneName = document.getElementById('form-exam-done-name');
        const formExamDoneResult = document.getElementById('form-exam-done-result');

        // Modal de Visualização de Plantão
        const viewHandoverModal = document.getElementById('view-handover-modal');
        const viewHandoverTitle = document.getElementById('view-handover-title');
        const viewHandoverContent = document.getElementById('view-handover-content');
        const closeViewHandoverModalBtn = document.getElementById('close-view-handover-modal');
        const patientSummaryModal = document.getElementById('patient-summary-modal');
        const showSummaryButton = document.getElementById('show-summary-button');
        const closeSummaryModalButton = document.getElementById('close-summary-modal-button');
        const patientSummaryContent = document.getElementById('patient-summary-content');

        // Modal de Resumo da Unidade
        const showUnitSummaryButton = document.getElementById('show-unit-summary-button');
        const unitSummaryModal = document.getElementById('unit-summary-modal');
        const closeUnitSummaryModalButton = document.getElementById('close-unit-summary-modal-button');
        const printUnitSummaryButton = document.getElementById('print-unit-summary-button');


        // Seletores de Notificações
        const notificationBellBtn = document.getElementById('notification-bell-btn');
        const notificationPanel = document.getElementById('notification-panel');
        const notificationList = document.getElementById('notification-list');
        const notificationIndicator = document.getElementById('notification-indicator');

        const examsDoneTextarea = document.getElementById('form-exams-done'); // MANTENHA ESTA LINHA POR ENQUANTO, VAMOS REMOVÊ-LA DEPOIS

        const screens = {
            loading: document.getElementById('loading-screen'),
            login: document.getElementById('login-screen'),
            register: document.getElementById('register-screen'),
            main: document.getElementById('main-content'),
            patientDetail: document.getElementById('patient-detail-screen')
        };

        // Autenticação
        const loginForm = document.getElementById('login-form');
        const registerForm = document.getElementById('register-form');
        const logoutButton = document.getElementById('logout-button');
        const userInfo = document.getElementById('user-info');

        // Painel de Pacientes (Dashboard)
        const patientList = document.getElementById('patient-list');
        const searchPatientInput = document.getElementById('search-patient');

        // Modais de Adicionar/Editar Paciente
        const addPatientModal = document.getElementById('add-patient-modal');
        const addPatientButton = document.getElementById('add-patient-button');
        const closeModalButton = document.getElementById('close-modal-button');
        const addPatientForm = document.getElementById('add-patient-form');
        const editPatientModal = document.getElementById('edit-patient-modal');
        const closeEditModalButton = document.getElementById('close-edit-modal-button');
        const editPatientForm = document.getElementById('edit-patient-form');
        const editPatientDetailsButton = document.getElementById('edit-patient-details-button');

        // Modal de Exclusão
        const deleteConfirmModal = document.getElementById('delete-confirm-modal');
        const confirmDeleteButton = document.getElementById('confirm-delete-button');
        const cancelDeleteButton = document.getElementById('cancel-delete-button');
        const cancelExamConfirmModal = document.getElementById('cancel-exam-confirm-modal');
        const confirmCancelExamButton = document.getElementById('confirm-cancel-exam-button');
        const cancelCancelExamButton = document.getElementById('cancel-cancel-exam-button');

        // Botões de Voltar dos Modais
        const backToHistoryListBtn = document.getElementById('back-to-history-list-btn');
        const backToLastHandoverBtn = document.getElementById('back-to-last-handover-btn');

        // Detalhes do Paciente e Formulário de Plantão
        const backToDashboardButton = document.getElementById('back-to-dashboard');
        const deletePatientButton = document.getElementById('delete-patient-button');
        const patientDetailName = document.getElementById('patient-detail-name');
        const patientDetailNumber = document.getElementById('patient-detail-number');
        const patientDetailRoom = document.getElementById('patient-detail-room');
        const patientDetailAge = document.getElementById('patient-detail-age');
        const handoversList = document.getElementById('handovers-list');
        const addHandoversForm = document.getElementById('add-handovers-form');

        // Filtro de Leitos
        const bedFilterContainer = document.getElementById('bed-filter-container');
        const bedFilterButton = document.getElementById('bed-filter-button');
        const bedFilterButtonText = document.getElementById('bed-filter-button-text');
        const bedFilterDropdown = document.getElementById('bed-filter-dropdown');
        const bedSearchInput = document.getElementById('bed-search-input');
        const bedFilterList = document.getElementById('bed-filter-list');
        const bedFilterClearButton = document.getElementById('bed-filter-clear-button');
        const bedFilterClearWrapper = document.getElementById('bed-filter-clear-wrapper');
        const bedFilterArrowIcon = document.getElementById('bed-filter-arrow-icon');

        // Campos do Formulário Avançado
        const diagnosisInput = document.getElementById('form-diagnosis');
        const diagnosisAutocompleteContainer = document.getElementById('diagnosis-autocomplete-container');
        const diagnosisAutocompleteList = document.getElementById('diagnosis-autocomplete-list');
        const medicationInput = document.getElementById('form-medications');
        const medicationAutocompleteContainer = document.getElementById('medication-autocomplete-container');
        const medicationAutocompleteList = document.getElementById('medication-autocomplete-list');
        const diagnosesTagsContainer = document.getElementById('diagnoses-tags-container');
        const comorbiditiesTagsContainer = document.getElementById('comorbidities-tags-container');
        const scheduledExamsTagsContainer = document.getElementById('scheduled-exams-tags-container');
        const pendingExamsTagsContainer = document.getElementById('pending-exams-tags-container');
        const addScheduledExamBtn = document.getElementById('add-scheduled-exam-btn');
        const medicationsListContainer = document.getElementById('medications-list-container');
        const recentMedsList = document.getElementById('recent-meds-list');

        // Visualização
        const viewToggleButton = document.getElementById('view-toggle-button');
        const viewToggleDropdown = document.getElementById('view-toggle-dropdown');
        const viewToggleIconGrid = document.getElementById('view-toggle-icon-grid');
        const viewToggleIconList = document.getElementById('view-toggle-icon-list');
        const noPatientsMessage = document.getElementById('no-patients-message'); // Mova ou adicione aqui para garantir que está definido

        // Área Extra de Medicações
        const medicationActionArea = document.getElementById('medication-action-area');
        const medicationEditorArea = document.getElementById('medication-editor-area');
        const medicationSearchEditor = document.getElementById('medication-search-editor');
        const medicationTimeEditor = document.getElementById('medication-time-editor');
        const cancelEditorBtn = document.getElementById('cancel-editor-btn');

        // Seletores dos botões de impressão
        const printLastHandoverButton = document.getElementById('print-last-handover-button');
        const printHandoverDetailButton = document.getElementById('print-handover-detail-button');

        /**
         * Gerencia a visibilidade dos diferentes modos do editor de medicação.
         * @param {'search' | 'time' | false} mode - O modo a ser exibido ou false para esconder.
         */
        function showMedicationEditor(mode) {
            if (!mode) {
                medicationEditorArea.classList.add('hidden');
                medicationActionArea.classList.remove('hidden');
                medicationSearchEditor.classList.add('hidden');
                medicationTimeEditor.classList.add('hidden');
                medicationInput.value = '';
            } else {
                medicationActionArea.classList.add('hidden');
                medicationEditorArea.classList.remove('hidden');

                if (mode === 'search') {
                    medicationTimeEditor.classList.add('hidden');
                    medicationSearchEditor.classList.remove('hidden');
                    medicationInput.focus();
                } else if (mode === 'time') {
                    medicationSearchEditor.classList.add('hidden');
                    medicationTimeEditor.classList.remove('hidden');
                }
            }
        }

        /**
         * FUNÇÃO DE PROCESSAMENTO
         * Itera sobre os registros de handover da semana e calcula os KPIs.
         * @param {Array} handovers - Array de objetos de handover da última semana.
         * @returns {object} - Um objeto contendo todos os KPIs calculados.
         */
        function processKPIs(handovers) {
            // A estrutura do objeto kpis foi alterada para armazenar valor E metadados
            const kpis = {
                maxFC: { value: -Infinity, timestamp: null },
                minPAS: { value: Infinity, timestamp: null },
                maxTemp: { value: -Infinity, timestamp: null },
                minSatO2: { value: Infinity, timestamp: null },
                maxFR: { value: -Infinity, timestamp: null },
                avgFC: { value: null, count: 0 },
                avgPAS: { value: null, count: 0 },
                maxNEWS2: { value: -Infinity, timestamp: null },
                maxFugulin: { value: -Infinity, timestamp: null },
                hypoglycemiaEpisodes: { count: 0, timestamps: [] },
                feverEpisodes: { count: 0, timestamps: [] },
                sosMedCount: { count: 0, timestamps: [] }
            };

            // Variáveis internas para cálculo de média
            let sumFC = 0, countFC = 0, sumPAS = 0, countPAS = 0;
            const sosMeds = ['dipirona', 'morfina', 'tramadol', 'ondansetrona', 'bromoprida'];

            handovers.forEach(h => {
                const timestamp = h.timestamp?.toDate ? h.timestamp.toDate() : new Date();

                // 1. Processa dados de Monitoramento
                if (h.monitoring) {
                    const mon = h.monitoring;

                    const fc = parseInt(mon.fc, 10);
                    if (!isNaN(fc)) {
                        if (fc > kpis.maxFC.value) {
                            kpis.maxFC.value = fc;
                            kpis.maxFC.timestamp = timestamp;
                        }
                        sumFC += fc;
                        countFC++;
                    }

                    const fr = parseInt(mon.fr, 10);
                    if (!isNaN(fr) && fr > kpis.maxFR.value) {
                        kpis.maxFR.value = fr;
                        kpis.maxFR.timestamp = timestamp;
                    }

                    const pa = parseInt((mon.pa || '').split('/')[0], 10);
                    if (!isNaN(pa)) {
                        if (pa < kpis.minPAS.value) {
                            kpis.minPAS.value = pa;
                            kpis.minPAS.timestamp = timestamp;
                        }
                        sumPAS += pa;
                        countPAS++;
                    }

                    const temp = parseFloat((mon.temp || '').replace(',', '.'));
                    if (!isNaN(temp)) {
                        if (temp > kpis.maxTemp.value) {
                            kpis.maxTemp.value = temp;
                            kpis.maxTemp.timestamp = timestamp;
                        }
                        if (temp > 37.8) {
                            kpis.feverEpisodes.timestamps.push(timestamp);
                        }
                    }
                    
                    const satO2 = parseInt(mon.sato2, 10);
                    if (!isNaN(satO2) && satO2 < kpis.minSatO2.value) {
                        kpis.minSatO2.value = satO2;
                        kpis.minSatO2.timestamp = timestamp;
                    }

                    const hgt = parseInt(mon.hgt, 10);
                    if (!isNaN(hgt) && hgt < 70) {
                        kpis.hypoglycemiaEpisodes.timestamps.push(timestamp);
                    }
                }

                // 2. Processa Scores
                if (h.news2?.score !== undefined && h.news2.score > kpis.maxNEWS2.value) {
                    kpis.maxNEWS2.value = h.news2.score;
                    kpis.maxNEWS2.timestamp = timestamp;
                }
                if (h.fugulin?.score !== undefined && h.fugulin.score > kpis.maxFugulin.value) {
                    kpis.maxFugulin.value = h.fugulin.score;
                    kpis.maxFugulin.timestamp = timestamp;
                }

                // 3. Processa Medicações SOS
                if (h.medications && Array.isArray(h.medications)) {
                    h.medications.forEach(med => {
                        const medNameLower = med.name.toLowerCase();
                        if (sosMeds.some(sosMed => medNameLower.includes(sosMed))) {
                            kpis.sosMedCount.timestamps.push(...med.times.map(t => new Date(t)));
                        }
                    });
                }
            });

            // Calcula médias e contagens finais
            kpis.avgFC.value = countFC > 0 ? Math.round(sumFC / countFC) : null;
            kpis.avgFC.count = countFC;
            kpis.avgPAS.value = countPAS > 0 ? Math.round(sumPAS / countPAS) : null;
            kpis.avgPAS.count = countPAS;
            kpis.hypoglycemiaEpisodes.count = kpis.hypoglycemiaEpisodes.timestamps.length;
            kpis.feverEpisodes.count = kpis.feverEpisodes.timestamps.length;
            kpis.sosMedCount.count = kpis.sosMedCount.timestamps.length;

            // Limpa valores iniciais
            if (kpis.maxFC.value === -Infinity) kpis.maxFC.value = null;
            if (kpis.minPAS.value === Infinity) kpis.minPAS.value = null;
            if (kpis.maxTemp.value === -Infinity) kpis.maxTemp.value = null;
            if (kpis.minSatO2.value === Infinity) kpis.minSatO2.value = null;
            if (kpis.maxFR.value === -Infinity) kpis.maxFR.value = null;
            if (kpis.maxNEWS2.value === -Infinity) kpis.maxNEWS2.value = null;
            if (kpis.maxFugulin.value === -Infinity) kpis.maxFugulin.value = null;

            return kpis;
        }


        /**
        * FUNÇÃO DE RENDERIZAÇÃO
        * Pega os KPIs processados e renderiza os cards de destaque no DOM.
        * @param {object} kpis - O objeto retornado pela função processKPIs.
        */
        function renderKPIs(kpis) {
            const kpiPanel = document.getElementById('kpi-panel');
            if (!kpiPanel) return;

            // Helper para formatar data/hora para os tooltips
            const formatTooltipDate = (date) => {
                if (!date) return '';
                return date.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', hour: '2-digit', minute: '2-digit' });
            };

            // Helper para criar um card individual, agora com um parâmetro para o tooltip
            const createKpiCard = (icon, label, value, unit = '', tooltipText = '') => {
                const displayValue = (value !== null && !isNaN(value)) ? value : 'N/A';
                const displayUnit = (value !== null && !isNaN(value)) ? unit : '';
                const titleAttr = tooltipText ? `title="${tooltipText}"` : '';

                return `
                    <div class="bg-gray-50 border border-gray-200 rounded-lg p-3 text-center" ${titleAttr}>
                        <p class="text-xs text-gray-500 font-medium">${icon} ${label}</p>
                        <p class="text-xl font-bold text-gray-800 mt-1">
                            ${displayValue}
                            <span class="text-sm font-medium text-gray-600">${displayUnit}</span>
                        </p>
                    </div>
                `;
            };

            // Prepara o texto dos tooltips
            const maxFcTooltip = kpis.maxFC.timestamp ? `Ocorrido em: ${formatTooltipDate(kpis.maxFC.timestamp)}` : '';
            const minPasTooltip = kpis.minPAS.timestamp ? `Ocorrido em: ${formatTooltipDate(kpis.minPAS.timestamp)}` : '';
            const maxTempTooltip = kpis.maxTemp.timestamp ? `Ocorrido em: ${formatTooltipDate(kpis.maxTemp.timestamp)}` : '';
            const minSatO2Tooltip = kpis.minSatO2.timestamp ? `Ocorrido em: ${formatTooltipDate(kpis.minSatO2.timestamp)}` : '';
            const maxFrTooltip = kpis.maxFR.timestamp ? `Ocorrido em: ${formatTooltipDate(kpis.maxFR.timestamp)}` : '';
            const maxNews2Tooltip = kpis.maxNEWS2.timestamp ? `Ocorrido em: ${formatTooltipDate(kpis.maxNEWS2.timestamp)}` : '';
            const maxFugulinTooltip = kpis.maxFugulin.timestamp ? `Ocorrido em: ${formatTooltipDate(kpis.maxFugulin.timestamp)}` : '';

            const avgFcTooltip = `Calculado a partir de ${kpis.avgFC.count} registros`;
            const avgPasTooltip = `Calculado a partir de ${kpis.avgPAS.count} registros`;

            const hypoglycemiaTooltip = kpis.hypoglycemiaEpisodes.count > 0 ? 'Ocorrências:\n' + kpis.hypoglycemiaEpisodes.timestamps.map(ts => formatTooltipDate(ts)).join('\n') : '';
            const feverTooltip = kpis.feverEpisodes.count > 0 ? 'Ocorrências:\n' + kpis.feverEpisodes.timestamps.map(ts => formatTooltipDate(ts)).join('\n') : '';
            const sosMedTooltip = kpis.sosMedCount.count > 0 ? 'Ocorrências:\n' + kpis.sosMedCount.timestamps.map(ts => formatTooltipDate(ts)).join('\n') : '';

            // Gera o HTML para todos os cards, passando os dados e os tooltips
            kpiPanel.innerHTML = [
                createKpiCard('❤️', 'Pico de FC', kpis.maxFC.value, 'bpm', maxFcTooltip),
                createKpiCard('🩸', 'Menor PAS', kpis.minPAS.value, 'mmHg', minPasTooltip),
                createKpiCard('🌡️', 'Pico Febril', kpis.maxTemp.value, '°C', maxTempTooltip),
                createKpiCard('💨', 'Menor SatO₂', kpis.minSatO2.value, '%', minSatO2Tooltip),
                createKpiCard('🫁', 'Pico de FR', kpis.maxFR.value, 'irpm', maxFrTooltip),
                createKpiCard('📈', 'Maior NEWS', kpis.maxNEWS2.value, '', maxNews2Tooltip),
                createKpiCard('🩺', 'Maior Fugulin', kpis.maxFugulin.value, '', maxFugulinTooltip),
                createKpiCard('📉', 'Hipoglicemias', kpis.hypoglycemiaEpisodes.count, 'eventos', hypoglycemiaTooltip),
                createKpiCard('🔥', 'Episódios de Febre', kpis.feverEpisodes.count, 'eventos', feverTooltip),
                createKpiCard('💊', 'Medicações SOS', kpis.sosMedCount.count, 'doses', sosMedTooltip),
                createKpiCard('❤️', 'Média FC', kpis.avgFC.value, 'bpm', avgFcTooltip),
                createKpiCard('🩸', 'Média PAS', kpis.avgPAS.value, 'mmHg', avgPasTooltip)
            ].join('');
        }

        /**
         * Função Mestra: Busca todos os dados relevantes da unidade (pacientes e handovers dos últimos 7 dias),
         * processa e agrega tudo em um único objeto de resumo para ser usado pelas funções de renderização.
         * VERSÃO CORRIGIDA: Calcula as médias diárias com base em TODOS os pacientes ativos em cada dia.
         */
        async function generateUnitSummaryData() {
            try {
                // --- 1. BUSCA DE DADOS BRUTOS ---
                const activePatients = currentPatientList;
                
                const sevenDaysAgo = new Date();
                sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7);
                const sevenDaysAgoTimestamp = Timestamp.fromDate(sevenDaysAgo);
                const patientsRef = collection(db, 'patients');

                const archivedPatientsQuery = query(patientsRef, where('status', '==', 'arquivado'), where('archivedAt', '>=', sevenDaysAgoTimestamp));
                const archivedSnapshot = await getDocs(archivedPatientsQuery);
                const archivedPatientsLast7Days = archivedSnapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                
                const allRelevantPatients = [...activePatients, ...archivedPatientsLast7Days.filter(ap => !activePatients.some(p => p.id === ap.id))];

                if (allRelevantPatients.length === 0) {
                    return { isEmpty: true };
                }
                
                const admissionsInWeek = allRelevantPatients.filter(p => p.createdAt && p.createdAt.toDate() >= sevenDaysAgo).length;
                const dischargesInWeek = archivedPatientsLast7Days.length;

                const allHandoversLast7Days = [];
                for (const patient of allRelevantPatients) {
                    const handoversRef = collection(db, 'patients', patient.id, 'handovers');
                    const handoversQuery = query(handoversRef, where('timestamp', '>=', sevenDaysAgoTimestamp), orderBy('timestamp', 'desc'));
                    const handoversSnapshot = await getDocs(handoversQuery);
                    handoversSnapshot.docs.forEach(doc => {
                        allHandoversLast7Days.push({ patientId: patient.id, ...doc.data() });
                    });
                }

                // --- 2. CÁLCULO DOS KPIs ---
                const averageFugulin = activePatients.reduce((acc, p) => { 
                    if (p.lastFugulinScore) {
                        acc.sum += p.lastFugulinScore;
                        acc.count++;
                    }
                    return acc;
                }, { sum: 0, count: 0 });
                
                const kpis = {
                    activePatients: activePatients.length,
                    averageFugulin: averageFugulin.count > 0 ? (averageFugulin.sum / averageFugulin.count).toFixed(1) : 'N/A',
                    highRiskPatients: activePatients.filter(p => p.lastNews2Score >= 5).length, 
                    admissionsInWeek: admissionsInWeek,
                    dischargesInWeek: dischargesInWeek
                };

                // --- 3. PREPARAÇÃO DOS DADOS PARA OS GRÁFICOS ---
                
                // Gráficos de Pizza (Fugulin) e Barras (Medicações)
                const fugulinCounts = activePatients.reduce((acc, p) => {
                    const classification = p.lastFugulinClassification || 'Não Classificado';
                    acc[classification] = (acc[classification] || 0) + 1;
                    return acc;
                }, {});
                const sortedFugulinLabels = FUGULIN_CLASSIFICATION_ORDER.filter(label => fugulinCounts[label] !== undefined);
                const fugulinChartData = {
                    labels: sortedFugulinLabels,
                    data: sortedFugulinLabels.map(label => fugulinCounts[label])
                };

                const medicationCounts = allHandoversLast7Days.reduce((acc, h) => {
                    if (h.medications) {
                        h.medications.forEach(med => {
                            acc[med.name] = (acc[med.name] || 0) + med.times.length;
                        });
                    }
                    return acc;
                }, {});
                const top5Medications = Object.entries(medicationCounts).sort((a, b) => b[1] - a[1]).slice(0, 5);
                const medicationChartData = {
                    labels: top5Medications.map(item => item[0].length > 25 ? item[0].substring(0, 22) + '...' : item[0]),
                    fullLabels: top5Medications.map(item => item[0]),
                    data: top5Medications.map(item => item[1])
                };
                
                // --- GRÁFICOS DE TENDÊNCIA E FLUXO ---
                
                // Pré-processa os handovers para busca rápida
                const handoversByPatient = allHandoversLast7Days.reduce((acc, h) => {
                    if (!acc[h.patientId]) acc[h.patientId] = [];
                    acc[h.patientId].push(h);
                    return acc;
                }, {});

                const dailyMetrics = {};
                for (let i = 6; i >= 0; i--) {
                    const targetDate = new Date();
                    targetDate.setDate(targetDate.getDate() - i);
                    const key = targetDate.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit' });
                    targetDate.setHours(23, 59, 59, 999); // Final do dia para comparação

                    let fugulinSum = 0, fugulinCount = 0, newsSum = 0, newsCount = 0;

                    // Itera sobre todos os pacientes relevantes
                    for (const patient of allRelevantPatients) {
                        const createdAt = patient.createdAt.toDate();
                        const archivedAt = patient.archivedAt ? patient.archivedAt.toDate() : null;

                        // Verifica se o paciente estava ativo no dia em questão
                        const wasActiveOnTargetDay = createdAt <= targetDate && (!archivedAt || archivedAt > targetDate);

                        if (wasActiveOnTargetDay) {
                            // Encontra o último handover do paciente ATÉ aquele dia
                            const patientHandovers = handoversByPatient[patient.id] || [];
                            const lastHandoverUpToDate = patientHandovers.find(h => h.timestamp.toDate() <= targetDate);
                            
                            // Se encontrou um handover, usa os scores dele.
                            // Se não, o paciente estava ativo mas sem registro de score nesse período.
                            if (lastHandoverUpToDate) {
                                if (lastHandoverUpToDate.fugulin?.score) {
                                    fugulinSum += lastHandoverUpToDate.fugulin.score;
                                    fugulinCount++;
                                }
                                if (lastHandoverUpToDate.news2?.score) {
                                    newsSum += lastHandoverUpToDate.news2.score;
                                    newsCount++;
                                }
                            }
                        }
                    }

                    dailyMetrics[key] = {
                        fugulinAvg: fugulinCount > 0 ? (fugulinSum / fugulinCount) : null,
                        newsAvg: newsCount > 0 ? (newsSum / newsCount) : null,
                        admissions: 0,
                        discharges: 0
                    };
                }

                // Preenche os dados de admissão e alta no objeto já criado
                allRelevantPatients.forEach(p => { 
                    if (p.createdAt && p.createdAt.toDate() >= sevenDaysAgo) {
                        const key = p.createdAt.toDate().toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit' });
                        if(dailyMetrics[key]) dailyMetrics[key].admissions++;
                    }
                });
                
                archivedSnapshot.docs.forEach(doc => {
                    const p = doc.data();
                    if (p.archivedAt) {
                        const key = p.archivedAt.toDate().toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit' });
                        if(dailyMetrics[key]) dailyMetrics[key].discharges++;
                    }
                });
                
                const trendsChartData = {
                    labels: Object.keys(dailyMetrics),
                    fugulinData: Object.values(dailyMetrics).map(d => d.fugulinAvg),
                    newsData: Object.values(dailyMetrics).map(d => d.newsAvg)
                };
                
                const flowChartData = {
                    labels: Object.keys(dailyMetrics),
                    admissionsData: Object.values(dailyMetrics).map(d => d.admissions),
                    dischargesData: Object.values(dailyMetrics).map(d => d.discharges)
                };


                // --- 4. PREPARAÇÃO DOS DADOS PARA AS TABELAS ---
                const highRiskPatients = activePatients
                    .filter(p => p.lastNews2Score >= 5)
                    .sort((a, b) => (b.lastNews2Score || 0) - (a.lastNews2Score || 0))
                    .slice(0, 5)
                    .map(p => {
                        const patientHandovers = allHandoversLast7Days
                            .filter(h => h.patientId === p.id)
                            .sort((a, b) => b.timestamp.toDate() - a.timestamp.toDate());
                        
                        let trend = '―';
                        if (patientHandovers.length > 1) {
                            const latestScore = patientHandovers[0].news2?.score;
                            const previousScore = patientHandovers[1].news2?.score;
                            if (latestScore > previousScore) trend = '↑';
                            if (latestScore < previousScore) trend = '↓';
                        }
                        return { bed: p.roomNumber, name: p.name, score: p.lastNews2Score, trend: trend, professional: p.lastProfessionalName || 'N/A' };
                    });

                const diagnosisCounts = activePatients.reduce((acc, p) => {
                    if (p.activeDiagnoses) {
                        p.activeDiagnoses.forEach(diag => { acc[diag] = (acc[diag] || 0) + 1; });
                    }
                    return acc;
                }, {});
                const diagnosisFrequency = Object.entries(diagnosisCounts)
                    .sort((a, b) => b[1] - a[1])
                    .map(([diag, count]) => ({ diagnosis: diag, count: count, percentage: ((count / activePatients.length) * 100).toFixed(1) }));

                // --- 5. RETORNO DO OBJETO DE RESUMO COMPLETO ---
                return {
                    kpis, fugulinChartData, medicationChartData, trendsChartData,
                    flowChartData, highRiskPatients, diagnosisFrequency
                };

            } catch (error) {
                console.error("Erro ao gerar resumo da unidade:", error);
                showToast("Falha ao carregar os dados da unidade.", "error");
                return null;
            }
        }

        /**
         * Renderiza os cards de KPI (Indicadores Chave de Performance) no modal de resumo da unidade.
         * @param {object} kpis - O objeto contendo os dados dos KPIs.
         */
        function renderUnitKPIs(kpis) {
            const kpiPanel = document.getElementById('unit-kpi-panel');
            if (!kpiPanel) return;

            // Array com as definições de cada card para facilitar a renderização
            const kpiData = [
                {
                    label: 'Pacientes Ativos',
                    value: kpis.activePatients,
                    icon: '🛏️', // Ícone de cama
                    tooltip: 'Número total de pacientes com status "ativo" na unidade.'
                },
                {
                    label: 'Média de Complexidade',
                    value: kpis.averageFugulin,
                    icon: '🩺', // Ícone de estetoscópio
                    tooltip: 'Média do score Fugulin de todos os pacientes ativos. Indica a carga de trabalho da enfermagem.'
                },
                {
                    label: 'Pacientes em Alto Risco',
                    value: kpis.highRiskPatients,
                    icon: '⚠️', // Ícone de alerta
                    tooltip: 'Número de pacientes com score NEWS igual ou superior a 5.'
                },
                {
                    label: 'Admissões na Semana',
                    value: kpis.admissionsInWeek,
                    icon: '➡️', // Ícone de seta entrando
                    tooltip: 'Total de novos pacientes cadastrados nos últimos 7 dias.'
                },
                {
                    label: 'Saídas na Semana',
                    value: kpis.dischargesInWeek,
                    icon: '⬅️', // Ícone de seta saindo
                    tooltip: 'Total de pacientes arquivados (considerados como saída) nos últimos 7 dias.'
                }
            ];

            // Gera o HTML para cada card e insere no painel
            kpiPanel.innerHTML = kpiData.map(kpi => `
                <div class="bg-gray-50 border border-gray-200 rounded-lg p-4 text-center shadow-sm" title="${kpi.tooltip}">
                    <p class="text-sm text-gray-600 font-medium flex items-center justify-center">
                        <span class="text-xl mr-2">${kpi.icon}</span>
                        ${kpi.label}
                    </p>
                    <p class="text-4xl font-bold text-indigo-600 mt-2">
                        ${kpi.value}
                    </p>
                </div>
            `).join('');
        }

        /**
         * Renderiza o gráfico de pizza com a distribuição de pacientes por classificação Fugulin.
         */
        function renderFugulinChart(chartData) {
            const ctx = document.getElementById('fugulin-distribution-chart')?.getContext('2d');
            if (!ctx) return;

            if (fugulinChart) {
                fugulinChart.destroy();
            }
            
            // Cria arrays de cores dinâmicos baseados nas legendas (labels) recebidas.
            const dynamicBackgroundColors = chartData.labels.map(
                label => FUGULIN_CHART_COLORS[label] || '#e5e7eb' // Usa a cor do mapa ou um cinza padrão
            );
            const dynamicBorderColors = chartData.labels.map(
                label => FUGULIN_CHART_BORDERS[label] || '#9ca3af' // Usa a cor da borda do mapa ou um cinza padrão
            );

            fugulinChart = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: chartData.labels,
                    datasets: [{
                        label: 'Nº de Pacientes',
                        data: chartData.data,
                        backgroundColor: dynamicBackgroundColors, // <-- Usa o array de cores dinâmico
                        borderColor: dynamicBorderColors,         // <-- Usa o array de bordas dinâmico
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'bottom',
                        }
                    }
                }
            });
        }

        /**
         * Renderiza o gráfico de barras com as medicações mais utilizadas na semana.
         */
        function renderMedicationChart(chartData) {
            const ctx = document.getElementById('medication-chart')?.getContext('2d');
            if (!ctx) return;

            if (medicationChart) {
                medicationChart.destroy();
            }

            medicationChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: chartData.labels,
                    datasets: [{
                        label: 'Doses Administradas',
                        data: chartData.data,
                        backgroundColor: 'rgba(79, 70, 229, 0.6)', // indigo-600 com opacidade
                        borderColor: 'rgba(79, 70, 229, 1)',
                        borderWidth: 1
                    }]
                },
                options: {
                    indexAxis: 'y', // Transforma em gráfico de barras horizontais
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            display: false // Não precisa de legenda para um único dataset
                        },
                        tooltip: {
                            callbacks: {
                                // Esta função é chamada para gerar o título do tooltip
                                title: function(tooltipItems) {
                                    // Pega o índice do item sobre o qual o mouse está
                                    const index = tooltipItems[0].dataIndex;
                                    // Usa o índice para buscar o nome completo no array 'fullLabels'
                                    return chartData.fullLabels[index];
                                },
                                // Esta função é chamada para gerar o corpo do tooltip
                                label: function(tooltipItem) {
                                    // Retorna a contagem de doses normalmente
                                    return ` Doses: ${tooltipItem.raw}`;
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            beginAtZero: true,
                            title: { display: true, text: 'Doses Administradas' },
                            ticks: {
                                precision: 0 // Garante que não hajam casas decimais
                            }
                        }
                    }
                }
            });
        }

        /**
         * Renderiza o gráfico de linha com as tendências de scores Fugulin vs. NEWS2.
         */
        function renderTrendsChartUnit(chartData) {
            const ctx = document.getElementById('trends-chart-unit')?.getContext('2d');
            if (!ctx) return;

            if (unitTrendsChart) {
                unitTrendsChart.destroy();
            }

            unitTrendsChart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: chartData.labels,
                    datasets: [
                        {
                            label: 'Média Fugulin',
                            data: chartData.fugulinData,
                            borderColor: 'rgb(59, 130, 246)', // blue-500
                            backgroundColor: 'rgba(59, 130, 246, 0.1)',
                            yAxisID: 'yFugulin',
                            tension: 0.1,
                            spanGaps: true
                        },
                        {
                            label: 'Média NEWS',
                            data: chartData.newsData,
                            borderColor: 'rgb(239, 68, 68)', // red-500
                            backgroundColor: 'rgba(239, 68, 68, 0.1)',
                            yAxisID: 'yNews',
                            tension: 0.1,
                            spanGaps: true
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        yFugulin: {
                            type: 'linear',
                            display: true,
                            position: 'left',
                            title: { display: true, text: 'Score Fugulin' }
                        },
                        yNews: {
                            type: 'linear',
                            display: true,
                            position: 'right',
                            title: { display: true, text: 'Score NEWS' },
                            grid: { drawOnChartArea: false } // Não desenha a grade para o segundo eixo
                        }
                    }
                }
            });
        }

        /**
         * Renderiza a tabela com o Top 5 pacientes com maior risco de deterioração (NEWS2 >= 5).
         * @param {Array<object>} highRiskPatients - Array de objetos de pacientes de alto risco.
         */
        function renderHighRiskTable(highRiskPatients) {
            const tableBody = document.getElementById('high-risk-table-body');
            if (!tableBody) return;

            if (!highRiskPatients || highRiskPatients.length === 0) {
                tableBody.innerHTML = `<tr><td colspan="5" class="p-4 text-center text-gray-500 italic">Nenhum paciente com risco elevado (NEWS2 ≥ 5) no momento.</td></tr>`;
                return;
            }

            tableBody.innerHTML = highRiskPatients.map(p => {
                let trendHtml = '';
                if (p.trend === '↑') {
                    trendHtml = `<span class="text-red-600 font-bold" title="Score Aumentou">${p.trend}</span>`;
                } else if (p.trend === '↓') {
                    trendHtml = `<span class="text-green-600 font-bold" title="Score Diminuiu">${p.trend}</span>`;
                } else {
                    trendHtml = `<span class="text-gray-500" title="Score Estável">${p.trend}</span>`;
                }

                return `
                    <tr class="border-b hover:bg-gray-50">
                        <td class="p-2 font-mono text-center">${p.bed}</td>
                        <td class="p-2">${p.name}</td>
                        <td class="p-2 font-bold text-center">${p.score}</td>
                        <td class="p-2 text-xl text-center">${trendHtml}</td>
                        <td class="p-2 text-gray-600 truncate" title="${p.professional}">${p.professional}</td>
                    </tr>
                `;
            }).join('');
        }

        /**
         * Renderiza a tabela com os diagnósticos mais frequentes na unidade.
         * @param {Array<object>} diagnosisFrequency - Array de objetos com diagnósticos e suas contagens.
         */
        function renderDiagnosisTable(diagnosisFrequency) {
            const tableBody = document.getElementById('diagnosis-frequency-table-body');
            if (!tableBody) return;

            if (!diagnosisFrequency || diagnosisFrequency.length === 0) {
                tableBody.innerHTML = `<tr><td colspan="3" class="p-4 text-center text-gray-500 italic">Nenhum diagnóstico registrado entre os pacientes ativos.</td></tr>`;
                return;
            }

            tableBody.innerHTML = diagnosisFrequency.map(d => `
                <tr class="border-b hover:bg-gray-50">
                    <td class="p-2">${d.diagnosis}</td>
                    <td class="p-2 text-center">${d.count}</td>
                    <td class="p-2 text-center">${d.percentage}%</td>
                </tr>
            `).join('');
        }

        /**
         * Renderiza o gráfico de barras agrupadas com Admissões vs. Saídas.
         */
        function renderFlowChart(chartData) {
            const ctx = document.getElementById('flow-chart-unit')?.getContext('2d');
            if (!ctx) return;

            if (unitFlowChart) {
                unitFlowChart.destroy();
            }
            
            unitFlowChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: chartData.labels,
                    datasets: [
                        {
                            label: 'Admissões',
                            data: chartData.admissionsData,
                            backgroundColor: 'rgba(22, 163, 74, 0.6)', // green-600
                        },
                        {
                            label: 'Saídas',
                            data: chartData.dischargesData,
                            backgroundColor: 'rgba(220, 38, 38, 0.6)', // red-600
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: { display: true, text: 'Nº de Pacientes' },
                            ticks: {
                                precision: 0 // Garante que não hajam casas decimais
                            }
                        }
                    }
                }
            });
        }


        /**
         * Gera um HTML formatado para o resumo da unidade, converte os gráficos em imagens
         * e aciona a impressão do navegador, seguindo o modelo da impressão do resumo do paciente.
         */
        async function handlePrintUnitSummary() {
            const printView = document.getElementById('print-view');
            // Valida se os dados necessários para a impressão estão disponíveis
            if (!currentUnitSummaryData || !fugulinChart || !unitTrendsChart || !medicationChart || !unitFlowChart) {
                showToast("Dados do resumo da unidade não estão prontos para impressão.", "error");
                return;
            }
            
            showToast("Preparando impressão...", 2000);

            // 1. Desestrutura os dados já processados e armazenados na variável global
            const { kpis, highRiskPatients, diagnosisFrequency } = currentUnitSummaryData;

            // 2. Converte todos os gráficos do canvas para imagens base64, que podem ser impressas
            const fugulinChartImg = fugulinChart.toBase64Image();
            const medicationChartImg = medicationChart.toBase64Image();
            const trendsChartImg = unitTrendsChart.toBase64Image();
            const flowChartImg = unitFlowChart.toBase64Image();

            // 3. Monta o HTML para impressão com estilos inline para garantir a formatação correta
            printView.innerHTML = `
                <div style="font-family: Arial, sans-serif; padding: 20px; color: #333;">
                    <div style="text-align: center; border-bottom: 2px solid #000; padding-bottom: 10px; margin-bottom: 20px;">
                        <h1 style="font-size: 24px; margin: 0;">Resumo Geral da Unidade</h1>
                        <p style="font-size: 12px; margin: 5px 0;">Relatório gerado em: ${new Date().toLocaleString('pt-BR')}</p>
                    </div>

                    <div style="margin-bottom: 20px; page-break-inside: avoid;">
                        <h3 style="font-size: 16px; font-weight: bold; margin-bottom: 10px;">Visão Geral da Unidade</h3>
                        <table style="width: 100%; border-collapse: collapse; font-size: 12px; text-align: center;">
                            <tbody>
                                <tr>
                                    <td style="border: 1px solid #ddd; padding: 8px;"><strong>Pacientes Ativos:</strong><br><span style="font-size: 24px; font-weight: bold;">${kpis.activePatients}</span></td>
                                    <td style="border: 1px solid #ddd; padding: 8px;"><strong>Média de Complexidade (Fugulin):</strong><br><span style="font-size: 24px; font-weight: bold;">${kpis.averageFugulin}</span></td>
                                    <td style="border: 1px solid #ddd; padding: 8px;"><strong>Pacientes em Alto Risco (NEWS≥5):</strong><br><span style="font-size: 24px; font-weight: bold;">${kpis.highRiskPatients}</span></td>
                                </tr>
                                <tr>
                                    <td style="border: 1px solid #ddd; padding: 8px;" colspan="2"><strong>Admissões (Últimos 7 dias):</strong><br><span style="font-size: 24px; font-weight: bold;">${kpis.admissionsInWeek}</span></td>
                                    <td style="border: 1px solid #ddd; padding: 8px;" colspan="1"><strong>Saídas (Últimos 7 dias):</strong><br><span style="font-size: 24px; font-weight: bold;">${kpis.dischargesInWeek}</span></td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    
                    <div style="display: flex; justify-content: space-between; gap: 20px; margin-top: 20px; page-break-inside: avoid;">
                        <div style="width: 48%; text-align: center;">
                            <h3 style="font-size: 16px; font-weight: bold; margin-bottom: 10px;">Distribuição por Complexidade</h3>
                            <img src="${fugulinChartImg}" style="width: 100%; max-width: 350px; border: 1px solid #eee; margin: 0 auto;" alt="Gráfico Fugulin"/>
                        </div>
                        <div style="width: 48%; text-align: center;">
                            <h3 style="font-size: 16px; font-weight: bold; margin-bottom: 10px;">5 Principais Medicações</h3>
                            <img src="${medicationChartImg}" style="width: 100%; max-width: 350px; border: 1px solid #eee; margin: 0 auto;" alt="Gráfico de Medicações"/>
                        </div>
                    </div>

                    <div style="margin-top: 25px; page-break-before: auto;">
                        <h3 style="font-size: 16px; font-weight: bold; margin-bottom: 10px;">Pacientes com Maior Risco de Deterioração</h3>
                        <table style="width: 100%; border-collapse: collapse; font-size: 11px;">
                            <thead style="background-color: #f2f2f2; text-align: left;">
                                <tr>
                                    <th style="border: 1px solid #ddd; padding: 5px;">Leito</th>
                                    <th style="border: 1px solid #ddd; padding: 5px;">Paciente</th>
                                    <th style="border: 1px solid #ddd; padding: 5px; text-align: center;">NEWS2</th>
                                    <th style="border: 1px solid #ddd; padding: 5px; text-align: center;">Tend.</th>
                                    <th style="border: 1px solid #ddd; padding: 5px;">Último Profissional</th>
                                </tr>
                            </thead>
                            <tbody>
                                ${highRiskPatients.length > 0 ? highRiskPatients.map(p => `
                                    <tr style="page-break-inside: avoid;">
                                        <td style="border: 1px solid #ddd; padding: 4px; text-align: center;">${p.bed}</td>
                                        <td style="border: 1px solid #ddd; padding: 4px;">${p.name}</td>
                                        <td style="border: 1px solid #ddd; padding: 4px; text-align: center; font-weight: bold;">${p.score}</td>
                                        <td style="border: 1px solid #ddd; padding: 4px; text-align: center; font-size: 14px;">${p.trend}</td>
                                        <td style="border: 1px solid #ddd; padding: 4px;">${p.professional}</td>
                                    </tr>`).join('') : '<tr><td colspan="5" style="text-align: center; padding: 10px; border: 1px solid #ddd;">Nenhum paciente com risco elevado.</td></tr>'}
                            </tbody>
                        </table>
                    </div>

                    <div style="margin-top: 25px; page-break-before: auto;">
                        <h3 style="font-size: 16px; font-weight: bold; margin-bottom: 10px;">Diagnósticos Mais Frequentes</h3>
                        <table style="width: 100%; border-collapse: collapse; font-size: 11px;">
                            <thead style="background-color: #f2f2f2; text-align: left;">
                                <tr>
                                    <th style="border: 1px solid #ddd; padding: 5px;">Diagnóstico</th>
                                    <th style="border: 1px solid #ddd; padding: 5px; text-align: center;">Nº Pac.</th>
                                    <th style="border: 1px solid #ddd; padding: 5px; text-align: center;">% Total</th>
                                </tr>
                            </thead>
                            <tbody>
                                ${diagnosisFrequency.length > 0 ? diagnosisFrequency.map(d => `
                                    <tr style="page-break-inside: avoid;">
                                        <td style="border: 1px solid #ddd; padding: 4px;">${d.diagnosis}</td>
                                        <td style="border: 1px solid #ddd; padding: 4px; text-align: center;">${d.count}</td>
                                        <td style="border: 1px solid #ddd; padding: 4px; text-align: center;">${d.percentage}%</td>
                                    </tr>
                                `).join('') : '<tr><td colspan="3" style="text-align: center; padding: 10px; border: 1px solid #ddd;">Nenhum diagnóstico registrado.</td></tr>'}
                            </tbody>
                        </table>
                    </div>
                </div>
            `;

            // 4. Aciona a impressão do navegador após um breve momento para renderizar
            setTimeout(() => window.print(), 300);
        }

        /**
         * Orquestra a exibição do modal de resumo da unidade, buscando os dados e renderizando os componentes.
         */
        async function showUnitSummary() {
            const modal = document.getElementById('unit-summary-modal');
            const loader = document.getElementById('unit-summary-loader');
            const contentContainer = document.getElementById('unit-summary-main-container');

            // 1. Prepara a UI para carregamento
            modal.classList.remove('hidden');
            loader.style.display = 'block';
            loader.innerHTML = `
                <svg class="animate-spin h-8 w-8 text-blue-600 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                <p class="mt-3 text-gray-600">Analisando dados da unidade...</p>`;
            contentContainer.classList.add('hidden');
            
            // 2. Chama a função que busca e processa todos os dados.
            const summaryData = await generateUnitSummaryData();

            currentUnitSummaryData = summaryData; // Guarda os dados para a impressão

            // 3. Verifica se os dados foram retornados e renderiza o conteúdo
            if (summaryData) {
                if (summaryData.isEmpty) {
                    loader.innerHTML = '<p class="text-gray-600 p-10">Não há pacientes ativos na unidade para gerar um resumo.</p>';
                } else {
                    // Renderiza os KPIs (Passo 3)
                    renderUnitKPIs(summaryData.kpis);
                    
                    // Renderiza os Gráficos (Passo 4)
                    renderFugulinChart(summaryData.fugulinChartData);
                    renderMedicationChart(summaryData.medicationChartData);
                    renderTrendsChartUnit(summaryData.trendsChartData);
                    renderFlowChart(summaryData.flowChartData);
                    
                    // **AQUI ESTÁ A MÁGICA DO PASSO 5**
                    // Renderiza as Tabelas
                    renderHighRiskTable(summaryData.highRiskPatients);
                    renderDiagnosisTable(summaryData.diagnosisFrequency);

                    // Esconde o loader e mostra o conteúdo principal
                    loader.style.display = 'none';
                    contentContainer.classList.remove('hidden');
                }
            } else {
                // Exibe uma mensagem de erro se a busca falhar
                loader.innerHTML = '<p class="text-red-600 p-10">Ocorreu um erro ao buscar os dados. Tente novamente.</p>';
            }
        }

        // --- FUNÇÕES DE NAVEGAÇÃO E UI ---~

        /**
         * Atualiza o indicador visual (bolinha vermelha) no ícone do sino.
         * @param {number} unreadCount - O número de notificações não lidas.
         */
        function updateBellIndicator(unreadCount) {
            if (unreadCount > 0) {
                notificationIndicator.classList.remove('hidden');
            } else {
                notificationIndicator.classList.add('hidden');
            }
        }

        /**
         * Renderiza a lista de notificações no painel dropdown.
         */
        function renderNotificationsPanel() {
            notificationList.innerHTML = ''; // Limpa a lista

            if (allNotifications.length === 0) {
                notificationList.innerHTML = '<p class="p-4 text-sm text-center text-gray-500">Nenhuma notificação encontrada.</p>';
                return;
            }

            allNotifications.slice(0, 20).forEach(notif => { // Mostra as últimas 20
                const item = document.createElement('div');
                item.className = `notification-item ${!notif.read ? 'unread' : ''}`;
                item.dataset.notifId = notif.id;
                item.dataset.patientId = notif.patientId;
                item.dataset.handoverId = notif.handoverId;

                const date = notif.timestamp?.toDate ? notif.timestamp.toDate() : new Date();
                const formattedDate = date.toLocaleString('pt-BR', { dateStyle: 'short', timeStyle: 'short' });

                item.innerHTML = `
                    <p class="text-sm text-gray-800">
                        <strong>${notif.actorName}</strong> adicionou um adendo na passagem de <strong>${notif.patientName}</strong>.
                    </p>
                    <p class="text-xs text-gray-500 mt-1">${formattedDate}</p>
                `;
                notificationList.appendChild(item);
            });
        }

        /**
         * FUNÇÃO DE PROCESSAMENTO
         * Itera sobre os plantões e extrai uma lista cronológica de eventos relevantes.
         * @param {Array} handovers - Array de objetos de handover, em ordem decrescente (mais novo primeiro).
         * @returns {Array} - Um array de objetos de evento, prontos para serem renderizados.
         */
        function processEventsForTable(handovers) {
            const events = [];
            const sosMeds = ['acetilsalicilico', 'bromoprida', 'codeina', 'diazepam', 'dipirona', 'haloperidol', 'ibuprofeno', 'metoclopramida', 'midazolam', 'morfina', 'ondansetrona', 'paracetamol', 'tramadol', 'epinefrina', 'norepinefrina', 'dobutamina'];

            // Alterado para um forEach com índice para podermos comparar com o plantão anterior
            handovers.forEach((h, index) => {
                const timestamp = h.timestamp?.toDate ? h.timestamp.toDate() : new Date();
                const professional = h.professionalName || 'N/A';

                // 1. Alertas Clínicos (Scores e Febre)
                if (h.news2?.score >= 5) {
                    // Pega o plantão anterior no tempo (que é o próximo item no nosso array invertido)
                    const previousHandover = handovers[index + 1];
                    // Pega o score anterior. Se não existir, considera 0.
                    const previousScore = previousHandover?.news2?.score ?? 0;

                    // CRIA O ALERTA APENAS SE:
                    // - O score atual for maior que o anterior, OU
                    // - Não houver um plantão anterior para comparar (é o primeiro registro da semana)
                    if (h.news2.score > previousScore || !previousHandover) {
                        events.push({
                            timestamp,
                            category: '⚠️ Alerta Clínico',
                            // Descrição melhorada para dar mais contexto
                            description: `Aumento do Score NEWS para: <strong>${h.news2.score}</strong> (anterior: ${previousHandover ? previousScore : 'N/A'})`,
                            professional
                        });
                    }
                }
                
                if (h.monitoring?.temp) {
                    const temp = parseFloat(h.monitoring.temp.replace(',', '.'));
                    if (temp > 37.8) {
                        events.push({
                            timestamp,
                            category: '⚠️ Alerta Clínico',
                            description: `Pico Febril: <strong>${temp.toFixed(1)}°C</strong>`,
                            professional
                        });
                    }
                }
                
                // 2. Novas Medicações SOS (lógica inalterada)
                if (h.medications && h.medications.length > 0) {
                    h.medications.forEach(med => {
                        const medNameLower = med.name.toLowerCase();
                        if (sosMeds.some(sosMed => medNameLower.includes(sosMed))) {
                            events.push({
                                timestamp,
                                category: '💉 Medicação SOS',
                                description: `Administrado <strong>${med.name}</strong> (${med.times.length}x)`,
                                professional
                            });
                        }
                    });
                }

                // 3. Exames e Procedimentos (lógica inalterada)
                if (h.examsDone && h.examsDone.length > 0) {
                    h.examsDone.forEach(exam => {
                        events.push({
                            timestamp,
                            category: '🧪 Resultado',
                            description: `Resultado de <strong>${exam.name}</strong>: "${(exam.result || '').substring(0, 50)}..."`,
                            professional
                        });
                    });
                }
                if (h.changes?.pendingExams?.added?.length > 0) {
                    h.changes.pendingExams.added.forEach(exam => {
                        events.push({
                            timestamp,
                            category: '🧪 Procedimento',
                            description: `Realizado <strong>${exam.name}</strong> (aguardando resultado)`,
                            professional
                        });
                    });
                }
                if (h.changes?.scheduledExams?.added?.length > 0) {
                    h.changes.scheduledExams.added.forEach(exam => {
                        events.push({
                            timestamp,
                            category: '📅 Agendamento',
                            description: `Agendado <strong>${exam.name}</strong>`,
                            professional
                        });
                    });
                }

                // 4. Novos Diagnósticos (lógica inalterada)
                if (h.changes?.diagnoses?.added?.length > 0) {
                    h.changes.diagnoses.added.forEach(diag => {
                        events.push({
                            timestamp,
                            category: '⚕️ Diagnóstico',
                            description: `Adicionado: <strong>${diag}</strong>`,
                            professional
                        });
                    });
                }
            });

            return events;
        }


        /**
        * FUNÇÃO DE RENDERIZAÇÃO
        * Pega a lista de eventos e renderiza as linhas da tabela no DOM.
        * @param {Array} events - O array de eventos retornado por processEventsForTable.
        */
        function renderEventsTable(events) {
            const tableBody = document.getElementById('events-table-body');
            if (!tableBody) return;

            if (events.length === 0) {
                tableBody.innerHTML = `
                    <tr>
                        <td colspan="4" class="text-center text-gray-500 p-4">
                            Nenhum evento relevante registrado na última semana.
                        </td>
                    </tr>
                `;
                return;
            }

            tableBody.innerHTML = events.map(event => {
                const formattedDate = event.timestamp.toLocaleString('pt-BR', {
                    day: '2-digit',
                    month: '2-digit',
                    hour: '2-digit',
                    minute: '2-digit'
                }).replace(',','');

                return `
                    <tr class="border-b hover:bg-gray-50">
                        <td class="p-2 text-sm text-gray-600 whitespace-nowrap">${formattedDate}</td>
                        <td class="p-2 text-sm text-gray-800 whitespace-nowrap">${event.category}</td>
                        <td class="p-2 text-sm text-gray-800">${event.description}</td>
                        <td class="p-2 text-sm text-gray-600 whitespace-nowrap">${event.professional}</td>
                    </tr>
                `;
            }).join('');
        }

        /**
         * FUNÇÃO DE PROCESSAMENTO
         * Prepara os dados do histórico para serem usados pela biblioteca Chart.js.
         * @param {Array} handovers - Array de objetos de handover em ordem CRONOLÓGICA (mais antigo primeiro).
         * @returns {object} - Um objeto contendo labels e datasets para o gráfico.
         */
        function prepareChartData(handovers) {
            const chartData = {
                labels: [],
                datasets: {
                    fc: [],
                    fr: [],
                    pas: [],
                    temp: [],
                    news2: []
                }
            };

            handovers.forEach(h => {
                const timestamp = h.timestamp?.toDate ? h.timestamp.toDate() : new Date();
                const label = timestamp.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', hour: '2-digit', minute: '2-digit' });
                chartData.labels.push(label);

                const mon = h.monitoring;
                const score = h.news2;

                // Adiciona o valor se existir, ou null para criar uma falha no gráfico
                const fc = mon?.fc ? parseInt(mon.fc, 10) : null;
                chartData.datasets.fc.push(isNaN(fc) ? null : fc);

                const fr = mon?.fr ? parseInt(mon.fr, 10) : null;
                chartData.datasets.fr.push(isNaN(fr) ? null : fr);

                const pas = mon?.pa ? parseInt(mon.pa.split('/')[0], 10) : null;
                chartData.datasets.pas.push(isNaN(pas) ? null : pas);

                const temp = mon?.temp ? parseFloat(mon.temp.replace(',', '.')) : null;
                chartData.datasets.temp.push(isNaN(temp) ? null : temp);

                const news2Score = score?.score !== undefined ? score.score : null;
                chartData.datasets.news2.push(news2Score);
            });

            return chartData;
        }


        /**
        * PASSO 3 - FUNÇÃO DE RENDERIZAÇÃO
        * Renderiza o gráfico de tendências usando Chart.js.
        * @param {object} chartData - O objeto retornado por prepareChartData.
        */
        function renderTrendsChart(chartData) {
            const ctx = document.getElementById('trends-chart').getContext('2d');
            if (!ctx) return;

            // Destrói qualquer gráfico anterior para evitar sobreposição e vazamento de memória
            if (weeklySummaryChart) {
                weeklySummaryChart.destroy();
            }

            weeklySummaryChart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: chartData.labels,
                    datasets: [
                        {
                            label: 'FC (bpm)',
                            data: chartData.datasets.fc,
                            borderColor: 'rgb(239, 68, 68)', // red-500
                            backgroundColor: 'rgba(239, 68, 68, 0.1)',
                            yAxisID: 'y',
                            tension: 0, // ALTERADO: Tensão definida para 0 para linhas retas em dados ausentes
                            spanGaps: true 
                        },
                        {
                            label: 'PAS (mmHg)',
                            data: chartData.datasets.pas,
                            borderColor: 'rgb(59, 130, 246)', // blue-500
                            backgroundColor: 'rgba(59, 130, 246, 0.1)',
                            yAxisID: 'y',
                            tension: 0, // ALTERADO: Tensão definida para 0
                            spanGaps: true 
                        },
                        {
                            label: 'FR (irpm)',
                            data: chartData.datasets.fr,
                            borderColor: 'rgb(22, 163, 74)', // green-600
                            backgroundColor: 'rgba(22, 163, 74, 0.1)',
                            yAxisID: 'y',
                            tension: 0, // ALTERADO: Tensão definida para 0
                            spanGaps: true 
                        },
                        {
                            label: 'Temp (°C)',
                            data: chartData.datasets.temp,
                            borderColor: 'rgb(249, 115, 22)', // orange-500
                            backgroundColor: 'rgba(249, 115, 22, 0.1)',
                            yAxisID: 'y1', 
                            tension: 0, // ALTERADO: Tensão definida para 0
                            spanGaps: true 
                        },
                        {
                            label: 'Score NEWS',
                            data: chartData.datasets.news2,
                            borderColor: 'rgb(107, 114, 128)', // gray-500
                            backgroundColor: 'rgba(107, 114, 128, 0.1)',
                            stepped: true, 
                            yAxisID: 'y1', 
                            tension: 0, // ALTERADO: Tensão definida para 0 (embora stepped já ajude)
                            spanGaps: true 
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    interaction: {
                        mode: 'index',
                        intersect: false,
                    },
                    scales: {
                        x: {
                            title: { display: true, text: 'Data e Hora do Registro' }
                        },
                        y: { 
                            type: 'linear',
                            display: true,
                            position: 'left',
                            title: { display: true, text: 'BPM / mmHg / IRPM' },
                            suggestedMin: 40 
                        },
                        y1: { 
                            type: 'linear',
                            display: true,
                            position: 'right',
                            title: { display: true, text: '°C / Score' },
                            grid: {
                                drawOnChartArea: false, 
                            },
                        }
                    },
                    plugins: {
                        tooltip: {
                            titleFont: { weight: 'bold' },
                            bodyFont: { size: 12 },
                        }
                    }
                }
            });
        }

        /**
         * Lida com o clique em uma notificação: exibe um loader, marca como lida, 
         * navega para o paciente e abre o modal da passagem de plantão correspondente.
         * @param {string} notifId - O ID da notificação.
         * @param {string} patientId - O ID do paciente.
         * @param {string} handoverId - O ID do handover.
         */
        async function handleNotificationClick(notifId, patientId, handoverId) {
                console.log(`[handleNotificationClick] Clicou na notificação ID: ${notifId}`);
                notificationPanel.classList.add('hidden');
                
                showActionLoader();

                try {
                    const notifRef = doc(db, 'notifications', notifId);
                    await updateDoc(notifRef, { read: true });

                    // A função showPatientDetail continuará a carregar a lista completa em segundo plano
                    await showPatientDetail(patientId);
                    
                    // Em vez de procurar em 'currentHandovers', buscamos o documento exato.
                    console.log(`Buscando handover específico: patientId=${patientId}, handoverId=${handoverId}`);
                    const handoverRef = doc(db, 'patients', patientId, 'handovers', handoverId);
                    const handoverSnap = await getDoc(handoverRef);

                    if (handoverSnap.exists()) {
                        // onstruímos o objeto de dados a partir do documento que acabamos de buscar.
                        const handoverData = { id: handoverSnap.id, ...handoverSnap.data() };

                        populateHandoverViewModal(handoverData);
                        viewHandoverModal.classList.remove('hidden'); 
                        
                        const adendosSection = document.getElementById('view-handover-adendos-section');
                        if (adendosSection) {
                            adendosSection.scrollIntoView({ behavior: 'smooth', block: 'end' });
                        }
                    } else {
                        // A mensagem de erro agora é mais precisa.
                        showToast("Não foi possível encontrar a passagem de plantão correspondente.", "error");
                        console.error(`Handover com ID ${handoverId} não foi encontrado no banco de dados.`);
                    }

                } catch (error) {
                    console.error("Erro ao processar clique na notificação:", error);
                    showToast("Ocorreu um erro ao abrir a notificação.", "error");
                } finally {
                    hideActionLoader();
                }
            }


        /**
         * Configura o listener em tempo real para as notificações do usuário logado.
         * @param {object} user - O objeto do usuário autenticado do Firebase.
         */
        function setupNotificationListener(user) {
            if (unsubscribeNotifications) {
                console.log("[setupNotificationListener] Cancelando listener de notificações anterior.");
                unsubscribeNotifications();
            }

            console.log(`[setupNotificationListener] Configurando listener para o usuário: ${user.uid}`);
            
            const q = query(collection(db, 'notifications'), where('recipientUid', '==', user.uid), orderBy('timestamp', 'desc'));

            unsubscribeNotifications = onSnapshot(q, (snapshot) => {
                const notifications = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                console.log(`[onSnapshot] Recebidas ${notifications.length} notificações.`);

                allNotifications = notifications;
                const unreadCount = allNotifications.filter(n => !n.read).length;
                
                updateBellIndicator(unreadCount);
                renderNotificationsPanel();
                
            }, (error) => {
                console.error("Erro ao buscar notificações em tempo real:", error);
                showToast("Erro ao carregar notificações.", "error");
            });
        }

        /**
         * Atualiza o indicador visual com 3 estados de conexão.
         * @param {'online' | 'offline' | 'connecting'} status - O estado atual da conexão.
         */
        function updateConnectionStatus(status) {
            const statusEl = document.getElementById('connection-status');
            if (!statusEl) return;

            // A classe 'hidden' esconde por padrão (no celular)
            // A classe 'sm:inline' mostra a partir de telas pequenas (sm) para cima
            const textClasses = "hidden sm:inline text-sm font-medium text-gray-600";

            switch (status) {
                case 'online':
                    statusEl.innerHTML = `
                        <div class="status-dot status-online" title="Conexão estabelecida."></div>
                        <span class="${textClasses}">Online</span>
                    `;
                    break;
                case 'connecting':
                    statusEl.innerHTML = `
                        <div class="status-dot status-connecting" title="Conectando..."></div>
                        <span class="${textClasses}">Conectando...</span>
                    `;
                    break;
                case 'offline':
                    statusEl.innerHTML = `
                        <div class="status-dot status-offline" title="Você está offline."></div>
                        <span class="${textClasses}">Offline</span>
                    `;
                    break;
            }
        }

        /**
         * Controla a visibilidade da mensagem "Nenhuma alergia adicionada"
         * com base no estado do módulo de alergias e no modo de edição.
         */
        function updateAllergyPlaceholder() {
            const radioYes = document.getElementById('allergy-radio-yes');
            const tagsContainer = document.getElementById('allergies-tags-container');
            const placeholder = document.getElementById('allergy-placeholder-message');
            const inputWrapper = document.getElementById('allergy-input-wrapper');

            // Garante que todos os elementos existem antes de prosseguir
            if (!radioYes || !tagsContainer || !placeholder || !inputWrapper) return;

            // A condição para mostrar a mensagem agora é:
            // 1. O botão "Sim" está marcado E
            // 2. O container de tags está vazio E
            // 3. O campo de input de texto está ESCONDIDO.
            const shouldShow = radioYes.checked && 
                            tagsContainer.children.length === 0 && 
                            inputWrapper.classList.contains('hidden');

            if (shouldShow) {
                placeholder.classList.remove('hidden');
            } else {
                placeholder.classList.add('hidden');
            }
        }

        /**
         * Cria e adiciona um novo checkbox de dispositivo customizado na tela,
         * AGORA com a nova estrutura de contêiner tracejado.
         * @param {string} deviceName - O nome do dispositivo a ser adicionado.
         * @param {boolean} isChecked - Define se o checkbox deve ser criado já marcado.
         */
        function addCustomDispositivo(deviceName, isChecked = true) {
            if (!deviceName || currentCustomDevices.includes(deviceName)) return;

            currentCustomDevices.push(deviceName);

            // 1. Cria o contêiner externo com o estilo tracejado
            const wrapperDiv = document.createElement('div');
            wrapperDiv.className = 'device-item-box';
            wrapperDiv.dataset.custom = 'true'; // Identificador para o contêiner customizado
            wrapperDiv.dataset.deviceName = deviceName;

            // 2. Cria o label, como antes
            const label = document.createElement('label');
            label.className = 'flex items-center space-x-2 w-full cursor-pointer';

            // 3. Cria o checkbox
            const checkbox = document.createElement('input');
            checkbox.type = 'checkbox';
            checkbox.name = 'dispositivo';
            checkbox.value = deviceName;
            checkbox.checked = isChecked;
            checkbox.className = 'rounded';

            // 4. Cria o texto (span)
            const span = document.createElement('span');
            span.textContent = deviceName;
            span.title = deviceName;

            // 5. Monta a estrutura: input e span dentro do label
            label.appendChild(checkbox);
            label.appendChild(span);

            // 6. Monta a estrutura final: label dentro do contêiner tracejado
            wrapperDiv.appendChild(label);
            
            // 7. Adiciona o contêiner completo à área de dispositivos customizados
            customDispositivosContainer.appendChild(wrapperDiv);
        }

        /**
         * Configura todos os listeners de evento para o módulo de dispositivos.
         * Garante que os listeners sejam adicionados apenas uma vez.
         */
        function setupDispositivosModule() {
            if (dispositivosListenersAttached) return;

            // NOVO: Listener para o botão "+ Novo Dispositivo"
            addCustomDispositivoBtn.addEventListener('click', () => {
                devicesAddedThisSession = [];
                outrosDispositivosInputWrapper.classList.remove('hidden');
                dispositivoOutrosInput.focus();
            });

            // ALTERADO: Listener do input de texto "Outros"
            dispositivoOutrosInput.addEventListener('keydown', (e) => {
                if (e.key === 'Enter') {
                    e.preventDefault();
                    const deviceName = dispositivoOutrosInput.value.trim();
                    if (deviceName) {
                        addCustomDispositivo(deviceName, true);
                        devicesAddedThisSession.push(deviceName);
                        dispositivoOutrosInput.value = '';
                        // Agora apenas escondemos o input, não precisamos mais mexer em checkbox
                        outrosDispositivosInputWrapper.classList.add('hidden');
                        setUnsavedChanges(true);
                    }
                }
            });

            // Listener para remoção de dispositivos customizados (permanece o mesmo)
            dispositivosGrid.addEventListener('change', (e) => {
                const target = e.target;
                if (target.type === 'checkbox' && target.closest('label[data-custom="true"]')) {
                    if (!target.checked) {
                        // 1. Pega a referência do checkbox que foi desmarcado.
                        const checkboxToRemove = target;
                        
                        // 2. Configura o texto do nosso novo modal genérico.
                        const modal = document.getElementById('generic-confirm-modal');
                        modal.querySelector('#generic-confirm-title').textContent = 'Remover Dispositivo';
                        modal.querySelector('#generic-confirm-text').textContent = 'Desmarcar esta opção removerá este dispositivo da lista. Deseja continuar?';
                        modal.querySelector('#generic-confirm-button').textContent = 'Sim, Remover';

                        const confirmBtn = modal.querySelector('#generic-confirm-button');
                        const cancelBtn = modal.querySelector('#generic-cancel-button');

                        // 3. Clona os botões para limpar quaisquer listeners antigos (truque de segurança).
                        const newConfirmBtn = confirmBtn.cloneNode(true);
                        const newCancelBtn = cancelBtn.cloneNode(true);
                        confirmBtn.parentNode.replaceChild(newConfirmBtn, confirmBtn);
                        cancelBtn.parentNode.replaceChild(newCancelBtn, cancelBtn);

                        // 4. Define o que acontece se o usuário confirmar a remoção.
                        newConfirmBtn.addEventListener('click', () => {
                            const deviceName = checkboxToRemove.value;
                            currentCustomDevices = currentCustomDevices.filter(d => d !== deviceName);
                            checkboxToRemove.closest('label').remove();
                            setUnsavedChanges(true);
                            modal.classList.add('hidden'); // Esconde o modal
                        });

                        // 5. Define o que acontece se o usuário cancelar.
                        newCancelBtn.addEventListener('click', () => {
                            checkboxToRemove.checked = true; // Remarca o checkbox
                            modal.classList.add('hidden'); // Esconde o modal
                        });

                        // 6. Exibe o modal.
                        modal.classList.remove('hidden');

                    }
                } else if (target.type === 'checkbox') {
                    setUnsavedChanges(true);
                }
            });

            dispositivosListenersAttached = true;
        }
        /**
         * Cria um elemento <span> estilizado como uma "pílula" para ser usado em uma lista.
         * Contém o texto e um botão de remoção oculto por padrão.
         * @param {string} text - O texto do item.
         * @returns {HTMLElement}
         */
        function createListItem(text) {
            const item = document.createElement('span');
            // Adicionamos as duas classes no mesmo elemento
            item.className = 'info-tag item-text'; 
            item.dataset.value = text;
            
            // O texto vai diretamente dentro do span principal
            const textNode = document.createTextNode(text + ' '); // Adiciona um espaço antes do botão de remover
            item.appendChild(textNode);

            const removeBtn = document.createElement('span');
            removeBtn.className = 'remove-item-btn';
            removeBtn.innerHTML = '×';
            item.appendChild(removeBtn);

            return item;
        }

        /**
         * Gerencia o estado (ativo/inativo) do botão principal de salvar.
         * Ativa o botão e aplica o estilo azul quando há alterações.
         * Desativa o botão e aplica um estilo cinza quando não há alterações.
         * @param {boolean} isUnsaved - true se houver alterações, false caso contrário.
         */
        function setUnsavedChanges(isUnsaved) {
            if (hasUnsavedChanges === isUnsaved) return; // Evita execuções desnecessárias

            hasUnsavedChanges = isUnsaved;
            const saveButton = addHandoversForm.querySelector('button[type="submit"]');
            if (!saveButton) return; // Verificação de segurança

            // Classes para o botão ATIVO
            const enabledClasses = ['bg-blue-600', 'hover:bg-blue-700'];
            // Classes para o botão INATIVO
            const disabledClasses = ['bg-gray-400', 'cursor-not-allowed', 'opacity-75'];

            if (isUnsaved) {
                // Habilita o botão
                saveButton.disabled = false;
                saveButton.classList.remove(...disabledClasses);
                saveButton.classList.add(...enabledClasses);
            } else {
                // Desabilita o botão
                saveButton.disabled = true;
                saveButton.classList.remove(...enabledClasses);
                saveButton.classList.add(...disabledClasses);
            }
        }

        /**
         * [VERSÃO DE DEPURAÇÃO]
         * Reseta completamente o estado e a UI do formulário de passagem de plantão.
         * Garante que nenhum dado de um plantão anterior persista e avisa sobre elementos não encontrados.
         */
        function resetFormState() {
            const safeClear = (elementId) => {
                const element = document.getElementById(elementId);
                if (element) {
                    element.innerHTML = '';
                } else {
                    console.warn(`[resetFormState] Aviso: Elemento com ID #${elementId} não foi encontrado.`);
                }
            };

            addHandoversForm.reset();
            currentMedications = [];
            patientExams = [];
            currentShiftCompletedExams = [];
            currentCustomDevices = [];
            originalPatientDevices = [];

            safeClear('diagnoses-tags-container');
            safeClear('comorbidities-tags-container');
            safeClear('allergies-tags-container');
            safeClear('precaucoes-container');
            safeClear('riscos-lpp-container');
            safeClear('riscos-quedas-container');
            safeClear('riscos-bronco-container');
            safeClear('riscos-iras-container');
            safeClear('medications-list-container');
            safeClear('custom-dispositivos-container');
            safeClear('fugulin-cuidado-corporal-container');
            safeClear('fugulin-motilidade-container');
            safeClear('fugulin-deambulacao-container');
            safeClear('fugulin-alimentacao-container');
            safeClear('fugulin-eliminacao-container');

            const allergyInputContainer = document.getElementById('allergy-input-container');
            if (allergyInputContainer) allergyInputContainer.classList.add('hidden');
            
            const allergyRadioYes = document.getElementById('allergy-radio-yes');
            if(allergyRadioYes) allergyRadioYes.checked = false;
            
            const allergyRadioNo = document.getElementById('allergy-radio-no');
            if(allergyRadioNo) allergyRadioNo.checked = false;

            document.querySelectorAll('#dispositivos-grid input[type="checkbox"]').forEach(chk => chk.checked = false);
            
            renderExams();
            resetAndCloseExamEditor();

            document.querySelectorAll('.input-wrapper').forEach(wrapper => wrapper.classList.add('hidden'));

            const editingModule = document.querySelector('.module-editing');
            if (editingModule) {
                exitEditMode(editingModule);
            }
            
            setUnsavedChanges(false);
        }

        /**
         * Ativa o modo de edição para um módulo, garantindo que apenas um esteja ativo por vez.
         * @param {HTMLElement} moduleElement - O elemento do card do módulo.
         */
        function enterEditMode(moduleElement) {
            if (!moduleElement || moduleElement.classList.contains('module-editing')) {
                return; 
            }

            if (activeEditingModule && activeEditingModule !== moduleElement) {
                exitEditMode(activeEditingModule);
            }

            activeEditingModule = moduleElement;
            moduleElement.classList.add('module-editing');

            // Adiciona uma verificação para esconder os botões 'x' via JS
            if (moduleElement.id === 'module-riscos' || moduleElement.id === 'module-cuidados-enfermagem') {
                const removeButtons = moduleElement.querySelectorAll('.info-tag .remove-item-btn');
                removeButtons.forEach(btn => {
                    btn.style.display = 'none'; // Aplica estilo inline para garantir
                });
            }
        }

        /**
         * Desativa o modo de edição para um módulo, limpa o estado de edição ativo
         * e garante que todas as áreas de input sejam escondidas e as de visualização, exibidas.
         * VERSÃO MODIFICADA: Reseta os botões de Precauções e Dispositivos.
         * @param {HTMLElement} moduleElement - O elemento do card do módulo.
         */
        function exitEditMode(moduleElement) {
            if (!moduleElement) return;

            moduleElement.classList.remove('module-editing');

            // Lógica específica para o módulo de medicações
            if (moduleElement.id === 'module-medicacoes') {
                const medicationInput = document.getElementById('form-medications');
                const timeEditor = document.getElementById('medication-time-editor');

                // Se o campo de busca estiver vazio e o editor de tempo não estiver aberto,
                // reseta a UI interna do módulo para o estado inicial.
                if (medicationInput.value.trim() === '' && timeEditor.classList.contains('hidden')) {
                    showMedicationEditor(false); 
                }
            }

            const inputWrappers = moduleElement.querySelectorAll('.input-wrapper');
            inputWrappers.forEach(wrapper => wrapper.classList.add('hidden'));

            const clickableAreas = moduleElement.querySelectorAll('.clickable-item-area');
            clickableAreas.forEach(area => area.classList.remove('hidden'));
            
            // --- INÍCIO DA MODIFICAÇÃO ---
            // Verifica se o módulo é um dos que têm o botão de Adicionar/Cancelar
            if (moduleElement.id === 'module-precaucoes' || moduleElement.id === 'module-dispositivos') {
                const triggerWrapper = moduleElement.querySelector('.trigger-wrapper');
                const cancelWrapper = moduleElement.querySelector('.cancel-action-wrapper');
                const inputField = moduleElement.querySelector('.input-wrapper input[type="text"]');

                // Garante que o botão "+ Novo" reapareça
                triggerWrapper?.classList.remove('hidden');
                // Garante que o botão "Cancelar" desapareça
                cancelWrapper?.classList.add('hidden');
                // Limpa o campo de texto
                if(inputField) inputField.value = '';
            }
            // --- FIM DA MODIFICAÇÃO ---

            if (activeEditingModule === moduleElement) {
                activeEditingModule = null;
            }
            // Se o módulo que está fechando é o de diagnóstico,
            // verifica se a mensagem de placeholder de alergia deve aparecer.
            if (moduleElement.id === 'module-diagnostico') {
                updateAllergyPlaceholder();
            }
        }

        /**
         * Ativa o modo de edição e o campo de input para o módulo de Precauções.
         * @param {HTMLElement} moduleCard - O elemento do card do módulo #module-precaucoes.
         */
        function activatePrecautionsInput(moduleCard) {
            // Não faz nada se o módulo não for encontrado ou já estiver em edição
            if (!moduleCard || moduleCard.classList.contains('module-editing')) {
                return;
            }

            const triggerWrapper = moduleCard.querySelector('.trigger-wrapper');
            const cancelWrapper = moduleCard.querySelector('.cancel-action-wrapper');
            const inputWrapper = moduleCard.querySelector('.input-wrapper');
            const inputField = inputWrapper?.querySelector('input[type="text"]');

            // Esconde o botão "+ Nova" e mostra o "Cancelar" e o campo de input
            if (triggerWrapper) triggerWrapper.classList.add('hidden');
            if (cancelWrapper) cancelWrapper.classList.remove('hidden');
            if (inputWrapper) inputWrapper.classList.remove('hidden');
            if (inputField) inputField.focus(); // Foca no campo para o usuário digitar

            // Coloca o card no estado visual de edição
            enterEditMode(moduleCard);
        }

        //
        /**
         * Move e posiciona uma lista de autocomplete para fora de seu contêiner original,
         * evitando problemas de 'overflow: hidden'.
         * @param {HTMLElement} inputElement - O campo de input que a lista deve seguir.
         * @param {HTMLElement} listElement - A lista de autocomplete a ser posicionada.
         */
        function positionAutocompleteList(inputElement, listElement) {
            // Guarda uma referência ao "lar" original da lista para podermos devolvê-la depois.
            if (!listElement.originalParent) {
                listElement.originalParent = listElement.parentElement;
            }

            // Move a lista para o final do <body> para que ela não seja cortada por nenhum contêiner.
            document.body.appendChild(listElement);

            // Calcula a posição e o tamanho do campo de input na tela.
            const rect = inputElement.getBoundingClientRect();

            // Aplica estilos para posicionar a lista perfeitamente.
            // Usamos 'position: fixed' para que ela ignore o scroll da página e se baseie na janela.
            listElement.style.position = 'fixed';
            listElement.style.top = `${rect.bottom + 2}px`; // 2px abaixo do input
            listElement.style.left = `${rect.left}px`;
            listElement.style.width = `${rect.width}px`;
            listElement.style.zIndex = '5000'; // Um z-index bem alto para garantir que fique na frente de tudo.
        }
        
        // Função para mostrar uma tela específica e esconder as outras
        const showScreen = (screenName) => {
            currentScreen = screenName;

            // Lógica aprimorada para garantir a URL correta
            if (screenName === 'main') {
                // Se a URL atual não for #painel, a corrige sem criar uma nova entrada no histórico.
                if (window.location.hash !== '#painel') {
                    history.replaceState({ screen: 'main' }, 'Painel de Pacientes', '#painel');
                }
            } else if (screenName === 'login') {
                // Limpa o hash para a página de login
                history.pushState({ screen: 'login' }, 'Login', ' ');
            }

            Object.values(screens).forEach(screen => screen.classList.add('hidden'));
            if (screens[screenName]) {
                screens[screenName].classList.remove('hidden');
                screens[screenName].classList.add('flex', 'flex-col');
            }
        };

        const actionLoadingOverlay = document.getElementById('action-loading-overlay');

        /**
         * Exibe o overlay de carregamento de tela cheia.
         */
        function showActionLoader() {
            if (actionLoadingOverlay) {
                actionLoadingOverlay.classList.remove('hidden');
            }
        }

        /**
         * Esconde o overlay de carregamento de tela cheia.
         */
        function hideActionLoader() {
            if (actionLoadingOverlay) {
                actionLoadingOverlay.classList.add('hidden');
            }
        }

        // Função para exibir notificações (toast)
        const showToast = (message, duration = 3000) => {
            const toast = document.getElementById('toast');
            const toastMessage = document.getElementById('toast-message');
            toastMessage.textContent = message;
            toast.classList.remove('hidden');
            setTimeout(() => {
                toast.classList.add('hidden');
            }, duration);
        };

        // Função para definir o modo de visualização e atualizar a UI
        const setViewMode = (mode) => {
            currentViewMode = mode;
            localStorage.setItem('patientViewMode', mode); // Salva a preferência do usuário

            // ATUALIZA O ÍCONE DO BOTÃO
            if (mode === 'grid') {
                viewToggleIconGrid.classList.remove('hidden');
                viewToggleIconList.classList.add('hidden');
            } else {
                viewToggleIconGrid.classList.add('hidden');
                viewToggleIconList.classList.remove('hidden');
            }

            // A MÁGICA ACONTECE AQUI:
            // Em vez de ler da tela, re-renderizamos a lista usando a
            // variável 'currentPatientList', que tem os dados completos.
            renderPatientList(currentPatientList);
            
            // Esconde o menu dropdown
            viewToggleDropdown.classList.add('hidden');
        };

        /**
         * Adiciona um estado ao histórico do navegador para "capturar" o botão de voltar.
         * @param {string} modalId - O ID do modal que está sendo aberto.
         */
        function pushHistoryState(modalId) {
            const state = { modalOpen: modalId };
            // Adiciona um estado com um hash único para este modal
            history.pushState(state, `Modal ${modalId}`, `#${modalId}`);
        }

        /**
         * Remove o estado do histórico do modal, efetivamente voltando para a página principal.
         * Isso é chamado quando o modal é fechado manualmente (pelo botão 'X' ou 'Cancelar').
         */
        function clearHistoryState() {
            // Verifica se o estado atual é de um modal antes de voltar
            if (history.state && history.state.modalOpen) {
                history.back();
            }
        }

        // Função para configurar o comportamento do acordeão no formulário
        function setupFormAccordion() {
            const accordionContainer = document.getElementById('form-accordion-container');
            if (!accordionContainer) return;

            const toggleAccordionSection = (header) => {
                const content = header.nextElementSibling;
                const icon = header.querySelector('svg');
                const isExpanded = content.style.maxHeight && content.style.maxHeight !== '0px';

                // Fecha a seção clicada se ela já estiver aberta
                if (isExpanded) {
                    content.style.maxHeight = '0px';
                    icon.style.transform = 'rotate(0deg)';
                } else {
                    // Fecha todas as outras seções antes de abrir a nova
                    accordionContainer.querySelectorAll('.accordion-form-content').forEach(item => {
                        item.style.maxHeight = '0px';
                        const otherIcon = item.previousElementSibling.querySelector('svg');
                        if (otherIcon) otherIcon.style.transform = 'rotate(0deg)';
                    });

                    // Abre a seção clicada
                    content.style.maxHeight = content.scrollHeight + 'px';
                    icon.style.transform = 'rotate(180deg)';
                }
            };
            
            // Adiciona o listener de clique ao container (delegação de evento)
            accordionContainer.addEventListener('click', (e) => {
                const header = e.target.closest('.accordion-form-header');
                if (header) {
                    toggleAccordionSection(header);
                }
            });

            // Listener para fechar o modal de histórico
            closeModuleHistoryModalBtn.addEventListener('click', () => {
                moduleHistoryModal.classList.add('hidden');
                clearHistoryState();
            });


            // Abre a primeira seção por padrão para guiar o usuário
            const firstHeader = accordionContainer.querySelector('.accordion-form-header');
            if (firstHeader) {
                // Atraso pequeno para garantir que o DOM está pronto para calcular o scrollHeight
                setTimeout(() => {
                    toggleAccordionSection(firstHeader);
                }, 100);
            }
            // Adiciona listener para o botão de histórico de exames
            const formAccordionContainer = document.getElementById('form-accordion-container');
            if(formAccordionContainer) {
                formAccordionContainer.addEventListener('click', (e) => {
                    const targetId = e.target.id;

                    // Mapeia os links do sumário para os seus botões de toggle internos
                    const summaryLinksMap = {
                        'quick-view-exam-history-btn': 'toggle-exam-history-btn',
                        'quick-view-recent-meds-btn': 'toggle-recent-meds-btn' // Link para o histórico de medicações
                    };


                    if (summaryLinksMap[targetId]) {
                        e.preventDefault();
                        e.stopPropagation();

                        // 1. Identifica os elementos relevantes
                        const header = e.target.closest('.accordion-form-header');
                        const content = header.nextElementSibling;
                        const isAccordionClosed = !content.style.maxHeight || content.style.maxHeight === '0px';

                        const internalToggleButton = document.getElementById(summaryLinksMap[targetId]);
                        const internalList = internalToggleButton.nextElementSibling;
                        const isInternalListHidden = internalList.classList.contains('hidden');

                        // 2. PRIMEIRO, garante que a lista interna esteja visível.
                        // Isso é crucial para que o cálculo da altura do acordeão seja correto.
                        if (isInternalListHidden) {
                            internalToggleButton.click(); // Simula o clique no botão "> Ver histórico" para mostrar a lista
                        }

                        // 3. AGORA, se o acordeão principal estava fechado, manda ele abrir.
                        // Como a lista interna já está visível, o cálculo de `scrollHeight` será correto.
                        if (isAccordionClosed) {
                            header.click(); // Simula o clique no cabeçalho para abrir o acordeão
                        }
                        
                        // 4. Se o acordeão já estava aberto, mas a lista interna estava escondida,
                        // o clique no `internalToggleButton` mudou a altura do conteúdo, então recalculamos.
                        else if (!isAccordionClosed && isInternalListHidden) {
                            recalculateOpenAccordionHeight();
                        }
                    }
                });
            }

            // Listener para o formulário de monitoramento
            const monitoringContent = document.getElementById('monitoring-form-content');
            if (monitoringContent) {
                monitoringContent.addEventListener('input', (e) => {
                    // Verifica se o evento veio de um dos nossos inputs
                    if (e.target.classList.contains('monitoring-input')) {
                        updateMonitoringSummary();
                    }
                });
            }

            // Listener para o campo de Pendências
            const pendingObsTextarea = document.getElementById('form-pending-obs');
            if (pendingObsTextarea) {
                pendingObsTextarea.addEventListener('input', updatePendingObsSummary);
            }

        }

        // Atualiza o sumário de monitoramento com base nos valores dos inputs
        function updateMonitoringSummary() {
            const summaryContainer = document.getElementById('summary-monitoramento');
            if (!summaryContainer) return;

            const values = {
                'PA': document.getElementById('form-sv-pa').value,
                'FC': document.getElementById('form-sv-fc').value,
                'FR': document.getElementById('form-sv-fr').value,
                'SatO₂': document.getElementById('form-sv-sato2').value,
                'Temp': document.getElementById('form-sv-temp').value,
                'HGT': document.getElementById('form-sv-hgt').value
            };

            const summaryParts = [];
            // Constrói a parte dos sinais vitais
            for (const [key, value] of Object.entries(values)) {
                if (value) {
                    summaryParts.push(`<strong>${key}:</strong> ${value}`);
                }
            }

            let summaryHTML = '';
            if (summaryParts.length > 0) {
                summaryHTML = summaryParts.join(' <span class="text-gray-300">|</span> ');
            }
            
            // Adiciona o campo "Outros" se preenchido
            const othersValue = document.getElementById('form-sv-others').value;
            if (othersValue) {
                if (summaryHTML !== '') {
                    summaryHTML += ' <span class="text-gray-300">|</span> ';
                }
                // Mostra um trecho do campo "Outros"
                summaryHTML += `<strong>Outros:</strong> ${othersValue.substring(0, 20)}${othersValue.length > 20 ? '...' : ''}`;
            }

            if (summaryHTML === '') {
                summaryContainer.innerHTML = `<p class="italic text-gray-500">Nenhum dado de monitoramento inserido.</p>`;
            } else {
                summaryContainer.innerHTML = `<p class="font-mono text-xs md:text-sm">${summaryHTML}</p>`;
            }
        }

        // Atualiza o sumário de pendências e observações
        function updatePendingObsSummary() {
            const summaryContainer = document.getElementById('summary-pending-obs');
            const textArea = document.getElementById('form-pending-obs');
            if (!summaryContainer || !textArea) return;

            const text = textArea.value.trim();

            if (text === '') {
                // Mantém a mensagem padrão quando o campo está vazio
                summaryContainer.innerHTML = `<p class="italic text-gray-500">Nenhuma pendência ou observação.</p>`;
            } else {
                // Exibe o título "Observação:", remove o limite de 80 caracteres e adiciona a margem
                summaryContainer.innerHTML = `
                    <div class="mb-2">
                        <p class="text-sm text-gray-700">
                            <strong class="font-medium">Observação:</strong> ${text}
                        </p>
                    </div>
                `;
            }
        }

        function updateMedicationSummary(source = 'desconhecido') {
            const summaryContainer = document.getElementById('summary-medicacoes');
            if (!summaryContainer) return;

            if (!currentMedications || currentMedications.length === 0) {
                summaryContainer.innerHTML = `<p class="italic text-gray-500">Nenhuma medicação adicionada neste plantão.</p>`;
                return;
            }

            let html = '<div class="space-y-1">';
            currentMedications.forEach(med => {
                if (med.times && med.times.length > 0) {
                    // Reutiliza a mesma lógica de agrupamento da função renderMedicationTimes
                    const groupedByDay = med.times.reduce((acc, timestamp) => {
                        const date = new Date(timestamp);
                        const dayKey = date.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit' });
                        if (!acc[dayKey]) {
                            acc[dayKey] = [];
                        }
                        acc[dayKey].push(timestamp);
                        return acc;
                    }, {});
                    
                    const summaryParts = Object.keys(groupedByDay).sort().map(dayKey => {
                        const timesOnDay = groupedByDay[dayKey].sort().map(ts => 
                            new Date(ts).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }).replace(':', 'h')
                        );
                        return `${timesOnDay.join(', ')} (${dayKey})`;
                    });
                    
                    html += `<p class="text-sm text-gray-700"><span class="font-semibold">${med.name}</span> (às ${summaryParts.join(' e ')})</p>`;

                } else {
                    // Caso uma medicação seja adicionada mas nenhum horário ainda
                    html += `<p class="text-sm text-gray-700"><span class="font-semibold">${med.name}</span> (nenhum horário registrado)</p>`;
                }
            });
            html += `</div>`;
            summaryContainer.innerHTML = html;
        }

        // Atualiza o sumário de Diagnósticos e Comorbidades
        function updateDiagnosisSummary() {
            const summaryContainer = document.getElementById('summary-diagnostico');
            if (!summaryContainer) return;

            const diagnoses = getTagsFromContainer('diagnoses-tags-container');
            const comorbidities = getTagsFromContainer('comorbidities-tags-container');
            const allergies = getTagsFromContainer('allergies-tags-container');
            const evolutionTextarea = document.getElementById('form-evolution');
            const evolutionText = evolutionTextarea ? evolutionTextarea.value.trim() : '';

            // Condição de verificação
            if (diagnoses.length === 0 && comorbidities.length === 0 && allergies.length === 0) {
                summaryContainer.innerHTML = `<p class="italic text-gray-500">Nenhum diagnóstico, comorbidade ou alergia adicionado.</p>`;
                return;
            }

            let html = '';

            if (diagnoses.length > 0) {
                html += `
                    <div class="flex flex-wrap items-center gap-2">
                        <span class="font-medium text-sm mr-2">Diagnósticos:</span>
                        ${diagnoses.map(tag =>
                            `<span class="inline-block bg-gray-200 text-gray-800 text-xs font-semibold px-2 py-0.5 rounded-full">${tag}</span>`
                        ).join(' ')}
                    </div>
                `;
            }

            if (comorbidities.length > 0) {
                html += `
                    <div class="flex flex-wrap items-center gap-2 ${diagnoses.length > 0 ? 'mt-2' : ''}">
                        <span class="font-medium text-sm mr-2">Comorbidades:</span>
                        ${comorbidities.map(tag =>
                            `<span class="inline-block bg-slate-100 text-slate-700 text-xs font-semibold px-2 py-0.5 rounded-full">${tag}</span>`
                        ).join(' ')}
                    </div>
                `;
            }

            if (allergies.length > 0) {
                html += `
                    <div class="flex flex-wrap items-center gap-2 mt-2">
                        <span class="font-medium text-sm mr-2">Alergias:</span>
                        ${allergies.map(tag =>
                            `<span class="inline-block bg-gray-300 text-gray-900 text-xs font-semibold px-2 py-0.5 rounded-full">${tag}</span>`
                        ).join(' ')}
                    </div>
                `;
            }
            
            if (evolutionText !== '') {
                // Adiciona um separador se já houver tags
                if (html !== '') {
                    html += '<div class="mt-2 pt-2 border-t border-gray-200/75"></div>';
                }
                const preview = evolutionText.substring(0, 120); // Limita para não quebrar o layout
                html += `<p class="text-sm text-gray-700"><strong class="font-medium">Evolução:</strong> ${preview}${evolutionText.length > 120 ? '...' : ''}</p>`;
            }

            summaryContainer.innerHTML = html;
        }

        // --- LÓGICA DE EXAMES ---
        function updateExamSummary() {
            const summaryContainer = document.getElementById('summary-exames');
            if (!summaryContainer) return;

            const scheduled = patientExams.filter(e => e.status === 'scheduled').map(e => e.name);
            const pending = patientExams.filter(e => e.status === 'pending').map(e => e.name);
            const completed = patientExams.filter(e => e.status === 'completed').map(e => e.name);

            if (scheduled.length === 0 && pending.length === 0 && completed.length === 0) {
                summaryContainer.innerHTML = `<p class="italic text-gray-500">Nenhum exame registrado para este plantão.</p>`;
                return;
            }

            let html = '';
            if (scheduled.length > 0) {
                html += `<p class="text-sm"><strong class="font-medium">Agendados:</strong> ${scheduled.join(', ')}</p>`;
            }
            if (pending.length > 0) {
                html += `<p class="text-sm mt-1"><strong class="font-medium">Pendentes:</strong> ${pending.join(', ')}</p>`;
            }
            if (completed.length > 0) {
                html += `<p class="text-sm mt-1"><strong class="font-medium">Finalizados:</strong> ${completed.join(', ')}</p>`;
            }
            summaryContainer.innerHTML = html;
        }


        
        // --- LÓGICA DE EVENTOS (SETUP) ---

        addHandoversForm.addEventListener('input', () => {
            setUnsavedChanges(true);
        });

        // Listener para avisar sobre saída com alterações não salvas
        window.addEventListener('beforeunload', (event) => {
            if (hasUnsavedChanges) {
                event.preventDefault();
                // A string não é exibida na maioria dos navegadores modernos, mas é necessária para acionar o prompt
                event.returnValue = '';
            }
        });

        // Listener para abrir o modal de histórico completo (delegação de evento)
        document.getElementById('patient-detail-screen').addEventListener('click', (e) => {
            if (e.target && e.target.id === 'open-full-history-btn') {
                currentHistoryPage = 1; // Reseta para a primeira página ao abrir
                renderHandoversList(currentHandovers); // Re-renderiza para garantir que a página 1 seja exibida
                fullHistoryModal.classList.remove('hidden');
                pushHistoryState('full-history-modal');
            }
        });

        // Listener para fechar o modal
        closeFullHistoryModalBtn.addEventListener('click', () => {
            fullHistoryModal.classList.add('hidden');
        });

        // Listener unificado para os eventos DENTRO do modal (paginação, adendo, etc.)
        fullHistoryContentWrapper.addEventListener('click', (e) => {
            // Procura pelo botão principal do item da lista
            const historyItemButton = e.target.closest('.history-item-button');
            
            if (historyItemButton) {
                const handoverId = historyItemButton.dataset.handoverId;
                const handoverData = currentHandovers.find(h => h.id === handoverId);

                if (handoverData) {
                    // Preenche e exibe o modal de detalhes
                    populateHandoverViewModal(handoverData);
                    
                    // Esconde o modal de histórico e mostra o de visualização
                    fullHistoryModal.classList.add('hidden');
                    viewHandoverModal.classList.remove('hidden');
                }
            }
        });

        // Listener para os botões de paginação no modal de histórico
        document.getElementById('full-history-pagination').addEventListener('click', (e) => {
            const pageButton = e.target.closest('.page-button');
            if (pageButton && !pageButton.disabled) {
                const newPage = parseInt(pageButton.dataset.page, 10);
                if (newPage) {
                    currentHistoryPage = newPage;
                    // Re-renderiza a lista com a nova página
                    renderHandoversList(currentHandovers);
                }
            }
        });

        // Listeners para o botão de visualização
        viewToggleButton.addEventListener('click', (e) => {
            e.stopPropagation();
            viewToggleDropdown.classList.toggle('hidden');
        });



        document.querySelectorAll('.view-option-button').forEach(button => {
            button.addEventListener('click', (e) => {
                e.preventDefault();
                const newMode = e.currentTarget.dataset.view;
                setViewMode(newMode);
            });
        });


        // Seleciona os elementos do novo modal
        const addAdendoModal = document.getElementById('add-adendo-modal');
        const closeAdendoModalButton = document.getElementById('close-adendo-modal-button');
        const addAdendoForm = document.getElementById('add-adendo-form');
        const adendoHandoverIdInput = document.getElementById('adendo-handover-id');
        const adendoTextInput = document.getElementById('adendo-text');


        if (editPatientDetailsButton) {
            editPatientDetailsButton.addEventListener('click', () => {
                // Verifica se temos um paciente carregado na tela
                if (currentPatientData) {
                    // Reutiliza a função que já existe para abrir e popular o modal de edição
                    openEditModal(currentPatientData);
                } else {
                    showToast("Não há dados do paciente para editar.", "error");
                }
            });
        }

        // INÍCIO - LÓGICA REATORADA DO MÓDULO DE EXAMES

        // --- Estado e Elementos da UI para Exames ---
        let patientExams = []; // Array principal que guardará os objetos de exame
        const examEditorArea = document.getElementById('exam-editor-area');
        const examListsContainer = document.getElementById('exam-lists-container');
        const addNewExamBtn = document.getElementById('add-new-exam-btn');
        const examMainActionArea = document.getElementById('exam-main-action-area');

        // Mapeamento de todos os elementos do editor para fácil acesso
        const editor = {
            id: document.getElementById('exam-editor-id'),
            mode: document.getElementById('exam-editor-mode'),
            title: document.getElementById('exam-editor-title'),
            nameInputWrapper: document.getElementById('exam-editor-name-input-wrapper'),
            nameInput: document.getElementById('exam-editor-name-input'),
            nameDisplayWrapper: document.getElementById('exam-editor-name-display-wrapper'),
            nameDisplay: document.getElementById('exam-editor-name-display'),
            flowChoiceWrapper: document.getElementById('exam-editor-flow-choice-wrapper'),
            flowScheduleBtn: document.getElementById('exam-editor-flow-schedule-btn'),
            flowRegisterBtn: document.getElementById('exam-editor-flow-register-btn'),
            datetimeWrapper: document.getElementById('exam-editor-datetime-wrapper'),
            datetimeLabel: document.getElementById('exam-editor-datetime-label'),
            datetimeInput: document.getElementById('exam-editor-datetime-input'),
            resultChoiceWrapper: document.getElementById('exam-editor-result-choice-wrapper'),
            resultYesBtn: document.getElementById('exam-editor-result-yes-btn'),
            resultNoBtn: document.getElementById('exam-editor-result-no-btn'),
            resultTextareaWrapper: document.getElementById('exam-editor-result-textarea-wrapper'),
            resultTextarea: document.getElementById('exam-editor-result-textarea'),
            actionsWrapper: document.getElementById('exam-editor-actions-wrapper'),
            cancelBtn: document.getElementById('exam-editor-cancel-btn'),
            saveBtn: document.getElementById('exam-editor-save-btn')
        };

        const closeExamEditorBtn = document.getElementById('close-exam-editor-btn');


        // --- Funções Principais de Renderização ---

        /**
         * Converte uma string de data e hora no formato "DD/MM/YYYY HH:mm" para um objeto Date.
         * @param {string} dateTimeString - A string de data e hora (ex: "16/08/2025 18:59").
         * @returns {Date} - O objeto Date correspondente.
         */
        function parseBrazilianDateTime(dateTimeString) {
            if (!dateTimeString) {
                return new Date(); // Retorna a data atual se a string for vazia
            }
            
            // Usa uma expressão regular para extrair as partes da data e hora
            const parts = dateTimeString.match(/(\d{2})\/(\d{2})\/(\d{4}) (\d{2}):(\d{2})/);
            
            if (!parts) {
                console.warn(`Formato de data inválido recebido: ${dateTimeString}. Usando data atual.`);
                return new Date(); // Retorna data atual como fallback se o formato for incorreto
            }
            
            // parts[0] é a string completa, os grupos de captura começam em parts[1]
            // parts -> ["16/08/2025 18:59", "16", "08", "2025", "18", "59"]
            const day = parseInt(parts[1], 10);
            const month = parseInt(parts[2], 10) - 1; // Mês é 0-indexado em JS (Jan=0, Dez=11)
            const year = parseInt(parts[3], 10);
            const hours = parseInt(parts[4], 10);
            const minutes = parseInt(parts[5], 10);
            
            return new Date(year, month, day, hours, minutes);
        }

        /**
         * Renderiza os exames do estado `patientExams` nas colunas corretas.
         * AGORA, a lista de agendados é ordenada por data (do mais próximo ao mais distante).
         */
        function renderExams() {
            const lists = {
                scheduled: document.getElementById('scheduled-exams-list'),
                pending: document.getElementById('pending-exams-list'),
            };

            // Limpa as listas antes de renderizar
            Object.values(lists).forEach(list => list.innerHTML = '');

            // 1. Filtra e ORDENA apenas os exames agendados
            const scheduledExams = patientExams
                .filter(exam => exam.status === 'scheduled')
                .sort((a, b) => (a.timestamp || 0) - (b.timestamp || 0)); // Ordena do menor timestamp (mais antigo/próximo) para o maior

            // 2. Filtra os exames pendentes (a ordem deles não importa tanto)
            const pendingExams = patientExams.filter(exam => exam.status === 'pending');

            // 3. Renderiza cada lista ordenada
            scheduledExams.forEach(exam => {
                const examItemEl = createExamItemElement(exam);
                lists.scheduled.appendChild(examItemEl);
            });

            pendingExams.forEach(exam => {
                const examItemEl = createExamItemElement(exam);
                lists.pending.appendChild(examItemEl);
            });
        }

        /**
         * Cria o elemento HTML para um único item de exame com os botões CORRETOS para cada fluxo.
         * - Adiciona destaque visual para exames agendados com mais de 24h de atraso.
         * - Formata a data de exames pendentes para exibir apenas data/hora, sem "Hoje" ou "Amanhã".
         * @param {object} exam - O objeto de exame.
         * @returns {HTMLElement} - O elemento div do item de exame.
         */
        function createExamItemElement(exam) {
            const item = document.createElement('div');
            item.dataset.id = exam.id;

            // --- 1. Definições de data e hora atuais ---
            const now = new Date();
            const examDate = new Date(exam.timestamp || 0);
            const twentyFourHoursInMillis = 24 * 60 * 60 * 1000;

            // Verifica se o exame está agendado E se a data dele já passou há mais de 24 horas.
            const isOverdue = exam.status === 'scheduled' && (now.getTime() - examDate.getTime()) > twentyFourHoursInMillis;
            
            // Define as classes base do card
            let cardClasses = 'exam-item bg-white p-2 rounded border shadow-sm text-sm';
            
            // Se estiver atrasado, adiciona as classes de destaque com um vermelho bem claro
            if (isOverdue) {
                cardClasses += ' bg-red-50 border-red-200'; 
            }
            item.className = cardClasses;

            let formattedTime = '';
            if (exam.timestamp && typeof exam.timestamp === 'number') {
                switch(exam.status) {
                    case 'scheduled':
                        // Para agendados, MANTÉM a lógica de "HOJE" e "Amanhã"
                        const today = new Date();
                        today.setHours(0, 0, 0, 0);
                        const tomorrow = new Date(today);
                        tomorrow.setDate(today.getDate() + 1);

                        const examDayOnly = new Date(examDate);
                        examDayOnly.setHours(0, 0, 0, 0);
                        
                        const examTimeStr = examDate.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });

                        if (examDayOnly.getTime() === today.getTime()) {
                            formattedTime = `<strong>HOJE</strong> às ${examTimeStr}`;
                        } else if (examDayOnly.getTime() === tomorrow.getTime()) {
                            formattedTime = `Amanhã às ${examTimeStr}`;
                        } else {
                            formattedTime = examDate.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric', hour: '2-digit', minute: '2-digit' });
                        }
                        break;

                    case 'pending':
                        // Para pendentes, USA APENAS a data e hora cruas, sem substituições.
                        formattedTime = examDate.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric', hour: '2-digit', minute: '2-digit' });
                        break;
                    
                    default:
                        // Fallback para qualquer outro caso
                        formattedTime = 'Não especificado';
                }
            } else {
                formattedTime = 'Não especificado';
            }

            // --- 4. Montagem do HTML do card (permanece a mesma lógica) ---
            let html = `<p class="font-bold text-gray-800">${exam.name}</p>`;
            let actionsHtml = '';

            switch (exam.status) {
                case 'scheduled':
                    html += `<p class="text-xs text-gray-500">Agendado para: ${formattedTime}</p>`;
                    actionsHtml = `
                        <button type="button" data-action="register-realization" title="Registrar Realização" class="text-blue-600 hover:text-blue-800 transition-colors">
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75 11.25 15 15 9.75M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" /></svg>
                        </button>
                        <button type="button" data-action="reschedule" title="Reagendar" class="text-blue-600 hover:text-blue-800 transition-colors">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-6"><path fill-rule="evenodd" d="M12 5.25c1.213 0 2.415.046 3.605.135a3.256 3.256 0 0 1 3.01 3.01c.044.583.077 1.17.1 1.759L17.03 8.47a.75.75 0 1 0-1.06 1.06l3 3a.75.75 0 0 0 1.06 0l3-3a.75.75 0 0 0-1.06-1.06l-1.752 1.751c-.023-.65-.06-1.296-.108-1.939a4.756 4.756 0 0 0-4.392-4.392 49.422 49.422 0 0 0-7.436 0A4.756 4.756 0 0 0 3.89 8.282c-.017.224-.033.447-.046.672a.75.75 0 1 0 1.497.092c.013-.217.028-.434.044-.651a3.256 3.256 0 0 1 3.01-3.01c1.19-.09 2.392-.135 3.605-.135Zm-6.97 6.22a.75.75 0 0 0-1.06 0l-3 3a.75.75 0 1 0 1.06 1.06l1.752-1.751c.023.65.06 1.296.108 1.939a4.756 4.756 0 0 0 4.392 4.392 49.413 49.413 0 0 0 7.436 0 4.756 4.756 0 0 0 4.392-4.392c.017-.223.032-.447.046-.672a.75.75 0 0 0-1.497-.092c-.013.217-.028.434-.044.651a3.256 3.256 0 0 1-3.01 3.01 47.953 47.953 0 0 1-7.21 0 3.256 3.256 0 0 1-3.01-3.01 47.759 47.759 0 0 1-.1-1.759L6.97 15.53a.75.75 0 0 0 1.06-1.06l-3-3Z" clip-rule="evenodd" /></svg>
                        </button>
                        <button type="button" data-action="cancel" title="Cancelar Exame" class="text-red-600 hover:text-red-800 transition-colors">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6"><path fill-rule="evenodd" d="M5.47 5.47a.75.75 0 011.06 0L12 10.94l5.47-5.47a.75.75 0 111.06 1.06L13.06 12l5.47 5.47a.75.75 0 11-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 01-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 010-1.06z" clip-rule="evenodd" /></svg>
                        </button>
                    `;
                    break;
                case 'pending':
                    html += `<p class="text-xs text-gray-500">Realizado em: ${formattedTime}</p>`;
                    actionsHtml = `
                        <button type="button" data-action="register-result" title="Registrar Resultado" class="text-blue-600 hover:text-blue-800 transition-colors">
                            <svg class="size-6" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24"><path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 21a9 9 0 1 1 0-18c1.052 0 2.062.18 3 .512M7 9.577l3.923 3.923 8.5-8.5M17 14v6m-3-3h6"/></svg>
                        </button>
                        <button type="button" data-action="cancel" title="Cancelar Exame" class="text-red-600 hover:text-red-800 transition-colors">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6"><path fill-rule="evenodd" d="M5.47 5.47a.75.75 0 011.06 0L12 10.94l5.47-5.47a.75.75 0 111.06 1.06L13.06 12l5.47 5.47a.75.75 0 11-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 01-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 010-1.06z" clip-rule="evenodd" /></svg>
                        </button>
                    `;
                    break;
            }
            
            item.innerHTML = `
                <div class="flex justify-between items-start">
                    <div>${html}</div>
                    <div class="flex space-x-2 text-base">${actionsHtml}</div>
                </div>
            `;
            return item;
        }


        // --- Funções de Gerenciamento do Editor ---

        /**
         * Abre o editor de exames no modo de registro de resultado para um exame PENDENTE.
         * @param {string} examId - O ID do exame pendente.
         */
        function openEditorForResult(examId) {
            resetAndCloseExamEditor(); // Garante que o editor está limpo
            const moduleExames = document.getElementById('module-exames');
            if (moduleExames) {
                enterEditMode(moduleExames);
            }
            const exam = patientExams.find(e => e.id === examId);
            if (!exam) {
                showToast("Erro: Exame não encontrado para registrar o resultado.", "error");
                return;
            }

            // Preenche o editor com os dados do exame
            editor.mode.value = 'register-result';
            editor.id.value = examId;
            editor.title.textContent = `Registrar Resultado`;
            editor.nameDisplay.textContent = exam.name;
            
            // Mostra os wrappers relevantes
            editor.nameDisplayWrapper.classList.remove('hidden'); // Mostra o nome (não editável)
            editor.datetimeWrapper.classList.remove('hidden');   // Mostra o seletor de data/hora para a data do resultado
            editor.resultTextareaWrapper.classList.remove('hidden'); // Mostra a área para digitar o resultado
            editor.actionsWrapper.classList.remove('hidden');    // Mostra os botões Salvar/Cancelar
            
            // Configura o label e o calendário
            editor.datetimeLabel.textContent = "Data e Hora do Resultado";
            flatpickrInstance = flatpickr("#exam-editor-datetime-input", configRegistro); // Usa configRegistro para permitir datas passadas

            // Exibe o editor e esconde o botão "+ Novo Exame"
            examEditorArea.classList.remove('hidden');
            examMainActionArea.classList.add('hidden');
            
            editor.resultTextarea.focus(); // Foca na área de resultado para digitação imediata
        }

        /**
         * Reseta o editor de exames para seu estado inicial e o esconde.
         */

        // Função para ABRIR editor de NOVO EXAME
        function openEditorForNewExam() {
            resetAndCloseExamEditor(); // Primeiro, garante que tudo está limpo
            const moduleExames = document.getElementById('module-exames');
            if (moduleExames) {
                enterEditMode(moduleExames);
            }
            editor.mode.value = 'new';
            editor.title.textContent = 'Adicionar Novo Exame';
            editor.nameInputWrapper.classList.remove('hidden');
            editor.flowChoiceWrapper.classList.remove('hidden');
            examEditorArea.classList.remove('hidden');
            examMainActionArea.classList.add('hidden');
            editor.nameInput.focus();

            // Inicia o Flatpickr para AGENDAMENTO
            flatpickrInstance = flatpickr("#exam-editor-datetime-input", configAgendamento);
        }

        /**
         * NOVA FUNÇÃO
         * Abre o editor de exames no modo de reagendamento para um exame existente.
         * @param {string} examId - O ID do exame a ser reagendado.
         */
        function openEditorForReschedule(examId) {
            resetAndCloseExamEditor(); // Garante que o editor está limpo
            const moduleExames = document.getElementById('module-exames');
            if (moduleExames) {
                enterEditMode(moduleExames);
            }
            const exam = patientExams.find(e => e.id === examId);
            if (!exam) {
                showToast("Erro: Exame não encontrado para reagendar.", "error");
                return;
            }

            // Preenche o editor com os dados do exame
            editor.mode.value = 'reschedule';
            editor.id.value = examId;
            editor.title.textContent = `Reagendar Exame`;
            editor.nameDisplay.textContent = exam.name;
            
            // Mostra os wrappers relevantes
            editor.nameDisplayWrapper.classList.remove('hidden'); // Mostra o nome (não editável)
            editor.datetimeWrapper.classList.remove('hidden');   // Mostra o seletor de data/hora
            editor.actionsWrapper.classList.remove('hidden');    // Mostra os botões Salvar/Cancelar
            
            // Configura o label e o calendário
            editor.datetimeLabel.textContent = "Selecione a Nova Data e Hora";
            flatpickrInstance = flatpickr("#exam-editor-datetime-input", configAgendamento);

            // Exibe o editor e esconde o botão "+ Novo Exame"
            examEditorArea.classList.remove('hidden');
            examMainActionArea.classList.add('hidden');
        }

        // Função para ABRIR editor de REGISTRO DE REALIZAÇÃO
        function openEditorForRegistration(examId) {
            resetAndCloseExamEditor(); // Primeiro, garante que tudo está limpo
            const moduleExames = document.getElementById('module-exames');
            if (moduleExames) {
                enterEditMode(moduleExames);
            }
            const exam = patientExams.find(e => e.id === examId);
            if (!exam) return;

            editor.mode.value = 'register-realization';
            editor.id.value = examId;
            editor.title.textContent = `Registrar Realização: ${exam.name}`;
            editor.nameDisplay.textContent = exam.name;
            editor.nameDisplayWrapper.classList.remove('hidden');
            editor.datetimeInput.value = new Date().toISOString().slice(0, 16);
            editor.datetimeLabel.textContent = "Data e Hora da Realização";
            editor.datetimeWrapper.classList.remove('hidden');
            editor.resultChoiceWrapper.classList.remove('hidden');
            examEditorArea.classList.remove('hidden');
            examMainActionArea.classList.add('hidden');

            // Inicia o Flatpickr para REGISTRO (com maxDate)
            flatpickrInstance = flatpickr("#exam-editor-datetime-input", configRegistro);
        }

        // Função para RESETAR E FECHAR o editor (agora também destrói o calendário)
        function resetAndCloseExamEditor() {
            // 1. Destrói a instância ativa do Flatpickr, se houver
            if (flatpickrInstance) {
                flatpickrInstance.destroy();
                flatpickrInstance = null; // Limpa a referência
            }

            // 2. Esconde a área de edição e mostra o botão de "+ Novo Exame"
            examEditorArea.classList.add('hidden');
            examMainActionArea.classList.remove('hidden');
            
            // 3. Reseta os valores dos campos do editor
            editor.id.value = '';
            editor.mode.value = '';
            editor.nameInput.value = '';
            editor.datetimeInput.value = '';
            editor.resultTextarea.value = '';
            
            // 4. Esconde todos os 'wrappers' internos do editor
            Object.values(editor).forEach(element => {
                if (element.id && element.id.endsWith('-wrapper')) {
                    element.classList.add('hidden');
                }
            });
        }
        // --- Novos Listeners para o fluxo interno do editor ---

        // Cenário A: Escolhendo 'Agendar'
        editor.flowScheduleBtn.addEventListener('click', () => {
            editor.datetimeLabel.textContent = "Agendar Para";
            editor.datetimeWrapper.classList.remove('hidden');
            editor.actionsWrapper.classList.remove('hidden');
            editor.flowChoiceWrapper.classList.add('hidden');
        });

        // Cenário A: Escolhendo 'Registrar Realização'
        editor.flowRegisterBtn.addEventListener('click', () => {
            editor.datetimeLabel.textContent = "Data e Hora da Realização";
            editor.datetimeInput.value = new Date().toISOString().slice(0, 16);
            editor.datetimeWrapper.classList.remove('hidden');
            editor.resultChoiceWrapper.classList.remove('hidden');
            editor.flowChoiceWrapper.classList.add('hidden');
        });

        // Cenários A e B: Escolhendo 'Sim' para resultado
        editor.resultYesBtn.addEventListener('click', () => {
            editor.resultTextareaWrapper.classList.remove('hidden');
            editor.actionsWrapper.classList.remove('hidden');
            editor.resultChoiceWrapper.classList.add('hidden');
            editor.resultTextarea.focus();
        });

        // Cenários A e B: Escolhendo 'Não' para resultado
        editor.resultNoBtn.addEventListener('click', () => {
            editor.resultTextarea.value = ''; 
            editor.saveBtn.click(); // Salva o estado "pendente"
        });

        // Botão "Salvar" - O CÉREBRO DO MÓDULO
        editor.saveBtn.addEventListener('click', () => {
            const id = editor.id.value;
            const mode = editor.mode.value;
            const name = id ? editor.nameDisplay.textContent : editor.nameInput.value.trim();

            if (!name) {
                showToast("O nome do exame é obrigatório.", "error");
                return;
            }

            const timestamp = editor.datetimeInput.value ? parseBrazilianDateTime(editor.datetimeInput.value).getTime() : Date.now();
            const result = editor.resultTextarea.value.trim();

            // --- Lógica Principal ---
            if (mode === 'reschedule') {
                const examIndex = patientExams.findIndex(e => e.id === id);
                if (examIndex > -1) {
                    const oldTimestamp = patientExams[examIndex].timestamp;
                    patientExams[examIndex].timestamp = timestamp;

                    currentShiftRescheduledExams.push({
                        id: id, name: name, oldTimestamp: oldTimestamp, newTimestamp: timestamp
                    });
                    showToast(`Exame '${name}' reagendado.`, 'success');
                }
            } else {
                let newStatus;

                // --- INÍCIO DA CORREÇÃO PRINCIPAL ---
                // Verifica se o fluxo de "Registrar Realização" foi iniciado,
                // olhando se os botões de resultado (Sim/Não) ou a área de texto do resultado estavam visíveis.
                const wasResultFlowInitiated = !editor.resultChoiceWrapper.classList.contains('hidden') || !editor.resultTextareaWrapper.classList.contains('hidden');

                if (result) {
                    // 1. Se tem texto de resultado, está sempre 'completed'.
                    newStatus = 'completed';
                } else if (mode === 'register-realization' || (mode === 'new' && wasResultFlowInitiated)) {
                    // 2. Fica 'pending' se:
                    //    a) Estamos atualizando um exame agendado para registrar sua realização.
                    //    b) Estamos criando um NOVO exame e o usuário clicou em "Registrar Realização" (e depois em "Não").
                    newStatus = 'pending';
                } else {
                    // 3. Em todos os outros casos (principalmente um novo exame via botão "Agendar"),
                    // o status é 'scheduled'.
                    newStatus = 'scheduled';
                }
                // --- FIM DA CORREÇÃO PRINCIPAL ---

                if (mode === 'new') {
                    const newExam = {
                        id: `exam_${Date.now()}`, name, status: newStatus, timestamp, result: result || ''
                    };

                    if (newStatus === 'completed') {
                        currentShiftCompletedExams.push(newExam);
                    } else {
                        patientExams.push(newExam);
                    }
                } else { // Atualizando um exame existente
                    const examIndex = patientExams.findIndex(e => e.id === id);
                    if (examIndex > -1) {
                        const originalExam = patientExams.splice(examIndex, 1)[0];

                        if (newStatus === 'completed') {
                            const completedExam = { ...originalExam, status: 'completed', result: result, timestamp: timestamp };
                            currentShiftCompletedExams.push(completedExam);
                            showToast(`Resultado de '${name}' salvo!`, 'success');
                        } else if (newStatus === 'pending') {
                            originalExam.status = 'pending';
                            originalExam.timestamp = timestamp;
                            patientExams.push(originalExam);
                        }
                    }
                }
            }
            
            renderExams();
            resetAndCloseExamEditor();
            setUnsavedChanges(true);
        });

        // --- Lógica de Manipulação de Eventos ---

        // Botão principal "+ Novo Exame"
        addNewExamBtn.addEventListener('click', openEditorForNewExam);

        // Botão "Cancelar" dentro do editor
        editor.cancelBtn.addEventListener('click', resetAndCloseExamEditor);

        // Botão "X"
        if (closeExamEditorBtn) {
            closeExamEditorBtn.addEventListener('click', resetAndCloseExamEditor);
        }

        // Delegação de eventos para as ações nos itens das listas
        examListsContainer.addEventListener('click', (e) => {
            const button = e.target.closest('button[data-action]');
            if (!button) return;

            const action = button.dataset.action;
            const examItem = button.closest('.exam-item');
            const examId = examItem.dataset.id;

            console.log('Ação do botão clicado:', action);

            if (action === 'register-realization') {
                openEditorForRegistration(examId);
            } else if (action === 'register-result') {
                openEditorForResult(examId);
            } else if (action === 'cancel') {
                // Guarda o ID do exame a ser cancelado no próprio botão de confirmação do modal
                confirmCancelExamButton.dataset.examId = examId;
                // Mostra o novo modal de confirmação
                cancelExamConfirmModal.classList.remove('hidden');
            } else if (action === 'reschedule') {
                openEditorForReschedule(examId);
            }
        });

        // ==================================================================
        //        BLOCO DE CÓDIGO CORRIGIDO PARA O FLUXO 'NOVO EXAME'
        // ==================================================================

        // 1. Botão "Agendar" (dentro do fluxo de "Novo Exame")
        editor.flowScheduleBtn.addEventListener('click', () => {
            editor.datetimeLabel.textContent = "Agendar Para";
            editor.datetimeWrapper.classList.remove('hidden');
            editor.actionsWrapper.classList.remove('hidden');
            editor.flowChoiceWrapper.classList.add('hidden');

            // --- LÓGICA ADICIONADA ---
            if (flatpickrInstance) {
                flatpickrInstance.destroy();
            }
            // Inicializa com a configuração de AGENDAMENTO (minDate: 'today')
            flatpickrInstance = flatpickr("#exam-editor-datetime-input", configAgendamento);
        });

        // 2. Botão "Registrar Realização" (dentro do fluxo de "Novo Exame")
        editor.flowRegisterBtn.addEventListener('click', () => {
            editor.datetimeLabel.textContent = "Data e Hora da Realização";
            editor.datetimeInput.value = new Date().toISOString().slice(0, 16);
            editor.datetimeWrapper.classList.remove('hidden');
            editor.resultChoiceWrapper.classList.remove('hidden');
            editor.flowChoiceWrapper.classList.add('hidden');

            // --- LÓGICA ADICIONADA ---
            if (flatpickrInstance) {
                flatpickrInstance.destroy();
            }
            // Inicializa com a configuração de REGISTRO (maxDate: new Date()) - o seu calendário "antigo"
            flatpickrInstance = flatpickr("#exam-editor-datetime-input", configRegistro);
        });

        editor.resultYesBtn.addEventListener('click', () => {
            editor.resultTextareaWrapper.classList.remove('hidden');
            editor.actionsWrapper.classList.remove('hidden'); // Mostra Salvar/Cancelar
            editor.resultChoiceWrapper.classList.add('hidden');
            editor.resultTextarea.focus();
        });

        editor.resultNoBtn.addEventListener('click', () => {
            // Se clicou em "Não", o exame está pendente. Simulamos um clique no botão de salvar para finalizar a ação.
            editor.resultTextarea.value = ''; // Garante que não há resultado
            editor.saveBtn.click();
        });

        // FIM - LÓGICA REATORADA DO MÓDULO DE EXAMES

        // Evento de submit do formulário de login
        loginForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const email = loginForm['login-email'].value;
            const password = loginForm['login-password'].value;
            try {
                await signInWithEmailAndPassword(auth, email, password);
                showToast('Login realizado com sucesso!');
            } catch (error) {
                console.error("Erro no login:", error);
                showToast(`Erro: ${error.message}`);
            }
        });

        // Evento de submit do formulário de cadastro
        registerForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const name = registerForm['register-name'].value;
            const email = registerForm['register-email'].value;
            const password = registerForm['register-password'].value;

            if (!email.endsWith('@testeficticio.com')) {
                showToast('Erro: O e-mail deve ser do domínio @testeficticio.com');
                return;
            }

            try {
                const userCredential = await createUserWithEmailAndPassword(auth, email, password);
                await updateProfile(userCredential.user, { displayName: name });
                showToast('Conta criada com sucesso! Você será logado.');
            } catch (error) {
                console.error("Erro no cadastro:", error);
                showToast(`Erro: ${error.message}`);
            }
        });

        // Evento de clique no botão de logout com modal de confirmação
        logoutButton.addEventListener('click', () => {
            // 1. Pega os elementos do modal genérico
            const modal = document.getElementById('generic-confirm-modal');
            const title = modal.querySelector('#generic-confirm-title');
            const text = modal.querySelector('#generic-confirm-text');
            const confirmBtn = modal.querySelector('#generic-confirm-button');
            const cancelBtn = modal.querySelector('#generic-cancel-button');

            // 2. Personaliza o conteúdo do modal para a ação de "Sair"
            title.textContent = 'Confirmar Saída';
            text.textContent = 'Você tem certeza que deseja sair do sistema?';
            confirmBtn.textContent = 'Sim, Sair';
            cancelBtn.textContent = 'Cancelar';

            // 3. Usa a técnica de clonar os botões para limpar listeners antigos e evitar cliques múltiplos
            const newConfirmBtn = confirmBtn.cloneNode(true);
            confirmBtn.parentNode.replaceChild(newConfirmBtn, confirmBtn);

            const newCancelBtn = cancelBtn.cloneNode(true);
            cancelBtn.parentNode.replaceChild(newCancelBtn, cancelBtn);

            // 4. Define o que acontece ao clicar em "Sim, Sair"
            newConfirmBtn.onclick = async () => {
                try {
                    await signOut(auth);
                    showToast('Você saiu da sua conta.');
                    modal.classList.add('hidden'); // Esconde o modal após a ação
                } catch (error) {
                    console.error("Erro no logout:", error);
                    showToast(`Erro: ${error.message}`);
                }
            };

            // 5. Define o que acontece ao clicar em "Cancelar"
            newCancelBtn.onclick = () => {
                modal.classList.add('hidden');
            };

            // 6. Mostra o modal configurado
            modal.classList.remove('hidden');
        });

        // --- Eventos do Modal de Última Passagem ---
        if (showLastHandoverButton && lastHandoverModal) {
            showLastHandoverButton.addEventListener('click', () => {
                populateLastHandoverModal();
            });
        }
        if (closeLastHandoverModalBtn && lastHandoverModal) {
            closeLastHandoverModalBtn.addEventListener('click', () => {
                // Apenas esconde o modal, sem mexer no histórico.
                lastHandoverModal.classList.add('hidden');
            });
        }

        // Evento de clique no botão de voltar ao Painel
        backToDashboardButton.addEventListener('click', () => {
            if (hasUnsavedChanges) {
                if (!confirm('Você tem alterações não salvas. Deseja sair mesmo assim?')) {
                    return;
                }
                // Se o usuário confirmar, reseta a flag para permitir a navegação.
                hasUnsavedChanges = false;
            }
            
            // Em vez de usar history.back(), chamamos a função que controla a tela.
            // Ela já atualiza o URL para #painel e mostra a tela correta.
            showScreen('main'); 
            
            // A lista de pacientes já estará carregada devido à alteração anterior.
            // A função sortAndRenderPatientList() garante que a ordem de exibição esteja atualizada.
            sortAndRenderPatientList();

            currentPatientId = null;
            if (unsubscribeHandovers) unsubscribeHandovers();
        });

        // Navegação entre telas de login e cadastro
        document.getElementById('go-to-register').addEventListener('click', (e) => {
            e.preventDefault();
            showScreen('register');
        });
        document.getElementById('go-to-login').addEventListener('click', (e) => {
            e.preventDefault();
            showScreen('login');
        });

        // Abre/Fecha o modal de adicionar paciente
        addPatientButton.addEventListener('click', () => {
            addPatientModal.classList.remove('hidden');
        });
        closeModalButton.addEventListener('click', () => {
            addPatientModal.classList.add('hidden');
        });

        // Abre/Fecha o modal de resumo do paciente
        showSummaryButton.addEventListener('click', () => {
            showWeeklySummary();
        });

        closeSummaryModalButton.addEventListener('click', () => {
            patientSummaryModal.classList.add('hidden');
        });

        // Abre/Fecha o modal de editar paciente
        closeEditModalButton.addEventListener('click', () => {
            editPatientModal.classList.add('hidden');
        });

        // Fecha o modal de visualização de plantão
        closeViewHandoverModalBtn.addEventListener('click', () => {
            viewHandoverModal.classList.add('hidden');
        });

        // Adiciona um novo paciente
        addPatientForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const submitButton = e.target.querySelector('button[type="submit"]');
            submitButton.disabled = true;
            submitButton.innerHTML = `<svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>Salvando...`;

            try {
                // ALTERADO: Captura os dados do formulário em variáveis
                const newPatientFormData = {
                    name: document.getElementById('new-patient-name').value,
                    dob: document.getElementById('new-patient-dob').value,
                    patientNumber: document.getElementById('new-patient-number').value,
                    roomNumber: document.getElementById('new-patient-room').value,
                    createdAt: serverTimestamp(), // O servidor irá definir o tempo exato
                    lastUpdatedAt: serverTimestamp(), // NOVO: Importante para ordenação
                    status: 'ativo'
                };

                // ALTERADO: Pega a referência do documento recém-criado
                const docRef = await addDoc(collection(db, 'patients'), newPatientFormData);
                
                showToast('Paciente adicionado com sucesso!');
                addPatientForm.reset();
                addPatientModal.classList.add('hidden');
                showPatientDetail(docRef.id); 

                // --- INÍCIO DA SOLUÇÃO ---
                // 1. Cria um objeto local para o novo paciente
                const newPatientData = {
                    id: docRef.id,
                    ...newPatientFormData,
                    createdAt: Timestamp.now(), // Usa o tempo local para a UI
                    lastUpdatedAt: Timestamp.now() // Usa o tempo local para a UI
                };

                // 2. Adiciona o novo paciente no início da lista em memória
                currentPatientList.unshift(newPatientData);

                // 3. Re-ordena e re-desenha a lista na tela
                sortAndRenderPatientList(); 
                // --- FIM DA SOLUÇÃO ---

            } catch (error) {
                console.error("Erro ao adicionar paciente:", error);
                showToast(`Erro: ${error.message}`, 'error');
            } finally {
                submitButton.disabled = false;
                submitButton.innerHTML = 'Salvar Paciente';
                setUnsavedChanges(false);
            }
        });

        // Edita um paciente
        editPatientForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const submitButton = e.target.querySelector('button[type="submit"]');
            const justification = document.getElementById('edit-patient-justification').value;

            if (!justification.trim()) {
                showToast("A justificativa é obrigatória.", "error");
                return;
            }
            
            submitButton.disabled = true;
            submitButton.innerHTML = `
                <div class="flex items-center justify-center">
                    <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                    </svg>
                    Salvando...
                </div>`;

            try {
                const patientId = document.getElementById('edit-patient-id').value;
                const correctedData = {
                    name: document.getElementById('edit-patient-name').value,
                    dob: document.getElementById('edit-patient-dob').value,
                    patientNumber: document.getElementById('edit-patient-number').value,
                    roomNumber: document.getElementById('edit-patient-room').value,
                    lastUpdatedAt: serverTimestamp() // Adiciona timestamp de atualização
                };

                const adendoData = {
                    patientId,
                    justification,
                    correctedData,
                    professionalId: currentUser.uid,
                    professionalName: currentUser.displayName,
                    timestamp: serverTimestamp()
                };

                const batch = writeBatch(db);

                // 1. Salva o adendo no histórico
                const adendoRef = doc(collection(db, 'patients', patientId, 'adendos'));
                batch.set(adendoRef, adendoData);

                // 2. Atualiza os dados principais do paciente
                const patientRef = doc(db, 'patients', patientId);
                batch.update(patientRef, correctedData);

                await batch.commit();

                showToast('Adendo salvo e dados do paciente atualizados!');
                
                // Fecha o modal de edição
                editPatientModal.classList.add('hidden');
                
                // ATUALIZA MANUALMENTE A UI SEM RECARREGAR A PÁGINA
                if (currentPatientId === patientId) {
                    // Atualiza os dados em memória
                    currentPatientData = { ...currentPatientData, ...correctedData };
                    
                    // Atualiza o cabeçalho da página de detalhes
                    patientDetailName.textContent = correctedData.name;
                    patientDetailNumber.textContent = correctedData.patientNumber;
                    patientDetailRoom.textContent = correctedData.roomNumber;
                    patientDetailAge.textContent = `${calculateAge(correctedData.dob)} anos`;

                    // Atualiza a lista de pacientes em memória para que o painel esteja correto quando você voltar
                    const patientIndex = currentPatientList.findIndex(p => p.id === patientId);
                    if (patientIndex !== -1) {
                        currentPatientList[patientIndex] = {
                            ...currentPatientList[patientIndex],
                            ...correctedData,
                            lastUpdatedAt: Timestamp.now() // Usa o tempo local para a UI
                        };
                    }
                }

            } catch (error) {
                console.error("Erro ao salvar correção:", error);
                showToast(`Erro: ${error.message}`, 'error');
            } finally {
                submitButton.disabled = false;
                submitButton.textContent = 'Salvar Correção';
            }
        });

        // Filtra pacientes com base na busca
        ['input', 'keyup', 'search'].forEach(evt => 
            searchPatientInput.addEventListener(evt, filterPatients)
        );
        bedFilterButton.addEventListener('click', (e) => {
            e.stopPropagation(); // Impede que o clique feche o menu imediatamente
            bedFilterDropdown.classList.toggle('hidden');
        });

        bedFilterClearWrapper.addEventListener('click', (e) => {
            e.stopPropagation();
            clearBedSelection();
            filterPatients();
        });

        bedFilterClearButton.addEventListener('click', () => {
            clearBedSelection();
            filterPatients();
        });

        bedSearchInput.addEventListener('input', () => {
            const searchTerm = bedSearchInput.value.toLowerCase();
            const allBeds = bedFilterList.querySelectorAll('label');
            allBeds.forEach(label => {
                const bedNumber = label.textContent.toLowerCase();
                label.style.display = bedNumber.includes(searchTerm) ? 'flex' : 'none';
            });
        });

        bedFilterList.addEventListener('change', () => {
            updateBedFilterButtonState();
            filterPatients();
        });

        // --- Eventos dos Autocompletes do Formulário ---
        diagnosisInput.addEventListener('input', (e) => {
            const query = e.target.value;
            clearTimeout(debounceTimer);

            if (query.length < 3) {
                diagnosisAutocompleteList.classList.add('hidden');
                return;
            }

            debounceTimer = setTimeout(async () => {
                // ETAPA 1: Mostra o estado de carregamento imediatamente
                const onSelectCallback = (selectedValue) => {
                    const container = document.getElementById('diagnoses-tags-container');
                    container.appendChild(createListItem(selectedValue));
                    diagnosisInput.value = '';
                    updateDiagnosisSummary();
                    setUnsavedChanges(true);
                };

                renderAndPositionAutocomplete(
                    diagnosisInput,
                    diagnosisAutocompleteList,
                    [],
                    query,
                    onSelectCallback,
                    'loading'
                );

                // ETAPA 2: Lógica de busca (Firestore + Gemini)
                console.log(`--- Iniciando busca inteligente para diagnóstico: "${query}" ---`);
                const directResults = await searchFirestoreCID(query, diagnosisInput, diagnosisAutocompleteList, false);
                
                let isSearchSufficient = false;
                if (directResults.length > 0) {
                    const userSearchTokens = query.toLowerCase().normalize("NFD").replace(/[\u0300-\u036f]/g, "").split(' ').filter(token => token.length > 3);
                    if (userSearchTokens.length === 0) {
                        isSearchSufficient = true;
                    } else {
                        isSearchSufficient = directResults.some(result => {
                            const resultTokens = new Set(result.search_tokens_normalized);
                            return userSearchTokens.every(userToken => resultTokens.has(userToken));
                        });
                    }
                }

                let finalResults = [...directResults];
                if (!isSearchSufficient) {
                    const geminiSearchTerms = await getGeminiSuggestions(query);
                    if (geminiSearchTerms && geminiSearchTerms.length > 0) {
                        const geminiPromises = geminiSearchTerms.map(term => searchFirestoreCID(term, diagnosisInput, diagnosisAutocompleteList, false));
                        const geminiResults = (await Promise.all(geminiPromises)).flat();
                        finalResults.push(...geminiResults);
                    }
                }
                
                const uniqueResults = [...new Map(finalResults.map(item => [item.id, item])).values()];
                const userQueryTokens = query.toLowerCase().split(' ').filter(t => t.length > 2);
                uniqueResults.forEach(result => {
                    const resultTokens = new Set(result.search_tokens_normalized);
                    let relevanceScore = 0;
                    userQueryTokens.forEach(userToken => { if (resultTokens.has(userToken)) relevanceScore++; });
                    if (result.searchable_name_normalized.startsWith(query.toLowerCase())) relevanceScore += 10;
                    result.relevanceScore = relevanceScore;
                });
                uniqueResults.sort((a, b) => b.relevanceScore - a.relevanceScore);
                
                const finalResultNames = uniqueResults.map(r => r.name);

                // ETAPA 3: Renderiza o resultado final
                renderAndPositionAutocomplete(
                    diagnosisInput,
                    diagnosisAutocompleteList,
                    finalResultNames,
                    query,
                    onSelectCallback,
                    finalResultNames.length > 0 ? 'has_results' : 'no_results'
                );

            }, 750);
        });

        // Novo listener para o botão "Confirmar" do nome da medicação
        document.getElementById('confirm-med-name-btn').addEventListener('click', () => {
            const inputValue = medicationInput.value.trim();
            if (inputValue) {
                handleMedicationSelection(inputValue);
                document.getElementById('confirm-med-name-btn').classList.add('hidden');
                medicationInput.value = ''; // Limpa o input após a confirmação
            }
        });

        // Listener de INPUT atualizado para o campo de medicação
        medicationInput.addEventListener('input', (e) => {
            const query = e.target.value;
            const confirmBtn = document.getElementById('confirm-med-name-btn');
            clearTimeout(debounceTimer);

            if (query.trim()) {
                confirmBtn.classList.remove('hidden');
            } else {
                confirmBtn.classList.add('hidden');
            }

            if (query.length < 2) {
                medicationAutocompleteList.classList.add('hidden');
                return;
            }

            debounceTimer = setTimeout(async () => {
                // ETAPA 1: Mostra o estado de carregamento
                const onSelectCallback = (selectedValue) => {
                    medicationInput.value = selectedValue;
                    confirmBtn.classList.remove('hidden');
                    hideActiveAutocomplete();
                    medicationInput.focus();
                };

                renderAndPositionAutocomplete(
                    medicationInput,
                    medicationAutocompleteList,
                    [],
                    query,
                    onSelectCallback,
                    'loading'
                );

                // ETAPA 2: Lógica de busca
                let allResults = [];
                const directResults = await fetchMedicationSuggestions(query, medicationInput, medicationAutocompleteList, false);
                allResults.push(...directResults);
                
                if (directResults.length === 0) {
                    const geminiSearchTerms = await getGeminiMedicationSuggestions(query);
                    if (geminiSearchTerms.length > 0) {
                        const geminiPromises = geminiSearchTerms.map(term => fetchMedicationSuggestions(term, medicationInput, medicationAutocompleteList, false));
                        const geminiResults = (await Promise.all(geminiPromises)).flat();
                        allResults.push(...geminiResults);
                    }
                }

                const uniqueResults = [...new Set(allResults)];

                // ETAPA 3: Renderiza o resultado final
                renderAndPositionAutocomplete(
                    medicationInput,
                    medicationAutocompleteList,
                    uniqueResults,
                    query,
                    onSelectCallback,
                    uniqueResults.length > 0 ? 'has_results' : 'no_results' // <-- NOVO ESTADO
                );

            }, 500);
        });

        // Deleta um paciente (lógica com modal)
        deletePatientButton.addEventListener('click', () => {
            if (!currentPatientId) return;
            deleteConfirmModal.classList.remove('hidden');
        });

        const printPatientButton = document.getElementById('print-patient-button');

        printPatientButton.addEventListener('click', handlePrintSummary);

        cancelDeleteButton.addEventListener('click', () => {
            deleteConfirmModal.classList.add('hidden');
            clearHistoryState();
        });

        confirmDeleteButton.addEventListener('click', async () => {
            if (!currentPatientId || !currentUser) return;

            try {
                // Encontra o paciente na lista local para pegar o nome antes de remover
                const patientToArchive = currentPatientList.find(p => p.id === currentPatientId);
                const patientName = patientToArchive ? patientToArchive.name : 'O paciente';

                // Remove o paciente da lista de dados local
                currentPatientList = currentPatientList.filter(p => p.id !== currentPatientId);

                // Encontra o card do paciente no painel
                const patientCard = document.querySelector(`.patient-card[data-id="${currentPatientId}"], .patient-list-item[data-id="${currentPatientId}"]`);
                
                // Feedback Visual
                if (patientCard) {
                    // 1. Adiciona a classe que dispara a animação de desaparecimento
                    patientCard.classList.add('is-archiving');
                    
                    // 2. Remove o elemento do DOM APÓS a animação terminar (350ms)
                    setTimeout(() => {
                        patientCard.remove();
                    }, 350); 
                }

                deleteConfirmModal.classList.add('hidden');
                showScreen('main');
                
                // Melhora a mensagem de confirmação para ser mais específica
                showToast(`'${patientName}' foi arquivado com sucesso.`);

                // A operação no banco de dados continua em segundo plano
                const patientRef = doc(db, 'patients', currentPatientId);
                await updateDoc(patientRef, {
                    status: 'arquivado',
                    archivedAt: serverTimestamp(),
                    archivedBy: {
                        uid: currentUser.uid,
                        name: currentUser.displayName
                    }
                });

                currentPatientId = null;

            } catch (error) {
                console.error("Erro ao arquivar paciente:", error);
                showToast(`Erro ao arquivar no servidor: ${error.message}. Por favor, recarregue a página.`);
                loadInitialPatients(); 
            }
        });


        // --- INÍCIO DO BLOCO CORRIGIDO E CENTRALIZADO DE CLIQUES ---
        // Listener unificado para cliques, corrigindo remoção de tags e implementando "clicar fora".
        document.addEventListener('click', e => {
            
            /* --- LÓGICA PARA FECHAR MODAIS AO CLICAR NO FUNDO (OVERLAY) DESATIVADA ---
            const openModals = document.querySelectorAll('.fixed.inset-0.z-50:not(.hidden)');
            openModals.forEach(modal => {
                // A condição 'e.target === modal' verifica se o clique foi exatamente no elemento
                // de fundo (o overlay semi-transparente) e não em seus filhos (o conteúdo do modal).
                if (e.target === modal) {
                    modal.classList.add('hidden');
                }
            });
            */

            // --- LÓGICA PARA FECHAR MENUS DROPDOWN AO CLICAR FORA ---
            const openDropdowns = document.querySelectorAll('.dropdown-menu:not(.hidden), #view-toggle-dropdown:not(.hidden), #bed-filter-dropdown:not(.hidden)');
            openDropdowns.forEach(dropdown => {
                // Verifica se o clique NÃO foi no próprio dropdown NEM no botão que o abre.
                const triggerButton = dropdown.previousElementSibling; // O botão que vem antes do menu
                if (!dropdown.contains(e.target) && !triggerButton.contains(e.target)) {
                    dropdown.classList.add('hidden');
                }
            });
            
            // 1. Verifica se um seletor customizado (Riscos, etc.) está aberto
            const openSelector = document.querySelector('.custom-select-options:not(.hidden)');
            if (openSelector) {
                const isClickInsideSelector = openSelector.contains(e.target);
                const isClickOnTrigger = activeSelectorInfo && activeSelectorInfo.origin.contains(e.target);

                if (!isClickInsideSelector && !isClickOnTrigger) {
                    hideActiveAutocomplete();
                }
            }

            // --- Lógica original para as outras ações (REMANESCE AQUI) ---
            if (e.target.closest('.allergy-module-box') && !e.target.closest('.remove-item-btn')) {
                return;
            }

            const addItemTrigger = e.target.closest('.add-item-trigger-btn');
            const cancelBtn = e.target.closest('.cancel-action-btn');
            const historyBtn = e.target.closest('.module-history-btn');
            const removeItemBtn = e.target.closest('.remove-item-btn');
            const clickableArea = e.target.closest('.clickable-item-area');
            const customOption = e.target.closest('.custom-select-option');

            // Ação: Clicou em qualquer lugar do módulo de Precauções para adicionar
            const precautionsModule = e.target.closest('#module-precaucoes');

            if (precautionsModule && !precautionsModule.classList.contains('module-editing')) {
                // Verifica se o clique NÃO foi em um botão que já tem ação própria
                const isClickOnInteractiveElement = e.target.closest('.add-item-trigger-btn, .cancel-action-btn, .remove-item-btn, .module-history-btn, .input-wrapper');
                
                if (!isClickOnInteractiveElement) {
                    activatePrecautionsInput(precautionsModule);
                }
            }

            // Ação: Clicou em "+ Adicionar" (Precauções/Dispositivos)
            if (addItemTrigger) {
                e.preventDefault();
                const moduleCard = addItemTrigger.closest('.bg-white.rounded-lg.shadow');
                if (moduleCard) {
                    // ALTERADO: Lógica de UI agora está aqui
                    const triggerWrapper = moduleCard.querySelector('.trigger-wrapper');
                    const cancelWrapper = moduleCard.querySelector('.cancel-action-wrapper');
                    const inputWrapper = moduleCard.querySelector('.input-wrapper');
                    const inputField = inputWrapper?.querySelector('input[type="text"]');

                    triggerWrapper?.classList.add('hidden');
                    cancelWrapper?.classList.remove('hidden');
                    inputWrapper?.classList.remove('hidden');
                    inputField?.focus();
                    
                    enterEditMode(moduleCard);
                }
                return;
            }

            // Ação: Clicou em "Cancelar"
            if (cancelBtn) {
                e.preventDefault();
                const moduleCard = cancelBtn.closest('.bg-white.rounded-lg.shadow');
                
                // NOVO: Bloco de lógica específico para o módulo de dispositivos
                if (moduleCard && moduleCard.id === 'module-dispositivos') {
                    // Itera sobre os dispositivos adicionados nesta sessão
                    devicesAddedThisSession.forEach(deviceName => {
                        // 1. Remove do array de estado principal
                        currentCustomDevices = currentCustomDevices.filter(d => d !== deviceName);

                        // 2. Remove o elemento do DOM usando o dataset que adicionamos
                        const deviceElementToRemove = moduleCard.querySelector(`.device-item-box[data-device-name="${deviceName}"]`);
                        if (deviceElementToRemove) {
                            deviceElementToRemove.remove();
                        }
                    });

                    // 3. Limpa o rastreador da sessão
                    devicesAddedThisSession = [];
                }
                
                // O código existente para fechar o modo de edição continua aqui
                if (moduleCard) {
                    const triggerWrapper = moduleCard.querySelector('.trigger-wrapper');
                    const cancelWrapper = moduleCard.querySelector('.cancel-action-wrapper');
                    const inputWrapper = moduleCard.querySelector('.input-wrapper');
                    const inputField = inputWrapper?.querySelector('input[type="text"]');

                    triggerWrapper?.classList.remove('hidden');
                    cancelWrapper?.classList.add('hidden');
                    inputWrapper?.classList.add('hidden');
                    if (inputField) inputField.value = '';
                    
                    exitEditMode(moduleCard);
                }
                checkUnsavedChanges();
                return;
            }
            
            // Ação: Clicou para ver o histórico.
            if (historyBtn) {
                e.stopPropagation();
                const moduleName = historyBtn.dataset.module;
                if (moduleName) showModuleHistory(moduleName);
                return;
            }

            // Ação: Clicou no 'x' para remover uma tag.
            if (removeItemBtn) {
                // Apenas permite a remoção se o módulo ancestral estiver em modo de edição
                if (removeItemBtn.closest('.module-editing')) {
                    // NOVO: Verifica se a tag pertence aos módulos de Riscos ou Cuidados
                    const moduleCard = removeItemBtn.closest('.module-card');
                    if (moduleCard && (moduleCard.id === 'module-riscos' || moduleCard.id === 'module-cuidados-enfermagem')) {
                        // Se pertencer a um desses módulos, impede a remoção e não faz nada.
                        return;
                    }
                    // FIM DA NOVA VERIFICAÇÃO
                    
                    e.preventDefault();
                    e.stopPropagation();

                    const tagToRemove = removeItemBtn.parentElement;
                    const container = tagToRemove.parentElement;

                    // Remove a tag do DOM
                    tagToRemove.remove();
                    setUnsavedChanges(true); // Marca que há alterações não salvas

                    // Lógica específica para o container de alergias
                    if (container.id === 'allergies-tags-container') {
                        if (container.children.length === 0) {
                            const radioNo = document.getElementById('allergy-radio-no');
                            if (radioNo) {
                                radioNo.click(); 
                            }
                        }
                        updateAllergyPlaceholder();
                    }

                    // Atualiza os sumários relevantes
                    updateDiagnosisSummary();
                    updateAllergyTitleVisibility();
                }
                return; // Finaliza a execução para este evento de clique
            }

            // Ação: Clicou em uma opção de um menu customizado
            if (customOption) {
                e.preventDefault();
                if (activeSelectorInfo) {
                    const selectedValue = customOption.dataset.value;
                    const selectedText = customOption.dataset.text;
                    const { origin, container } = activeSelectorInfo;
                    const tagContainer = origin.querySelector('.tags-display-area');
                    if (tagContainer) {
                        tagContainer.innerHTML = '';
                        const newTag = createListItem(selectedText);
                        newTag.dataset.score = selectedValue;
                        tagContainer.appendChild(newTag);
                        setUnsavedChanges(true);
                        updateAllergyPlaceholder();
                    }
                    container.classList.add('hidden');
                    origin.parentElement.appendChild(container);
                    activeSelectorInfo = null;
                }
                updateLiveScores();
                return;
            }

            // Ação 6: Clicou em uma das áreas principais de um módulo.
            if (clickableArea) {
                e.preventDefault();
                e.stopPropagation();
                
                // Caso 6a: A área é um seletor customizado (Riscos, Cuidados, Consciência).
                if (clickableArea.dataset.risk || clickableArea.dataset.fugulin || clickableArea.dataset.monitoring) {
                    
                    // Busca pelo container de opções de forma mais robusta.
                    const wrapper = clickableArea.closest('.item-section-wrapper');
                    const optionsContainer = wrapper ? wrapper.querySelector('.custom-select-options') : null;
                    const moduleCard = clickableArea.closest('.bg-white.rounded-lg.shadow');
                    if (moduleCard) {
                        enterEditMode(moduleCard);
                    }


                    if (!optionsContainer) return; // Se não encontrar o container, para a execução.

                    if (optionsContainer === openSelector) {
                        optionsContainer.classList.add('hidden');
                        if (activeSelectorInfo) activeSelectorInfo.origin.parentElement.appendChild(optionsContainer);
                        activeSelectorInfo = null;
                        return;
                    }
                    if (openSelector) {
                         openSelector.classList.add('hidden');
                         if (activeSelectorInfo) activeSelectorInfo.origin.parentElement.appendChild(openSelector);
                    }
                    
                    activeSelectorInfo = { origin: clickableArea, container: optionsContainer };
                    const type = clickableArea.dataset.risk ? 'risk' : (clickableArea.dataset.fugulin ? 'fugulin' : 'monitoring');
                    const key = clickableArea.dataset.risk || clickableArea.dataset.fugulin || clickableArea.dataset.monitoring;
                    let optionsData;
                    if (type === 'risk') optionsData = riskOptions[key];
                    else if (type === 'fugulin') optionsData = fugulinOptions[key];
                    else optionsData = monitoringOptions[key];

                    if (optionsData) {
                        optionsContainer.innerHTML = optionsData.map(opt => `<div class="custom-select-option" data-value="${typeof opt === 'string' ? opt : opt.value}" data-text="${typeof opt === 'string' ? opt : opt.text}">${typeof opt === 'string' ? opt : opt.text}</div>`).join('');
                        const rect = clickableArea.getBoundingClientRect();
                        optionsContainer.style.top = `${window.scrollY + rect.bottom + 4}px`;
                        optionsContainer.style.left = `${window.scrollX + rect.left}px`;
                        optionsContainer.style.width = `${rect.width}px`;
                        document.body.appendChild(optionsContainer);
                        positionFloatingList(clickableArea, optionsContainer);
                        optionsContainer.classList.remove('hidden');
                    }
                } 
                // Caso 6b: A área é um campo de texto simples (Diagnóstico, Comorbidades, etc.).
                else {
                    const moduleCard = clickableArea.closest('.bg-white.rounded-lg.shadow');
                    if (moduleCard) {
                        enterEditMode(moduleCard);
                        // AJUSTE AQUI: Procura o input-wrapper DENTRO da área clicada
                        const inputWrapper = clickableArea.querySelector('.input-wrapper');
                        if (inputWrapper) {
                            inputWrapper.classList.remove('hidden');
                            const inputField = inputWrapper.querySelector('input[type="text"]');
                            if (inputField) inputField.focus();
                        }
                    }
                }
                return;
            }

            // Ação: Clicou fora de um módulo em edição
            const openModule = document.querySelector('.module-editing');
            if (openModule && !e.target.closest('.module-editing')) {

                // Condição especial para o Módulo de Medicações
                if (openModule.id === 'module-medicacoes') {
                    const medicationInput = document.getElementById('form-medications');
                    const timeEditor = document.getElementById('medication-time-editor');

                    // A mágica está aqui:
                    // SÓ FECHA SE o input de busca estiver vazio E o editor de tempo não estiver ativo.
                    if (medicationInput.value.trim() === '' && timeEditor.classList.contains('hidden')) {
                        showMedicationEditor(false); // Reseta a UI interna do módulo
                        exitEditMode(openModule);     // Remove a borda azul e o estado de edição
                    }
                } else {
                    // Comportamento padrão para todos os outros módulos
                    exitEditMode(openModule);
                }
            }
        });

        /**
         * Esconde a lista de autocomplete ou seletor customizado ativo,
         * retornando-o para seu contêiner original no DOM.
         */
        function hideActiveAutocomplete() {

            // --- LOG ADICIONADO ---
            if (activeAutocomplete || document.querySelector('.custom-select-options:not(.hidden)')) {
                console.log("[DEBUG] A função hideActiveAutocomplete foi chamada.");
            }
            
            // O resto da função continua igual...
            const openSelector = document.querySelector('.custom-select-options:not(.hidden)');
            if (openSelector && activeSelectorInfo) {
                openSelector.classList.add('hidden');
                openSelector.style.position = '';
                openSelector.style.top = '';
                openSelector.style.left = '';
                openSelector.style.width = '';
                openSelector.style.zIndex = '';
                if (activeSelectorInfo.origin && activeSelectorInfo.origin.parentElement) {
                    activeSelectorInfo.origin.parentElement.appendChild(openSelector);
                }
                activeSelectorInfo = null;
            }

            if (activeAutocomplete && activeAutocomplete.listElement) {
                const { listElement } = activeAutocomplete;
                listElement.classList.add('hidden');
                if (listElement.originalParent) {
                    listElement.style.position = '';
                    listElement.style.top = '';
                    listElement.style.left = '';
                    listElement.style.width = '';
                    listElement.style.zIndex = '';
                    listElement.originalParent.appendChild(listElement);
                }
                activeAutocomplete = null;
            }
        }

        // --- LISTENER CORRIGIDO E ROBUSTO PARA EVENTOS DE 'KEYDOWN' (TECLA PRESSIONADA) NO FORMULÁRIO ---
        addHandoversForm.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') {
                const targetInput = e.target;
                const inputValue = targetInput.value.trim();
                // Lógica para alergias
                if (targetInput.id === 'form-allergies') {
                    e.preventDefault();
                    const moduleCard = targetInput.closest('#module-diagnostico');
                    if (inputValue) {
                        const container = document.getElementById('allergies-tags-container');
                        container.appendChild(createListItem(inputValue));
                        targetInput.value = '';
                        setUnsavedChanges(true);
                        updateDiagnosisSummary();
                        updateAllergyTitleVisibility(); // Atualiza o título após adicionar tag
                    } else {
                        // Enter com campo vazio: fecha o input E o modo de edição
                        targetInput.parentElement.classList.add('hidden');
                        if (moduleCard) {
                            exitEditMode(moduleCard); 
                        }
                        updateAllergyTitleVisibility(); // Atualiza o título ao fechar
                    }
                    return;
                }

                // Mapeamento dos inputs que devem criar tags com Enter
                const enterToAddTagMap = {
                    'form-comorbidities': { containerId: 'comorbidities-tags-container' },
                    'form-precaucoes':    { containerId: 'precaucoes-container' },
                };

                const config = enterToAddTagMap[targetInput.id];

                // Se o input for um dos mapeados e tiver texto
                if (config) {
                    e.preventDefault();

                    if (inputValue) {
                        const moduleCard = targetInput.closest('.bg-white.rounded-lg.shadow');
                        
                        if (config.isDevice) {
                            addCustomDispositivo(inputValue, true);
                        } else {
                            const container = document.getElementById(config.containerId);
                            container.appendChild(createListItem(inputValue));
                        }
                        
                        setUnsavedChanges(true);
                        if (config.containerId === 'comorbidities-tags-container' || config.containerId === 'diagnoses-tags-container') {
                            updateDiagnosisSummary();
                        }
                        
                        if (moduleCard) {
                            // ALTERADO: Lógica de UI agora está aqui
                            const triggerWrapper = moduleCard.querySelector('.trigger-wrapper');
                            const cancelWrapper = moduleCard.querySelector('.cancel-action-wrapper');
                            const inputWrapper = moduleCard.querySelector('.input-wrapper');
                            
                            triggerWrapper?.classList.remove('hidden');
                            cancelWrapper?.classList.add('hidden');
                            inputWrapper?.classList.add('hidden');
                            if(targetInput) targetInput.value = '';

                            exitEditMode(moduleCard);
                        }
                    }
                } else if (targetInput.tagName !== 'TEXTAREA') {
                    e.preventDefault();
                }
            }
        });

        // Função para finalizar a edição de um campo de monitoramento
        function finishMonitoringInputEdit(inputElement) {
            const value = inputElement.value.trim();
            const wrapper = inputElement.closest('.clickable-item-area'); // Procura o pai mais próximo
            
            // Verificação de segurança: se não encontrar o wrapper, interrompe a execução
            if (!wrapper) {
                console.error("Não foi possível encontrar o contêiner '.clickable-item-area' para o input.", inputElement);
                return;
            }

            const displayArea = wrapper.querySelector('.monitoring-display-area');

            if (displayArea) {
                displayArea.textContent = value;
                displayArea.classList.remove('hidden'); // Mostra a área de texto novamente
            }

            inputElement.classList.add('hidden'); // Esconde o campo de input

            // Se o valor mudou, marca como "não salvo" e atualiza os scores
            if (inputElement.value !== (inputElement.dataset.originalValue || '')) {
                setUnsavedChanges(true);
                updateLiveScores();
            }
        }

        // Delegação de evento para os inputs do módulo de monitoramento
        document.getElementById('module-monitoramento').addEventListener('focusout', (e) => {
            if (e.target.classList.contains('monitoring-input')) {
                finishMonitoringInputEdit(e.target);
            }
        });

        document.getElementById('module-monitoramento').addEventListener('keydown', (e) => {
            if (e.key === 'Enter' && e.target.classList.contains('monitoring-input')) {
                e.preventDefault();
                finishMonitoringInputEdit(e.target);
            }
        });

        addHandoversForm.addEventListener('submit', async (e) => {
            e.preventDefault();

            const submitButton = e.target.querySelector('button[type="submit"]');
            submitButton.disabled = true;
            submitButton.innerHTML = `<div class="flex items-center justify-center">...Salvando...</div>`;

            try {
                // Se algum módulo ainda estiver em modo de edição, fecha ele antes de salvar.
                const openModule = document.querySelector('.module-editing');
                if (openModule && openModule.id !== 'module-medicacoes' && openModule.id !== 'module-exames') {
                    exitEditMode(openModule);
                }

                const batch = writeBatch(db);
                const patientRef = doc(db, 'patients', currentPatientId);
                const newHandoverRef = doc(collection(patientRef, 'handovers'));

                // --- INÍCIO DA CORREÇÃO ---
                // 1. DADOS VITAIS E SCORES SÃO CALCULADOS PRIMEIRO
                // Coleta todos os dados de monitoramento do formulário (com fallback para o estado anterior se vazio)
                const finalVitalsForSave = getFinalVitalsData();
                
                // Calcula o NEWS2 com os dados mais recentes
                const news2Result = calculateNEWS2(finalVitalsForSave);

                // Coleta os dispositivos atuais para o cálculo do Fugulin
                const devicesToSave = Array.from(document.querySelectorAll('#dispositivos-grid input[type="checkbox"]:checked')).map(chk => chk.value);

                // Calcula o Fugulin com os dados mais recentes
                const fugulinResult = calculateFugulin({
                    news2: news2Result,
                    dispositivos: devicesToSave,
                    medicamentos: currentMedications,
                    consciencia: finalVitalsForSave.consciencia,
                    cuidadoCorporal: getFugulinScoreFromDOMorState('cuidadoCorporal'),
                    motilidade:     getFugulinScoreFromDOMorState('motilidade'),
                    deambulacao:    getFugulinScoreFromDOMorState('deambulacao'),
                    alimentacao:    getFugulinScoreFromDOMorState('alimentacao'),
                    eliminacao:     getFugulinScoreFromDOMorState('eliminacao')
                });

                // 2. DADOS DOS OUTROS MÓDULOS SÃO COLETADOS
                const allergyRadioNo = document.getElementById('allergy-radio-no');
                let currentAllergies = allergyRadioNo && allergyRadioNo.checked 
                    ? [] 
                    : getItemsFromContainer('allergies-tags-container');

                const currentDiagnoses = getItemsFromContainer('diagnoses-tags-container');
                const currentComorbidities = getItemsFromContainer('comorbidities-tags-container');
                const currentPrecautions = getItemsFromContainer('precaucoes-container');
                const currentRisks = {
                    lpp: getItemsFromContainer('riscos-lpp-container'),
                    quedas: getItemsFromContainer('riscos-quedas-container'),
                    bronco: getItemsFromContainer('riscos-bronco-container'),
                    iras: getItemsFromContainer('riscos-iras-container')
                };
                
                const getFinalCareItem = (key) => {
                    const kebabKey = key.replace(/([A-Z])/g, '-$1').toLowerCase();
                    const items = getItemsFromContainer(`fugulin-${kebabKey}-container`);
                    if (items.length > 0) return items;
                    return originalPatientState.nursingCare?.[key] || [];
                };
                
                const nursingCareData = {
                    cuidadoCorporal: getFinalCareItem('cuidadoCorporal'),
                    motilidade:      getFinalCareItem('motilidade'),
                    deambulacao:     getFinalCareItem('deambulacao'),
                    alimentacao:     getFinalCareItem('alimentacao'),
                    eliminacao:      getFinalCareItem('eliminacao')
                };

                const evolutionText = document.getElementById('form-evolution').value.trim();
                const pendingObsText = document.getElementById('form-pending-obs').value.trim();
                
                // Monta o objeto de monitoramento para o handover usando os dados já coletados
                // Apenas os dados que foram efetivamente inseridos no formulário neste plantão serão salvos.
                const monitoringDataForHistory = {};
                const paInput = document.getElementById('form-sv-pa').value.trim();
                if (paInput) monitoringDataForHistory.pa = paInput;

                const fcInput = document.getElementById('form-sv-fc').value.trim();
                if (fcInput) monitoringDataForHistory.fc = fcInput;

                const frInput = document.getElementById('form-sv-fr').value.trim();
                if (frInput) monitoringDataForHistory.fr = frInput;

                const sato2Input = document.getElementById('form-sv-sato2').value.trim();
                if (sato2Input) monitoringDataForHistory.sato2 = sato2Input;

                const tempInput = document.getElementById('form-sv-temp').value.trim();
                if (tempInput) monitoringDataForHistory.temp = tempInput;

                const hgtInput = document.getElementById('form-sv-hgt').value.trim();
                if (hgtInput) monitoringDataForHistory.hgt = hgtInput;

                const othersInput = document.getElementById('form-sv-others').value.trim();
                if (othersInput) monitoringDataForHistory.others = othersInput;

                // O estado do checkbox de O2 e o nível de consciência são sempre informações do plantão atual.
                monitoringDataForHistory.o2Supplement = document.getElementById('form-sv-o2').checked;
                const conscienciaTag = document.querySelector('#monitoring-consciencia-container .item-text');
                if (conscienciaTag) {
                    monitoringDataForHistory.consciencia = [conscienciaTag.dataset.value];
                }
                
                const scheduledExamsToSave = patientExams.filter(e => e.status === 'scheduled');
                const pendingExamsToSave = patientExams.filter(e => e.status === 'pending');
                const completedExamsThisShift = [...currentShiftCompletedExams];

                // 3. AGORA, OS OBJETOS SÃO CONSTRUÍDOS COM OS DADOS CORRETOS E JÁ CALCULADOS
                const handoverData = {
                    professionalId: currentUser.uid, professionalName: currentUser.displayName,
                    timestamp: serverTimestamp(),
                    evolution: evolutionText,
                    pendingObs: pendingObsText,
                    monitoring: monitoringDataForHistory,
                    nursingCare: nursingCareData,
                    news2: news2Result, // <- Agora usa a variável correta
                    fugulin: fugulinResult, // <- Agora usa a variável correta
                    risks: currentRisks,
                    medications: currentMedications,
                    examsDone: completedExamsThisShift,
                    diagnoses: currentDiagnoses,
                    comorbidities: currentComorbidities,
                    allergies: currentAllergies,
                    precautions: currentPrecautions,
                    devices: devicesToSave,
                    scheduledExams: scheduledExamsToSave,
                    pendingExams: pendingExamsToSave,
                    rescheduledExams: currentShiftRescheduledExams,
                    changes: {
                        diagnoses: calculateDelta(originalPatientState.diagnoses, currentDiagnoses),
                        comorbidities: calculateDelta(originalPatientState.comorbidities, currentComorbidities),
                        allergies: calculateDelta(originalPatientState.allergies, currentAllergies),
                        precautions: calculateDelta(originalPatientState.precautions, currentPrecautions),
                        devices: calculateDelta(originalPatientState.devices, devicesToSave),
                        risks: {
                            lpp: calculateDelta(originalPatientState.risks.lpp, currentRisks.lpp),
                            quedas: calculateDelta(originalPatientState.risks.quedas, currentRisks.quedas),
                            bronco: calculateDelta(originalPatientState.risks.bronco, currentRisks.bronco),
                            iras: calculateDelta(originalPatientState.risks.iras, currentRisks.iras),
                        },
                        nursingCare: {
                            cuidadoCorporal: calculateDelta(originalPatientState.nursingCare.cuidadoCorporal, nursingCareData.cuidadoCorporal),
                            motilidade: calculateDelta(originalPatientState.nursingCare.motilidade, nursingCareData.motilidade),
                            deambulacao: calculateDelta(originalPatientState.nursingCare.deambulacao, nursingCareData.deambulacao),
                            alimentacao: calculateDelta(originalPatientState.nursingCare.alimentacao, nursingCareData.alimentacao),
                            eliminacao: calculateDelta(originalPatientState.nursingCare.eliminacao, nursingCareData.eliminacao),
                        },
                        fugulinScoreChange: (originalPatientState.lastFugulinScore !== fugulinResult.score)
                            ? { from: originalPatientState.lastFugulinScore || 'N/A', to: fugulinResult.score }
                            : null,
                        scheduledExams: calculateDeltaForExams(originalPatientState.scheduledExams, scheduledExamsToSave),
                        pendingExams: calculateDeltaForExams(originalPatientState.pendingExams, pendingExamsToSave),
                    }
                };

                const patientUpdateData = {
                    lastUpdatedAt: serverTimestamp(),
                    lastProfessionalName: currentUser.displayName,
                    activeDiagnoses: currentDiagnoses,
                    activeComorbidities: currentComorbidities,
                    activeAllergies: currentAllergies,
                    activeDevices: devicesToSave,
                    activePrecautions: currentPrecautions,
                    activeRisks: currentRisks,
                    activeNursingCare: nursingCareData,
                    activeScheduledExams: scheduledExamsToSave,
                    activePendingExams: pendingExamsToSave,
                    lastNews2Score: news2Result.score, // <- Agora usa a variável correta
                    lastNews2Level: news2Result.level, // <- Agora usa a variável correta
                    lastFugulinScore: fugulinResult.score, // <- Agora usa a variável correta
                    lastFugulinClassification: fugulinResult.classification, // <- Agora usa a variável correta
                    lastEvolution: evolutionText,
                    lastPendingObs: pendingObsText,
                    completedExams: arrayUnion(...completedExamsThisShift)
                };
                // --- FIM DA CORREÇÃO ---

                console.log("DADOS DO HANDOVER A SEREM SALVOS:", JSON.parse(JSON.stringify(handoverData)));
                console.log("DADOS DO PACIENTE A SEREM ATUALIZADOS:", JSON.parse(JSON.stringify(patientUpdateData)));

                batch.set(newHandoverRef, handoverData);
                batch.update(patientRef, patientUpdateData);
                await batch.commit();

                showToast("Passagem de plantão salva com sucesso!");

                currentPatientData = { ...currentPatientData, ...patientUpdateData };

                originalPatientState = {
                    ...originalPatientState,
                    ...patientUpdateData,
                    monitoring: finalVitalsForSave,
                    evolution: evolutionText,
                    pendingObs: pendingObsText
                };

                const patientIndex = currentPatientList.findIndex(p => p.id === currentPatientId);
                if (patientIndex !== -1) {
                    currentPatientList[patientIndex] = {
                        ...currentPatientList[patientIndex],
                        ...patientUpdateData,
                        lastUpdatedAt: Timestamp.now()
                    };
                }

                const newHandoverForUI = { ...handoverData, id: newHandoverRef.id, timestamp: Timestamp.now() };
                currentHandovers.unshift(newHandoverForUI);

                // Atualiza o cabeçalho da página de detalhes com os DADOS CORRETOS
                const patientDetailNews2 = document.getElementById('patient-detail-news2');
                const patientDetailFugulin = document.getElementById('patient-detail-fugulin');

                // Atualiza a tag NEWS2 no cabeçalho
                if (patientUpdateData.lastNews2Score !== undefined && patientUpdateData.lastNews2Level) {
                    const news2Classes = { 'Risco Baixo': 'news2-low', 'Risco Baixo-Médio': 'news2-low-medium', 'Risco Médio': 'news2-medium', 'Risco Alto': 'news2-high' };
                    const badgeClass = news2Classes[patientUpdateData.lastNews2Level] || 'bg-gray-100 text-gray-800';
                    patientDetailNews2.innerHTML = `<span class="text-xs font-medium px-2.5 py-0.5 rounded-full ${badgeClass}">NEWS: ${patientUpdateData.lastNews2Score} - ${patientUpdateData.lastNews2Level}</span>`;
                }

                // Atualiza a tag Fugulin no cabeçalho
                if (patientUpdateData.lastFugulinClassification) {
                    const fugulinClasses = { 'Cuidados Mínimos': 'fugulin-minimos', 'Cuidados Intermediários': 'fugulin-intermediarios', 'Cuidados de Alta Dependência': 'fugulin-alta-dependencia', 'Cuidados Semi-Intensivos': 'fugulin-semi-intensivos', 'Cuidados Intensivos': 'fugulin-intensivos' };
                    const badgeClass = fugulinClasses[patientUpdateData.lastFugulinClassification] || 'bg-gray-100 text-gray-800';
                    patientDetailFugulin.innerHTML = `<span class="status-badge text-xs font-medium px-2.5 py-0.5 rounded-full ${badgeClass}">Fugulin: ${patientUpdateData.lastFugulinScore} - ${patientUpdateData.lastFugulinClassification}</span>`;
                }
                
                renderHandoversList(currentHandovers);
                partiallyResetFormForNewShift();

            } catch (error) {
                console.error("Erro ao salvar passagem de plantão:", error);
                showToast(`Erro ao salvar: ${error.message}`, 'error');
            } finally {
                submitButton.disabled = false;
                submitButton.textContent = 'Salvar Passagem de Plantão';
                setUnsavedChanges(false);
            }
        });

        medicationsListContainer.addEventListener('click', e => {
            const medBlock = e.target.closest('.medication-block');
            if (!medBlock) return;
            const medName = medBlock.dataset.medName;

            if (e.target.closest('.add-med-time-btn-emoji')) {
                // Abre o editor de tempo para um card JÁ EXISTENTE
                openMedTimePicker(medName, medBlock);
            }
            
            // Lógica para o botão de excluir medicação
            if (e.target.closest('.remove-med-btn')) {
                // Pega as referências do editor de tempo
                const timeEditor = document.getElementById('medication-time-editor');
                const timeEditorLabel = document.getElementById('medication-time-editor-label');

                // Verifica se o editor de tempo está visível
                const isTimeEditorOpen = !timeEditor.classList.contains('hidden');

                if (isTimeEditorOpen) {
                    // Extrai o nome da medicação que está sendo editada no momento
                    const currentlyEditingMedName = timeEditorLabel.textContent.replace('Selecione o horário para ', '');

                    // Se a medicação que está sendo removida é a mesma que está sendo editada...
                    if (medName === currentlyEditingMedName) {
                        // ...então, retorna a UI para a etapa de busca de uma nova medicação.
                        showMedicationEditor('search');
                    }
                }
                currentMedications = currentMedications.filter(m => m.name !== medName);
                medBlock.remove();
                updateMedicationSummary('removeMedication');
                setUnsavedChanges(true);
            }
        });
        
        /**
         * Listener global para detectar cliques fora de um módulo em edição.
         * Se um clique ocorrer fora, o módulo volta ao seu estado normal (como se "Confirmar" fosse clicado).
         */
        window.addEventListener('click', (e) => {
            // 1. Procura se existe algum módulo em modo de edição
            const editingModule = document.querySelector('.module-editing');
            
            // 2. Se não houver nenhum, não faz nada
            if (!editingModule) {
                return;
            }

            // 3. Verifica se o alvo do clique (e.target) NÃO está dentro do módulo em edição
            // O .closest() retorna null se o clique for fora do elemento especificado.
            if (!e.target.closest('.module-editing')) {
                // 4. Se o clique foi fora, chama a função para sair do modo de edição
                exitEditMode(editingModule);
            }
        });
        
        // --- FUNÇÕES DE DADOS ---

        /**
         * Limpa todos os campos de entrada e exibição dentro do módulo de Monitoramento.
         */
        function resetMonitoringModule() {
            const module = document.getElementById('module-monitoramento');
            if (!module) return;

            // 1. Limpa os campos de texto de exibição
            const displayAreas = module.querySelectorAll('.monitoring-display-area');
            displayAreas.forEach(area => {
                area.textContent = '';
            });

            // 2. Limpa os valores dos inputs escondidos
            const inputFields = module.querySelectorAll('.monitoring-input');
            inputFields.forEach(input => {
                input.value = '';
            });

            // 3. Limpa a tag de seleção de Consciência
            const conscienciaContainer = document.getElementById('monitoring-consciencia-container');
            if (conscienciaContainer) {
                conscienciaContainer.innerHTML = '';
            }
            // E reseta o select escondido que pode ter sido usado para o NEWS2 (boa prática)
            const conscienciaSelect = document.getElementById('form-sv-consciencia');
            if (conscienciaSelect) {
            conscienciaSelect.value = 'A'; // Volta para o padrão 'Alerta'
            }


            // 4. Desmarca o checkbox de O₂
            const o2Checkbox = document.getElementById('form-sv-o2');
            if (o2Checkbox) {
                o2Checkbox.checked = false;
            }
            
            // 5. Garante que todas as áreas clicáveis estejam visíveis e os inputs escondidos
            module.querySelectorAll('.input-wrapper').forEach(wrapper => wrapper.classList.add('hidden'));
            module.querySelectorAll('.clickable-item-area').forEach(area => area.classList.remove('hidden'));
        }

        /**
         * Filtra o histórico de plantões e exibe as alterações de um módulo específico em um modal.
         * Exibe corretamente o histórico de Cuidados de Enfermagem.
         * Exibe os scores NEWS2 e Fugulin salvos no plantão correspondente.
         * @param {string} moduleName - O nome do módulo (ex: 'diagnostico', 'medicacoes').
         */
        function showModuleHistory(moduleName) {
            const moduleTitles = {
                diagnostico: '⚕️ Histórico de Diagnóstico e Evolução',
                precaucoes: '🛡️ Histórico de Precauções',
                riscos: '⚠️ Histórico de Riscos Assistenciais',
                medicacoes: '💉 Histórico de Medicações Administradas',
                dispositivos: '🔌 Histórico de Dispositivos',
                cuidados: '🩺 Histórico de Cuidados de Enfermagem',
                exames: '🧪 Histórico de Exames e Procedimentos',
                monitoramento: '📈 Histórico de Monitoramento',
                observacoes: '📝 Histórico de Observações Gerais',
            };

            moduleHistoryTitle.textContent = moduleTitles[moduleName] || 'Histórico do Módulo';

            if (!currentHandovers || currentHandovers.length === 0) {
                moduleHistoryContent.innerHTML = '<p class="text-center text-gray-500 italic py-4">Nenhum histórico de plantão encontrado para este paciente.</p>';
                moduleHistoryModal.classList.remove('hidden');
                return;
            }

            let historyHtml = '';
            let entryCount = 0;

            const formatChangeList = (delta, prefix = '') => {
                let log = '';
                if (delta?.added?.length) {
                    log += delta.added.map(item => `<li><span class="text-green-600 font-bold">+</span> ${prefix}: <strong>${item}</strong></li>`).join('');
                }
                if (delta?.removed?.length) {
                    log += delta.removed.map(item => `<li><span class="text-red-600 font-bold">-</span> ${prefix}: <strong>${item}</strong></li>`).join('');
                }
                return log;
            };

            currentHandovers.forEach(h => {
                const date = h.timestamp?.toDate ? h.timestamp.toDate().toLocaleString('pt-BR', { dateStyle: 'short', timeStyle: 'short' }) : 'Data indefinida';
                const professional = h.professionalName || 'Profissional não identificado';
                let moduleEntryContent = '';
                const changes = h.changes || {};
                
                // NOVO: Bloco para adicionar scores
                let scoresHtml = '';
                if (moduleName === 'monitoramento' && h.news2?.score !== undefined) {
                    scoresHtml = `<div class="mt-2 pt-2 border-t border-dashed border-gray-200 font-mono text-xs text-gray-500"><strong>NEWS no plantão:</strong> ${h.news2.score} (${h.news2.level})</div>`;
                }
                if (moduleName === 'cuidados' && h.fugulin?.score !== undefined) {
                    scoresHtml = `<div class="mt-2 pt-2 border-t border-dashed border-gray-200 font-mono text-xs text-gray-500"><strong>Fugulin no plantão:</strong> ${h.fugulin.score} (${h.fugulin.classification})</div>`;
                }

                switch (moduleName) {
                    case 'diagnostico':
                        let diagLog = '';
                        if (changes.evolution) diagLog += `<li>📄 <strong>Evolução/Plano atualizado:</strong> "<i>${changes.evolution}</i>"</li>`;
                        diagLog += formatChangeList(changes.diagnoses, 'Diagnóstico');
                        diagLog += formatChangeList(changes.comorbidities, 'Comorbidade');
                        diagLog += formatChangeList(changes.allergies, 'Alergia');
                        if (diagLog) moduleEntryContent = `<ul class="list-none space-y-1">${diagLog}</ul>`;
                        break;

                    case 'precaucoes':
                        if (changes.precautions && (changes.precautions.added.length > 0 || changes.precautions.removed.length > 0)) {
                            moduleEntryContent = `<ul class="list-none space-y-1">${formatChangeList(changes.precautions, 'Precaução')}</ul>`;
                        }
                        break;

                    case 'riscos':
                        let riskLog = '';
                        const riskLabels = {
                            lpp: 'Risco LPP', quedas: 'Risco Quedas', bronco: 'Risco Broncoaspiração', iras: 'Risco IRAS'
                        };

                        if (changes.risks) {
                            for (const key in changes.risks) {
                                const delta = changes.risks[key];
                                // Lógica de Substituição
                                if (delta.removed?.length === 1 && delta.added?.length === 1) {
                                    riskLog += `<li>🔄 <strong>${riskLabels[key]}</strong> alterado para "<strong>${delta.added[0]}</strong>", antes estava "<s class="text-gray-500">${delta.removed[0]}</s>"</li>`;
                                } else { // Lógica para adições/remoções simples
                                    riskLog += formatChangeList(delta, riskLabels[key]);
                                }
                            }
                        }

                        if (riskLog) moduleEntryContent = `<ul class="list-none space-y-1">${riskLog}</ul>`;
                        break;
                    
                    case 'dispositivos':
                        if (changes.devices && (changes.devices.added.length > 0 || changes.devices.removed.length > 0)) {
                            moduleEntryContent = `<ul class="list-none space-y-1">${formatChangeList(changes.devices, 'Dispositivo')}</ul>`;
                        }
                        break;

                    case 'cuidados':
                        let careLog = '';
                        let itemChanged = false; // Flag para saber se um item foi alterado
                        const careLabels = {
                            cuidadoCorporal: 'Cuidado Corporal', motilidade: 'Motilidade', deambulacao: 'Deambulação',
                            alimentacao: 'Alimentação', eliminacao: 'Eliminação'
                        };

                        if (changes.nursingCare) {
                            for (const key in changes.nursingCare) {
                                const delta = changes.nursingCare[key];
                                // Lógica de Substituição: 1 item removido e 1 item adicionado
                                if (delta.removed?.length === 1 && delta.added?.length === 1) {
                                    careLog += `<li>🔄 <strong>${careLabels[key]}</strong> alterado para "<strong>${delta.added[0]}</strong>", antes estava "<s class="text-gray-500">${delta.removed[0]}</s>"</li>`;
                                    itemChanged = true;
                                } else { // Lógica para adições/remoções simples
                                    if (delta.added?.length > 0) {
                                        careLog += formatChangeList({ added: delta.added }, careLabels[key]);
                                        itemChanged = true;
                                    }
                                    if (delta.removed?.length > 0) {
                                        careLog += formatChangeList({ removed: delta.removed }, careLabels[key]);
                                        itemChanged = true;
                                    }
                                }
                            }
                        }

                        // Se NENHUM item mudou, mas o score Fugulin mudou, mostra isso.
                        // A nova condição verifica se o valor anterior não era 'N/A'
                        if (!itemChanged && changes.fugulinScoreChange && changes.fugulinScoreChange.from !== 'N/A') {
                            careLog += `<li>📊 Score Fugulin alterado de <strong>${changes.fugulinScoreChange.from}</strong> para <strong>${changes.fugulinScoreChange.to}</strong> devido a outras atualizações (ex: monitoramento, dispositivos).</li>`;
                        }

                        if (careLog) moduleEntryContent = `<ul class="list-none space-y-1">${careLog}</ul>`;
                        break;

                    case 'medicacoes':
                        if (h.medications && h.medications.length > 0) {
                            moduleEntryContent = `<ul class="list-disc pl-5">${h.medications.map(m => `<li>Administrado <strong>${m.name}</strong> às ${m.times.map(ts => new Date(ts).toLocaleTimeString('pt-BR', {hour: '2-digit', minute:'2-digit'})).join(', ')}</li>`).join('')}</ul>`;
                        }
                        break;

                    case 'exames':
                        let examLog = '';

                        // 1. Exames FINALIZADOS (✓)
                        if (Array.isArray(h.examsDone) && h.examsDone.length > 0) {
                            examLog += h.examsDone.map(exam =>
                                `<li><span class="text-green-600" title="Finalizado">✓</span> Finalizou <strong>${exam.name}</strong> com resultado: "<i>${exam.result || 'não informado'}</i>"</li>`
                            ).join('');
                        }

                        // 2. Exames REAGENDADOS (🔄)
                        if (Array.isArray(h.rescheduledExams) && h.rescheduledExams.length > 0) {
                            examLog += h.rescheduledExams.map(exam => 
                                `<li><span title="Reagendado">🔄</span> Reagendou <strong>${exam.name}</strong> de <s class="text-gray-500">${formatDate(exam.oldTimestamp)}</s> para <strong>${formatDate(exam.newTimestamp)}</strong></li>`
                            ).join('');
                        }
                        
                        // // 3. Exames AGENDADOS (📅) e CANCELADOS (❌)
                        if (changes.scheduledExams) {
                            if (changes.scheduledExams?.added?.length > 0) {
                                examLog += changes.scheduledExams.added.map(examObj =>
                                    `<li><span title="Agendado">📅</span> Agendou: <strong>${examObj.name}</strong> para ${formatDate(examObj.timestamp)}</li>`
                                ).join('');
                            }
                            if (changes.scheduledExams?.removed?.length > 0) {
                                examLog += changes.scheduledExams.removed.map(examObj =>
                                    `<li><span class="text-red-600" title="Cancelado">❌</span> Cancelou Agendamento: <strong>${examObj.name}</strong></li>`
                                ).join('');
                            }
                        }

                        // 4. Exames REALIZADOS (🔬) e CANCELADOS PENDENTES (❌)
                        if (changes.pendingExams) {
                            if (changes.pendingExams?.added?.length > 0) {
                                examLog += changes.pendingExams.added.map(examObj =>
                                    `<li><span title="Realizado">🔬</span> Realizou: <strong>${examObj.name}</strong> em ${formatDate(examObj.timestamp)} (aguardando resultado)</li>`
                                ).join('');
                            }
                            if (changes.pendingExams.removed?.length > 0) {
                                examLog += changes.pendingExams.removed.map(examObj =>
                                    `<li><span class="text-red-600" title="Cancelado">❌</span> Cancelou Exame Pendente: <strong>${examObj.name}</strong></li>`
                                ).join('');
                            }
                        }

                        // Se qualquer uma das condições acima produziu um log, preenche o conteúdo
                        if (examLog) {
                            moduleEntryContent = `<ul class="list-none space-y-1">${examLog}</ul>`;
                        }
                        break;

                    case 'monitoramento':
                        if (h.monitoring && Object.values(h.monitoring).some(v => v)) {
                            const vitals = Object.entries(h.monitoring).filter(([_, val]) => val).map(([key, val]) => `${key.toUpperCase()}: <strong>${val}</strong>`).join(' | ');
                            if (vitals) moduleEntryContent = `<p class="font-mono text-sm">${vitals}</p>`;
                        }
                        break;

                    case 'observacoes':
                        if (changes.pendingObs) {
                            moduleEntryContent = `<p class="italic">"<strong>${changes.pendingObs}</strong>"</p>`;
                        }
                        break;
                }

                if ((moduleEntryContent && moduleEntryContent.replace(/<[^>]*>/g, '').trim() !== '') || scoresHtml) {
                    entryCount++;
                    historyHtml += `
                        <div class="p-3 border rounded-md bg-gray-50">
                            <p class="text-xs text-gray-500">Em ${date} por <strong>${professional}</strong></p>
                            <div class="mt-1 text-sm text-gray-800">${moduleEntryContent || ''}${scoresHtml}</div>
                        </div>
                    `;
                }
            });

            if (entryCount === 0) {
                historyHtml = '<p class="text-center text-gray-500 italic py-4">Nenhuma alteração registrada para este módulo no histórico.</p>';
            }

            moduleHistoryContent.innerHTML = historyHtml;
            moduleHistoryModal.classList.remove('hidden'); // Garante que o modal apareça no final
        }

        function calculateAge(dobString) {
            if (!dobString) return '?';
            const birthDate = new Date(dobString);
            const today = new Date();
            let age = today.getFullYear() - birthDate.getFullYear();
            const m = today.getMonth() - birthDate.getMonth();
            if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) {
                age--;
            }
            return age;
        }

        /**
         * Formata um timestamp (número ou objeto do Firestore) para uma string legível (DD/MM HH:mm).
         * @param {object|number|null} timestamp - O timestamp a ser formatado.
         * @returns {string} - A data e hora formatadas, ou uma string padrão se a data for inválida.
         */
        function formatDate(timestamp) {
            if (!timestamp) return 'Data não especificada';

            try {
                // Converte o timestamp para um objeto Date, seja ele um número ou um objeto do Firestore
                const date = timestamp.toDate ? timestamp.toDate() : new Date(timestamp);
                
                // Verifica se a data resultante é válida
                if (isNaN(date.getTime())) {
                    return 'Data inválida';
                }

                return date.toLocaleString('pt-BR', {
                    day: '2-digit',
                    month: '2-digit',
                    hour: '2-digit',
                    minute: '2-digit'
                });
            } catch (error) {
                console.error("Erro ao formatar data:", error);
                return 'Erro na data';
            }
        }

        /**
         * Compara duas listas de objetos de exame (baseado em seus IDs) e retorna o que foi adicionado e removido.
         * @param {Array<object>} originalExams - A lista de exames antes da edição.
         * @param {Array<object>} currentExams - A lista de exames após a edição.
         * @returns {{added: Array<object>, removed: Array<object>}}
         */
        function calculateDeltaForExams(originalExams = [], currentExams = []) {
            const originalIdSet = new Set(originalExams.map(exam => exam.id));
            const currentIdSet = new Set(currentExams.map(exam => exam.id));

            const added = currentExams.filter(exam => !originalIdSet.has(exam.id));
            const removed = originalExams.filter(exam => !currentIdSet.has(exam.id));

            return { added, removed };
        }


        /**
         * Calcula o score NEWS2 com base nos parâmetros fisiológicos.
         * @param {object} vitals - Objeto contendo os sinais vitais.
         * @returns {object} - Objeto com o score total, o nível de risco e o status do O2.
         */
        function calculateNEWS2(vitals) {
            let score = 0;
            const { fr, satO2, o2Supplement, pa, fc, consciencia, temp } = vitals;

            // Se o valor não for um número válido, a condição será falsa e não pontuará.
            // 1. Frequência Respiratória (FR)
            if (!isNaN(fr)) {
                if (fr <= 8) score += 3;
                else if (fr >= 9 && fr <= 11) score += 1;
                else if (fr >= 21 && fr <= 24) score += 2;
                else if (fr >= 25) score += 3;
            }

            // 2. Saturação de Oxigênio (SatO2)
            if (!isNaN(satO2)) {
                if (satO2 <= 91) score += 3;
                else if (satO2 >= 92 && satO2 <= 93) score += 2;
                else if (satO2 >= 94 && satO2 <= 95) score += 1;
            }
            
            // 3. O₂ Suplementar
            if (o2Supplement) score += 2;

            // 4. Pressão Arterial Sistólica (PAS)
            if (!isNaN(pa)) {
                if (pa <= 90) score += 3;
                else if (pa >= 91 && pa <= 100) score += 2;
                else if (pa >= 101 && pa <= 110) score += 1;
                else if (pa >= 220) score += 3;
            }

            // 5. Frequência Cardíaca (FC)
            if (!isNaN(fc)) {
                if (fc <= 40) score += 3;
                else if (fc >= 41 && fc <= 50) score += 1;
                else if (fc >= 91 && fc <= 110) score += 1;
                else if (fc >= 111 && fc <= 130) score += 2;
                else if (fc >= 131) score += 3;
            }

            // 6. Nível de Consciência
            if (consciencia !== 'A') score += 3;

            // 7. Temperatura
            if (!isNaN(temp)) {
                if (temp <= 35.0) score += 3;
                else if (temp >= 35.1 && temp <= 36.0) score += 1;
                else if (temp >= 38.1 && temp <= 39.0) score += 1;
                else if (temp >= 39.1) score += 2;
            }

            // Determinar o nível de risco
            let level = 'Risco Baixo';
            if (score >= 7) {
                level = 'Risco Alto';
            } else if (score >= 5) {
                level = 'Risco Médio';
            } else if (score >= 1 && score <= 4) {
                const hasIndividualScoreOf3 = [
                    fr <= 8 || fr >= 25,
                    satO2 <= 91,
                    pa <= 90 || pa >= 220,
                    fc <= 40 || fc >= 131,
                    consciencia !== 'A',
                    temp <= 35.0
                ].some(condition => condition);
                
                // Eleva para 'Risco Médio' se um parâmetro individual pontuar 3, 
                // OU se o score total for 3 ou 4 E o paciente estiver em uso de O2.
                if (hasIndividualScoreOf3 || (score >= 3 && o2Supplement)) {
                    level = 'Risco Médio';
                } else {
                    level = 'Risco Baixo-Médio';
                }
            }
            
            // Retorna também o status do O2, que é usado no cálculo do Fugulin.
            return { score: score, level: level, o2Supplement: o2Supplement };
        }

        /**
         * Coleta os dados vitais completos, mesclando dados do formulário com o estado anterior.
         * @returns {object} - Um objeto completo com todos os dados brutos para cálculo e salvamento.
         */
        function getFinalVitalsData() {
            const lastMonitoring = originalPatientState.monitoring || {};
            
            const getValue = (inputId, fallbackKey) => {
                const input = document.getElementById(inputId);
                return input && input.value.trim() !== '' ? input.value.trim() : (lastMonitoring[fallbackKey] || '');
            };

            let finalConsciencia = 'A'; // Padrão
            let finalConscienciaText = 'Alerta (A)'; // Padrão
            const conscienciaTag = document.querySelector('#monitoring-consciencia-container .item-text');

            if (conscienciaTag) {
                finalConsciencia = conscienciaTag.dataset.score;
                finalConscienciaText = conscienciaTag.dataset.value;
            } else if (lastMonitoring.consciencia && lastMonitoring.consciencia.length > 0) {
                const option = monitoringOptions.consciencia.find(opt => opt.text === lastMonitoring.consciencia[0]);
                if(option) {
                    finalConsciencia = option.value;
                    finalConscienciaText = option.text;
                }
            }
            
            const paValue = getValue('form-sv-pa', 'pa');

            return {
                fr: parseInt(getValue('form-sv-fr', 'fr'), 10),
                satO2: parseInt(getValue('form-sv-sato2', 'sato2'), 10),
                o2Supplement: document.getElementById('form-sv-o2').checked,
                pa: paValue ? parseInt(paValue.split('/')[0], 10) : NaN,
                paString: paValue, // Retorna o texto original "120/80"
                fc: parseInt(getValue('form-sv-fc', 'fc'), 10),
                consciencia: finalConsciencia, // Retorna a letra 'A', 'V', 'P', 'U'
                conscienciaText: finalConscienciaText, // Retorna o texto completo "Alerta (A)"
                temp: parseFloat((getValue('form-sv-temp', 'temp') || '').replace(',', '.')),
                hgt: getValue('form-sv-hgt', 'hgt'),
                others: getValue('form-sv-others', 'others')
            };
        }

        /**
         * Calcula o score e a classificação de Fugulin com base em 9 áreas de cuidado,
         * sendo 5 avaliadas diretamente e 4 estimadas a partir de outros dados.
         * @param {object} params - Objeto com dados do paciente e do formulário.
         * @returns {object} - Objeto com o score total e a classificação.
         */
        function calculateFugulin(params) {
            const {
                news2, dispositivos, medicamentos, consciencia,
                cuidadoCorporal, motilidade, deambulacao, alimentacao, eliminacao
            } = params;
            
            let score = 0;

            // --- Parte 1: 5 Áreas de Cuidado avaliadas diretamente ---
            score += parseInt(cuidadoCorporal, 10) || 1;
            score += parseInt(motilidade, 10) || 1;
            score += parseInt(deambulacao, 10) || 1;
            score += parseInt(alimentacao, 10) || 1;
            score += parseInt(eliminacao, 10) || 1;

            // --- Parte 2: 4 Áreas estimadas a partir de outros dados ---
            
            // 2.1 Estado Mental
            const scoreEstadoMental = (consciencia === 'A') ? 1 : 4;
            score += scoreEstadoMental;

            // 2.2 Oxigenação
            let scoreOxigenacao = 1;
            if (news2.o2Supplement) scoreOxigenacao = 3;
            else if (news2.fr > 20 || news2.satO2 < 95) scoreOxigenacao = 2;
            score += scoreOxigenacao;

            // 2.3 Sinais Vitais
            let scoreSinaisVitais = 1;
            if (news2.score >= 5) scoreSinaisVitais = 3; // Risco Médio/Alto
            else if (news2.score >= 1) scoreSinaisVitais = 2; // Risco Baixo-Médio
            score += scoreSinaisVitais;

            // 2.4 Terapêutica
            let scoreTerapeutica = 1;
            if (dispositivos.includes('CVC') || dispositivos.includes('PICC') || dispositivos.length > 2 || medicamentos.length > 5) scoreTerapeutica = 4;
            else if (dispositivos.length > 0) scoreTerapeutica = 3;
            else if (medicamentos.some(m => m.name.toLowerCase().includes('injetável'))) scoreTerapeutica = 2;
            score += scoreTerapeutica;

            // --- Classificação Final ---
            let classification = 'Não Classificado';
            if (score >= 9 && score <= 12) classification = 'Cuidados Mínimos';
            else if (score >= 13 && score <= 18) classification = 'Cuidados Intermediários';
            else if (score >= 19 && score <= 24) classification = 'Cuidados de Alta Dependência';
            else if (score >= 25 && score <= 30) classification = 'Cuidados Semi-Intensivos';
            else if (score >= 31) classification = 'Cuidados Intensivos';
            
            return { score, classification };
        }

        /**
         * [VERSÃO CORRIGIDA]
         * Coleta os dados do formulário (com fallback para os dados antigos),
         * calcula os escores NEWS2 e Fugulin em tempo real e atualiza a UI.
         */
        function updateLiveScores() {
            const news2Display = document.getElementById('live-news2-score');
            const fugulinDisplay = document.getElementById('live-fugulin-score');
            if (!news2Display || !fugulinDisplay) return;

            // 1. Usa a nova função unificada para obter os dados vitais completos.
            const finalVitals = getFinalVitalsData();
            const news2Result = calculateNEWS2(finalVitals);

            // 2. Atualiza o display do NEWS2 se houver um score.
            if (news2Result.score > 0) {
                news2Display.textContent = `NEWS: ${news2Result.score}`;
            } else {
                news2Display.textContent = '';
            }

            // 3. Coleta os outros dados necessários para o Fugulin.
            const fugulinData = {
                news2: news2Result,
                dispositivos: Array.from(document.querySelectorAll('#dispositivos-grid input[type="checkbox"]:checked')).map(chk => chk.value),
                medicamentos: currentMedications,
                consciencia: finalVitals.consciencia,
                cuidadoCorporal: getFugulinScoreFromDOMorState('cuidadoCorporal'),
                motilidade:     getFugulinScoreFromDOMorState('motilidade'),
                deambulacao:    getFugulinScoreFromDOMorState('deambulacao'),
                alimentacao:    getFugulinScoreFromDOMorState('alimentacao'),
                eliminacao:     getFugulinScoreFromDOMorState('eliminacao')
            };

            // 4. Calcula e exibe o Fugulin.
            const fugulinResult = calculateFugulin(fugulinData);
            fugulinDisplay.textContent = `Fugulin: ${fugulinResult.score}`;
        }

        // 3. Adiciona um único listener ao formulário para capturar todas as alterações
        addHandoversForm.addEventListener('input', updateLiveScores);

        // Garante a atualização também ao marcar/desmarcar o checkbox de O2
        document.getElementById('form-sv-o2').addEventListener('change', updateLiveScores);


        function renderExamHistory(handovers) {
            const examHistoryList = document.getElementById('exam-history-list');
            examHistoryList.innerHTML = '';
            const examEvents = [];

            // Itera em todos os plantões para coletar os eventos de exame
            [...handovers].reverse().forEach(h => { // .reverse() para começar do mais antigo
                const timestamp = h.timestamp?.toDate ? h.timestamp.toDate() : new Date();

                // 1. Coleta exames realizados
                if (h.examsDone && h.examsDone.trim() !== '') {
                    examEvents.push({
                        type: 'Realizado',
                        text: h.examsDone,
                        timestamp: timestamp
                    });
                }
                // 2. Coleta resultados de exames pendentes
                if (h.changes?.pendingExams?.resolved && Object.keys(h.changes.pendingExams.resolved).length > 0) {
                    Object.entries(h.changes.pendingExams.resolved).forEach(([exam, result]) => {
                        examEvents.push({
                            type: 'Resultado',
                            text: `<strong>${exam}:</strong> ${result}`,
                            timestamp: timestamp
                        });
                    });
                }
            });

            if (examEvents.length === 0) {
                examHistoryList.innerHTML = '<p class="italic text-gray-500">Nenhum exame realizado ou resultado liberado no histórico.</p>';
                return;
            }

            // Renderiza os eventos no HTML
            let html = '<ul class="space-y-2">';
            examEvents.forEach(event => {
                const formattedDate = event.timestamp.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric', hour: '2-digit', minute: '2-digit' });
                const badgeClass = event.type === 'Resultado' ? 'bg-blue-100 text-blue-800' : 'bg-gray-100 text-gray-800';
                
                html += `
                    <li class="border-b border-gray-200 pb-2">
                        <div class="flex justify-between items-center text-xs text-gray-500 mb-1">
                            <span>${formattedDate}</span>
                            <span class="font-semibold px-2 py-0.5 rounded-full ${badgeClass}">${event.type}</span>
                        </div>
                        <p class="text-gray-800">${event.text}</p>
                    </li>
                `;
            });
            html += '</ul>';
            examHistoryList.innerHTML = html;
        }

        function populateBedFilter(beds) {
            bedFilterList.innerHTML = '';
            if (beds.length === 0) {
                bedFilterList.innerHTML = '<p class="text-xs text-center text-gray-500 p-2">Nenhum leito ocupado.</p>';
                return;
            }
            beds.forEach(bed => {
                const label = document.createElement('label');
                label.className = 'flex items-center space-x-2 p-2 rounded-md hover:bg-gray-100 cursor-pointer';
                label.innerHTML = `<input type="checkbox" value="${bed}" class="h-4 w-4 rounded border-gray-300 text-blue-600 focus:ring-blue-500"><span class="text-sm text-gray-700">${bed}</span>`;
                bedFilterList.appendChild(label);
            });
        }

        function updateBedFilterButtonState() {
            const selectedCount = bedFilterList.querySelectorAll('input:checked').length;
            if (selectedCount === 0) {
                bedFilterButtonText.textContent = 'Buscar por Leito';
                bedFilterClearWrapper.classList.add('hidden');
                bedFilterArrowIcon.classList.remove('hidden');
            } else {
                bedFilterButtonText.textContent = `${selectedCount} leito(s) selecionado(s)`;
                bedFilterClearWrapper.classList.remove('hidden');
                bedFilterArrowIcon.classList.add('hidden');
            }
        }

        function clearBedSelection() {
            bedFilterList.querySelectorAll('input[type="checkbox"]').forEach(cb => cb.checked = false);
            bedSearchInput.value = '';
            bedFilterList.querySelectorAll('label').forEach(label => label.style.display = 'flex');
            updateBedFilterButtonState();
        }

        function calculateDelta(original = [], current = []) {
            const originalSet = new Set(original);
            const currentSet = new Set(current);
            return {
                added: current.filter(item => !originalSet.has(item)),
                removed: original.filter(item => !currentSet.has(item))
            };
        }

        function createListItemElement(text, options = {}) {
            const item = document.createElement('span');
            // A classe 'item-text' será usada para estilização via CSS
            item.className = 'item-text'; 
            item.dataset.value = text;
            item.textContent = text;

            const removeBtn = document.createElement('span');
            // A classe do botão de remoção é a mesma, mas adicionamos 'hidden' do Tailwind
            removeBtn.className = 'tag-remove hidden'; 
            removeBtn.innerHTML = '&times;';
            // Adicionamos o botão dentro do span para manipulação futura
            item.appendChild(removeBtn);

            return item;
        }

        function renderItemsAsList(containerId, items) {
            const container = document.getElementById(containerId);
            if (!container) {
                console.warn(`[renderItemsAsList] Aviso: Contêiner com ID #${containerId} não foi encontrado.`);
                return;
            }
            
            container.innerHTML = ''; // Apenas limpa o conteúdo

            if (items && Array.isArray(items) && items.length > 0) {
                items.forEach(text => {
                    if (typeof text === 'string' && text.trim() !== '') {
                        const tagElement = createListItem(text);
                        container.appendChild(tagElement);
                    }
                });
            }
        }

        function getItemsFromContainer(containerId) {
            const container = document.getElementById(containerId);
            if (!container) return []; // Segurança para caso o container não exista

            // Agora, como o elemento com a classe .item-text é o mesmo que tem o data-value, isto vai funcionar.
            return Array.from(container.querySelectorAll('.item-text'))
                .map(item => item.dataset.value);
        }


        // Variável global para rastrear a lista de autocomplete ativa
        let activeAutocomplete = null;

        /**
         * FUNÇÃO UNIFICADA E ROBUSTA
         * Pega o SCORE NUMÉRICO de um item de cuidado, buscando primeiro no formulário (DOM)
         * e, se não encontrar, busca no estado original do paciente.
         * @param {string} key - A chave do cuidado (ex: 'cuidadoCorporal').
         * @returns {string} - O score numérico correspondente (ex: '1', '2', '3', '4').
         */
        function getFugulinScoreFromDOMorState(key) {
            const kebabKey = key.replace(/([A-Z])/g, '-$1').toLowerCase();
            const container = document.getElementById(`fugulin-${kebabKey}-container`);
            const tag = container ? container.querySelector('[data-score]') : null;

            // 1. Se o usuário selecionou algo no formulário, usa o score da tag.
            if (tag) {
                return tag.dataset.score;
            }
            
            // 2. Se não, busca o valor de texto original do paciente.
            const originalCareText = (originalPatientState.nursingCare?.[key] || [])[0];
            if (originalCareText) {
                // Encontra a opção correspondente nos dados para obter o score numérico.
                const option = fugulinOptions[key].find(opt => opt.text === originalCareText);
                if (option) return option.value;
            }
            
            // 3. Fallback final para o valor mínimo se nada for encontrado.
            return '1';
        }

        /**
         * [VERSÃO MODIFICADA COM LOGS]
         * Renderiza e posiciona a lista de autocomplete, agora com suporte a estados de carregamento e "nenhum resultado".
         * @param {HTMLInputElement} inputElement - O input que acionou a lista.
         * @param {HTMLDivElement} listElement - O elemento <div> da lista.
         * @param {string[]} suggestions - As sugestões a serem exibidas.
         * @param {string} customValue - O valor que o usuário digitou.
         * @param {Function} onSelectCallback - A função a ser chamada quando um item é selecionado.
         * @param {'has_results' | 'loading' | 'no_results'} state - O estado atual da busca.
         */
        function renderAndPositionAutocomplete(inputElement, listElement, suggestions, customValue, onSelectCallback, state = 'has_results') {
            hideActiveAutocomplete();

            if (!listElement.originalParent) {
                listElement.originalParent = listElement.parentElement;
            }
            document.body.appendChild(listElement);
            listElement.innerHTML = '';

            // Renderiza a opção "Usar este texto" se aplicável
            if (state !== 'no_results' && customValue) {
                const customItem = document.createElement('div');
                customItem.className = 'autocomplete-item cursor-pointer p-3 hover:bg-gray-100 border-b border-dashed';
                customItem.innerHTML = `<p class="font-semibold text-gray-800">${customValue}</p><p class="text-xs text-gray-500">Usar este texto</p>`;
                customItem.addEventListener('click', () => onSelectCallback(customValue));
                listElement.appendChild(customItem);
            }

            // Renderiza os diferentes estados da lista (carregando, sem resultados, com resultados)
            if (state === 'loading') {
                const loadingItem = document.createElement('div');
                loadingItem.className = 'p-4 text-center';
                loadingItem.innerHTML = `
                    <svg class="animate-spin h-6 w-6 text-blue-600 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                    </svg>
                    <p class="mt-2 text-sm text-gray-500">Buscando sugestões...</p>
                `;
                listElement.appendChild(loadingItem);
            } else if (state === 'no_results') {
                const noResultsItem = document.createElement('div');
                noResultsItem.className = 'p-3 text-center text-sm text-gray-500 italic';
                noResultsItem.textContent = 'Nenhuma sugestão encontrada.';
                listElement.appendChild(noResultsItem);
            } else {
                suggestions.forEach(suggestion => {
                    const item = document.createElement('div');
                    item.className = 'autocomplete-item cursor-pointer p-3 hover:bg-gray-100';
                    item.textContent = suggestion;
                    item.addEventListener('click', () => onSelectCallback(suggestion));
                    listElement.appendChild(item);
                });
            }

            // Posiciona e exibe a lista
            positionFloatingList(inputElement, listElement);
            listElement.classList.remove('hidden');
            activeAutocomplete = { listElement, inputElement };
            
            // Variável para controlar se o rato está sobre a lista
            let isMouseOverList = false;

            listElement.addEventListener('mouseenter', () => {
                isMouseOverList = true;
            });
            listElement.addEventListener('mouseleave', () => {
                isMouseOverList = false;
            });

            // O evento 'blur' é uma alternativa mais fiável ao 'focusout' neste contexto.
            // Ele é acionado quando o elemento perde o foco.
            const handleBlur = () => {
                // Damos uma pequeníssima janela de tempo (50ms) antes de fechar.
                // Se, nesse tempo, o rato entrar na lista (o que acontece ao clicar na scrollbar),
                // a flag 'isMouseOverList' será 'true' e o fecho é cancelado.
                setTimeout(() => {
                    if (!isMouseOverList) {
                        hideActiveAutocomplete();
                    }
                }, 50);
            };

            // Usamos { once: true } para garantir que este listener seja adicionado apenas uma vez por cada abertura da lista,
            // evitando acumulação de listeners no mesmo elemento.
            inputElement.addEventListener('blur', handleBlur, { once: true });
        }

        /**
         * Posiciona uma lista flutuante de forma absoluta na página, abaixo de um elemento de referência,
         * permitindo que ela acompanhe a rolagem.
         * @param {HTMLElement} triggerElement - O elemento que acionou a lista (input, botão, etc.).
         * @param {HTMLElement} listElement - O elemento da lista a ser posicionado.
         */
        function positionFloatingList(triggerElement, listElement) {
            if (!listElement.originalParent) {
                listElement.originalParent = listElement.parentElement;
            }
            document.body.appendChild(listElement);

            const rect = triggerElement.getBoundingClientRect();

            listElement.style.position = 'absolute';
            // O cálculo do 'top' agora SOMA a posição de rolagem da janela
            listElement.style.top = `${rect.bottom + window.scrollY + 4}px`; // 4px de espaço
            listElement.style.left = `${rect.left + window.scrollX}px`;
            listElement.style.width = `${rect.width}px`;
            listElement.style.zIndex = '10000';
        }

        /**
         * Chama a API do Gemini para obter sugestões de termos de busca formais da CID-10
         * a partir de uma consulta em linguagem natural.
         * VERSÃO OTIMIZADA COM CACHE.
         * @param {string} userQuery - O termo digitado pelo usuário (ex: "cancer").
         * @returns {Promise<string[]>} - Uma promessa que resolve para um array de termos de busca sugeridos.
         */
        
        // Cria o cache fora da função para que ele persista entre as chamadas
        const geminiCache = new Map();

        async function getGeminiSuggestions(userQuery) {
            // Se a busca já estiver no cache, retorna o resultado guardado imediatamente.
            if (geminiCache.has(userQuery)) {
                console.log(`[Cache] Usando resultado em cache para: "${userQuery}"`);
                return geminiCache.get(userQuery);
            }

            const API_KEY = "AIzaSyA9MGTgQxLsUW2vwJdF172LjtEUgT763bE";
            const API_URL = `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-latest:generateContent?key=${API_KEY}`;

            const prompt = `
            Você é um assistente médico especialista em CID-10 e terminologia médica. Sua tarefa é analisar a busca do usuário e retornar um array JSON com até 5 termos de busca (tokens) que sejam clinicamente relevantes e otimizados para uma consulta 'array-contains-any' em um banco de dados Firestore. O campo de busca se chama 'search_tokens_normalized' e contém termos em minúsculas e sem acentos.

            A busca do usuário é: "${userQuery}".

            Siga estas regras estritamente:
            1.  **Nomes Formais (CID):** O banco de dados utiliza os nomes formais das doenças, baseados na classificação CID-10. Dê prioridade máxima aos termos técnicos e formais em vez de gírias ou descrições vagas.
            2.  **Correção de Erros:** Se a busca parecer ter um erro de digitação, inclua o termo corrigido. (Ex: "diabetis" -> "diabetes").
            3.  **Sinônimos e Abreviações:** Inclua sinônimos médicos, abreviações comuns e termos relacionados que levem ao nome formal. (Ex: "IAM" -> "infarto", "agudo", "miocardio", "sindrome", "coronariana").
            4.  **Linguagem Natural:** Se a busca for uma descrição (ex: "dor de barriga forte"), traduza para os termos técnicos mais prováveis. (-> "dor", "abdominal", "aguda", "colica", "gastroenterite").
            5.  **Priorize Relevância:** Os termos mais específicos e importantes devem vir primeiro no array.
            6.  **Formato:** A saída DEVE ser um array JSON de strings.
            7.  **Múltiplos Conceitos:** Se a busca contiver múltiplos conceitos (ex: "fratura de fêmur"), os tokens para ambos os conceitos ("fratura", "femur") devem ser retornados para permitir uma classificação de resultados mais precisa.


            Exemplos:
            -   Busca: "cancer de mama" -> ["neoplasia", "maligna", "mama", "carcinoma", "tumor", "cid", "c50"]
            -   Busca: "Pressão alta" -> ["hipertensao", "arterial", "sistemica", "essencial", "cid", "i10"]
            -   Busca: "dpoc" -> ["doenca", "pulmonar", "obstrutiva", "cronica", "enfisema", "bronquite", "cid", "j44"]
            `;

            try {
                const response = await fetch(API_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{ parts: [{ text: prompt }] }]
                    }),
                });

                if (!response.ok) {
                    throw new Error(`Erro na API do Gemini: ${response.statusText}`);
                }

                const data = await response.json();
                const jsonText = data.candidates[0].content.parts[0].text;

                const match = jsonText.match(/(\[.*\])/s);
                if (match && match[0]) {
                    const extractedJson = match[0];
                    const suggestions = JSON.parse(extractedJson);

                    geminiCache.set(userQuery, suggestions);
                    console.log(`[Gemini Tokens] Sugestões para "${userQuery}":`, suggestions);
                    return suggestions;
                } else {
                    console.warn(`[Gemini] A resposta da IA não continha um JSON válido: "${jsonText}"`);
                    geminiCache.set(userQuery, []);
                    return [];
                }

            } catch (error) {
                console.error("Erro ao chamar ou processar API do Gemini:", error);
                geminiCache.set(userQuery, []);
                return [];
            }
        }

        /**
         * Chama a API do Gemini para obter sugestões de nomes de medicamentos
         * a partir de uma consulta em linguagem natural, nome popular ou erro de digitação.
         * @param {string} userQuery - O termo digitado pelo usuário (ex: "remedio pra febre").
         * @returns {Promise<string[]>} - Uma promessa que resolve para um array de nomes de medicamentos sugeridos.
         */
        async function getGeminiMedicationSuggestions(userQuery) {

            const API_KEY = "AIzaSyA9MGTgQxLsUW2vwJdF172LjtEUgT763bE";
            const API_URL = `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash-latest:generateContent?key=${API_KEY}`;

            const prompt = `
                Você é um farmacêutico especialista. Sua tarefa é analisar a busca do usuário por um medicamento e retornar um array JSON com até 5 nomes de princípios ativos para pesquisar em um banco de dados.

                A busca do usuário é: "${userQuery}".

                Siga estas regras estritamente:
                1.  **Correção de Erros:** Corrija erros de digitação comuns. (Ex: "dipiron" -> "dipirona").
                2.  **Princípio Ativo:** Se o usuário digitar um nome comercial, inclua o princípio ativo. (Ex: "Tylenol" -> "paracetamol").
                3.  **Busca por Doença:** Se o usuário digitar uma condição (ex: "remédio para febre"), sugira os medicamentos mais comuns para essa condição.
                4.  **Priorize Genéricos:** Dê preferência a princípios ativos (nomes genéricos) nas sugestões.
                5.  **Formato:** A saída DEVE ser um array JSON de strings, sem nenhum texto adicional.

                Exemplos:
                -   Busca: "remedio pra dor de cabeça" -> ["dipirona", "paracetamol", "ibuprofeno", "dorflex", "cefaliv"]
                -   Busca: "aas" -> ["acido acetilsalicilico", "aspirina"]
                -   Busca: "amoxilina" -> ["amoxicilina"]
            `;

            try {
                const response = await fetch(API_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{ parts: [{ text: prompt }] }]
                    }),
                });

                if (!response.ok) {
                    throw new Error(`Erro na API do Gemini: ${response.statusText}`);
                }

                const data = await response.json();
                const jsonText = data.candidates[0].content.parts[0].text;

                const match = jsonText.match(/(\[.*\])/s);

                if (match && match[0]) {
                    const extractedJson = match[0];
                    const suggestions = JSON.parse(extractedJson);
                    console.log(`[Gemini Meds] Sugestões para "${userQuery}":`, suggestions);
                    return suggestions;
                } else {
                    console.warn(`[Gemini Meds] A resposta da IA não continha um JSON válido: "${jsonText}"`);
                    return [];
                }

            } catch (error) {
                console.error("Erro ao chamar ou processar API do Gemini para medicações:", error);
                return [];
            }
        }
        
        /**
         * Busca por prefixo e tokens, classifica os resultados por relevância e os exibe.
         * @param {string} queryText - O texto a ser pesquisado.
         * @param {HTMLElement} inputElement - O elemento de input que originou a busca.
         * @param {HTMLElement} listElement - O elemento da lista onde renderizar.
         * @param {boolean} [renderUI=true] - Se deve renderizar o autocomplete ou apenas retornar os dados.
         * @returns {Promise<object[]>} - Uma promessa que resolve para um array de objetos de resultado completos.
         */
        async function searchFirestoreCID(queryText, inputElement, listElement, renderUI = true) {
            const normalizedQuery = queryText.toLowerCase().normalize("NFD").replace(/[\u0300-\u036f]/g, "");
            if (normalizedQuery.length < 2) {
                if (renderUI) listElement.classList.add('hidden');
                return [];
            }

            const diagnosesRef = collection(db, 'diagnoses');
            const searchTokens = normalizedQuery.replace(/[.,\/#!$%\^&\*;:{}=\-_`~()]/g, "").split(' ').filter(token => token);

            // Otimização: Se a busca tiver poucas palavras, podemos combinar as buscas.
            const prefixQuery = query(diagnosesRef, where('searchable_name_normalized', '>=', normalizedQuery), where('searchable_name_normalized', '<=', normalizedQuery + '\uf8ff'), limit(10));
            const tokenQuery = query(diagnosesRef, where('search_tokens_normalized', 'array-contains-any', searchTokens), limit(10));

            try {
                const [prefixSnapshot, tokenSnapshot] = await Promise.all([getDocs(prefixQuery), getDocs(tokenQuery)]);
                const resultsMap = new Map();

                prefixSnapshot.docs.forEach(doc => resultsMap.set(doc.id, { id: doc.id, ...doc.data() }));
                tokenSnapshot.docs.forEach(doc => resultsMap.set(doc.id, { id: doc.id, ...doc.data() }));

                const combinedResults = Array.from(resultsMap.values());

                // --- INÍCIO DA LÓGICA DE RANQUEAMENTO CORRIGIDA ---
                const rankedResults = combinedResults.map(result => {
                    let score = 0;
                    const resultTokens = new Set(result.search_tokens_normalized || []);
                    const resultNameNormalized = result.searchable_name_normalized || '';

                    // Prioridade alta para correspondência exata do início da frase
                    if (resultNameNormalized.startsWith(normalizedQuery)) {
                        score += 100;
                    }

                    // A pontuação principal agora vem da contagem de tokens correspondentes
                    let matchingTokensCount = 0;
                    searchTokens.forEach(searchToken => {
                        if (resultTokens.has(searchToken)) {
                            matchingTokensCount++;
                        }
                    });

                    // Pontuação exponencial para valorizar múltiplos acertos.
                    // 1 token = 50 pts, 2 tokens = 200 pts, 3 tokens = 450 pts.
                    // Isso garante que "neoplasia ... mama" (2 acertos) sempre venha antes de "neoplasia ..." (1 acerto).
                    score += matchingTokensCount * matchingTokensCount * 50;

                    // Bônus adicional se TODOS os tokens da busca estiverem presentes no resultado
                    if (matchingTokensCount > 0 && matchingTokensCount === searchTokens.length) {
                        score += 50;
                    }

                    // Penalidade para resultados que têm muitos tokens extras não relacionados
                    const extraTokens = resultTokens.size - matchingTokensCount;
                    score -= extraTokens * 5;

                    // Pequena penalidade pelo comprimento para servir como desempate
                    score -= result.name.length * 0.01;

                    return { ...result, score };
                });

                rankedResults.sort((a, b) => b.score - a.score);
                // --- FIM DA LÓGICA DE RANQUEAMENTO CORRIGIDA ---

                if (renderUI) {
                    const finalSuggestions = rankedResults.slice(0, 10).map(r => r.name);
                    const onSelectCallback = (selectedValue) => {
                        const containerId = (inputElement.id === 'form-diagnosis') 
                            ? 'diagnoses-tags-container' 
                            : 'comorbidities-tags-container';

                        const container = document.getElementById(containerId);

                        if (container) {
                            container.appendChild(createListItem(selectedValue));
                            inputElement.value = '';
                            updateDiagnosisSummary();
                            setUnsavedChanges(true);
                        }
                    };
                    renderAndPositionAutocomplete(inputElement, listElement, finalSuggestions, queryText, onSelectCallback);
                }

                return rankedResults;

            } catch (error) {
                console.error("Erro na busca de diagnósticos:", error);
                if (renderUI) showToast("Erro ao buscar diagnósticos.", "error");
                return [];
            }
        }

        /**
         * Busca por medicações no Firestore com ranking de relevância.
         * Pode renderizar a UI ou apenas retornar os resultados.
         * @param {string} queryText - O texto a ser pesquisado.
         * @param {HTMLElement} inputElement - O elemento de input que originou a busca.
         * @param {HTMLElement} listElement - O elemento da lista onde renderizar.
         * @param {boolean} [renderUI=true] - Se deve renderizar o autocomplete ou apenas retornar os dados.
         * @returns {Promise<string[]>} - Uma promessa que resolve para um array de nomes de medicamentos ordenado por relevância.
         */
/**
         * Busca por medicações no Firestore com ranking de relevância e limite de 10 sugestões.
         * Pode renderizar a UI ou apenas retornar os resultados.
         * @param {string} queryText - O texto a ser pesquisado.
         * @param {HTMLElement} inputElement - O elemento de input que originou a busca.
         * @param {HTMLElement} listElement - O elemento da lista onde renderizar.
         * @param {boolean} [renderUI=true] - Se deve renderizar o autocomplete ou apenas retornar os dados.
         * @returns {Promise<string[]>} - Uma promessa que resolve para um array de nomes de medicamentos ordenado por relevância.
         */
        async function fetchMedicationSuggestions(queryText, inputElement, listElement, renderUI = true) {
            const normalizedQuery = queryText.toLowerCase().normalize("NFD").replace(/[\u0300-\u036f]/g, "");
            if (normalizedQuery.length < 2) {
                if (renderUI) listElement.classList.add('hidden');
                return [];
            }

            const medicationsRef = collection(db, 'medications');
            const searchTokens = normalizedQuery.replace(/[.,\/#!$%\^&\*;:{}=\-_`~()]/g, "").split(' ').filter(token => token);
            
            const prefixQuery = query(medicationsRef, where('searchable_name_normalized', '>=', normalizedQuery), where('searchable_name_normalized', '<=', normalizedQuery + '\uf8ff'), limit(10));
            const tokenQuery = query(medicationsRef, where('search_tokens_normalized', 'array-contains-any', searchTokens), limit(10));

            try {
                const [prefixSnapshot, tokenSnapshot] = await Promise.all([getDocs(prefixQuery), getDocs(tokenQuery)]);
                const resultsMap = new Map();
                
                // Coleta os dados completos do documento, não apenas o nome
                prefixSnapshot.docs.forEach(doc => resultsMap.set(doc.id, { id: doc.id, ...doc.data() }));
                tokenSnapshot.docs.forEach(doc => resultsMap.set(doc.id, { id: doc.id, ...doc.data() }));

                const combinedResults = Array.from(resultsMap.values());

                const rankedResults = combinedResults.map(result => {
                    let score = 0;
                    const resultTokens = new Set(result.search_tokens_normalized || []);
                    const resultNameNormalized = result.searchable_name_normalized || '';

                    if (resultNameNormalized.startsWith(normalizedQuery)) {
                        score += 100;
                    }

                    let matchingTokensCount = 0;
                    searchTokens.forEach(searchToken => {
                        if (resultTokens.has(searchToken)) {
                            matchingTokensCount++;
                        }
                    });
                    
                    score += matchingTokensCount * matchingTokensCount * 50;

                    if (matchingTokensCount > 0 && matchingTokensCount === searchTokens.length) {
                        score += 50;
                    }
                    
                    const extraTokens = resultTokens.size - matchingTokensCount;
                    score -= extraTokens * 5;
                    score -= result.name.length * 0.01;

                    return { ...result, score };
                });

                rankedResults.sort((a, b) => b.score - a.score);

                // Pega os nomes dos 10 melhores resultados
                const finalSuggestions = rankedResults.slice(0, 10).map(r => r.name);

                if (renderUI) {
                    renderAndPositionAutocomplete(inputElement, listElement, finalSuggestions, queryText, (selectedValue) => {
                        inputElement.value = selectedValue;
                        document.getElementById('confirm-med-name-btn').classList.remove('hidden');
                        hideActiveAutocomplete();
                        inputElement.focus();
                    });
                }
                
                // Retorna a lista de nomes já ordenada e limitada
                return finalSuggestions;

            } catch (error) {
                console.error("Erro na busca de medicações:", error);
                if (renderUI) showToast("Erro ao buscar medicações.", "error");
                return [];
            }
        }



        /**
         * Renderiza a lista de horários de uma medicação no formato "HHhMM (DD/MM)".
         * Agrupa horários do mesmo dia e formata a lista com "e" no final.
         * @param {object} medObject - O objeto da medicação (ex: { name: 'Dipirona', times: [...] }).
         * @param {HTMLElement} container - O elemento <div> onde o texto será exibido.
         */
        function renderMedicationTimes(medObject, container) {
            if (!medObject.times || medObject.times.length === 0) {
                container.innerHTML = '<p class="text-gray-500">Nenhum horário adicionado.</p>';
                return;
            }

            // 1. Agrupa os timestamps (que são números) por dia
            const groupedByDay = medObject.times.reduce((acc, timestamp) => {
                const date = new Date(timestamp);
                const dayKey = date.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit' });
                if (!acc[dayKey]) {
                    acc[dayKey] = [];
                }
                acc[dayKey].push(timestamp);
                return acc;
            }, {});

            // 2. Formata a exibição para cada dia
            const summaryParts = Object.keys(groupedByDay).sort().map(dayKey => {
                // Ordena os horários do dia
                const timesOnDay = groupedByDay[dayKey]
                    .sort()
                    .map(ts => new Date(ts).toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }).replace(':', 'h'));
                
                // Junta os horários com vírgula e substitui a última vírgula por "e"
                let timesText;
                if (timesOnDay.length > 1) {
                    timesText = timesOnDay.slice(0, -1).join(', ') + ' e ' + timesOnDay.slice(-1);
                } else {
                    timesText = timesOnDay[0];
                }
                
                return `${timesText} (${dayKey})`;
            });

            // 3. Define o HTML final
            container.innerHTML = `<p class="font-semibold text-blue-700">Às ${summaryParts.join(' | ')}</p>`;
        }

        // Cria o Card de Medicação
        function createMedicationCard(medObject) {
            const container = medicationsListContainer;
            const block = document.createElement('div');
            block.className = 'medication-block';
            block.dataset.medName = medObject.name;

            block.innerHTML = `
                <div class="flex-grow">
                    <p class="font-bold text-gray-800">${medObject.name}</p>
                    <div class="med-times-display text-xs text-gray-500 mt-1">
                    </div>
                </div>
                <div class="med-actions">
                    
                    <button type="button" class="add-med-time-btn-emoji text-blue-600 hover:text-blue-800 transition-colors" title="Outro horário">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" />
                        </svg>
                    </button>
                    <button type="button" class="remove-med-btn text-red-500 hover:text-red-700 transition-colors" title="Excluir Medicação">
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="h-6 w-6">
                        <path fill-rule="evenodd" d="M5.47 5.47a.75.75 0 011.06 0L12 10.94l5.47-5.47a.75.75 0 111.06 1.06L13.06 12l5.47 5.47a.75.75 0 11-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 01-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 010-1.06z" clip-rule="evenodd" />
                        </svg>
                    </button>
                </div>
            `;
            container.appendChild(block);

            const timesContainer = block.querySelector('.med-times-display');
            renderMedicationTimes(medObject, timesContainer); // Renderiza o estado inicial ("Nenhum horário...")

            return block;
        }

        /**
         * Abre o seletor de tempo, implementa a lógica de data e atualiza o card.
         * Agora com opções para "Adicionar" um novo horário ou "Corrigir" o último.
         * @param {string} medName - O nome do medicamento.
         * @param {HTMLElement} medBlock - O elemento do card da medicação.
         */
        function openMedTimePicker(medName, medBlock) {
            const medObject = currentMedications.find(m => m.name === medName);
            if (!medObject) {
                console.error("Objeto da medicação não encontrado para:", medName);
                return;
            }
            enterEditMode(document.getElementById('module-medicacoes'));

            // Mostra a UI do editor de tempo
            showMedicationEditor('time');
            document.getElementById('medication-time-editor-label').textContent = `Selecione o horário para ${medName}`;

            const timePickerContainer = document.getElementById('inline-time-picker-container');
            
            // NOVO: Referências aos novos botões
            const correctBtn = document.getElementById('correct-med-time-btn');
            const addBtn = document.getElementById('add-med-time-btn');
            
            let selectedTimestamp = null;
            let fpInstance = null;

            timePickerContainer.innerHTML = '';

            // Lógica para finalizar a edição (comum a ambos os botões)
            const finalizeTimeEdit = () => {
                const timesContainer = medBlock.querySelector('.med-times-display');
                if (timesContainer) {
                    renderMedicationTimes(medObject, timesContainer);
                }

                updateMedicationSummary('updateMedicationTime');
                setUnsavedChanges(true);
                
                if (fpInstance) fpInstance.destroy();
                showMedicationEditor(false);
                exitEditMode(document.getElementById('module-medicacoes'));
            };
            
            // Usando o truque de clonagem para evitar múltiplos listeners
            const newCorrectBtn = correctBtn.cloneNode(true);
            const newAddBtn = addBtn.cloneNode(true);
            correctBtn.parentNode.replaceChild(newCorrectBtn, correctBtn);
            addBtn.parentNode.replaceChild(newAddBtn, addBtn);

            // Lógica do botão "Corrigir"
            newCorrectBtn.addEventListener('click', () => {
                if (!selectedTimestamp) selectedTimestamp = new Date().getTime();
                
                // A mágica está aqui: substitui o ÚLTIMO item do array
                if (medObject.times.length > 0) {
                    medObject.times[medObject.times.length - 1] = selectedTimestamp;
                }
                finalizeTimeEdit();
            });

            // Lógica do botão "Adicionar"
            newAddBtn.addEventListener('click', () => {
                if (!selectedTimestamp) selectedTimestamp = new Date().getTime();
                
                // Apenas adiciona um novo item ao array
                medObject.times.push(selectedTimestamp);
                finalizeTimeEdit();
            });

            // NOVO: Mostra o botão "Corrigir" apenas se já houver horários
            if (medObject.times.length > 0) {
                newCorrectBtn.classList.remove('hidden');
            } else {
                newCorrectBtn.classList.add('hidden');
            }

            // Configuração do Flatpickr (seletor de tempo)
            fpInstance = flatpickr(timePickerContainer, {
                enableTime: true,
                noCalendar: true,
                dateFormat: "H:i",
                time_24hr: true,
                minuteIncrement: 5,
                locale: "pt",
                inline: true,
                defaultDate: new Date(),
                onChange: (selectedDates) => {
                    if (selectedDates.length > 0) {
                        const now = new Date();
                        const selectedTime = selectedDates[0];
                        
                        let finalDateTime = new Date(
                            now.getFullYear(), now.getMonth(), now.getDate(),
                            selectedTime.getHours(), selectedTime.getMinutes()
                        );

                        if (finalDateTime.getTime() > now.getTime()) {
                            finalDateTime.setDate(finalDateTime.getDate() - 1);
                        }
                        
                        selectedTimestamp = finalDateTime.getTime();
                    }
                }
            });
        }

        /**
         * Função central para lidar com a seleção de uma nova medicação ou adicionar um novo horário a uma existente.
         * @param {string} medName - O nome do medicamento selecionado.
         */
        function handleMedicationSelection(medName) {
            // 1. Procura se a medicação já existe no estado da aplicação.
            let medObject = currentMedications.find(m => m.name === medName);
            let medBlock;

            if (medObject) {
                // 2. Se JÁ EXISTE: encontra o card (elemento HTML) correspondente no DOM.
                medBlock = medicationsListContainer.querySelector(`.medication-block[data-med-name="${medName}"]`);
            } else {
                // 3. Se NÃO EXISTE: cria um novo objeto, adiciona ao estado e cria um novo card.
                medObject = { name: medName, times: [] };
                currentMedications.push(medObject);
                medBlock = createMedicationCard(medObject);
            }

            // 4. Verifica se o card foi encontrado ou criado com sucesso.
            if (!medBlock) {
                showToast("Ocorreu um erro ao tentar adicionar a medicação.", "error");
                console.error("Não foi possível encontrar ou criar o medBlock para:", medName);
                return;
            }

            // 5. Abre o seletor de tempo para o card (seja ele novo ou existente).
            openMedTimePicker(medName, medBlock);
        }

        function renderRecentMedications(handovers) {
            recentMedsList.innerHTML = '';
            const meds24h = new Map();
            handovers.forEach(h => {
                if (h.medications && h.medications.length > 0) {
                    h.medications.forEach(med => {
                        if (!meds24h.has(med.name)) {
                            meds24h.set(med.name, h.timestamp.toDate().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }));
                        }
                    });
                }
            });
            if (meds24h.size === 0) {
                recentMedsList.innerHTML = '<p class="italic text-gray-500">Nenhuma medicação registrada nas últimas 24h.</p>';
            } else {
                const list = document.createElement('ul');
                list.className = 'list-disc pl-5 space-y-1';
                meds24h.forEach((time, name) => {
                    const item = document.createElement('li');
                    item.innerHTML = `${name} (às ${time})`;
                    list.appendChild(item);
                });
                recentMedsList.appendChild(list);
            }
        }


        // --- NOVO BLOCO DE FUNÇÕES PARA CARREGAMENTO PAGINADO ---

        /**
         * Carrega o lote INICIAL de pacientes (agora usando getDocs para evitar duplicação).
         */
        async function loadInitialPatients() {
            // Reseta o estado da paginação
            lastVisiblePatientDoc = null;
            allPatientsLoaded = false;
            isLoadingPatients = false;
            currentPatientList = []; // Limpa a lista em memória
            patientList.innerHTML = ''; // Limpa o HTML da lista
            
            // Mostra o spinner enquanto carrega
            document.getElementById('load-more-trigger').classList.remove('hidden');

            try {
                const patientsRef = collection(db, 'patients');
                const q = query(
                    patientsRef,
                    where('status', '!=', 'arquivado'),
                    orderBy('lastUpdatedAt', 'desc'),
                    limit(PATIENTS_PER_PAGE)
                );

                const snapshot = await getDocs(q);

                if (snapshot.empty) {
                    renderPatientList([]); // Renderiza a mensagem de lista vazia
                    return;
                }
                
                lastVisiblePatientDoc = snapshot.docs[snapshot.docs.length - 1];
                const initialPatients = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                currentPatientList = initialPatients;
                
                sortAndRenderPatientList();
                
                if (initialPatients.length < PATIENTS_PER_PAGE) {
                    allPatientsLoaded = true;
                    document.getElementById('load-more-trigger').classList.add('hidden');
                } else {
                    setupInfiniteScrollObserver();
                }

            } catch (error) {
                console.error("Erro ao buscar pacientes iniciais:", error);
                showToast("Erro ao carregar pacientes.", "error");
            } finally {
                // Esconde o spinner se todos os pacientes já foram carregados
                if (allPatientsLoaded) {
                    document.getElementById('load-more-trigger').classList.add('hidden');
                }
            }
        }

        /**
         * Carrega o PRÓXIMO lote de pacientes quando o usuário rola a página.
         */
        async function loadMorePatients() {
            if (isLoadingPatients || allPatientsLoaded) {
                return; // Não faz nada se já estiver carregando ou se tudo já foi carregado
            }

            isLoadingPatients = true;
            document.getElementById('load-more-trigger').classList.remove('hidden');

            try {
                const patientsRef = collection(db, 'patients');
                const q = query(
                    patientsRef,
                    where('status', '!=', 'arquivado'),
                    orderBy('lastUpdatedAt', 'desc'),
                    startAfter(lastVisiblePatientDoc), // Começa a busca DEPOIS do último paciente visível
                    limit(PATIENTS_PER_PAGE)
                );

                const snapshot = await getDocs(q);

                if (snapshot.empty) {
                    allPatientsLoaded = true; // Não há mais pacientes
                    document.getElementById('load-more-trigger').classList.add('hidden');
                    return;
                }

                lastVisiblePatientDoc = snapshot.docs[snapshot.docs.length - 1];
                const newPatients = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                
                currentPatientList.push(...newPatients); // Adiciona os novos pacientes à lista existente
                sortAndRenderPatientList();

                if (newPatients.length < PATIENTS_PER_PAGE) {
                    allPatientsLoaded = true;
                    document.getElementById('load-more-trigger').classList.add('hidden');
                }

            } catch (error) {
                console.error("Erro ao carregar mais pacientes:", error);
                showToast("Erro ao carregar mais pacientes.", "error");
            } finally {
                isLoadingPatients = false;
                if (allPatientsLoaded) {
                    document.getElementById('load-more-trigger').classList.add('hidden');
                }
            }
        }

        /**
         * Configura o IntersectionObserver para chamar loadMorePatients.
         */
        function setupInfiniteScrollObserver() {
            const trigger = document.getElementById('load-more-trigger');
            if(!trigger) return;

            const observer = new IntersectionObserver((entries) => {
                if (entries[0].isIntersecting) {
                    loadMorePatients();
                }
            }, {
                root: null, // viewport
                rootMargin: '0px',
                threshold: 0.1
            });

            observer.observe(trigger);
        }

        /**
         * Compara o estado ATUAL do formulário com o estado ORIGINAL do paciente
         * e habilita/desabilita o botão de salvar de acordo.
         */
        function checkUnsavedChanges() {
            // Helper para comparar dois arrays de strings, ignorando a ordem.
            const areArraysEqualUnordered = (arr1 = [], arr2 = []) => {
                if (arr1.length !== arr2.length) return false;
                const set1 = new Set(arr1);
                return arr2.every(item => set1.has(item));
            };

            // 1. Verifica campos de texto simples
            if (document.getElementById('form-evolution').value.trim() !== (originalPatientState.evolution || '')) {
                setUnsavedChanges(true);
                return;
            }
            if (document.getElementById('form-pending-obs').value.trim() !== (originalPatientState.pendingObs || '')) {
                setUnsavedChanges(true);
                return;
            }
            
            // 2. Verifica se novas medicações foram adicionadas (sempre é uma alteração)
            if (currentMedications.length > 0) {
                setUnsavedChanges(true);
                return;
            }

            // 3. Verifica se exames foram concluídos, reagendados ou cancelados
            if (currentShiftCompletedExams.length > 0 || currentShiftRescheduledExams.length > 0) {
                setUnsavedChanges(true);
                return;
            }
            
            // 4. Verifica listas de tags (Diagnósticos, Comorbidades, Precauções, etc.)
            const currentDiagnoses = getItemsFromContainer('diagnoses-tags-container');
            if (!areArraysEqualUnordered(currentDiagnoses, originalPatientState.diagnoses)) {
                setUnsavedChanges(true);
                return;
            }

            const currentComorbidities = getItemsFromContainer('comorbidities-tags-container');
            if (!areArraysEqualUnordered(currentComorbidities, originalPatientState.comorbidities)) {
                setUnsavedChanges(true);
                return;
            }

            const currentPrecautions = getItemsFromContainer('precaucoes-container');
            if (!areArraysEqualUnordered(currentPrecautions, originalPatientState.precautions)) {
                setUnsavedChanges(true);
                return;
            }
            
            // 5. Verifica as alergias (leva em conta o radio button)
            const allergyRadioYes = document.getElementById('allergy-radio-yes');
            const currentAllergies = allergyRadioYes.checked ? getItemsFromContainer('allergies-tags-container') : [];
            if (!areArraysEqualUnordered(currentAllergies, originalPatientState.allergies)) {
                setUnsavedChanges(true);
                return;
            }
            
            // 6. Verifica os dispositivos
            const currentDevices = Array.from(document.querySelectorAll('#dispositivos-grid input[type="checkbox"]:checked')).map(chk => chk.value);
            if (!areArraysEqualUnordered(currentDevices, originalPatientState.devices)) {
                setUnsavedChanges(true);
                return;
            }

            // Se chegou até aqui, não há alterações não salvas.
            setUnsavedChanges(false);
        }

        /**
         * Pega a lista global de pacientes, ordena por criticidade e chama a renderização.
         */
        function sortAndRenderPatientList() {
            if (!currentPatientList || currentPatientList.length === 0) {
                renderPatientList([]); // Garante que a mensagem de "nenhum paciente" apareça
                return;
            }

            const fugulinSeverityMap = {
                'Cuidados Intensivos': 5,
                'Cuidados Semi-Intensivos': 4,
                'Cuidados de Alta Dependência': 3,
                'Cuidados Intermediários': 2,
                'Cuidados Mínimos': 1
            };

            currentPatientList.sort((a, b) => {
                // Critério 1: NEWS2 Score (maior primeiro)
                const scoreA = a.lastNews2Score ?? -1;
                const scoreB = b.lastNews2Score ?? -1;
                if (scoreB !== scoreA) {
                    return scoreB - scoreA;
                }

                // Critério 2: Fugulin (mais grave primeiro)
                const severityA = fugulinSeverityMap[a.lastFugulinClassification] || 0;
                const severityB = fugulinSeverityMap[b.lastFugulinClassification] || 0;
                if (severityB !== severityA) {
                    return severityB - severityA;
                }

                // Critério 3: Data da Última Atualização (mais recente primeiro)
                const timeA = a.lastUpdatedAt ? a.lastUpdatedAt.toMillis() : 0;
                const timeB = b.lastUpdatedAt ? b.lastUpdatedAt.toMillis() : 0;
                return timeB - timeA;
            });

            // Chama a função que redesenha a lista inteira com a nova ordem
            renderPatientList(currentPatientList);
        }


        /**
         * Renderiza a lista COMPLETA de pacientes no DOM, limpando o conteúdo anterior.
         * Esta função é responsável apenas por criar o HTML e os listeners.
         * @param {Array} patients - A lista de pacientes JÁ ORDENADA a ser renderizada.
         */
        function renderPatientList(patients) {
            patientList.innerHTML = ''; // Limpa a lista para evitar duplicatas

            // 1. Extrai todos os números de leito da lista de pacientes atual.
            const availableBeds = [...new Set(patients.map(p => p.roomNumber).filter(Boolean))].sort((a, b) => a - b);
            
            // 2. Chama a função para popular o dropdown do filtro com os leitos encontrados.
            populateBedFilter(availableBeds);

            // Define as classes do container com base no modo de visualização
            if (currentViewMode === 'grid') {
                patientList.className = 'mt-6 px-4 sm:px-0 grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3';
            } else {
                patientList.className = 'mt-6 px-4 sm:px-0 flex flex-col gap-3';
            }
            
            // Lógica da mensagem de "nenhum paciente"
            const hasPatients = patients.length > 0;
            if (noPatientsMessage && patientList) {
                noPatientsMessage.style.display = hasPatients ? 'none' : 'block';
                patientList.style.display = hasPatients ? (currentViewMode === 'list' ? 'flex' : 'grid') : 'none';
            }
            if (!hasPatients) return;

            // A partir daqui, é o seu código original de criação de cards que já estava funcionando
            patients.forEach(patient => {
                const patientName = patient.name || 'Nome Indefinido';
                const patientNumber = patient.patientNumber || 'N/A';
                const roomNumber = patient.roomNumber || 'N/A';
                const age = calculateAge(patient.dob);
                let admissionDate = 'N/A';
                if (patient.createdAt && patient.createdAt.toDate) {
                    admissionDate = patient.createdAt.toDate().toLocaleDateString('pt-BR');
                }

                let news2BadgeHTML = '';
                if (patient.lastNews2Level) {
                    const news2Classes = { 'Risco Baixo': 'news2-low', 'Risco Baixo-Médio': 'news2-low-medium', 'Risco Médio': 'news2-medium', 'Risco Alto': 'news2-high' };
                    const badgeClass = news2Classes[patient.lastNews2Level] || 'bg-gray-100 text-gray-800';
                    news2BadgeHTML = `<span class="status-badge text-xs font-medium px-2.5 py-0.5 rounded-full ${badgeClass}">NEWS ${patient.lastNews2Score}</span>`;
                }

                let fugulinBadgeHTML = '';
                if (patient.lastFugulinClassification) {
                    const fugulinClasses = { 'Cuidados Mínimos': 'fugulin-minimos', 'Cuidados Intermediários': 'fugulin-intermediarios', 'Cuidados de Alta Dependência': 'fugulin-alta-dependencia', 'Cuidados Semi-Intensivos': 'fugulin-semi-intensivos', 'Cuidados Intensivos': 'fugulin-intensivos' };
                    const badgeClass = fugulinClasses[patient.lastFugulinClassification] || 'bg-gray-100 text-gray-800';
                    const textoAbreviado = patient.lastFugulinClassification.replace('Cuidados de ', '').replace('Cuidados ', '');
                    fugulinBadgeHTML = `<span class="status-badge text-xs font-medium px-2.5 py-0.5 rounded-full ${badgeClass}">${textoAbreviado}</span>`;
                }

                let patientElementHTML = '';
                if (currentViewMode === 'grid') {
                    patientElementHTML = `
                        <div class="patient-card bg-white rounded-lg shadow-md hover:shadow-xl transition-shadow duration-300 flex flex-col">
                            <div class="p-4 flex-grow relative">
                                <div class="absolute top-2 right-2">
                                    <button class="options-button p-2 rounded-full hover:bg-gray-100">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5 text-gray-500"><circle cx="12" cy="12" r="1"/><circle cx="12" cy="5" r="1"/><circle cx="12" cy="19" r="1"/></svg>
                                    </button>
                                    <div class="dropdown-menu hidden absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg z-40">
                                        <a href="#" class="edit-patient-button block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Editar Dados Cadastrais</a>
                                        <a href="#" class="delete-patient-button-from-card block px-4 py-2 text-sm text-red-600 hover:bg-gray-100">Arquivar Paciente</a>
                                    </div>
                                </div>
                                <div class="patient-info-wrapper cursor-pointer">
                                    <p class="text-2xl font-bold text-blue-700">${roomNumber}</p>
                                    <h3 class="text-lg font-bold text-gray-800 truncate pr-8 mt-1">${patientName}</h3>
                                    <p class="text-sm text-gray-600 mt-2"><strong>${age} anos</strong></p>
                                    <p class="text-xs text-gray-500">Prontuário: ${patientNumber}</p>
                                </div>
                            </div>
                            <div class="bg-gray-50 border-t px-4 py-3">
                                <div class="flex flex-wrap gap-2 items-center justify-between min-h-[28px]">
                                    <div class="flex flex-wrap gap-2 items-center">
                                        ${news2BadgeHTML || ''}
                                        ${fugulinBadgeHTML || ''}
                                    </div>
                                    <p class="text-xs font-semibold text-gray-500 whitespace-nowrap">INT: ${admissionDate}</p>
                                </div>
                            </div>
                        </div>`;
                } else {
                    patientElementHTML = `
                        <div class="patient-list-item bg-white rounded-lg shadow-sm hover:shadow-md transition-shadow duration-200 flex items-center p-2 sm:p-3">
                            <div class="patient-info-wrapper cursor-pointer flex-grow flex items-center gap-x-2 sm:gap-x-4 min-w-0">
                                <div class="text-center w-14 sm:w-16 flex-shrink-0">
                                    <p class="text-xs text-gray-500">Leito</p>
                                    <p class="text-2xl font-bold text-blue-600">${roomNumber}</p>
                                </div>
                                <div class="flex-1 min-w-0 border-l pl-2 sm:pl-4">
                                    <p class="text-base font-semibold text-gray-900 truncate">${patientName}</p>
                                    <p class="text-sm text-gray-600 mt-1 flex flex-wrap items-center">
                                        <strong class="mr-1">${age} anos</strong>
                                        <span class="hidden sm:inline text-gray-300 mx-1">|</span>
                                        <span class="text-xs w-full sm:w-auto">Pront.: ${patientNumber}</span>
                                    </p>
                                </div>
                            </div>
                            <div class="flex flex-col sm:flex-row items-center gap-x-2 sm:gap-x-3 ml-2 sm:ml-4 flex-shrink-0">
                                ${news2BadgeHTML || ''}
                                ${fugulinBadgeHTML || ''}
                            </div>
                            <div class="relative ml-2 sm:ml-4 flex-shrink-0">
                                <button class="options-button p-2 rounded-full hover:bg-gray-100">
                                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="h-5 w-5 text-gray-500"><circle cx="12" cy="12" r="1"/><circle cx="12" cy="5" r="1"/><circle cx="12" cy="19" r="1"/></svg>
                                </button>
                                <div class="dropdown-menu hidden absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg z-40">
                                    <a href="#" class="edit-patient-button block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Editar Dados cadastrais</a>
                                    <a href="#" class="delete-patient-button-from-card block px-4 py-2 text-sm text-red-600 hover:bg-gray-100">Arquivar Paciente</a>
                                </div>
                            </div>
                        </div>`;
                }
                
                const tempDiv = document.createElement('div');
                tempDiv.innerHTML = patientElementHTML.trim();
                const itemContainer = tempDiv.firstChild;

                if (itemContainer) {
                    itemContainer.dataset.id = patient.id;
                    itemContainer.dataset.name = patientName.toLowerCase();
                    itemContainer.dataset.number = patientNumber;
                    itemContainer.dataset.room = roomNumber;
                    itemContainer.dataset.dob = patient.dob;
                    itemContainer.dataset.news2Score = patient.lastNews2Score || '';
                    itemContainer.dataset.fugulinClass = patient.lastFugulinClassification || '';

                    const optionsButton = itemContainer.querySelector('.options-button');
                    const dropdownMenu = itemContainer.querySelector('.dropdown-menu');
                    const editButton = itemContainer.querySelector('.edit-patient-button');
                    const deleteButton = itemContainer.querySelector('.delete-patient-button-from-card');
                    const patientInfoWrapper = itemContainer.querySelector('.patient-info-wrapper');

                    if (optionsButton && dropdownMenu) {
                        optionsButton.addEventListener('click', (e) => {
                            e.stopPropagation();
                            document.querySelectorAll('.dropdown-menu').forEach(menu => {
                                if (menu !== dropdownMenu) menu.classList.add('hidden');
                            });
                            dropdownMenu.classList.toggle('hidden');
                        });
                    }
                    if (editButton) {
                        editButton.addEventListener('click', (e) => {
                            e.preventDefault();
                            e.stopPropagation();
                            openEditModal(patient);
                            if (dropdownMenu) dropdownMenu.classList.add('hidden');
                        });
                    }
                    if (deleteButton) {
                        deleteButton.addEventListener('click', (e) => {
                            e.preventDefault();
                            e.stopPropagation();
                            currentPatientId = patient.id;
                            deleteConfirmModal.classList.remove('hidden');
                            if (dropdownMenu) dropdownMenu.classList.add('hidden');
                        });
                    }
                    if (patientInfoWrapper) {
                        patientInfoWrapper.addEventListener('click', () => showPatientDetail(patient.id));
                    }
                    patientList.appendChild(itemContainer);
                }
            });
        }

        function openEditModal(patient) {
            document.getElementById('edit-patient-id').value = patient.id;
            document.getElementById('edit-patient-name').value = patient.name || '';
            document.getElementById('edit-patient-dob').value = patient.dob || '';
            document.getElementById('edit-patient-number').value = patient.patientNumber || '';
            document.getElementById('edit-patient-room').value = patient.roomNumber || '';
            document.getElementById('edit-patient-justification').value = '';
            
            // Garante que o modal está visível
            editPatientModal.classList.remove('hidden');
        }

        function filterPatients() {
            const searchTerm = searchPatientInput.value.toLowerCase();
            const selectedBeds = Array.from(bedFilterList.querySelectorAll('input:checked')).map(cb => cb.value);
            
            const cards = document.querySelectorAll('.patient-card, .patient-list-item');
            let found = false;
            
            // CORREÇÃO PRINCIPAL: Determina qual estilo de display usar
            // Se estiver em modo lista, usamos 'flex', senão, o padrão 'block' para a grade.
            const displayStyle = currentViewMode === 'list' ? 'flex' : 'block';

            cards.forEach(card => {
                const nameMatch = (card.dataset.name || '').includes(searchTerm) || (card.dataset.number || '').includes(searchTerm);
                const bedMatch = selectedBeds.length === 0 || selectedBeds.includes(card.dataset.room);
                
                if (nameMatch && bedMatch) {
                    // Aplica o estilo de display correto
                    card.style.display = displayStyle; 
                    found = true;
                } else {
                    card.style.display = 'none';

                }
            });
            
            // A lógica para a mensagem "nenhum paciente" já estava correta
            const noPatientsMessage = document.getElementById('no-patients-message');
            if (noPatientsMessage) {
            noPatientsMessage.style.display = found ? 'none' : 'block';
            }
        }

        /**
         * Preenche o modal de "Última Passagem" e sua seção de adendos.
         * VERSÃO FINAL: Reseta COMPLETAMENTE o estado da UI de adendos a cada abertura.
         */
        function populateLastHandoverModal() {
            if (!currentHandovers || currentHandovers.length === 0) {
                showToast("Nenhum plantão anterior encontrado para este paciente.", "warning");
                return;
            }

            const latestHandover = currentHandovers[0];
            currentlyViewedHandover = latestHandover;
            const lastHandoverTitle = document.getElementById('last-handover-title');

            const lastHandoverSubtitle = document.getElementById('last-handover-subtitle');
            const lastHandoverContent = document.getElementById('last-handover-content');
            const adendosSection = document.getElementById('last-handover-adendos-section');

            // Garante que, ao abrir o modal, a UI de adendos esteja no estado inicial.
            const formWrapper = adendosSection.querySelector('.inline-adendo-form-wrapper');
            const triggerWrapper = adendosSection.querySelector('.add-adendo-trigger-wrapper');
            const toggleBtn = adendosSection.querySelector('.toggle-adendos-view-btn');

            formWrapper.classList.add('hidden');        // 1. Esconde o formulário
            triggerWrapper.classList.remove('hidden'); // 2. Garante que o botão de adicionar esteja visível
            formWrapper.querySelector('textarea').value = ''; // 3. Limpa o texto
            toggleBtn.dataset.state = 'last';             // 4. Reseta o estado da setinha para "recolhido"

            const date = latestHandover.timestamp?.toDate ? latestHandover.timestamp.toDate() : new Date();

            // Preenche o nome do paciente
            if (lastHandoverTitle && currentPatientData) {
                lastHandoverTitle.innerHTML = `Última Passagem de Plantão: <span class="font-bold text-gray-800">${currentPatientData.name || 'Paciente'}</span>`;
            }

            // Preenche o subtítulo com os dados do plantão
            if (lastHandoverSubtitle) {
                lastHandoverSubtitle.innerHTML = `Registrado por <strong>${latestHandover.professionalName || 'N/A'}</strong> em ${date.toLocaleString('pt-BR', { dateStyle: 'long', timeStyle: 'short' })}`;
            }
            
            if (lastHandoverContent) {
                lastHandoverContent.innerHTML = generateDetailedHandoverHtml(latestHandover);
            }
            
            renderAdendosList(latestHandover.adendos, adendosSection);
            
            lastHandoverModal.classList.remove('hidden');
        }
        
        /**
         * Preenche o módulo de monitoramento (áreas de visualização E inputs escondidos)
         * com os dados de um handover específico.
         * @param {object | null} monitoringData - O objeto de monitoramento do último handover.
         */
        function populateMonitoringModule(monitoringData) {
            const data = monitoringData || {};
            const module = document.getElementById('module-monitoramento');
            if (!module) return;

            const fillItem = (id, value) => {
                const input = document.getElementById(id);
                const displayArea = input?.closest('.clickable-item-area')?.querySelector('.monitoring-display-area');
                if (input) input.value = value || '';
                if (displayArea) displayArea.textContent = value || '';
            };

            fillItem('form-sv-pa', data.pa);
            fillItem('form-sv-fc', data.fc);
            fillItem('form-sv-fr', data.fr);
            fillItem('form-sv-sato2', data.sato2);
            fillItem('form-sv-temp', data.temp);
            fillItem('form-sv-hgt', data.hgt);
            fillItem('form-sv-others', data.others);

            // Esta parte agora vai funcionar, pois `data.o2Supplement` e `data.consciencia` existirão
            const o2Checkbox = document.getElementById('form-sv-o2');
            if (o2Checkbox) o2Checkbox.checked = data.o2Supplement || false;

            const conscienciaContainer = document.getElementById('monitoring-consciencia-container');
            if (conscienciaContainer) {
                conscienciaContainer.innerHTML = '';
                const conscienciaText = (data.consciencia && data.consciencia.length > 0) ? data.consciencia[0] : null;
                if (conscienciaText) {
                    const option = monitoringOptions.consciencia.find(opt => opt.text === conscienciaText);
                    if (option) {
                        const tag = createListItem(option.text);
                        tag.dataset.score = option.value;
                        conscienciaContainer.appendChild(tag);
                    }
                }
            }
        }

        /**
         * Trunca um nome longo, mantendo as primeiras palavras até um limite de caracteres,
         * para exibição em telas pequenas.
         * @param {string} fullName - O nome completo do paciente.
         * @param {number} maxLength - O número máximo de caracteres permitidos (padrão: 25).
         * @returns {string} - O nome truncado ou o nome original se for curto o suficiente.
         */
        function truncateNameByWords(fullName, maxLength = 25) {
            // Se a tela for maior que a de um celular ou o nome já for curto, retorna o nome completo.
            if (window.innerWidth > 768 || fullName.length <= maxLength) {
                return fullName;
            }

            const words = fullName.split(' ');
            let truncatedName = '';

            // Constrói o nome palavra por palavra até atingir o limite de caracteres
            for (const word of words) {
                if ((truncatedName + word).length + 1 > maxLength) {
                    break; // Para se a próxima palavra exceder o limite
                }
                truncatedName += word + ' ';
            }

            // Caso a primeira palavra sozinha já seja maior que o limite
            if (truncatedName.trim() === '') {
                return words[0].substring(0, maxLength - 3) + '...';
            }

            // Adiciona "..." se o nome foi de fato truncado
            return truncatedName.trim() + (truncatedName.trim() !== fullName.trim() ? '...' : '');
        }

        // MODIFICADO: Esta função agora apenas cria o registro no histórico e chama a função de renderização.
        async function showPatientDetail(patientId, preloadedData = null, fromHistory = false) {
            // Se não estivermos vindo do histórico (ou seja, foi um clique do usuário),
            // criamos uma nova entrada no histórico.
            if (!fromHistory) {
                history.pushState({ screen: 'patientDetail', patientId: patientId }, `Paciente ${patientId}`, `#paciente/${patientId}`);
            }
            // A função de renderização é chamada em ambos os casos.
            renderPatientDetail(patientId, preloadedData);
        }

        // Esta função contém a lógica para buscar os dados e renderizar a tela de detalhes do paciente.
        // Ela NÃO mexe com o histórico do navegador, evitando loops.
        async function renderPatientDetail(patientId, preloadedData = null) {
            // O corpo inteiro da sua função showPatientDetail antiga vem aqui, exceto a linha history.pushState.
            showActionLoader();
            currentPatientId = patientId;
            currentHistoryPage = 1;
            
            resetFormState();

            try {
                let patientData;

                if (preloadedData) {
                    patientData = preloadedData;
                } else {
                    const patientRef = doc(db, 'patients', patientId);
                    const patientSnap = await getDoc(patientRef);
                    if (patientSnap.exists()) {
                        patientData = patientSnap.data();
                    } else {
                        showToast("Paciente não encontrado.", "error");
                        showScreen('main');
                        return;
                    }
                }
                
                currentPatientData = { id: patientId, ...patientData };

                // Primeiro, carrega os handovers para encontrar os últimos dados.
                const handoversRef = collection(db, 'patients', patientId, 'handovers');
                const q = query(handoversRef, orderBy('timestamp', 'desc'));
                const handoversSnapshot = await getDocs(q);
                const handoversHistory = handoversSnapshot.docs.map(doc => doc.data());
                const lastHandoverWithMonitoring = handoversHistory.find(h => h.monitoring && Object.values(h.monitoring).some(v => v));


                resetFormState();
                populateMonitoringModule(lastHandoverWithMonitoring ? lastHandoverWithMonitoring.monitoring : null);

                // Captura o estado original de TODOS os módulos para calcular as mudanças depois
                originalPatientState = {
                    diagnoses: patientData.activeDiagnoses || [],
                    comorbidities: patientData.activeComorbidities || [],
                    allergies: patientData.activeAllergies || [],
                    precautions: patientData.activePrecautions || [],
                    risks: patientData.activeRisks || { lpp: [], quedas: [], bronco: [], iras: [] },
                    nursingCare: patientData.activeNursingCare || { mobilidade: [], dieta: [], respiracao: [], eliminacoes: [], pele: [] },
                    devices: patientData.activeDevices || [],
                    scheduledExams: patientData.activeScheduledExams || [],
                    pendingExams: patientData.activePendingExams || [],
                    // Adiciona os campos de texto para comparação
                    evolution: patientData.lastEvolution || '',
                    pendingObs: patientData.lastPendingObs || '',
                    monitoring: lastHandoverWithMonitoring ? lastHandoverWithMonitoring.monitoring : {}

                };

                // Preenche os detalhes do cabeçalho
                patientDetailName.textContent = truncateNameByWords(patientData.name);
                patientDetailNumber.textContent = patientData.patientNumber;
                patientDetailRoom.textContent = patientData.roomNumber;
                patientDetailAge.textContent = `${calculateAge(patientData.dob)} anos`;
                const admissionDate = patientData.createdAt?.toDate ? patientData.createdAt.toDate().toLocaleDateString('pt-BR') : 'N/A';
                document.getElementById('patient-detail-admission-date').textContent = admissionDate;

                const patientDetailNews2 = document.getElementById('patient-detail-news2');
                const patientDetailFugulin = document.getElementById('patient-detail-fugulin');

                // Tag NEWS2
                if (patientData.lastNews2Score !== undefined && patientData.lastNews2Level) {
                    const news2Score = patientData.lastNews2Score;
                    const news2Level = patientData.lastNews2Level;
                    const news2Classes = {
                        'Risco Baixo': 'news2-low',
                        'Risco Baixo-Médio': 'news2-low-medium',
                        'Risco Médio': 'news2-medium',
                        'Risco Alto': 'news2-high'
                    };
                    const badgeClass = news2Classes[news2Level] || 'bg-gray-100 text-gray-800';
                    patientDetailNews2.innerHTML = `<span class="text-xs font-medium px-2.5 py-0.5 rounded-full ${badgeClass}">NEWS: ${news2Score} - ${news2Level}</span>`;
                } else {
                    patientDetailNews2.textContent = 'Ainda não calculado.';
                }

                // Tag Fugulin
                if (patientData.lastFugulinClassification) {
                    const fugulinClasses = {
                        'Cuidados Mínimos': 'fugulin-minimos',
                        'Cuidados Intermediários': 'fugulin-intermediarios',
                        'Cuidados de Alta Dependência': 'fugulin-alta-dependencia',
                        'Cuidados Semi-Intensivos': 'fugulin-semi-intensivos',
                        'Cuidados Intensivos': 'fugulin-intensivos'
                    };
                    const badgeClass = fugulinClasses[patientData.lastFugulinClassification] || 'bg-gray-100 text-gray-800';
                    patientDetailFugulin.innerHTML = `<span class="status-badge text-xs font-medium px-2.5 py-0.5 rounded-full ${badgeClass}">Fugulin: ${patientData.lastFugulinScore} - ${patientData.lastFugulinClassification}</span>`;
                } else {
                    patientDetailFugulin.innerHTML = '';
                }

                renderItemsAsList('diagnoses-tags-container', patientData.activeDiagnoses || []);
                renderItemsAsList('comorbidities-tags-container', patientData.activeComorbidities || []);
                renderItemsAsList('allergies-tags-container', patientData.activeAllergies || []);
                renderItemsAsList('precaucoes-container', patientData.activePrecautions || []);
                
                if (patientData.activeRisks) {
                    renderItemsAsList('riscos-lpp-container', patientData.activeRisks.lpp || []);
                    renderItemsAsList('riscos-quedas-container', patientData.activeRisks.quedas || []);
                    renderItemsAsList('riscos-bronco-container', patientData.activeRisks.bronco || []);
                    renderItemsAsList('riscos-iras-container', patientData.activeRisks.iras || []);
                }
                
                if (patientData.activeNursingCare) {
                    const care = patientData.activeNursingCare;
                    const renderFugulinItem = (key, items) => {
                        const kebabKey = key.replace(/([A-Z])/g, '-$1').toLowerCase();
                        const container = document.getElementById(`fugulin-${kebabKey}-container`);
                        if(container && items && items.length > 0) {
                            container.innerHTML = '';
                            items.forEach(itemText => {
                                const option = fugulinOptions[key].find(opt => opt.text === itemText);
                                if (option) {
                                    const tag = createListItem(option.text);
                                    tag.dataset.score = option.value;
                                    container.appendChild(tag);
                                }
                            });
                        }
                    };
                    renderFugulinItem('cuidadoCorporal', care.cuidadoCorporal || []);
                    renderFugulinItem('motilidade', care.motilidade || []);
                    renderFugulinItem('deambulacao', care.deambulacao || []);
                    renderFugulinItem('alimentacao', care.alimentacao || []);
                    renderFugulinItem('eliminacao', care.eliminacao || []);
                }

                currentShiftCompletedExams = []; 
                patientExams = [
                    ...(patientData.activeScheduledExams || []),
                    ...(patientData.activePendingExams || [])
                ];
                renderExams();
                
                setupDispositivosModule();
                const activeDevices = patientData.activeDevices || [];
                originalPatientDevices = [...activeDevices];
                document.querySelectorAll('#dispositivos-grid input[type="checkbox"]').forEach(chk => chk.checked = false);
                customDispositivosContainer.innerHTML = '';
                activeDevices.forEach(deviceName => {
                    const staticCheckbox = dispositivosGrid.querySelector(`input[value="${deviceName}"]`);
                    if (staticCheckbox) { staticCheckbox.checked = true; } 
                    else { addCustomDispositivo(deviceName, true); }
                });
                
                if ((patientData.activeAllergies || []).length > 0) {
                    document.getElementById('allergy-radio-yes').checked = true;
                    document.getElementById('allergy-input-container').classList.remove('hidden');
                } else if (patientData.hasOwnProperty('activeAllergies')) { 
                    document.getElementById('allergy-radio-no').checked = true;
                    document.getElementById('allergy-input-container').classList.add('hidden');
                }
                updateAllergyTitleVisibility();
                if (!patientDetailListenersAttached) {
                    setupAllergyToggle(
                        document.getElementById('allergy-radio-yes'),
                        document.getElementById('allergy-radio-no'),
                        document.getElementById('allergy-input-container'),
                        document.getElementById('allergies-tags-container')
                    );
                    patientDetailListenersAttached = true;
                }
                updateAllergyPlaceholder();
                resetMonitoringModule();
                updateLiveScores();

                showScreen('patientDetail');
                loadHandovers(patientId);
            } catch (error) {
                console.error("Erro ao carregar detalhes do paciente:", error);
                showToast('Erro ao carregar detalhes do paciente.', 'error');
                showScreen('main');
            } finally {
                hideActionLoader();
            }
        }


        async function loadHandovers(patientId) {
            if (unsubscribeHandovers) unsubscribeHandovers();

            const handoversRef = collection(db, 'patients', patientId, 'handovers');
            const q = query(handoversRef, orderBy('timestamp', 'desc'));

            unsubscribeHandovers = onSnapshot(q, (snapshot) => {
                try {
                    const allHandovers = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                    currentHandovers = allHandovers;

                    renderHandoversList(allHandovers);

                    // --- INÍCIO DA LÓGICA DE ATUALIZAÇÃO EM TEMPO REAL ---
                    // Verifica se um dos modais de detalhe está aberto
                    const isLastHandoverModalOpen = !lastHandoverModal.classList.contains('hidden');
                    const isViewHandoverModalOpen = !viewHandoverModal.classList.contains('hidden');

                    if (isLastHandoverModalOpen || isViewHandoverModalOpen) {
                        // Encontra o handover que está sendo visualizado na nova lista de dados
                        const updatedHandoverData = allHandovers.find(h => h.id === currentlyViewedHandover.id);
                        
                        if (updatedHandoverData) {
                            currentlyViewedHandover = updatedHandoverData; // Atualiza a variável global
                            // Repopula o modal que estiver aberto com os novos dados (incluindo o novo adendo)
                            if (isLastHandoverModalOpen) {
                                populateLastHandoverModal();
                            }
                            if (isViewHandoverModalOpen) {
                                populateHandoverViewModal(updatedHandoverData);
                            }
                        }
                    }
                    // --- FIM DA LÓGICA DE ATUALIZAÇÃO EM TEMPO REAL ---

                } catch (error) {
                    console.error("Erro ao processar o snapshot do histórico:", error);
                    showToast("Erro ao carregar atualizações do histórico.", "error");
                }
            }, (error) => {
                console.error("Erro de conexão ao buscar histórico:", error);
                showToast("Não foi possível conectar para buscar o histórico.", "error");
            });
        }

        /**
         * Cria o corpo HTML detalhado de um registro de handover.
         * É robusto para lidar com formatos de dados antigos e novos (legado).
         * @param {object} handover - O objeto de handover do Firestore.
         * @returns {string} - Uma string HTML contendo a lista de detalhes.
         */
        function createHandoverDetailHtml(handover) {
            // Helper interno para renderizar um campo somente se ele tiver valor
            const renderField = (label, value) => {
                if (!value || (typeof value === 'string' && value.trim() === '')) return '';
                return `<div class="py-2 sm:grid sm:grid-cols-3 sm:gap-4"><dt class="text-sm font-medium text-gray-600">${label}</dt><dd class="mt-1 text-sm text-gray-800 sm:mt-0 sm:col-span-2 whitespace-pre-wrap">${value}</dd></div>`;
            };

            let detailHtml = '<dl>';

            // --- Seção de Evolução e Alterações de Estado ---
            // Lida com campos de nomes antigos (legado) usando ||
            detailHtml += renderField('Evolução / Plano', handover.evolution || handover.clinicalSituation?.evolution);

            if (handover.fugulin) {
                detailHtml += renderField('Classificação Fugulin', `<strong>${handover.fugulin.score} - ${handover.fugulin.classification}</strong>`);
            }

            const { changes } = handover;
            if (changes) {
                const changeLog = [];
                const renderChangeList = (items, prefix, icon) => items.map(item => `<li>${icon} ${prefix}: <strong>${item}</strong></li>`).join('');
                
                if (changes.diagnoses?.added?.length) changeLog.push(renderChangeList(changes.diagnoses.added, 'Adicionado Diagnóstico', '✅'));
                if (changes.diagnoses?.removed?.length) changeLog.push(renderChangeList(changes.diagnoses.removed, 'Removido Diagnóstico', '❌'));
                // Adicione mais lógicas de 'changes' aqui se necessário (comorbidades, alergias, etc.)

                if (changeLog.length > 0) {
                    detailHtml += `<div class="py-2"><dt class="text-sm font-medium text-gray-600">Alterações de Estado:</dt><dd><ul class="list-none mt-1 text-sm space-y-1">${changeLog.join('')}</ul></dd></div>`;
                }
            }

            // --- Seção de Medicações ---
            if (handover.medications && handover.medications.length > 0) {
                const medsHtml = `<ul class="list-none space-y-1">${handover.medications.map(m => 
                    `<li>💊 <strong>${m.name}</strong> às ${m.times.map(
                        ts => new Date(ts).toLocaleTimeString('pt-BR', {hour: '2-digit', minute:'2-digit'})
                    ).join(', ')}</li>`
                ).join('')}</ul>`;
                detailHtml += renderField('Medicações Administradas', medsHtml);
            }
            
            // --- Seção de Exames (Lógica Robusta) ---
            const examsDoneData = handover.examsDone;
            let examsDoneHtml = '';
            if (Array.isArray(examsDoneData) && examsDoneData.length > 0) {
                // Formato NOVO (array de objetos)
                examsDoneHtml = '<ul class="list-none space-y-1 pl-2">' + examsDoneData.map(exam => `<li><strong>${exam.name}:</strong> ${exam.result}</li>`).join('') + '</ul>';
            } else if (typeof examsDoneData === 'string' && examsDoneData.trim() !== '') {
                // Formato ANTIGO (string)
                examsDoneHtml = `<p class="whitespace-pre-wrap">${examsDoneData}</p>`;
            }
            detailHtml += renderField('Exames Realizados', examsDoneHtml);

            // --- Seção de Monitoramento ---
            const mon = handover.monitoring;
            if (mon && Object.values(mon).some(v => v)) {
                const monitoringText = `PA: ${mon.pa||'-'} | FC: ${mon.fc||'-'} | FR: ${mon.fr||'-'} | SatO2: ${mon.sato2||'-'} | Temp: ${mon.temp||'-'} | HGT: ${mon.hgt||'-'}`;
                detailHtml += renderField('📈 Monitoramento', monitoringText);
                detailHtml += renderField('Outros (Diurese/Dreno)', mon.others);
            }

            // --- Seção de Pendências (Lógica Robusta com Legado) ---
            detailHtml += renderField('⚠️ Pendências', handover.pendingObs || handover.pending?.description);

            detailHtml += '</dl>';
            return detailHtml;
        }

        // --- FUNÇÕES DE RENDERIZAÇÃO DE PLANTÕES ---

        /**
         * Renderiza o histórico de plantões com um sistema de paginação DENTRO DO MODAL.
         * @param {object[]} handovers - A lista completa de todos os handovers.
         */
        function renderHandoversList(handovers) {
            const modalContent = document.getElementById('full-history-content');
            const modalPagination = document.getElementById('full-history-pagination');

            // Limpa o conteúdo anterior do modal
            modalContent.innerHTML = '';
            modalPagination.innerHTML = '';

            if (handovers.length === 0) {
                // Se não há histórico, o modal mostrará uma mensagem vazia, o que está correto.
                return;
            }

            // --- LÓGICA DE PAGINAÇÃO ---
            const ITEMS_PER_PAGE = 10;
            const totalPages = Math.ceil(handovers.length / ITEMS_PER_PAGE);
            if (currentHistoryPage > totalPages) currentHistoryPage = totalPages > 0 ? totalPages : 1;

            const startIndex = (currentHistoryPage - 1) * ITEMS_PER_PAGE;
            const endIndex = startIndex + ITEMS_PER_PAGE;
            const paginatedHandovers = handovers.slice(startIndex, endIndex);

            // --- RENDERIZAÇÃO DA LISTA DE ITENS DA PÁGINA ATUAL NO MODAL ---
            paginatedHandovers.forEach(h => {
                const hDate = h.timestamp?.toDate ? h.timestamp.toDate() : new Date();
                const hFormattedDate = hDate.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric' });
                const hFormattedTime = hDate.toLocaleString('pt-BR', { hour: '2-digit', minute: '2-digit' });

                const historyItem = document.createElement('button');
                historyItem.className = 'history-item-button';
                historyItem.dataset.handoverId = h.id;
                
                historyItem.innerHTML = `
                    <div class="history-item-info">
                        <div class="history-item-icon">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2"><path stroke-linecap="round" stroke-linejoin="round" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-3 7h3m-3 4h3m-6-4h.01M9 16h.01" /></svg>
                        </div>
                        <div>
                            <p class="font-semibold text-gray-800">${h.professionalName || 'Profissional não identificado'}</p>
                            <p class="text-sm text-gray-500">${hFormattedDate} às ${hFormattedTime}</p>
                        </div>
                    </div>
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2"><path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" /></svg>
                `;
                modalContent.appendChild(historyItem);
            });

            // --- RENDERIZAÇÃO DOS CONTROLES DE PAGINAÇÃO ---
            if (totalPages > 1) {
                let paginationHtml = '<div class="flex items-center justify-between w-full">';

                // Botão "Anterior"
                paginationHtml += `<button data-page="${currentHistoryPage - 1}" class="page-button flex items-center px-4 py-2 text-sm font-medium text-gray-700 bg-white border border-gray-300 rounded-md hover:bg-gray-50 ${currentHistoryPage === 1 ? 'opacity-50 cursor-not-allowed' : ''}" ${currentHistoryPage === 1 ? 'disabled' : ''}>&larr; Anterior</button>`;

                // Indicador de página
                paginationHtml += `<span class="text-sm text-gray-700">Página <strong>${currentHistoryPage}</strong> de <strong>${totalPages}</strong></span>`;

                // Botão "Próximo"
                paginationHtml += `<button data-page="${currentHistoryPage + 1}" class="page-button flex items-center px-4 py-2 text-sm font-medium text-gray-700 bg-white border border-gray-300 rounded-md hover:bg-gray-50 ${currentHistoryPage === totalPages ? 'opacity-50 cursor-not-allowed' : ''}" ${currentHistoryPage === totalPages ? 'disabled' : ''}>Próximo &rarr;</button>`;

                paginationHtml += '</div>';
                modalPagination.innerHTML = paginationHtml;
            }
        }

        // Esta função cria uma seção do modal, com título, ícone e conteúdo.
        // Se o conteúdo for vazio, ela exibe uma mensagem padrão.
        function renderSummarySection(title, icon, content) {
            let bodyHtml;

            // Se o conteúdo estiver vazio (null ou string vazia), mostra a mensagem padrão.
            if (!content || content.trim() === '') {
                bodyHtml = '<p class="text-gray-500 italic px-2">Nenhum registro encontrado.</p>';
            } else {
                // Se houver conteúdo, exibe-o.
                bodyHtml = `<div class="text-gray-700 space-y-2 px-2">${content}</div>`;
            }

            return `
                <div class="mb-5">
                    <h3 class="text-lg font-semibold text-gray-800 border-b pb-2 mb-3 flex items-center">${icon} ${title}</h3>
                    ${bodyHtml}
                </div>
            `;
        }
        
        /**
         * Salva um novo adendo em um registro de handover específico no Firestore,
         * criando e enviando notificações para todos os profissionais envolvidos.
         * @param {string} handoverId - O ID do documento de handover.
         * @param {string} adendoText - O texto do adendo.
         * @param {HTMLButtonElement} submitButton - O botão que acionou o salvamento.
         */
        async function saveAdendo(handoverId, adendoText, submitButton) {
            if (!adendoText.trim()) {
                showToast("O texto do adendo não pode estar vazio.", "error");
                return;
            }

            const originalButtonText = submitButton.innerHTML;
            submitButton.disabled = true;
            submitButton.innerHTML = 'Salvando...';

            try {
                const handoverRef = doc(db, 'patients', currentPatientId, 'handovers', handoverId);
                
                // 1. Busca os dados atuais do handover para identificar os envolvidos
                const handoverSnap = await getDoc(handoverRef);
                if (!handoverSnap.exists()) {
                    throw new Error("A passagem de plantão original não foi encontrada.");
                }
                const handoverData = handoverSnap.data();

                // 2. Monta a lista de destinatários da notificação (usando um Set para evitar duplicatas)
                const recipients = new Set();
                
                // Adiciona o autor original do plantão
                if (handoverData.professionalId) {
                    recipients.add(handoverData.professionalId);
                }
                
                // Adiciona todos que já fizeram adendos anteriormente
                if (Array.isArray(handoverData.adendos)) {
                    handoverData.adendos.forEach(adendo => {
                        if (adendo.professionalId) {
                            recipients.add(adendo.professionalId);
                        }
                    });
                }
                
                // Garante que não notificamos o próprio autor do adendo atual
                recipients.delete(currentUser.uid);

                console.log(`[saveAdendo] Adendo salvo por ${currentUser.displayName}. Notificando ${recipients.size} usuários:`, Array.from(recipients));

                // 3. Prepara o novo adendo
                const newAdendoData = {
                    text: adendoText.trim(),
                    professionalId: currentUser.uid,
                    professionalName: currentUser.displayName,
                    timestamp: new Date() // Usar new Date() é mais confiável para arrayUnion
                };

                // 4. Cria um "batch" para salvar o adendo e as notificações de uma vez
                const batch = writeBatch(db);

                // Adiciona a atualização do handover ao batch
                batch.update(handoverRef, {
                    adendos: arrayUnion(newAdendoData)
                });

                // 5. Cria uma notificação para cada destinatário
                if (recipients.size > 0) {
                    recipients.forEach(recipientUid => {
                        const notificationRef = doc(collection(db, 'notifications'));
                        batch.set(notificationRef, {
                            recipientUid: recipientUid,
                            actorUid: currentUser.uid,
                            actorName: currentUser.displayName || 'Usuário desconhecido',
                            patientId: currentPatientId,
                            patientName: currentPatientData.name || 'Paciente desconhecido',
                            handoverId: handoverId,
                            type: 'new_adendo',
                            timestamp: serverTimestamp(),
                            read: false
                        });
                    });
                }

                // 6. Executa todas as operações
                await batch.commit();

                showToast("Adendo salvo com sucesso!");

            } catch (error) {
                console.error("Erro ao salvar adendo e criar notificações:", error);
                showToast(`Erro ao salvar: ${error.message}`, 'error');
            } finally {
                submitButton.disabled = false;
                submitButton.innerHTML = originalButtonText;
                
                // Esconde o formulário após salvar
                const formWrapper = submitButton.closest('.inline-adendo-form-wrapper');
                if (formWrapper) {
                    formWrapper.querySelector('textarea').value = '';
                    formWrapper.classList.add('hidden');
                    formWrapper.closest('.adendos-container').querySelector('.add-adendo-trigger-wrapper').classList.remove('hidden');
                }
            }
        }

        /**
         * Renderiza a lista de adendos, controlando a visibilidade de "último" vs "todos".
         * VERSÃO REFORÇADA: Gerencia explicitamente a visibilidade de todos os elementos
         * para eliminar bugs de estado ao navegar entre modais.
         */
        function renderAdendosList(adendos = [], adendosSection) {
            if (!adendosSection) {
                console.error("renderAdendosList foi chamada sem uma seção de adendos válida.");
                return;
            }

            const listContainer = adendosSection.querySelector('.adendos-list');
            const visibleWrapper = adendosSection.querySelector('[id^="adendos-visible-wrapper"]');
            const toggleBtn = adendosSection.querySelector('.toggle-adendos-view-btn');
            const triggerWrapper = adendosSection.querySelector('.add-adendo-trigger-wrapper');
            const formWrapper = adendosSection.querySelector('.inline-adendo-form-wrapper');

            // --- INÍCIO DA LÓGICA REFORÇADA ---
            
            // Garante que o formulário esteja escondido e o botão de adicionar visível por padrão
            if (formWrapper.classList.contains('hidden')) {
                triggerWrapper.classList.remove('hidden');
            }

            const hasAdendos = adendos && adendos.length > 0;

            if (hasAdendos) {
                // Se HÁ adendos:
                visibleWrapper.classList.remove('hidden');
                triggerWrapper.classList.remove('justify-end');
                triggerWrapper.classList.add('justify-start');
            } else {
                // Se NÃO HÁ adendos:
                visibleWrapper.classList.add('hidden');
                triggerWrapper.classList.remove('justify-start');
                triggerWrapper.classList.add('justify-end');
            }
            
            // --- FIM DA LÓGICA REFORÇADA ---

            if (!hasAdendos) {
                listContainer.innerHTML = '';
                return;
            };

            listContainer.innerHTML = '';
            const sortedAdendos = [...adendos].sort((a, b) => (a.timestamp?.toMillis() || 0) - (b.timestamp?.toMillis() || 0));
            const currentState = toggleBtn.dataset.state; // Lê o estado atual
            const itemsToRender = currentState === 'all' ? sortedAdendos : [sortedAdendos[sortedAdendos.length - 1]];

            itemsToRender.forEach(adendo => {
                const date = adendo.timestamp?.toDate ? adendo.timestamp.toDate() : new Date();
                const formattedDate = date.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', hour: '2-digit', minute: '2-digit' });
                
                const adendoEl = document.createElement('div');
                adendoEl.className = 'adendo-bubble';
                adendoEl.innerHTML = `
                    <p class="text-gray-800 whitespace-pre-wrap">${adendo.text}</p>
                    <p class="adendo-meta">
                        <strong>${adendo.professionalName}</strong> - ${formattedDate}
                    </p>
                `;
                listContainer.appendChild(adendoEl);
            });
            
            // Lógica dos ÍCONES (agora mais explícita)
            const expandIcon = toggleBtn.querySelector('.icon-expand');
            const collapseIcon = toggleBtn.querySelector('.icon-collapse');

            if (sortedAdendos.length > 1) {
                toggleBtn.classList.remove('hidden'); // Garante que o botão apareça
                if (currentState === 'all') {
                    expandIcon.classList.add('hidden');
                    collapseIcon.classList.remove('hidden');
                    toggleBtn.title = 'Ver apenas último adendo';
                } else {
                    expandIcon.classList.remove('hidden');
                    collapseIcon.classList.add('hidden');
                    toggleBtn.title = `Ver todos os ${sortedAdendos.length} adendos`;
                }
            } else {
                toggleBtn.classList.add('hidden'); // Garante que o botão suma se só tiver 1 adendo
            }
            
            listContainer.scrollTop = listContainer.scrollHeight;
        }

        /**
         * NOVA FUNÇÃO DE IMPRESSÃO
         * Coleta os dados do resumo semanal, converte o gráfico para imagem e monta um HTML para impressão.
         */
        async function handlePrintSummary() {
            const printView = document.getElementById('print-view');
            if (!currentPatientData || !weeklySummaryChart) {
                showToast("Dados do resumo ainda não carregados. Tente novamente.", "error");
                return;
            }
            
            showToast("Preparando impressão...", 2000);

            // 1. RE-BUSCAR OS DADOS para garantir que a impressão é fiel ao que está no banco
            const sevenDaysAgo = new Date();
            sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7);
            const handoversRef = collection(db, 'patients', currentPatientId, 'handovers');
            const q = query(handoversRef, where('timestamp', '>=', Timestamp.fromDate(sevenDaysAgo)), orderBy('timestamp', 'asc'));
            const snapshot = await getDocs(q);
            const recentHandovers = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));

            // 2. PROCESSAR OS DADOS (reutilizando as funções que já criamos)
            const kpis = processKPIs(recentHandovers);
            const events = processEventsForTable(recentHandovers.slice().reverse());
            const chartImage = weeklySummaryChart.toBase64Image(); // Converte o gráfico em imagem!

            // 3. COLETAR DADOS DO CABEÇALHO DO PACIENTE
            const { name, patientNumber, roomNumber, dob } = currentPatientData;
            const age = calculateAge(dob);

            // 4. MONTAR O HTML PARA IMPRESSÃO (com estilos inline para garantir a formatação)
            printView.innerHTML = `
                <div style="font-family: Arial, sans-serif; padding: 20px; color: #333;">
                    <div style="text-align: center; border-bottom: 2px solid #000; padding-bottom: 10px; margin-bottom: 20px;">
                        <h1 style="font-size: 24px; margin: 0;">Resumo Clínico da Semana</h1>
                        <h2 style="font-size: 18px; margin: 5px 0;">${name}</h2>
                        <p style="font-size: 12px; margin: 0;">
                            <strong>Leito:</strong> ${roomNumber} | 
                            <strong>Prontuário:</strong> ${patientNumber} | 
                            <strong>Idade:</strong> ${age} anos
                        </p>
                    </div>

                    <div style="margin-bottom: 20px; page-break-inside: avoid;">
                        <h3 style="font-size: 16px; font-weight: bold; margin-bottom: 10px;">Destaques da Semana</h3>
                        <table style="width: 100%; border-collapse: collapse; font-size: 11px;">
                            <tbody>
                                <tr>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Pico de FC:</strong> ${kpis.maxFC.value || 'N/A'} bpm</td>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Menor PAS:</strong> ${kpis.minPAS.value || 'N/A'} mmHg</td>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Pico Febril:</strong> ${kpis.maxTemp.value ? kpis.maxTemp.value.toFixed(1) : 'N/A'} °C</td>
                                </tr>
                                <tr>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Menor SatO₂:</strong> ${kpis.minSatO2.value || 'N/A'} %</td>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Pico de FR:</strong> ${kpis.maxFR.value || 'N/A'} irpm</td>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Maior NEWS2:</strong> ${kpis.maxNEWS2.value || 'N/A'}</td>
                                </tr>
                                <tr>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Hipoglicemias:</strong> ${kpis.hypoglycemiaEpisodes.count}</td>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Episódios de Febre:</strong> ${kpis.feverEpisodes.count}</td>
                                    <td style="border: 1px solid #ddd; padding: 5px;"><strong>Meds SOS:</strong> ${kpis.sosMedCount.count} doses</td>
                                </tr>
                            </tbody>
                        </table>
                    </div>

                    <div style="margin-bottom: 20px; page-break-inside: avoid;">
                        <h3 style="font-size: 16px; font-weight: bold; margin-bottom: 10px;">Tendências de Sinais Vitais e Scores</h3>
                        <img src="${chartImage}" style="width: 100%; max-width: 100%; border: 1px solid #eee;" alt="Gráfico de Tendências"/>
                    </div>

                    <div>
                        <h3 style="font-size: 16px; font-weight: bold; margin-bottom: 10px;">Linha do Tempo de Eventos Relevantes</h3>
                        <table style="width: 100%; border-collapse: collapse; font-size: 11px;">
                            <thead style="background-color: #f2f2f2; text-align: left;">
                                <tr>
                                    <th style="border: 1px solid #ddd; padding: 5px;">Data/Hora</th>
                                    <th style="border: 1px solid #ddd; padding: 5px;">Categoria</th>
                                    <th style="border: 1px solid #ddd; padding: 5px;">Evento / Descrição</th>
                                    <th style="border: 1px solid #ddd; padding: 5px;">Profissional</th>
                                </tr>
                            </thead>
                            <tbody>
                                ${events.map(event => `
                                    <tr style="page-break-inside: avoid;">
                                        <td style="border: 1px solid #ddd; padding: 5px; white-space: nowrap;">${event.timestamp.toLocaleString('pt-BR', {day: '2-digit', month: '2-digit', hour: '2-digit', minute: '2-digit'})}</td>
                                        <td style="border: 1px solid #ddd; padding: 5px; white-space: nowrap;">${event.category}</td>
                                        <td style="border: 1px solid #ddd; padding: 5px;">${event.description}</td>
                                        <td style="border: 1px solid #ddd; padding: 5px;">${event.professional}</td>
                                    </tr>
                                `).join('')}
                                ${events.length === 0 ? '<tr><td colspan="4" style="text-align: center; padding: 10px; border: 1px solid #ddd;">Nenhum evento relevante.</td></tr>' : ''}
                            </tbody>
                        </table>
                    </div>
                </div>
            `;

            // 5. Aciona a impressão do navegador
            setTimeout(() => {
                window.print();
            }, 200); // Um pequeno delay para garantir que a imagem do gráfico seja renderizada
        }

        /**
         * Orquestra a busca de dados e a renderização do novo modal de resumo da semana.
         */
        async function showWeeklySummary() {
            const loader = document.getElementById('summary-loader');
            const contentContainer = document.getElementById('summary-main-container');
            const titleElement = document.getElementById('patient-summary-title');
            const subtitle = document.getElementById('patient-summary-subtitle');
            
            // Define o título estático e preenche o nome do paciente de forma destacada
            if (titleElement) {
                titleElement.textContent = 'Resumo Clínico da Semana';
            }
            if (titleElement && currentPatientData) {
                titleElement.innerHTML = `Resumo Clínico Semanal: <span class="font-bold text-gray-800">${currentPatientData.name || 'Paciente'}</span>`;
            }

            // 1. Prepara a UI: mostra o loader e esconde o conteúdo
            loader.style.display = 'block';
            contentContainer.classList.add('hidden');
            subtitle.textContent = `Analisando dados dos últimos 7 dias...`;
            patientSummaryModal.classList.remove('hidden');

            try {
                // 2. Define o período de busca (últimos 7 dias)
                const sevenDaysAgo = new Date();
                sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7);
                const sevenDaysAgoTimestamp = Timestamp.fromDate(sevenDaysAgo);

                // 3. Busca os plantões do paciente no período
                const handoversRef = collection(db, 'patients', currentPatientId, 'handovers');
                const q = query(handoversRef, where('timestamp', '>=', sevenDaysAgoTimestamp), orderBy('timestamp', 'asc')); // asc para o gráfico
                
                const snapshot = await getDocs(q);
                const recentHandovers = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));

                if (recentHandovers.length === 0) {
                    loader.innerHTML = '<p class="text-gray-600 p-10">Nenhum registro encontrado nos últimos 7 dias.</p>';
                    return;
                }
                
                // 4. CHAMA AS FUNÇÕES PARA PROCESSAR E RENDERIZAR CADA BLOCO
                // (Estas funções serão criadas nos próximos passos)
                
                // PASSO 1: KPIs
                const kpis = processKPIs(recentHandovers);
                renderKPIs(kpis);

                // PASSO 3: Gráfico
                const chartData = prepareChartData(recentHandovers);
                renderTrendsChart(chartData);
                
                // PASSO 2: Tabela de Eventos
                const events = processEventsForTable(recentHandovers.slice().reverse()); // Passa uma cópia revertida para a tabela
                renderEventsTable(events);
                
                // 5. Finaliza a UI: esconde o loader e mostra o conteúdo
                subtitle.textContent = `Dados de ${sevenDaysAgo.toLocaleDateString('pt-BR')} até hoje.`;
                loader.style.display = 'none';
                contentContainer.classList.remove('hidden');

            } catch (error) {
                console.error("Erro ao gerar resumo da semana:", error);
                loader.innerHTML = `<p class="text-red-600 p-10">Ocorreu um erro ao buscar os dados: ${error.message}</p>`;
                showToast("Erro ao carregar o resumo da semana.", "error");
            }
        }

        /**
         * NOVA FUNÇÃO MESTRE PARA HISTORICO DE PLANTAO
         * Gera o corpo HTML detalhado e estilizado para QUALQUER registro de handover.
         * Esta função centraliza a lógica de exibição para garantir consistência visual.
         * @param {object} handover - O objeto de handover do Firestore.
         * @returns {string} - Uma string HTML contendo o conteúdo detalhado do modal.
         */
        function generateDetailedHandoverHtml(handover) {
            if (!handover) return '<p class="text-center text-gray-500 italic">Dados da passagem de plantão não encontrados.</p>';

            // Mapeamento de títulos para exibição correta
            const titleMap = {
                cuidadoCorporal: 'Cuidado Corporal / Pele',
                motilidade: 'Motilidade / Movimentação',
                deambulacao: 'Deambulação',
                alimentacao: 'Alimentação / Hidratação',
                eliminacao: 'Cuidado com Eliminações'
            };

            const formatDate = (timestamp) => {
                if (!timestamp) return 'Data indefinida';
                const date = timestamp.toDate ? timestamp.toDate() : new Date(timestamp);
                return date.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric', hour: '2-digit', minute: '2-digit' });
            };

            const renderListAsCommaString = (items) => {
                if (!items || items.length === 0) return '<span class="text-gray-500 text-xs italic">Nenhum registro.</span>';
                return items.join(', ');
            };

            const renderObjectAsList = (obj, map) => {
                const entries = Object.entries(obj).filter(([_, v]) => v && v.length > 0);
                if (entries.length === 0) return '<span class="text-gray-500 text-xs italic">Nenhum registro.</span>';
                
                return `<ul>${entries.map(([key, value]) => `<li><strong>${map[key] || key}:</strong> ${value.join(', ')}</li>`).join('')}</ul>`;
            };

            let contentHtml = '';

            // Seção: Diagnóstico e Evolução
            contentHtml += `<div class="summary-section">
                <h3 class="summary-section-header">⚕️ Diagnóstico e Evolução</h3>
                <div class="summary-item"><span class="summary-item-label">Diagnósticos:</span><span class="summary-item-value">${renderListAsCommaString(handover.diagnoses)}</span></div>
                <div class="summary-item"><span class="summary-item-label">Comorbidades:</span><span class="summary-item-value">${renderListAsCommaString(handover.comorbidities)}</span></div>
                <div class="summary-item"><span class="summary-item-label">Alergias:</span><span class="summary-item-value">${renderListAsCommaString(handover.allergies)}</span></div>
                <div class="summary-item"><span class="summary-item-label">Evolução / Plano do Plantão:</span><p class="summary-item-value whitespace-pre-wrap">${handover.evolution || '<span class="text-gray-500 text-xs italic">Nenhuma.</span>'}</p></div>
            </div>`;

            // Seção: Segurança do Paciente
            let risksHtml = '<span class="text-gray-500 text-xs italic">Nenhum risco avaliado.</span>';
            if (handover.risks && Object.values(handover.risks).some(v => v && v.length > 0)) {
                const riskLabelMap = { lpp: 'LPP', quedas: 'Quedas', bronco: 'Broncoaspiração', iras: 'IRAS' };
                risksHtml = Object.entries(handover.risks)
                    .filter(([_, value]) => value && value.length > 0)
                    .map(([key, value]) => `<strong>${riskLabelMap[key] || key}:</strong> ${value.join(', ')}`)
                    .join('<br>');
            }
            contentHtml += `<div class="summary-section">
                <h3 class="summary-section-header">🛡️ Segurança do Paciente</h3>
                <div class="summary-item"><span class="summary-item-label">Riscos Assistenciais:</span><div class="summary-item-value">${risksHtml}</div></div>
                <div class="summary-item"><span class="summary-item-label">Precauções:</span><span class="summary-item-value">${renderListAsCommaString(handover.precautions)}</span></div>
                <div class="summary-item"><span class="summary-item-label">Dispositivos:</span><span class="summary-item-value">${renderListAsCommaString(handover.devices)}</span></div>
            </div>`;
            
            // Seção: Cuidados de Enfermagem
            contentHtml += `<div class="summary-section">
                <h3 class="summary-section-header">🩺 Cuidados de Enfermagem</h3>
                <div class="summary-item-value">${renderObjectAsList(handover.nursingCare || {}, titleMap)}</div>
            </div>`;
            
            // Seção: Monitoramento e Scores
            const mon = handover.monitoring;
            let monitoringDetailsHtml = '<span class="text-gray-500 text-xs italic">Nenhum dado de monitoramento registrado.</span>';
            if (mon && Object.values(mon).some(v => v)) {
                const fields = [{ label: 'PA', value: mon.pa }, { label: 'FC', value: mon.fc }, { label: 'FR', value: mon.fr }, { label: 'SatO₂', value: mon.sato2 }, { label: 'Temp', value: mon.temp }, { label: 'HGT', value: mon.hgt }];
                monitoringDetailsHtml = fields.filter(f => f.value).map(f => `<strong>${f.label}:</strong> ${f.value}`).join(' | ');
                if (mon.others) {
                    monitoringDetailsHtml += `<div class="mt-2"><span class="summary-item-label">Diurese / Drenos / Outros:</span><p class="summary-item-value whitespace-pre-wrap">${mon.others}</p></div>`;
                }
            }
            contentHtml += `<div class="summary-section">
                <h3 class="summary-section-header">📈 Monitoramento e Scores</h3>
                <div class="summary-item"><span class="summary-item-label">Scores:</span><span class="summary-item-value"><strong>NEWS:</strong> ${handover.news2?.score ?? 'N/A'} (${handover.news2?.level ?? 'N/A'}) | <strong>Fugulin:</strong> ${handover.fugulin?.score ?? 'N/A'} (${handover.fugulin?.classification ?? 'N/A'})</span></div>
                <div class="summary-item mt-3"><span class="summary-item-label">Sinais Vitais Registrados:</span><div class="summary-item-value">${monitoringDetailsHtml}</div></div>
            </div>`;

            // Seção: Medicações
            contentHtml += `<div class="summary-section">
                <h3 class="summary-section-header">💉 Medicações do Plantão</h3>
                <div class="summary-item-value">${handover.medications && handover.medications.length > 0 ? `<ul>${handover.medications.map(m => `<li><strong>${m.name}</strong> às ${m.times.map(ts => new Date(ts).toLocaleTimeString('pt-BR', {hour: '2-digit', minute:'2-digit'})).join(', ')}</li>`).join('')}</ul>` : '<span class="text-gray-500 text-xs italic">Nenhuma medicação administrada.</span>'}</div>
            </div>`;

            // Seção: Exames e Procedimentos
            let examsHtml = '';
            if (handover.examsDone?.length > 0) examsHtml += `<div class="summary-item"><span class="summary-item-label">Resultados no Plantão:</span><div class="summary-item-value"><ul>${handover.examsDone.map(e => `<li><strong>${e.name}:</strong> ${e.result || 'não informado'} <span class="text-xs text-gray-500 italic">- Em ${formatDate(e.timestamp)}</span></li>`).join('')}</ul></div></div>`;
            if (handover.scheduledExams?.length > 0) examsHtml += `<div class="summary-item"><span class="summary-item-label">Deixou Agendado:</span><div class="summary-item-value"><ul>${handover.scheduledExams.map(e => `<li><strong>${e.name}</strong> - <span class="text-xs text-gray-500 italic">Para ${formatDate(e.timestamp)}</span></li>`).join('')}</ul></div></div>`;
            if (handover.pendingExams?.length > 0) examsHtml += `<div class="summary-item"><span class="summary-item-label">Deixou Pendente de Resultado:</span><div class="summary-item-value"><ul>${handover.pendingExams.map(e => `<li><strong>${e.name}</strong> - <span class="text-xs text-gray-500 italic">Realizado em ${formatDate(e.timestamp)}</span></li>`).join('')}</ul></div></div>`;
            contentHtml += `<div class="summary-section">
                <h3 class="summary-section-header">🧪 Exames e Procedimentos</h3>
                ${examsHtml || '<span class="text-gray-500 text-xs italic">Nenhuma atividade de exame registrada.</span>'}
            </div>`;
            
            // Seção: Observações
            contentHtml += `<div class="summary-section">
                <h3 class="summary-section-header">📝 Observações e Pendências</h3>
                <p class="summary-item-value whitespace-pre-wrap">${handover.pendingObs || '<span class="text-gray-500 text-xs italic">Nenhuma.</span>'}</p>
            </div>`;

            return contentHtml;
        }

        /**
         * Preenche o modal de visualização de um handover específico.
         * VERSÃO FINAL: Reseta COMPLETAMENTE o estado da UI de adendos a cada abertura.
         */
        function populateHandoverViewModal(handover) {
            if (!handover) return;

            currentlyViewedHandover = handover;

            const viewHandoverMainTitle = document.getElementById('view-handover-main-title');
            
            const viewHandoverSubtitle = document.getElementById('view-handover-subtitle');
            const viewHandoverContent = document.getElementById('view-handover-content');
            const adendosSection = document.getElementById('view-handover-adendos-section'); 

            // Garante que, ao abrir o modal, a UI de adendos esteja no estado inicial.
            const formWrapper = adendosSection.querySelector('.inline-adendo-form-wrapper');
            const triggerWrapper = adendosSection.querySelector('.add-adendo-trigger-wrapper');
            const toggleBtn = adendosSection.querySelector('.toggle-adendos-view-btn');
            
            formWrapper.classList.add('hidden');        // 1. Esconde o formulário
            triggerWrapper.classList.remove('hidden'); // 2. Garante que o botão de adicionar esteja visível
            formWrapper.querySelector('textarea').value = ''; // 3. Limpa o texto
            toggleBtn.dataset.state = 'last';             // 4. Reseta o estado da setinha para "recolhido"

            const date = handover.timestamp?.toDate ? handover.timestamp.toDate() : new Date();
            const formattedDate = date.toLocaleString('pt-BR', { dateStyle: 'full', timeStyle: 'short' });

            // Preenche o nome do paciente
            if (viewHandoverMainTitle && currentPatientData) {
                viewHandoverMainTitle.innerHTML = `Detalhes do Plantão: <span class="font-bold text-gray-800">${currentPatientData.name || 'Paciente'}</span>`;
            }
            // Preenche o subtítulo com os dados do plantão
            viewHandoverSubtitle.innerHTML = `Registrado por <strong>${handover.professionalName || 'N/A'}</strong> em ${formattedDate}`;
            viewHandoverContent.innerHTML = generateDetailedHandoverHtml(handover);
            
            renderAdendosList(handover.adendos, adendosSection);
        }

        
        /**
         * Controla a visibilidade do título "Alergias Conhecidas" com base no modo de edição.
         * O rótulo e o campo de texto aparecem APENAS quando o módulo está em edição.
         */
        function updateAllergyTitleVisibility() {
            // Referência ao elemento que contém o rótulo e o input,
            // ou o contêiner principal do módulo de alergias.
            const allergyModuleBox = document.querySelector('.allergy-module-box'); 
            
            // Referência ao rótulo a ser exibido/ocultado.
            const label = document.getElementById('allergy-description-label');
            
            // Referência ao grupo de input de alergias
            const inputGroup = document.getElementById('allergy-input-group');

            if (!allergyModuleBox || !label || !inputGroup) return;

            // A nova regra: verifica se a caixa principal está no modo de edição.
            if (allergyModuleBox.classList.contains('module-editing')) {
                // Se estiver no modo de edição, remove a classe 'hidden' para mostrar.
                label.classList.remove('hidden');
                inputGroup.classList.remove('hidden');
            } else {
                // Se NÃO estiver no modo de edição, adiciona a classe 'hidden' para esconder.
                label.classList.add('hidden');
                inputGroup.classList.add('hidden');
            }
        }

        /**
         * Configura os listeners de evento para os controles de alergia.
         */
        const setupAllergyToggle = (radioYes, radioNo, inputContainer, tagsContainer) => {
            const allergyBox = inputContainer.closest('.allergy-module-box');
            const allergyInputWrapper = document.getElementById('allergy-input-wrapper');
            const allergyInputField = document.getElementById('form-allergies');
            const moduleCard = radioYes.closest('#module-diagnostico');

            const updateAllergyUI = (isYesChecked) => {
                if (isYesChecked) {
                    inputContainer.classList.remove('hidden');
                    allergyBox.classList.add('is-active');
                    updateAllergyTitleVisibility(); // Atualiza o título ao marcar "Sim"
                    updateAllergyPlaceholder();
                } else {
                    inputContainer.classList.add('hidden');
                    allergyInputWrapper.classList.add('hidden');
                    allergyBox.classList.remove('is-active');
                    exitEditMode(moduleCard);
                    updateAllergyTitleVisibility(); // Atualiza o título ao marcar "Não"
                    updateAllergyPlaceholder();
                }
            };

            radioYes.addEventListener('change', () => {
                updateAllergyUI(true); // Mantém a lógica de exibição principal

                // Pega as referências dos elementos que precisamos manipular
                const allergyInputWrapper = document.getElementById('allergy-input-wrapper');
                const allergyInputField = document.getElementById('form-allergies');
                const moduleCard = radioYes.closest('#module-diagnostico');

                // Garante que os elementos existem antes de tentar manipulá-los
                if (allergyInputWrapper && allergyInputField && moduleCard) {
                    // Entra no modo de edição do card
                    enterEditMode(moduleCard);
                    // Remove a classe 'hidden' do contêiner do input para torná-lo visível
                    allergyInputWrapper.classList.remove('hidden');
                    // Foca automaticamente no campo de texto
                    allergyInputField.focus();
                }

                setUnsavedChanges(true);
            });

            radioNo.addEventListener('change', () => {
                if (tagsContainer.children.length > 0) {
                    document.getElementById('clear-allergies-confirm-modal').classList.remove('hidden');
                } else {
                    updateAllergyUI(false);
                    setUnsavedChanges(true);
                    updateAllergyPlaceholder();
                }
            });

            if (allergyBox) {
                allergyBox.addEventListener('click', (e) => {
                    if (radioYes.checked && !e.target.closest('.remove-item-btn')) {
                        enterEditMode(moduleCard);
                        allergyInputWrapper.classList.remove('hidden');
                        allergyInputField.focus();
                        updateAllergyTitleVisibility(); // Garante que o título apareça ao entrar em edição
                        updateAllergyPlaceholder();
                    }
                });
            }
        };

        /**
         * NOVA FUNÇÃO
         * Gera um HTML condensado e limpo de um handover para impressão.
         * @param {object} handover - O objeto de handover do Firestore.
         * @returns {string} - Uma string HTML formatada para impressão.
         */
        function generateCondensedHandoverHtml(handover) {
            if (!handover) return '<p>Dados do plantão não disponíveis.</p>';

            const formatDate = (timestamp) => {
                if (!timestamp) return 'N/A';
                const date = timestamp.toDate ? timestamp.toDate() : new Date(timestamp);
                return date.toLocaleString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric', hour: '2-digit', minute: '2-digit' });
            };
            
            // Helper para criar uma seção de impressão apenas se houver conteúdo
            const renderPrintSection = (title, content) => {
                if (!content || (typeof content === 'string' && content.trim() === '')) return '';
                return `
                    <div style="margin-bottom: 12px; page-break-inside: avoid;">
                        <h4 style="font-size: 14px; font-weight: 700; border-bottom: 1px solid #ccc; padding-bottom: 2px; margin-bottom: 6px;">${title}</h4>
                        <div style="font-size: 12px; line-height: 1.4;">${content}</div>
                    </div>
                `;
            };

            let html = '';

            // Seção: Diagnóstico e Evolução
            let diagHtml = '';
            if (handover.diagnoses?.length > 0) diagHtml += `<p><strong>Diagnósticos:</strong> ${handover.diagnoses.join(', ')}</p>`;
            if (handover.comorbidities?.length > 0) diagHtml += `<p><strong>Comorbidades:</strong> ${handover.comorbidities.join(', ')}</p>`;
            if (handover.allergies?.length > 0) diagHtml += `<p><strong>Alergias:</strong> ${handover.allergies.join(', ')}</p>`;
            if (handover.evolution) diagHtml += `<div style="margin-top: 5px;"><strong>Evolução/Plano:</strong><p style="white-space: pre-wrap; margin-left: 5px;">${handover.evolution}</p></div>`;
            html += renderPrintSection('Diagnóstico e Evolução', diagHtml);

            // Seção: Segurança do Paciente
            let securityHtml = '';
            if (handover.risks && Object.values(handover.risks).some(v => v?.length > 0)) {
                const riskItems = Object.entries(handover.risks).filter(([_, v]) => v?.length > 0).map(([k, v]) => `<strong>Risco de ${k}:</strong> ${v.join(', ')}`).join('<br>');
                securityHtml += `<p><strong>Riscos:</strong><br>${riskItems}</p>`;
            }
            if (handover.precautions?.length > 0) securityHtml += `<p style="margin-top: 5px;"><strong>Precauções:</strong> ${handover.precautions.join(', ')}</p>`;
            if (handover.devices?.length > 0) securityHtml += `<p style="margin-top: 5px;"><strong>Dispositivos:</strong> ${handover.devices.join(', ')}</p>`;
            html += renderPrintSection('Segurança do Paciente', securityHtml);

            // Seção: Cuidados de Enfermagem
            if (handover.nursingCare) {
                const careItems = Object.entries(handover.nursingCare).filter(([_, v]) => v?.length > 0).map(([k, v]) => `<li><strong>${k.charAt(0).toUpperCase() + k.slice(1)}:</strong> ${v.join(', ')}</li>`).join('');
                html += renderPrintSection('Cuidados de Enfermagem', `<ul>${careItems}</ul>`);
            }

            // Seção: Monitoramento e Scores
            let monitoringHtml = '';
            if (handover.news2) monitoringHtml += `<strong>NEWS:</strong> ${handover.news2.score} (${handover.news2.level}) `;
            if (handover.fugulin) monitoringHtml += `<strong>Fugulin:</strong> ${handover.fugulin.score} (${handover.fugulin.classification})`;
            if (handover.monitoring) {
                const vitals = Object.entries(handover.monitoring).filter(([k,v]) => v && k !== 'others').map(([k,v]) => `<strong>${k.toUpperCase()}:</strong> ${v}`).join(' | ');
                if (vitals) monitoringHtml += `<p style="margin-top: 5px;"><strong>Sinais Vitais:</strong> ${vitals}</p>`;
                if (handover.monitoring.others) monitoringHtml += `<p style="margin-top: 5px;"><strong>Outros:</strong> ${handover.monitoring.others}</p>`;
            }
            html += renderPrintSection('Monitoramento e Scores', monitoringHtml);

            // Seção: Medicações
            if (handover.medications?.length > 0) {
                const medsList = handover.medications.map(m => `<li><strong>${m.name}</strong> às ${m.times.map(ts => new Date(ts).toLocaleTimeString('pt-BR', {hour:'2-digit', minute:'2-digit'})).join(', ')}</li>`).join('');
                html += renderPrintSection('Medicações do Plantão', `<ul>${medsList}</ul>`);
            }
            
            // Seção: Exames
            let examsHtml = '';
            if (handover.examsDone?.length > 0) examsHtml += `<div><strong>Resultados no Plantão:</strong><ul>${handover.examsDone.map(e => `<li><strong>${e.name}:</strong> ${e.result || 'N/A'} (em ${formatDate(e.timestamp)})</li>`).join('')}</ul></div>`;
            if (handover.scheduledExams?.length > 0) examsHtml += `<div style="margin-top:5px;"><strong>Deixou Agendado:</strong><ul>${handover.scheduledExams.map(e => `<li><strong>${e.name}</strong> (para ${formatDate(e.timestamp)})</li>`).join('')}</ul></div>`;
            if (handover.pendingExams?.length > 0) examsHtml += `<div style="margin-top:5px;"><strong>Deixou Pendente:</strong><ul>${handover.pendingExams.map(e => `<li><strong>${e.name}</strong> (realizado ${formatDate(e.timestamp)})</li>`).join('')}</ul></div>`;
            html += renderPrintSection('Exames e Procedimentos', examsHtml);

            // Seção: Observações e Adendos
            let obsHtml = '';
            if(handover.pendingObs) obsHtml += `<p><strong>Observações/Pendências:</strong> ${handover.pendingObs}</p>`;
            if (handover.adendos?.length > 0) {
                obsHtml += `<div style="margin-top:8px; border-top: 1px dashed #ddd; padding-top: 8px;"><strong>Adendos:</strong><ul>${handover.adendos.map(a => `<li>- "${a.text}" <span style="font-size:10px; color:#555;">(Por ${a.professionalName} em ${formatDate(a.timestamp)})</span></li>`).join('')}</ul></div>`;
            }
            html += renderPrintSection('Observações, Pendências e Adendos', obsHtml);
            
            return html;
        }
        
        /**
         * NOVA FUNÇÃO AUXILIAR: Centraliza a lógica de fechar o editor de alergias.
         * Esconde o campo de input e desativa o modo de edição do card principal.
         */
        function closeAllergyEditor() {
            const allergyInputWrapper = document.getElementById('allergy-input-wrapper');
            const moduleCard = document.getElementById('module-diagnostico');

            if (allergyInputWrapper) {
                allergyInputWrapper.classList.add('hidden');
            }
            if (moduleCard) {
                exitEditMode(moduleCard);
            }
            // Atualiza a visibilidade do título ao fechar, garantindo consistência
            updateAllergyTitleVisibility();
        }
        
        /**
         * Configura todas as interações e validações para o módulo de monitoramento.
        */
        function setupMonitoringModuleInteractions() {
            const module = document.getElementById('module-monitoramento');
            if (!module) return;

            // --- INÍCIO DA ALTERAÇÃO ---

            // Função auxiliar para ativar a edição de um input específico
            const activateMonitoringInput = (input) => {
                if (!input) return;
                const area = input.closest('.clickable-item-area');
                if (!area) return;

                const display = area.querySelector('.monitoring-display-area');
                
                if (display) display.classList.add('hidden');
                input.classList.remove('hidden');
                input.focus();
                input.select(); // Seleciona o texto para fácil edição
                input.dataset.originalValue = input.value;
            };

            // --- FIM DA ALTERAÇÃO ---

            // 1. LÓGICA DE CLIQUE PARA ABRIR A EDIÇÃO (permanece a mesma)
            module.addEventListener('click', (e) => {
                const targetArea = e.target.closest('.clickable-item-area[data-monitoring-item]');
                if (!targetArea) return;

                const input = targetArea.querySelector('.monitoring-input');
                
                // --- ALTERAÇÃO: Usa a função auxiliar ---
                if (input && input.classList.contains('hidden')) {
                    activateMonitoringInput(input);
                }
            });

            // 2. LÓGICA PARA FINALIZAR A EDIÇÃO (AO SAIR DO CAMPO OU TECLAR ENTER/TAB)
            const finishEdit = (input) => {
                const targetArea = input.closest('.clickable-item-area');
                const display = targetArea.querySelector('.monitoring-display-area');

                display.textContent = input.value;
                input.classList.add('hidden');
                display.classList.remove('hidden');

                if (input.value !== (input.dataset.originalValue || '')) {
                    setUnsavedChanges(true);
                    updateLiveScores();
                }
            };

            module.addEventListener('focusout', (e) => {
                if (e.target.classList.contains('monitoring-input')) {
                    finishEdit(e.target);
                }
            });

            module.addEventListener('keydown', (e) => {
                if (e.key === 'Enter' && e.target.classList.contains('monitoring-input')) {
                    e.preventDefault();
                    finishEdit(e.target);
                }

                // --- INÍCIO DA ALTERAÇÃO ---
                if (e.key === 'Tab') {
                    e.preventDefault(); // Impede o comportamento padrão de pular para o próximo elemento da página

                    const allInputs = Array.from(module.querySelectorAll('.monitoring-input'));
                    const currentIndex = allInputs.findIndex(input => input === e.target);

                    if (currentIndex > -1) {
                        // Finaliza a edição do input atual
                        finishEdit(e.target);

                        // Calcula o índice do próximo input, voltando ao início se for o último
                        const nextIndex = (currentIndex + 1) % allInputs.length;
                        const nextInput = allInputs[nextIndex];

                        // Ativa a edição no próximo input
                        activateMonitoringInput(nextInput);
                    }
                }
                // --- FIM DA ALTERAÇÃO ---
            });

            // 3. LÓGICA DE VALIDAÇÃO DE ENTRADA DE DADOS (permanece a mesma)
            const validationRules = {
                numeric: /[^0-9]/g,
                pressure: /[^0-9\/]/g,
                decimal: /[^0-9.,]/g,
                text: /a^/ 
            };

            module.addEventListener('input', (e) => {
                const input = e.target;
                const validationType = input.dataset.validate;
                if (validationType && validationRules[validationType]) {
                    input.value = input.value.replace(validationRules[validationType], '');
                }
            });
        }

        /**
        * Reseta parcialmente o formulário para um novo plantão, limpando apenas os campos
        * que são específicos de um único turno (evolução, medicações, monitoramento, etc.).
        */
        function partiallyResetFormForNewShift() {
            // 1. Limpa os campos de texto principais
            const evolutionTextarea = document.getElementById('form-evolution');
            const pendingObsTextarea = document.getElementById('form-pending-obs');
            if (evolutionTextarea) evolutionTextarea.value = '';
            if (pendingObsTextarea) pendingObsTextarea.value = '';

            // 2. Limpa o módulo de Medicações
            const medicationsListContainer = document.getElementById('medications-list-container');
            if (medicationsListContainer) medicationsListContainer.innerHTML = '';
            currentMedications = []; // Limpa o array de estado das medicações
            showMedicationEditor(false); // Garante que o editor de medicação esteja fechado

            // 3. Limpa o módulo de Monitoramento (reutilizando a função que já existe)
            resetMonitoringModule();
            
            // 4. Limpa o estado de exames finalizados no plantão
            currentShiftCompletedExams = [];
            currentShiftRescheduledExams = [];

            // Limpa o módulo de Monitoramento (reutilizando a função que já existe)
            // A função populateMonitoringModule(null) vai limpar todos os campos visuais e os inputs.
            populateMonitoringModule(null);
            
            // Zera o estado das medicações e exames do turno
            currentMedications = [];
            currentShiftCompletedExams = [];
            currentShiftRescheduledExams = [];

            // Limpa a memória de monitoramento do estado de comparação
            if (originalPatientState && originalPatientState.monitoring) {
                originalPatientState.monitoring = {};
            }
            
            // Reseta o estado de "alterações não salvas" para desabilitar o botão de salvar
            setUnsavedChanges(false);

            // Garante que o editor de exame esteja fechado
            resetAndCloseExamEditor();
        }

        /**
         * Percorre a lista estática de dispositivos e adiciona um tooltip (atributo 'title') 
         * em cada um com base no mapeamento 'deviceTooltips'.
         */
        function setupDeviceTooltips() {
            const deviceCheckboxes = document.querySelectorAll('#dispositivos-grid input[type="checkbox"]');
            
            deviceCheckboxes.forEach(checkbox => {
                const abbreviation = checkbox.value;
                const fullName = deviceTooltips[abbreviation];
                
                // O <span> com o texto é o próximo elemento irmão do checkbox
                const spanElement = checkbox.nextElementSibling;

                if (spanElement && fullName) {
                    spanElement.title = fullName;
                }
            });
        }

        // Botão "Voltar" (ou "Cancelar") dentro do modal: apenas fecha o modal.
        cancelCancelExamButton.addEventListener('click', () => {
            cancelExamConfirmModal.classList.add('hidden');
            clearHistoryState();
        });

        // Botão "Sim, Cancelar" dentro do modal: executa a lógica de cancelamento.
        confirmCancelExamButton.addEventListener('click', () => {
            const examIdToCancel = confirmCancelExamButton.dataset.examId;

            if (examIdToCancel) {
                patientExams = patientExams.filter(e => e.id !== examIdToCancel);
                renderExams();
                setUnsavedChanges(true);
                showToast('Exame cancelado.', 'success'); // Adiciona feedback para o usuário
            }

            // Esconde o modal e limpa o ID guardado
            cancelExamConfirmModal.classList.add('hidden');
            delete confirmCancelExamButton.dataset.examId;
        });

        // --- Listeners para o Novo Modal de Confirmação de Alergias ---

        // Seleciona os elementos do novo modal
        const clearAllergiesModal = document.getElementById('clear-allergies-confirm-modal');
        const confirmClearAllergiesBtn = document.getElementById('confirm-clear-allergies-button');
        const cancelClearAllergiesBtn = document.getElementById('cancel-clear-allergies-button');

        // Ação do botão "Cancelar" ou "Voltar"
        cancelClearAllergiesBtn.addEventListener('click', () => {
            // Esconde o modal
            clearAllergiesModal.classList.add('hidden');

            // Desfaz a ação: como o usuário cancelou, o radio "Não" é desmarcado e o "Sim" é remarcado.
            const radioNo = document.getElementById('allergy-radio-no');
            const radioYes = document.getElementById('allergy-radio-yes');
            if (radioNo) radioNo.checked = false;
            if (radioYes) radioYes.checked = true;
            clearHistoryState();
        });

        // Ação do botão "Sim, Remover"
        confirmClearAllergiesBtn.addEventListener('click', () => {
            const tagsContainer = document.getElementById('allergies-tags-container');
            const allergyInputContainer = document.getElementById('allergy-input-container');
            const allergyBox = allergyInputContainer.closest('.allergy-module-box');
            const moduleCard = allergyBox.closest('#module-diagnostico');

            // Limpa as tags
            if (tagsContainer) tagsContainer.innerHTML = '';

            // Esconde a área de input de alergias
            if (allergyInputContainer) allergyInputContainer.classList.add('hidden');
            if (allergyBox) allergyBox.classList.remove('is-active');
            if (moduleCard) exitEditMode(moduleCard);
            updateAllergyTitleVisibility();

            // Marca que há alterações e atualiza o resumo
            setUnsavedChanges(true);
            updateDiagnosisSummary();

            // Fecha o modal de confirmação
            clearAllergiesModal.classList.add('hidden');
        });

        // --- INICIALIZAÇÃO DA APLICAÇÃO ---
        function main() {

        // Listener para controlar a navegação entre páginas do navegador
        window.addEventListener('popstate', (event) => {
            const state = event.state;
            const hash = window.location.hash;

            // Se o usuário clicar em "voltar" e houver alterações não salvas na tela de detalhes, pergunta antes de sair.
            if (currentScreen === 'patientDetail' && hasUnsavedChanges) {
                if (!confirm('Você tem alterações não salvas. Deseja sair mesmo assim?')) {
                    // Se o usuário cancelar, empurra o estado de volta para o histórico para "cancelar" a ação de voltar.
                    history.pushState({ screen: 'patientDetail', patientId: currentPatientId }, `Paciente ${currentPatientId}`, `#paciente/${currentPatientId}`);
                    return;
                }
                hasUnsavedChanges = false;
            }

            // Fecha TODOS os modais abertos, garantindo uma navegação limpa.
            document.querySelectorAll('.fixed.inset-0.z-50:not(.hidden)').forEach(modal => {
                modal.classList.add('hidden');
            });

            // Lógica de navegação baseada no estado ou na URL
            if (state?.screen === 'patientDetail' && state.patientId) {
                renderPatientDetail(state.patientId); // Renderiza a tela do paciente
            } else if (hash.startsWith('#paciente/')) {
                const patientId = hash.substring('#paciente/'.length);
                if (patientId) {
                    renderPatientDetail(patientId);
                } else {
                    showScreen('main');
                }
            } else {
                // Se não for uma tela de paciente, volta para o painel principal.
                showScreen('main');
            }
        });

        const mainContentArea = document.querySelector('#main-content > main');
            if (mainContentArea) {
                mainContentArea.addEventListener('scroll', () => {
                    // Se uma lista de autocomplete estiver ativa
                    if (activeAutocomplete && activeAutocomplete.listElement) {
                        // Reposiciona a lista para acompanhar a rolagem
                        positionFloatingList(activeAutocomplete.inputElement, activeAutocomplete.listElement);
                    }
                });
            }

        // Listener para abrir/fechar o painel de notificações
        notificationBellBtn.addEventListener('click', (e) => {
            e.stopPropagation();
            notificationPanel.classList.toggle('hidden');
        });

        // Listener para cliques nos itens da lista de notificação
        notificationList.addEventListener('click', (e) => {
            const item = e.target.closest('.notification-item');
            if (item) {
                const { notifId, patientId, handoverId } = item.dataset;
                handleNotificationClick(notifId, patientId, handoverId);
            }
        });

        // Fecha o painel se clicar em qualquer outro lugar
        window.addEventListener('click', (e) => {
            if (!notificationPanel.classList.contains('hidden') && !e.target.closest('#notification-panel') && !e.target.closest('#notification-bell-btn')) {
                notificationPanel.classList.add('hidden');
            }
        });

        setupDeviceTooltips();

        // Elementos dos modais
        const lastHandoverModal = document.getElementById('last-handover-modal');
        const fullHistoryModal = document.getElementById('full-history-modal');
        const openHistoryFromLastBtn = document.getElementById('open-full-history-from-last-handover-btn');

        // Listener para o novo botão "Visualizar Histórico"
        if(openHistoryFromLastBtn) {
            openHistoryFromLastBtn.addEventListener('click', () => {
                // Esconde o modal atual
                lastHandoverModal.classList.add('hidden');
                // Mostra o modal de histórico completo
                fullHistoryModal.classList.remove('hidden');
            });
        }

        // Botão Voltar: Do modal de DETALHES (3) para a LISTA DE HISTÓRICO (2)
        if (backToHistoryListBtn) {
            backToHistoryListBtn.addEventListener('click', () => {
                viewHandoverModal.classList.add('hidden');
                fullHistoryModal.classList.remove('hidden');
                pushHistoryState('full-history-modal');
            });
        }

        // Botão Voltar: Da LISTA DE HISTÓRICO (2) para o modal de ÚLTIMA PASSAGEM (1)
        if (backToLastHandoverBtn) {
            backToLastHandoverBtn.addEventListener('click', () => {
                // Esconde o modal de histórico completo
                fullHistoryModal.classList.add('hidden');

                // Em vez de apenas mostrar o modal antigo, chamamos a função
                // que o popula novamente. Isso reseta o estado e recarrega os dados corretos.
                populateLastHandoverModal();
                
                // A função populateLastHandoverModal já cuida de tornar o modal visível.
            });
        }
        // --- ADIÇÃO: FECHA O MODO DE EDIÇÃO DE ALERGIA AO CLICAR FORA DO INPUT ---
        const allergyInputField = document.getElementById('form-allergies');
        if (allergyInputField) {
            allergyInputField.addEventListener('focusout', (e) => {
                // Pequeno delay para permitir cliques em outros botões (como o de salvar)
                setTimeout(() => {
                    // Se o foco não foi para um elemento dentro do próprio módulo, fecha a edição
                    const moduleCard = e.target.closest('.module-editing');
                    if (moduleCard && !moduleCard.contains(document.activeElement)) {
                        e.target.parentElement.classList.add('hidden'); // Esconde o input
                        exitEditMode(moduleCard);
                    }
                }, 100);
            });
        }

            setupMonitoringModuleInteractions();

            // Garante que o botão 'X' do modal de histórico sempre funcione
            const closeModuleHistoryModalBtn = document.getElementById('close-module-history-modal');
            const moduleHistoryModal = document.getElementById('module-history-modal');

            if (closeModuleHistoryModalBtn && moduleHistoryModal) {
                closeModuleHistoryModalBtn.addEventListener('click', () => {
                    moduleHistoryModal.classList.add('hidden');
                });
            }

            // Ativa modo de edição por foco em módulos específicos
            const focusToEditModules = [
                { selector: '#module-dispositivos' },
                { selector: '#module-cuidados-enfermagem' },
                { selector: '#module-monitoramento' }, // Já que ele tem inputs
                { selector: '#module-observacoes' }   // Já que ele tem um textarea
            ];

            focusToEditModules.forEach(config => {
                const moduleElement = document.querySelector(config.selector);
                if (moduleElement) {
                    moduleElement.addEventListener('focusin', (e) => {
                        // VERIFICAÇÃO: Se o elemento que recebeu o foco (e.target)
                        // for o botão de histórico, ele não faz nada e interrompe a função.
                        if (e.target.closest('.module-history-btn')) {
                            return;
                        }

                        // Se o foco não veio do botão de histórico, entra no modo de edição normalmente.
                        enterEditMode(moduleElement);
                    });
                }
            });

            const evolutionTextarea = document.getElementById('form-evolution');
            if (evolutionTextarea) {
                evolutionTextarea.addEventListener('focus', () => {
                    const moduleCard = evolutionTextarea.closest('#module-diagnostico');
                    if (moduleCard) {
                        enterEditMode(moduleCard);
                    }
                });
            }

            const savedViewMode = localStorage.getItem('patientViewMode');
            if (savedViewMode === 'list' || savedViewMode === 'grid') {
                currentViewMode = savedViewMode;
            }
            // Aplica o modo de visualização correto ao contêiner ANTES de carregar os dados.
            if (currentViewMode === 'list') {
                // Altera o ícone do botão
                viewToggleIconGrid.classList.add('hidden');
                viewToggleIconList.classList.remove('hidden');
                // Altera as classes do contêiner da lista para o layout de lista
                patientList.className = 'mt-6 px-4 sm:px-0 flex flex-col gap-2';
            } else {
                // Garante que o layout de grade seja o padrão se não for lista
                viewToggleIconGrid.classList.remove('hidden');
                viewToggleIconList.classList.add('hidden');
                patientList.className = 'mt-6 px-4 sm:px-0 grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3';
            }
            setupFormAccordion(); // Configura o acordeão do formulário

            document.getElementById('form-evolution').addEventListener('input', updateDiagnosisSummary);
    

            // Monitora o estado da autenticação do usuário
            onAuthStateChanged(auth, (user) => {
                if (user) {
                    currentUser = user;
                    userInfo.textContent = `Olá, ${user.displayName || user.email}`;

                    loadInitialPatients();

                    const hash = window.location.hash;
                    if (hash.startsWith('#paciente/')) {
                        const patientId = hash.substring('#paciente/'.length);
                        if (patientId) {
                            // CORREÇÃO: Em vez de renderizar diretamente, chama a função principal de navegação.
                            // Ela cuidará de definir o estado do histórico e renderizar a tela.
                            showPatientDetail(patientId, null, true); // O 'true' evita criar uma nova entrada no histórico
                        } else {
                            history.replaceState({ screen: 'main' }, 'Painel de Pacientes', '#painel');
                            showScreen('main');
                        }
                    } else {
                        history.replaceState({ screen: 'main' }, 'Painel de Pacientes', '#painel');
                        showScreen('main');
                    }

                    setupNotificationListener(user);
                } else {
                    currentUser = null;
                    if (unsubscribePatients) unsubscribePatients();
                    if (unsubscribeHandovers) unsubscribeHandovers();
                    if (unsubscribeNotifications) unsubscribeNotifications();
                    history.replaceState({ screen: 'login' }, 'Login', ' ');
                    showScreen('login');
                }
                screens.loading.classList.add('hidden');
            });

            const addNewMedicationBtn = document.getElementById('add-new-medication-btn');
            const medicationSearchWrapper = document.getElementById('medication-search-wrapper');
            const medicationInput = document.getElementById('form-medications');
            const moduleMedicacoes = document.getElementById('module-medicacoes');

            if (addNewMedicationBtn) {
                addNewMedicationBtn.addEventListener('click', () => {
                    showMedicationEditor('search'); // Mostra o editor de BUSCA
                    enterEditMode(moduleMedicacoes);
                });
            }

            if (cancelEditorBtn) {
                cancelEditorBtn.addEventListener('click', () => {
                    // 1. Pega o nome da medicação que está sendo editada a partir do título do editor
                    const titleLabel = document.getElementById('medication-time-editor-label');
                    const medNameToCancel = titleLabel.textContent.replace('Selecione o horário para ', '');

                    // 2. Encontra o objeto da medicação no array de estado
                    const medObject = currentMedications.find(m => m.name === medNameToCancel);

                    // 3. Se o objeto existir E não tiver nenhum horário salvo, significa que é uma medicação nova
                    if (medObject && medObject.times.length === 0) {
                        // 3a. Remove a medicação do array de estado
                        currentMedications = currentMedications.filter(m => m.name !== medNameToCancel);
                        
                        // 3b. Remove o card da medicação da tela
                        const medBlockToRemove = document.querySelector(`.medication-block[data-med-name="${medNameToCancel}"]`);
                        if (medBlockToRemove) {
                            medBlockToRemove.remove();
                        }
                        // (Não precisa marcar `setUnsavedChanges`, pois estamos revertendo uma adição)
                    }

                    // 4. Executa a lógica original de fechar o editor
                    showMedicationEditor(false);
                    exitEditMode(moduleMedicacoes);
                });
            }

        // Abre/Fecha o modal de resumo da unidade
        if (showUnitSummaryButton) {
            showUnitSummaryButton.addEventListener('click', showUnitSummary);
        }

        if (closeUnitSummaryModalButton && unitSummaryModal) {
            closeUnitSummaryModalButton.addEventListener('click', () => {
                unitSummaryModal.classList.add('hidden');
                clearHistoryState();
            });
        }

         if (printUnitSummaryButton) {
            printUnitSummaryButton.addEventListener('click', handlePrintUnitSummary);
        }

        // Listener unificado para a tecla "Enter" no body
        document.body.addEventListener('keydown', (e) => {
            // Verifica se a tecla é "Enter" e se a tecla Shift NÃO está pressionada
            if (e.key === 'Enter' && !e.shiftKey) {
                
                // --- INÍCIO DA CORREÇÃO: Lógica de proteção contra duplo 'Enter' ---
                if (e.target.id === 'form-medications') {
                    // Se o 'Enter' foi pressionado na caixa de busca de medicação,
                    // este listener do 'body' deve ignorá-lo completamente.
                    // O listener específico do campo de texto cuidará da ação.
                    return; 
                }

                // Lógica para salvar Adendo (permanece a mesma)
                if (e.target.matches('textarea[id^="adendo-text-"]')) {
                    e.preventDefault();
                    const adendoSection = e.target.closest('.adendos-container');
                    if (adendoSection) {
                        const saveBtn = adendoSection.querySelector('.save-adendo-btn');
                        saveBtn.click();
                    }
                    return;
                }

                // Lógica para salvar Horário de Medicação (permanece a mesma)
                const timeEditor = document.getElementById('medication-time-editor');
                if (timeEditor && !timeEditor.classList.contains('hidden')) {
                    e.preventDefault();
                    
                    const correctBtn = document.getElementById('correct-med-time-btn');
                    const addBtn = document.getElementById('add-med-time-btn');

                    if (correctBtn && !correctBtn.classList.contains('hidden')) {
                        correctBtn.click();
                    } else if (addBtn) {
                        addBtn.click();
                    }
                }
            }
        });

        // Listener para os botões "Adicionar Adendo" e os formulários inline
        document.body.addEventListener('click', (e) => {
            const triggerBtn = e.target.closest('.add-adendo-trigger-btn');
            const cancelBtn = e.target.closest('.cancel-adendo-btn');
            const saveBtn = e.target.closest('.save-adendo-btn');
            const toggleViewBtn = e.target.closest('.toggle-adendos-view-btn');

            if (triggerBtn) {
                const adendoSection = triggerBtn.closest('.adendos-container');
                if (adendoSection) {
                    const formWrapper = adendoSection.querySelector('.inline-adendo-form-wrapper');
                    const triggerWrapper = adendoSection.querySelector('.add-adendo-trigger-wrapper');
                    if (formWrapper && triggerWrapper) {
                        triggerWrapper.classList.add('hidden'); // Esconde o botão de adicionar
                        formWrapper.classList.remove('hidden');
                        formWrapper.querySelector('textarea').focus();
                    }
                }
            }

            if (cancelBtn) {
                const adendoSection = cancelBtn.closest('.adendos-container');
                if (adendoSection) {
                    const formWrapper = adendoSection.querySelector('.inline-adendo-form-wrapper');
                    const triggerWrapper = adendoSection.querySelector('.add-adendo-trigger-wrapper');
                    
                    if(formWrapper && triggerWrapper) {
                        formWrapper.querySelector('textarea').value = '';
                        formWrapper.classList.add('hidden');
                        // CORREÇÃO: garante que o wrapper do botão seja exibido.
                        triggerWrapper.classList.remove('hidden');
                    }
                }
            }

            if (saveBtn) {
                const adendoSection = saveBtn.closest('.adendos-container');
                const textarea = adendoSection.querySelector('textarea');
                const handoverId = currentlyViewedHandover?.id;

                if (handoverId && textarea) {
                    saveAdendo(handoverId, textarea.value, saveBtn);
                }
            }
            
            if (toggleViewBtn) {
                const adendosSection = toggleViewBtn.closest('.adendos-container');
                const currentState = toggleViewBtn.dataset.state;
                
                // Inverte o estado
                toggleViewBtn.dataset.state = (currentState === 'last') ? 'all' : 'last';
                
                // Re-renderiza a lista com o novo estado
                renderAdendosList(currentlyViewedHandover.adendos, adendosSection);
            }
        });

        // --- Listeners de Conexão do Navegador ---
        window.addEventListener('offline', () => {
            console.log("Navegador detectou offline.");
            updateConnectionStatus('offline');
        });

        window.addEventListener('online', () => {
            console.log("Navegador detectou online. Tentando sincronizar...");
            updateConnectionStatus('connecting'); 
            // O status mudará para 'online' de fato quando o onSnapshot confirmar a conexão.
        });

        }

        const monitoringModule = document.getElementById('module-monitoramento');
        if (monitoringModule) {
            const monitoringInputs = monitoringModule.querySelectorAll('.monitoring-input');
            const monitoringConfirmBtn = monitoringModule.querySelector('.confirm-edit-btn');

            // Entra no modo de edição quando qualquer input recebe foco
            monitoringInputs.forEach(input => {
                input.addEventListener('focus', () => {
                    enterEditMode(monitoringModule);
                });
            });

            // Sai do modo de edição ao clicar em 'Confirmar'
            if (monitoringConfirmBtn) {
                monitoringConfirmBtn.addEventListener('click', () => {
                    exitEditMode(monitoringModule);
                });
            }
        }

        // Listener para a tecla "Enter" no campo de medicação
        medicationInput.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') {
                e.preventDefault(); // Previne o comportamento padrão do Enter

                const inputValue = medicationInput.value.trim();

                // Verifica se há algo escrito no campo
                if (inputValue) {
                    // 1. Chama a função principal que cria o card e abre o seletor de tempo.
                    //    (Exatamente a mesma ação do botão "Confirmar")
                    handleMedicationSelection(inputValue);
                    
                    // 2. Reseta a UI da busca para o estado inicial.
                    document.getElementById('confirm-med-name-btn').classList.add('hidden');
                    medicationInput.value = ''; // Limpa o campo de texto
                    hideActiveAutocomplete();   // Garante que a lista de sugestões feche
                }
            }
        });

        // Listener para a tecla "Enter" no campo de diagnóstico
        diagnosisInput.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') {
                e.preventDefault(); // Previne o comportamento padrão (submeter formulário)
                const inputValue = diagnosisInput.value.trim();

                // Se houver texto no campo, usa esse texto para criar a tag
                if (inputValue) {
                    const container = document.getElementById('diagnoses-tags-container');
                    container.appendChild(createListItem(inputValue));
                    diagnosisInput.value = ''; // Limpa o campo
                    updateDiagnosisSummary();
                    setUnsavedChanges(true);
                    hideActiveAutocomplete(); // Esconde a lista de sugestões
                }
            }
        });

        // Fecha menus de opções se clicar fora
        window.addEventListener('click', () => {
            document.querySelectorAll('.dropdown-menu').forEach(menu => menu.classList.add('hidden'));
        });

        // Seleciona os elementos necessários: o botão, a área de busca e o card do módulo.
        const addNewMedicationBtn = document.getElementById('add-new-medication-btn');
        const medicationSearchWrapper = document.getElementById('medication-search-wrapper');
        // A variável 'medicationInput' já foi declarada no topo do script, então apenas a usamos aqui.
        const moduleMedicacoes = document.getElementById('module-medicacoes');

        // Garante que todos os elementos existem antes de adicionar o listener para evitar erros
        if (addNewMedicationBtn && medicationSearchWrapper && medicationInput && moduleMedicacoes) {
            
            addNewMedicationBtn.addEventListener('click', () => {
                // 1. Mostra a caixa de busca de medicação
                medicationSearchWrapper.classList.remove('hidden');
                
                // 2. Foca automaticamente no campo de input para o usuário já poder digitar
                medicationInput.focus();

                // 3. Ativa o modo de edição para o módulo de medicações, mantendo a consistência da UI
                enterEditMode(moduleMedicacoes);
            });
        }

        // Inicializa os calendários de data de nascimento nos modais
        flatpickr("#new-patient-dob", configDatePickerNascimento);
        flatpickr("#edit-patient-dob", configDatePickerNascimento);

        if (printLastHandoverButton) {
            printLastHandoverButton.addEventListener('click', () => {
                if (!currentPatientData || !currentHandovers || currentHandovers.length === 0) {
                    showToast("Não há dados para imprimir.", "error");
                    return;
                }

                const latestHandover = currentHandovers[0];
                const printView = document.getElementById('print-view');
                const handoverHtml = generateCondensedHandoverHtml(latestHandover);
                const date = latestHandover.timestamp?.toDate ? latestHandover.timestamp.toDate() : new Date();
                
                // Coleta dos dados do paciente
                const patientName = currentPatientData.name || 'N/A';
                const patientNumber = currentPatientData.patientNumber || 'N/A';
                const roomNumber = currentPatientData.roomNumber || 'N/A';
                const age = calculateAge(currentPatientData.dob);
                const admissionDate = currentPatientData.createdAt?.toDate().toLocaleDateString('pt-BR') || 'N/A';

                printView.innerHTML = `
                    <div style="padding: 1rem; font-family: 'Inter', sans-serif; color: #333;">
                        <h2 style="font-size: 1.2rem; font-weight: 700;">Última Passagem de Plantão - ${patientName}</h2>

                        <div style="font-size: 0.9rem; color: #555; margin-top: 0.25rem;">
                            <span style="margin-right: 0.75rem;"><strong>Idade:</strong> ${age} anos</span>
                            <span style="margin-right: 0.75rem;"><strong>Leito:</strong> ${roomNumber}</span>
                            <span style="margin-right: 0.75rem;"><strong>Pront.:</strong> ${patientNumber}</span>
                            <span><strong>Internação:</strong> ${admissionDate}</span>
                        </div>
                        
                        <p style="font-size: 0.8rem; color: #555; margin-top: 0.75rem; padding-bottom: 0.75rem; border-bottom: 2px solid #ccc; margin-bottom: 1rem;">
                            <strong>Profissional:</strong> ${latestHandover.professionalName || 'N/A'} | 
                            <strong>Data da Passagem:</strong> ${date.toLocaleString('pt-BR', {dateStyle: 'long', timeStyle: 'short'})}
                        </p>
                        ${handoverHtml}
                    </div>
                `;
                
                setTimeout(() => window.print(), 200);
            });
        }

        if (printHandoverDetailButton) {
            printHandoverDetailButton.addEventListener('click', () => {
                if (!currentPatientData || !currentlyViewedHandover) {
                    showToast("Não há dados de plantão para imprimir.", "error");
                    return;
                }

                const printView = document.getElementById('print-view');
                const handoverHtml = generateCondensedHandoverHtml(currentlyViewedHandover);
                const date = currentlyViewedHandover.timestamp?.toDate ? currentlyViewedHandover.timestamp.toDate() : new Date();

                // Coleta dos dados do paciente
                const patientName = currentPatientData.name || 'N/A';
                const patientNumber = currentPatientData.patientNumber || 'N/A';
                const roomNumber = currentPatientData.roomNumber || 'N/A';
                const age = calculateAge(currentPatientData.dob);
                const admissionDate = currentPatientData.createdAt?.toDate().toLocaleDateString('pt-BR') || 'N/A';

                printView.innerHTML = `
                    <div style="padding: 1rem; font-family: 'Inter', sans-serif; color: #333;">
                        <h2 style="font-size: 1.2rem; font-weight: 700;">Detalhes do Plantão - ${patientName}</h2>

                        <div style="font-size: 0.9rem; color: #555; margin-top: 0.25rem;">
                            <span style="margin-right: 0.75rem;"><strong>Idade:</strong> ${age} anos</span>
                            <span style="margin-right: 0.75rem;"><strong>Leito:</strong> ${roomNumber}</span>
                            <span style="margin-right: 0.75rem;"><strong>Pront.:</strong> ${patientNumber}</span>
                            <span><strong>Internação:</strong> ${admissionDate}</span>
                        </div>

                        <p style="font-size: 0.8rem; color: #555; margin-top: 0.75rem; padding-bottom: 0.75rem; border-bottom: 2px solid #ccc; margin-bottom: 1rem;">
                            <strong>Profissional:</strong> ${currentlyViewedHandover.professionalName || 'N/A'} | 
                            <strong>Data da Passagem:</strong> ${date.toLocaleString('pt-BR', {dateStyle: 'long', timeStyle: 'short'})}
                        </p>
                        ${handoverHtml}
                    </div>
                `;

                setTimeout(() => window.print(), 200);
            });
        }

        // Inicia a aplicação depois que todos os recursos da página foram carregados
        window.addEventListener('load', main);

    </script>
    <script>
      // Registra o Service Worker para funcionamento offline
      if ('serviceWorker' in navigator) {
        window.addEventListener('load', () => {
          navigator.serviceWorker.register('/service-worker.js')
            .then(registration => {
              console.log('Service Worker registrado com sucesso:', registration);
            })
            .catch(error => {
              console.error('Falha ao registrar o Service Worker:', error);
            });
        });
      }
    </script>
    <div id="action-loading-overlay" class="hidden fixed inset-0 bg-gray-900 bg-opacity-60 flex items-center justify-center z-[9999]">
        <div class="text-center">
            <svg class="animate-spin h-10 w-10 text-white mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <p class="mt-4 text-lg font-medium text-white">Carregando...</p>
        </div>
    </div>
</body>
</html>
