# AI Agent WhatsApp - Automação com n8n & Evolution API

Este repositório contém a infraestrutura completa e a lógica para um **Agente de Inteligência Artificial** integrado diretamente no WhatsApp. A solução utiliza o **n8n** como motor de orquestração e a **Evolution API** para a comunicação com o WhatsApp.

## 🏗️ Arquitetura do Projeto

O projeto é baseado em containers Docker, garantindo que todos os serviços rodem de forma isolada, escalável e segura.

* **n8n**: Orquestrador de fluxos onde reside a inteligência do agente e as integrações.
* **Evolution API v2**: Interface robusta para conexão e gestão de instâncias de WhatsApp.
* **PostgreSQL**: Base de dados relacional (instâncias separadas para n8n e Evolution).
* **Redis**: Armazenamento em cache e gestão de filas para alta performance e estabilidade.

## 📂 Estrutura de Ficheiros

* `workflow-n8n/`: Pasta com o ficheiro de workflow.
    * `AI Agent Whatsapp.json`: O fluxo completo pronto para ser importado no n8n.
* `docker-compose.yaml`: Ficheiro de configuração para subir toda a stack de serviços de forma automática.

## 🚀 Como Instalar e Configurar

### 1. Pré-requisitos
* Docker e Docker Compose instalados.
* Configurar um ficheiro `.env` na raiz do projeto com as variáveis necessárias (Baseie-se nas variáveis chamadas no `docker-compose.yaml`).

### 2. Iniciar os Serviços
No terminal, na pasta raiz do projeto, execute:
```bash
docker-compose up -d
```

### 3. Configurar o n8n
* Aceda ao n8n em http://localhost:5678
* Importe o ficheiro AI Agent Whatsapp.json
* Configure as suas credenciais de IA (ex: OpenAI API Key) dentro dos nós do agente.

### 4. Configurar a Evolution API
* Aceda à API em http://localhost:8080
* Crie uma nova instância e ligue o seu WhatsApp via QR Code.
* Configure o Webhook da instância para enviar as mensagens recebidas para a URL de Webhook do n8n.

## ⚙️ Detalhes da Stack (Containers)

A infraestrutura deste projeto foi desenhada para ser resiliente e performática, separando as responsabilidades de banco de dados e cache entre o motor de automação (n8n) e o gateway de comunicação (Evolution API).

## Tabela de Serviços

| Serviço | Container Name | Imagem | Porta Externa | Finalidade |
| :--- | :--- | :--- | :--- | :--- |
| **n8n** | `n8n` | `docker.n8n.io/n8nio/n8n` | `5678` | Interface de automação e orquestração da IA. |
| **Evolution API** | `evolution_api` | `evoapicloud/evolution-api:v2.3.2` | `8080` | Gateway para conexão e gestão do WhatsApp. |
| **Postgres (Evolution)** | `postgres_evolution` | `postgres:15` | `5436` | Persistência de dados das instâncias da Evolution API. |
| **Redis (Evolution)** | `redis_evolution` | `redis:latest` | `6380` | Cache de sessões e mensagens da Evolution API. |
| **Postgres (n8n)** | `postgres_n8n` | `postgres:15` | Interna | Banco de dados para workflows e usuários do n8n. |
| **Redis (n8n)** | `redis_n8n` | `redis:latest` | `${REDIS_PORT}` | Gerenciamento de execução de filas (Queue Mode) do n8n. |

### 🌐 Rede e Conectividade

Todos os serviços estão interconectados através da rede Docker Bridge: `evolution_n8n_net`. Isso permite que o n8n se comunique com a Evolution API e os bancos de dados utilizando apenas o nome do serviço como hostname, garantindo maior segurança e isolamento.

### 💾 Persistência de Dados (Volumes)

Os dados são persistidos localmente no host através dos seguintes volumes:
- `evolution_instances`: Dados das sessões WhatsApp.
- `postgres_evolution_data`: Base de dados da Evolution API.
- `redis_evolution_data`: Cache da Evolution API.
- `n8n_data`: Configurações e binários do n8n.
- `postgres_data`: Base de dados dos workflows.
- `redis_data`: Dados de execução do n8n.

