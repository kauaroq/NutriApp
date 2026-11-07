# NutriApp

## Arquitetura do Projeto
O projeto foi consolidado em um **único arquivo HTML** (`index.html`), buscando simplicidade e execução direta no navegador sem necessidade de ferramentas de build (como Node.js, Webpack ou Vite).

### Tecnologias Utilizadas
1. **HTML5**: Estrutura base da aplicação.
2. **React 18 & ReactDOM**: Bibliotecas para criação da interface de usuário, carregadas via CDN (`esm.sh`).
3. **Babel Standalone**: Compilador utilizado em tempo de execução no navegador para traduzir o código JSX e ESModules.
4. **Tailwind CSS**: Framework de CSS utilitário, inserido via CDN oficial (`cdn.tailwindcss.com`) para estilização.
5. **Lucide React**: Biblioteca de ícones carregada via módulos ES.
6. **Firebase v10**: Serviços de backend como Autenticação, Realtime Database e Storage, importados via CDN oficial do Google (`gstatic.com`).
7. **SheetJS (xlsx)**: Biblioteca carregada via CDN para geração e formatação de planilhas Excel (`.xlsx`) no cliente.
8. **Google Gemini API**: IA generativa para criação de planos alimentares e execução de dupla auditoria clínica/nutricional.

### Estrutura e Funcionalidades
- **Autenticação**: Sistema de Login e Cadastro separados para Paciente e Nutricionista conectados ao Firebase. Nutricionistas exigem um código de validação para registro. O controle de acesso e visualização é gerenciado via atributo `role` armazenado no Firestore, eliminando verificações hardcoded.
- **Visão do Nutricionista (Admin)**: Acesso a uma lista de todos os pacientes, gerenciamento e aprovação de requisições de consultas, acesso às fichas completas dos pacientes, visualização e acompanhamento de rotinas, chat com paciente, e exclusão completa de usuários/pacientes com limpeza em cascata de dados (anamnese, planilhas, rotinas, consultas, chats e notificações). Pode apagar mensagens de pacientes no chat.
- **Visão do Paciente**: Visualização isolada (não vê outros usuários). Acesso ao plano alimentar diário, preenchimento da ficha completa (anamnese), chat geral com a nutricionista, agendamento de consultas (aguardando aprovação) e chat específico por consulta aprovada.
- **Chat em Tempo Real**: Sistema de mensagens bidirecional usando Firestore. Existem dois tipos de chat: Chat geral entre paciente e nutricionista, e Chat Específico vinculado a uma consulta aprovada (com data, horário e observações). A nutricionista tem privilégios de exclusão de mensagens.
- **Ficha Completa do Paciente (Anamnese Aprimorada com Informações Opcionais)**: Interface moderna e completa dividida em seções temáticas (Dados Corporais & Antropometria com cálculo em tempo real de IMC, Rotina de Refeições & Hábitos com frequência alimentar diária e consumo de água, Preferências & Aversões Alimentares, Estilo de Vida, Sono & Saúde Intestinal, e Suplementação/Observações). Permite que o paciente preencha uma série de detalhes opcionais como quantas vezes come por dia, horários habituais, alimentos favoritos, aversões e suplementação, mantendo total flexibilidade. A nutricionista visualiza todos os dados de forma estruturada e o pipeline de IA consome esses dados para planos alimentares altamente personalizados.
- **Plano Alimentar (Multi-Planilhas & Geração com IA)**: Permite que a nutricionista anexe múltiplas planilhas (arquivos Excel, PDF, CSV) para um mesmo paciente (selecionando um ou múltiplos arquivos simultaneamente), além de gerar novas planilhas com IA baseadas na Anamnese sem sobrescrever as anteriores. Cada planilha possui gerenciamento individual no Firebase Realtime Database (`planos/{pacienteId}/{planilhaId}`), permitindo que a nutricionista visualize, baixe ou exclua planilhas existentes individualmente. O paciente tem acesso a todo o seu acervo de planilhas para download e visualização interativa do cardápio das refeições no app.
- **Visualização e Edição de Planilhas Integrada (Spreadsheet Viewer & Editor com Chatbot IA Opcional)**: Sistema completo para abrir, inspecionar e editar planilhas (`.xlsx`, `.xls`, `.csv` e geradas por IA) diretamente dentro da aplicação. Para nutricionistas, inclui edição de células com barra de fórmulas/coordenadas, adição e exclusão de linhas e colunas, **chatbot opcional com IA (Google Gemini)** para solicitar modificações em linguagem natural diretamente na planilha já pronta (ex: substituição de alimentos, ajustes calóricos/macros e inclusão de alternativas), histórico de desfeitas (Undo), navegação entre abas/worksheets do arquivo, busca rápida em tempo real, exportação/download e salvamento direto no Firebase Realtime Database (`planos/{pacienteId}/{planilhaId}`). Para pacientes, disponibiliza modo de visualização seguro, com alternância de abas, busca e download. Inclui também visualizador embutido para documentos em formato PDF.
- **Dashboard Principal**: Agora integrado ao banco de dados, buscando e exibindo as próximas consultas confirmadas, tanto para a visão da Nutricionista (todas as consultas) quanto do Paciente (suas próprias consultas), além da contagem em tempo real de planos alimentares ativos.
- **Aba de Rotina e Checklist com Avisos**: Aba dedicada para o paciente estruturar sua rotina diária através de checklist interativo (água, alimentação, suplementos, exercícios e hábitos). Permite configurar horários específicos para avisos/notificações de cada tarefa, traz um gerador rápido de metas e avisos de água ao longo do dia, controle de progresso diário e persistência em tempo real no Firebase Realtime Database (`rotinas/{pacienteId}`).
- **Central de Notificações Dinâmicas**: Remoção completa de notificações artificiais/estáticas. O dropdown do sino agora reflete unicamente dados reais: alertas de mensagens não lidas e lembretes da rotina do usuário com horários e status em tempo real, além de suporte a notificações web no navegador.
- **Sistema de Confirmações e Notificações na Interface (Toasts & Modais UI)**: Eliminação de todas as caixas de diálogo nativas do navegador (`window.confirm`, `confirm`, `window.alert`, `alert`). Implementação de modais reativos com confirmação de ações destrutivas (exclusão de planilhas individuais/coletivas, mensagens do chat, tarefas de rotina e carregamento de modelos) e sistema flutuante de Toasts animados com suporte a múltiplos tipos (sucesso, erro, alerta e informativo) garantindo uma experiência premium e 100% integrada à interface.

### Regras do Projeto (Conforme usuário)
- É proibido remover ou alterar funcionalidades existentes não solicitadas.
- Atualizar sempre este `PROJECT.md` antes de qualquer alteração que possa comprometer o projeto.
- Realizar backup do `.html` antes de modificá-lo no futuro.
- Elaborar um planejamento prévio para qualquer mudança.

### Changelog / Correções
- **Aprimoramento da Ficha do Paciente com Campos Opcionais e Modo de Leitura Limpo para a Nutricionista**:
  - Implementada nova arquitetura para a Ficha do Paciente (`PatientFichaView`), estruturada em seções temáticas e cards com design premium:
    - **Dados Corporais & Antropometria**: Idade, peso, altura, objetivo principal e cálculo em tempo real do Índice de Massa Corporal (IMC) com diagnóstico visual colorido.
    - **Rotina Alimentar & Refeições (Opcional)**: Frequência de refeições diárias ("Quantas vezes come por dia") com botões rápidos (3 refeições, 4 refeições, 5 refeições, 6+ refeições ou digitação personalizada), consumo habitual de água e horários habituais do dia (acordar, almoçar, jantar, dormir).
    - **Preferências e Aversões Alimentares (Opcional)**: Alimentos favoritos do paciente, alimentos que não gosta ou prefere evitar (aversões gastronômicas) e campo de restrições/alergias médicas.
    - **Saúde, Estilo de Vida & Hábitos**: Nível de atividade física com seletor (Sedentário, Leve, Moderado, Intenso) e descrição do exercício, qualidade e horas de sono, saúde digestiva/intestinal, histórico médico e medicamentos.
    - **Suplementação & Observações Adicionais (Opcional)**: Uso de suplementos/vitaminas, relação com a comida/fome emocional e observações livres para a nutricionista.
  - **Visualização Otimizada para a Nutricionista (`readOnly`)**: Todas as opções de acessibilidade e botões de clique rápido voltados para o preenchimento pelo paciente foram ocultados na visão da nutricionista. A tela agora exibe apenas os dados preenchidos de forma limpa, direta e sem poluição visual, indicando com elegância campos não preenchidos (`"Não informado pelo paciente"`).
  - Adicionado indicador visual de preenchimento ("Progresso da Ficha") na visão do paciente para incentivar o preenchimento sem torná-lo obrigatório.
  - Integração do novo ecossistema de dados da ficha com a geração de plano alimentar com IA (`generateAIMealPlanPipeline`) e com o chatbot assistente de planilhas (`SpreadsheetModal`).
- **Chatbot Opcional com IA para Modificação de Planilhas Prontas**:
  - Implementado painel lateral de chatbot com IA integrado ao `SpreadsheetModal`, acionado opcionalmente pelo botão "Assistente IA" na barra de ferramentas do editor.
  - Permite que a nutricionista envie instruções em linguagem natural (ex: "Troque o leite integral por bebida vegetal", "Aumente as proteínas do almoço", "Crie uma ceia leve sem glúten").
  - O pipeline de IA analisa a planilha atual e as necessidades do paciente, aplica as alterações diretamente na matriz da planilha (`sheetsData`) e detalha as alterações feitas no chat.
  - Preserva 100% o histórico de edições (Undo): qualquer modificação da IA pode ser revertida com um clique no botão "Desfazer".
  - Inclui chips de comandos rápidos, indicador de processamento, tratamento de erros e integração direta com o salvamento no Firebase Realtime Database.
- **Exclusão Completa de Usuários / Pacientes pela Nutricionista**:
  - Implementada a possibilidade da nutricionista excluir pacientes do sistema, acessível tanto pela lista da aba "Pacientes" quanto pelo cabeçalho da visualização de ficha completa do paciente.
  - Confirmação de segurança via modal reativo (`showConfirm`) com variante de perigo (danger), esclarecendo a irreversibilidade e a lista de dados apagados.
  - Limpeza completa em cascata no Firebase Realtime Database: exclusão do cadastro (`users/{id}`), ficha de anamnese (`fichas/{id}`), todas as planilhas do histórico (`planos/{id}`), tarefas e conclusões da rotina (`rotinas/{id}`), notificações não lidas (`unreadMessages`), histórico do chat direto (`chats/{chatId}`) e consultas agendadas (`consultas`) com seus respectivos chats vinculados.
  - Indicador visual de processamento (`Loader2`) durante a exclusão para evitar múltiplos cliques, seguido de feedback por Toast (`notify.success` / `notify.error`).
- **Visualização e Edição de Planilhas Integrada no App**:
  - Implementado o componente `SpreadsheetModal` com integração completa com a biblioteca SheetJS (`xlsx`) para visualização e edição matricial de arquivos `.xlsx`, `.xls`, `.csv` e planos gerados por IA.
  - Suporte a múltiplas abas da planilha (worksheets) com troca de abas instantânea.
  - Barra de fórmulas/edição com coordenadas de células (ex: A1, B4) e inputs responsivos.
  - Ferramentas de edição para a nutricionista: edição in-loco (duplo clique ou barra superior), adição/exclusão de linhas e colunas, desfazer alterações (Undo), tela cheia e botão de salvar diretamente no Firebase Realtime Database com feedback via Toasts.
  - Modo de visualização para o paciente: consulta confortável e detalhada das refeições e auditorias na planilha, com busca por alimentos/nutrientes e alternância de abas.
  - Visualizador integrado para arquivos PDF anexados.
- **Notificações Visuais e Confirmações de Exclusão na Interface**:
  - Removidos todos os popups nativos do navegador (`window.confirm` e `window.alert`).
  - Adicionado componente de modal customizado de confirmação (`ConfirmModal`) com backdrop escuro desfocado, ícones contextuais (exclusão/alerta), botões de Cancelar e Confirmar com estilos semânticos e suporte a atalhos.
  - Adicionado sistema de notificações flutuantes em tempo real (`ToastContainer` / `notify`) para feedback de ações (sucesso, erros de validação, avisos e status de operações).
  - Aplicado a todas as rotinas de exclusão (planilhas do paciente, mensagens de chat, itens da rotina) e de mensagens operacionais da aplicação.
- **Remoção de Notificações Artificiais e Implementação da Rotina Interativa**: Removidas todas as notificações estáticas e mockadas ("Hora da Água", "Lanche da Manhã", "Consulta Confirmada"). Adicionada aba "Rotina" com checklist diário, gerador inteligente de lembretes de hidratação, seleção de horários de aviso, sincronização com o sino de notificações e persistência no Firebase Realtime Database.
- **Correção da Conexão e Modelos da API Google Gemini**: Atualizado o pipeline de geração com IA e o validador de chave para utilizar os modelos oficiais e ativos do Gemini (`gemini-3.7-flash`, `gemini-3.5-flash`, `gemini-flash-latest`), com sistema de failover automático caso um modelo esteja em alta demanda (429/503), além de sincronização em tempo real da chave via Firebase Realtime Database (`config/geminiApiKey`) e exibição detalhada de erros retornados pela API.
- **Suporte a Múltiplas Planilhas e Exclusão Individual/Coletiva**: Implementado upload múltiplo (seleção de vários arquivos ao mesmo tempo ou adições sucessivas) e histórico completo de planilhas por paciente. A nutricionista pode excluir qualquer planilha existente individualmente. O status `planoAtivo` do paciente é atualizado dinamicamente (ativo se houver ao menos 1 planilha, inativo se todas forem excluídas). O paciente pode baixar qualquer planilha do seu histórico e alternar a visualização interativa entre planos gerados por IA. Suporte e migração transparente para o formato legado anterior.
- **Solução Inteligente de Upload e Download de Planilhas via Firebase Realtime Database**: Substituição do upload bloqueante no Firebase Storage por persistência inteligente direta no Firebase Realtime Database (`planos/{pacienteId}`). Armazena arquivos Excel/PDF/CSV em Base64 Data URL e mantém a estrutura JSON completa dos planos gerados por IA, garantindo download instantâneo em qualquer navegador/dispositivo sem erros de CORS, além de visualização interativa do plano no app para o paciente.
- **Geração de Planilha Alimentar com IA e Dupla Verificação**: Implementada a geração automática de planilhas (.xlsx) na visão da nutricionista a partir da anamnese do paciente, com 2 etapas de auditoria de IA (clínica e hábitos/formatação), pré-visualização, relatório de validação e entrega automática.
- **Correção de Permissão no Chat**: A nutricionista agora pode apagar qualquer mensagem (tanto as enviadas por ela quanto as recebidas dos pacientes) em todos os chats, incluindo o chat geral do paciente.
- **Notificações de Mensagens Não Lidas**: Sistema leve via Firebase RTDB (`unreadMessages/{uid}/{chatId}`) para alertar pacientes/nutricionistas sobre novas mensagens, exibido na lista de pacientes e no menu superior (sino), sem gerar spam.
- **Correção do bug de navegação de abas**: A ficha ou chat do paciente agora é fechada ao alterar a aba principal.

