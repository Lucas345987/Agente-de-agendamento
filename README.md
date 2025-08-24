# Projeto: Agente de Agendamento Inteligente

Este projeto consiste em um sistema automatizado e inteligente para o agendamento de compromissos, construído em uma plataforma de automação de fluxos de trabalho (como o n8n). O agente é capaz de interagir com usuários por meio de texto e áudio, processar suas solicitações e gerenciar eventos diretamente em uma agenda do Google.

## Sobre o Projeto

O "Agente de Agendamento Inteligente" foi desenvolvido para otimizar e automatizar o processo de marcação de horários. Ele funciona como um assistente virtual que compreende as solicitações dos usuários, verifica informações sobre serviços e disponibilidades, e realiza ações como criar, consultar, atualizar ou deletar eventos.

A arquitetura é dividida em dois componentes principais:

1.  **O Agente de Conversação Principal:** Responsável por receber e interpretar as mensagens do usuário.
2.  **O MCP (Master Control Program) de Agendamento:** Um "cérebro" de ferramentas que executa as ações práticas no Google Calendar e Google Sheets.

## Funcionalidades Principais

  * **Comunicação Multicanal:** Recebe solicitações de usuários via Webhook, permitindo integração com diversas plataformas de mensagem (como WhatsApp, Telegram, etc.).
  * **Processamento de Texto e Áudio:** O agente é capaz de entender tanto mensagens de texto digitadas quanto mensagens de voz, que são automaticamente convertidas para texto.
  * **Inteligência Artificial Conversacional:** Utiliza um modelo de IA avançado (`OpenRouter Chat`) para interpretar a intenção do usuário, manter o contexto da conversa e formular respostas coesas.
  * **Integração com Ferramentas (Tools):** O agente não apenas conversa, mas também executa tarefas reais. Ele utiliza um conjunto de ferramentas para interagir com serviços externos.
  * **Gerenciamento de Agenda:** Total integração com o Google Calendar para:
      * Criar novos eventos (`criarEvento`).
      * Atualizar eventos existentes (`atualizarEvento`).
      * Buscar por agendamentos (`buscarEvento`).
      * Cancelar/deletar eventos (`DeletarEventor`).
  * **Consulta de Informações Dinâmicas:** Acessa uma planilha do Google Sheets (`Servicos`) para obter informações atualizadas sobre os serviços oferecidos, preços ou horários disponíveis, sem a necessidade de alterar o fluxo de trabalho.

## Arquitetura e Fluxo de Trabalho

O sistema é modular, separando a lógica da conversa da execução das tarefas.

### 1\. Fluxo do Agente de Agendamento Inteligente

Este é o fluxo principal que lida com a interação do usuário.

1.  **Webhook:** O fluxo é iniciado ao receber uma requisição (POST), geralmente vinda de uma plataforma de mensagens.
2.  **Tratamento de Dados:** Os dados brutos recebidos são processados e preparados.
3.  **Switch (É texto ou áudio?):** O sistema verifica se a mensagem recebida é um texto ou um áudio.
      * **Se for Texto:** A mensagem segue diretamente para o processamento da IA.
      * **Se for Áudio:** A mensagem de áudio (geralmente em formato Base64) é primeiro convertida para um arquivo e enviada a uma API de transcrição (via `HTTP Request`) para ser transformada em texto.
4.  **AI Agent:** O texto da solicitação do usuário é enviado para o agente de IA. Este agente, equipado com memória e um conjunto de ferramentas, entende o que o usuário deseja (ex: "Quero marcar um corte de cabelo para sexta às 15h").
5.  **Uso das Ferramentas (MCP Client):** Para executar a solicitação, o agente de IA aciona a ferramenta apropriada do "MCP de Agendamento" (por exemplo, a ferramenta `criarEvento`).
6.  **Envio da Resposta:** Com base no resultado da ação, o agente de IA formula uma resposta final (ex: "Confirmado\! Seu corte de cabelo está agendado para sexta-feira às 15h.") e a envia de volta ao usuário.
<img width="1676" height="560" alt="Captura de tela 2025-08-24 134840" src="https://github.com/user-attachments/assets/10273887-d1d7-472f-a816-2d87b656b713" />

-------
### 2\. Fluxo do MCP de Agendamento

Este fluxo funciona como um backend de ferramentas que fica à disposição do agente principal.

1.  **MCP Server Trigger:** Este nó aguarda ser "chamado" pelo agente principal.
2.  **Disponibilização das Ferramentas (Tools):** Ele expõe um conjunto de cinco funções que o agente de IA pode utilizar:
      * **`criarEvento`:** Conecta-se à API do Google Calendar para criar um novo evento.
      * **`atualizarEvento`:** Modifica um evento já existente no Google Calendar.
      * **`buscarEvento`:** Realiza consultas na agenda para verificar horários disponíveis ou detalhes de um evento.
      * **`DeletarEventor`:** Remove um evento da agenda.
      * **`Servicos`:** Conecta-se à API do Google Sheets para ler dados de uma planilha específica, como lista de serviços, durações e preços.

## Tecnologias Utilizadas

  * **Plataforma de Automação:** n8n ou similar.
  * **Modelo de Linguagem (LLM):** `OpenRouter Chat`.
  * **Serviços Google:**
      * Google Calendar API.
      * Google Sheets API.
  * **API de Transcrição de Áudio (Speech-to-Text).**
  * **Webhook** para integração com sistemas de terceiros.
<img width="1445" height="623" alt="Captura de tela 2025-08-24 134823" src="https://github.com/user-attachments/assets/5c0d5e30-5515-4ba4-a5fa-c32015ae67b0" />
