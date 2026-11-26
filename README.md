#  Rede Ritmo — Campanha Aula Experimental Grátis (NoCode & LowCode)

Projeto avaliativo desenvolvido para o curso **SCTEC Aperfeiçoar - NoCode e LowCode**. A solução centraliza os pedidos de aula experimental gratuita das três unidades da Rede Ritmo na Grande Florianópolis (Centro, Estreito e São José), automatizando a padronização, validação, classificação de prioridade e notificação para a equipe comercial.

---

##  Links do Projeto

* **Página da Campanha (Base44):** https://rederitimoprojetothaize.base44.app/
* **Quadro de Acompanhamento (Trello):** https://trello.com/b/WBY09XHh/rederitmoprojeto
* **Base de Dados:** Armazenada na **Data Table nativa do n8n** *(não exportável junto ao `.json`, demonstrada em execução no vídeo de entrega)*.
* **Arquivo do Fluxo:** Disponível neste repositório (`[RedeRitmo.json]`).

---

##  O Problema

A Rede Ritmo divulgava sua campanha mensal de aula experimental em anúncios, Instagram e WhatsApp, mas recebia os pedidos de forma espalhada. Com isso, a recepção não conseguia priorizar quem tinha urgência para iniciar os treinos, perdendo alunos interessados para a concorrência.

---

##  Como a Solução Funciona

1. **Captação:** O visitante preenche o formulário na landing page (nome, WhatsApp, e-mail, unidade, modalidade e previsão de início) e os dados são enviados via requisição `POST` para um **Webhook** no n8n.
2. **Padronização:** O fluxo sanitiza os dados antes de qualquer processamento:
   * Remove espaços extras nas pontas do nome (`trim`).
   * Converte o e-mail para letras minúsculas (`toLowerCase`).
   * Mantém apenas os dígitos numéricos no WhatsApp.
3. **Validações e Segurança:**
   * Verifica o campo isca oculto (*honeypot*) para bloquear envios automatizados de robôs.
   * Valida nome obrigatório e formato válido de e-mail.
   * Consulta a Data Table do n8n para impedir pedidos duplicados com o mesmo e-mail.
   * Em caso de erro ou duplicidade, o fluxo interrompe a execução e retorna uma mensagem clara (`400` ou `409`) diretamente na interface da página.
4. **Regra de Negócio — Classificação de Leads:**
   * **Quente:** Pretende começar *esta semana* (prioridade máxima de contato).
   * **Morno:** Pretende começar *este mês*.
   * **Frio:** Está *apenas pesquisando*.
5. **Integração Externa:** O fluxo cria automaticamente um cartão no **Trello** com os dados do lead e a unidade correspondente, utilizando credenciais configuradas de forma segura no n8n.
6. **Registro e Confirmação:** O pedido padronizado e classificado é salvo na **Data Table do n8n** e o Webhook devolve a confirmação visual de sucesso na página ao usuário.

---

## Como Reproduzir / Rodar o Projeto

1. **Importar o Workflow no n8n:**
   * Acesse sua instância do n8n, crie um novo workflow, clique no menu de três pontos (`...`) no canto superior direito e selecione **Import from File**.
   * Selecione o arquivo `.json` disponível neste repositório.
2. **Configurar a Base de Dados (Data Table):**
   * Crie uma **Data Table** no n8n com as colunas utilizadas no fluxo (`nome`, `whatsapp`, `email`, `unidade`, `modalidade`, `inicio`, `classificacao`) e vincule-a aos nós de consulta de duplicidade e gravação de lead.
3. **Configurar Credenciais do Trello:**
   * No nó do Trello, adicione suas credenciais de API e selecione o quadro e as listas correspondentes aos níveis de prioridade.
4. **Conectar à Página:**
   * Ative o workflow no n8n (`Active`), copie a **Production URL** do nó Webhook e configure-a no envio do formulário da página no Base44.

---

##  Como Testar

1. Acesse o link da página publicada no **Base44**.
2. **Teste de Fluxo Completo e Padronização:** Preencha um nome com espaços extras (ex.: `"  Paula Silva  "`), e-mail em maiúsculas (ex.: `"PAULA@Email.com"`), WhatsApp com máscara `(48) 99999-0000` e selecione **"Esta semana"** (Lead Quente). Envie e confira a mensagem verde de confirmação na página, a criação do card no Trello e o registro limpo na tabela do n8n.
3. **Teste de Regra de Negócio:** Envie novos cadastros variando o campo de início entre **"Este mês"** (Morno) e **"Pesquisando"** (Frio) para validar os diferentes caminhos no nó `Switch`.
4. **Teste de Validações (Erros):**
   * Tente enviar novamente um e-mail já cadastrado para validar o bloqueio de duplicidade (status `409`) e a mensagem de alerta na tela.
   * Tente enviar o formulário com e-mail inválido ou campo obrigatório vazio para validar a resposta de erro (`400`).

---

## Apresentação em Vídeo

* **Vídeo Demonstrativo:** [em breve]

---
##  Tecnologias Utilizadas

* **Front-end / Landing Page:** Base44
* **Orquestração e Automação:** n8n (Cloud)
* **Armazenamento de Dados:** n8n Data Tables
* **Gestão de Tarefas / Notificação:** Trello
