# Finance AI

### IA / Telegram / Automação / Assistente Financeiro

## Visão Geral

Finance AI é um assistente financeiro privado baseado em Telegram documentado aqui como estudo de caso público e seguro.

O produto combina automação no Telegram, entrada financeira em linguagem natural, processamento de voz, interpretação assistida por LLM, regras financeiras determinísticas, fluxos de assinatura/cobrança e painel administrativo.

Este repositório não contém código proprietário, credenciais, bot tokens, URLs de produção, dados de usuários, dumps de banco ou secrets de provedores.

## Problema

Ferramentas de finanças pessoais geralmente exigem formulários estruturados, mas usuários descrevem gastos, receitas, compromissos e parcelas em linguagem conversacional.

O desafio de engenharia é permitir entrada flexível em linguagem natural sem deixar o modelo de IA virar a fonte da verdade financeira, enquanto o sistema também lida com retries de webhook do Telegram, processamento assíncrono, estados de cobrança e privacidade.

## Solução

O código privado implementa um backend Laravel com ingestão de webhooks Telegram, processamento em filas, extração de intenção com LLM, serviços financeiros determinísticos, persistência MySQL, workers Redis/Horizon, cobrança via Telegram Stars e painel admin React/TypeScript.

O modelo ajuda a interpretar mensagens. O backend controla cálculo, validação, persistência e estado do usuário.

## Fluxo do Produto

```mermaid
flowchart LR
    A[Usuario no Telegram] --> B[Webhook]
    B --> C[Registro idempotente]
    C --> D[Worker de fila]
    D --> E{Texto ou voz}
    E --> F[Speech-to-text]
    E --> G[Extracao de intencao via LLM]
    F --> G
    G --> H[Motor financeiro]
    H --> I[(MySQL)]
    H --> J[Resposta no Telegram]
```

## Arquitetura do Sistema

```mermaid
flowchart TB
    A[Telegram Bot API] --> B[Endpoint webhook Laravel]
    B --> C[Middleware e idempotencia]
    C --> D[Workers de fila]
    D --> E[Camada de provedor IA]
    D --> F[Servicos do dominio financeiro]
    D --> G[Servicos de billing e entitlement]
    F --> H[(MySQL)]
    D --> I[(Filas Redis)]

    J[Painel admin React] --> K[API admin]
    K --> H

    E --> L[DeepSeek / provedor compativel OpenAI]
    E --> M[Speech-to-text]
```

## Stack Técnica

- Backend: Laravel 13, PHP 8.3/8.4.
- Filas: Redis e Laravel Horizon.
- Banco de dados: MySQL.
- Admin frontend: React 19, TypeScript, Vite e Tailwind CSS.
- IA: provedor DeepSeek com opção compatível com OpenAI.
- Voz: fluxo local com whisper.cpp e suporte a fallback hospedado.
- Mensageria/cobrança: Telegram Bot API e Telegram Stars.
- Observabilidade: integração Sentry no código privado.

## Engenharia Backend

O backend inclui webhook Telegram, roteamento de comandos, processamento conversacional, serviços financeiros, isolamento de usuário, assinaturas, cobrança Stars, reembolsos, automações de engajamento, métricas admin e endpoints de health/readiness.

Comandos passam por rotas determinísticas quando isso é mais seguro. Mensagens conversacionais passam por extração de intenção e validação backend antes de qualquer registro financeiro ser persistido.

## Dados / Banco de Dados

O modelo privado inclui usuários, updates Telegram, conversas, transações financeiras, compromissos, parcelas, orçamentos, recorrências, lembretes, uso de IA, registros de billing, ledger de Stars, logs de auditoria e métricas administrativas.

Valores monetários são representados em centavos inteiros para evitar drift de ponto flutuante.

## Integrações

- Telegram Bot API para mensagens, callbacks e cobrança por Stars.
- DeepSeek para extração de intenção financeira.
- Speech-to-text para mensagens de voz.
- Redis/Horizon para processamento assíncrono.
- Sentry para visibilidade de erros.

## Segurança

Detalhes confirmados no código privado:

- Middleware de validação de segredo do webhook Telegram.
- Processamento idempotente de updates/webhooks.
- Tokens opacos em callbacks, sem embutir valores financeiros.
- API admin baseada em sessão com CSRF e TOTP 2FA opcional.
- Dados financeiros isolados por usuário.
- Validação backend antes da persistência.
- Nenhuma chave de provedor ou bot token publicada neste case.

## Desafios de Engenharia

- Manter a saída da IA como interpretação, enquanto serviços backend seguem como fonte da verdade financeira.
- Tratar retries de webhook sem duplicar transações.
- Suportar voz sem bloquear o caminho de resposta do webhook.
- Lidar com roteamento de modelos, timeouts, JSON inválido e falhas de provedor.
- Representar compromissos, parcelas, recorrências e gastos realizados de forma consistente.
- Implementar cobrança via Telegram Stars e estados de entitlement.
- Dar visibilidade administrativa sem expor dados privados de usuários desnecessariamente.

## Testes e Confiabilidade

O projeto privado contém testes para respostas DeepSeek, saída inválida/vazia da IA, retry e timeout, speech-to-text local, entrega de webhook Telegram, fluxos conversacionais, ciclo de vida de transações, isolamento de usuário, billing, ledger Stars, reembolsos, autenticação admin, métricas e inteligência financeira.

O runbook de deploy inclui health/readiness, workers de fila, scheduler, backup/restore e smoke tests.

## Infraestrutura e Deploy

A documentação privada descreve produção em VPS com Nginx, PHP-FPM, MySQL, Redis, workers, scheduler, Certbot, rotação de logs e automação de deploy.

Domínios, secrets, bot tokens e caminhos de servidor não são publicados.

## Produção

O projeto inclui runbooks de produção para deploy, configuração de webhook Telegram, workers, scheduler, health checks e controles operacionais. Este case expõe a arquitetura sem expor a superfície operacional real.

## Capturas de Tela

Capturas de tela não foram incluídas porque interfaces conversacionais e financeiras podem expor dados privados com facilidade. Somente capturas sanitizadas devem ser adicionadas futuramente.

## Meu Papel / O Que Construí

Este case demonstra backend, IA aplicada e automação: integração Telegram, serviços Laravel, desenho de workflow com LLM, lógica financeira determinística, filas, billing, admin, testes e preparação de produção.

## Decisões Técnicas Principais

- Usar LLM para interpretação, não como sistema de registro.
- Armazenar dinheiro em centavos inteiros.
- Processar webhooks Telegram de forma idempotente e assíncrona.
- Manter payloads de callback opacos.
- Separar automação do usuário de operações admin e billing.
- Publicar estudo de caso seguro em vez de código privado.
