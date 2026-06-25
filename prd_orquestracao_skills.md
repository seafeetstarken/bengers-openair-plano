# 📋 Resumo PRD: Orquestração e Planejamento de Skills (Bangers Open Air)

Este documento descreve os requisitos de produto, arquitetura técnica e o planejamento operacional para a **Orquestração de Skills** dentro do ecossistema digital do **Bangers Open Air 2027**. 

A orquestração utiliza as ferramentas [NotFair](file:///C:/Users/juanf/OneDrive/Documents/Bengers-OpenAir/skills/NotFair) e [antigravity-awesome-skills](file:///C:/Users/juanf/OneDrive/Documents/Bengers-OpenAir/skills/antigravity-awesome-skills) para automatizar a inteligência de tráfego, captura de leads, deploy de tags e ativação conversacional.

---

## 🎯 1. Objetivos do Produto

O motor de orquestração de skills serve como a "engrenagem invisível" que alimenta os planos comerciais, principalmente a central **The Headliner AI**.

*   **Autonomia Operacional:** Permitir que agentes inteligentes realizem tarefas técnicas complexas (como deploy de tags e monitoramento de banco de dados) sem intervenção humana direta.
*   **Geração Preditiva & Ativação de Tráfego:** Viabilizar a criação ágil de campanhas de tráfego pago baseadas nas preferências reais de bandas coletadas pelo app.
*   **Captura de Dados Proprietária:** Monitorar e consolidar em tempo real os leads e interações dos usuários em bancos de dados estruturados (Firebase/CRM).

---

## 🏗️ 2. Arquitetura da Orquestração

A centralização e isolamento das ferramentas sob a pasta `skills/` seguem o modelo de independência de Git e execução local por comandos automatizados.

```mermaid
graph TD
    subgraph App Principal (Bangers)
        A[index.html / App V2] -->|Coleta de Dados / Eventos| B[(Firebase / CRM)]
    end

    subgraph Orquestração (NotFair Engine)
        C[nf.py CLI] -->|1. Validação| D[Ambiente & Chaves]
        C -->|2. GTM Deploy| E[Google Tag Manager API]
        F[listen_leads.py] -->|3. Escuta / Watch| B
    end

    subgraph Catálogo de Inteligência (Awesome Skills)
        G[scripts/google-ads/] -->|4. Geração de Ads| H[Google Ads API]
        I[Playbooks / Prompts] -->|5. Ideação & Social| J[MLabs / VPS]
    end

    B -.-> F
```

---

## ⚙️ 3. Mapeamento Funcional das Skills

A orquestração é dividida em duas grandes frentes de skills clonadas na pasta `skills/`:

### A. Core Engine & Tracking ([`NotFair`](file:///C:/Users/juanf/OneDrive/Documents/Bengers-OpenAir/skills/NotFair))
Este motor gerencia a parte estrutural, validação de configurações e captura de eventos críticos.
*   **Validador de Ambiente (`validate`):** Garante que todas as credenciais de APIs e chaves necessárias para o festival estejam ativas e seguras.
*   **GTM Auto-Deploy (`gtm-deploy`):** Cria e injeta programaticamente tags de conversão do Google Ads, Facebook Pixel e Analytics diretamente no contêiner do GTM.
*   **Leads Real-time Listener (`listen_leads.py`):** Mantém uma escuta ativa (`watch`) na base do Firebase para identificar leads VIPs, disparar alarmes no CRM e iniciar funis conversacionais.

### B. Marketing & Playbooks ([`antigravity-awesome-skills`](file:///C:/Users/juanf/OneDrive/Documents/Bengers-OpenAir/skills/antigravity-awesome-skills))
Este repositório contém os scripts geradores e regras de negócio para as inteligências de tráfego.
*   **Automação de Tráfego (`google-ads/`):** Contém ferramentas como o `starken_campaign.py` e `keyword_research.py` para gerar grupos de anúncios locais baseados na preferência musical segmentada no aplicativo.
*   **Biblioteca de Prompting & Execução:** Playbooks para ideação de cópias criativas direcionados para o time de redação e social media do festival.

---

## 🗺️ 4. Planejamento de Uso (Cronograma & Fases)

O uso programático das skills está atrelado ao Roadmap Geral do projeto do festival:

| Fase | Ação de Orquestração | Script / Comando Principal | Objetivo Comercial |
| :--- | :--- | :--- | :--- |
| **Fase 1** *(Imediato)* | **Validação Técnica** | `python skills/NotFair/bin/nf.py validate bangers` | Garantir a conexão de banco de dados e APIs do festival. |
| **Fase 2** *(Mês 1-2)* | **Estrutura de Tracking** | `python skills/NotFair/bin/nf.py gtm-deploy bangers --publish` | Habilitar a medição de conversão e pixel na loja e-commerce básica. |
| **Fase 3** *(Mês 2-4)* | **Captura e Escuta de Leads** | `python skills/NotFair/bin/listen_leads.py watch` | Alimentar o CRM em tempo real com novos cadastros e quiz. |
| **Fase 4** *(Mês 4-8)* | **Tráfego IA e Ativação** | `python scripts/google-ads/starken_campaign.py` | Disparar campanhas hiper-personalizadas e automação social. |

---

## 🔒 5. Segurança & Boas Práticas

> [!WARNING]
> **Isolamento de Credenciais:** As chaves de API e arquivos de serviço (`service-account.json`, etc.) do tenant `bangers` **nunca** devem ser salvos dentro das pastas de código do aplicativo. Eles devem ser mantidos no diretório de segurança dedicado (`C:\dev\tenants\bangers`).

> [!IMPORTANT]
> **Git Protection:** A pasta `skills/` e seus subdiretórios devem permanecer ignorados no arquivo de controle de versão principal para evitar commits acidentais de códigos das automações.
