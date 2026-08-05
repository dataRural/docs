![Logo UFRRJ](media/ufrrj.png)
<h1 style="text-align: center; font-size: 2.5rem; color: #284b8c">Documento de Especificação Funcional</h1>
<h2 style="text-align: center; font-size: 2rem; color: #284b8c">DataRural</h2>

<div style="page-break-after: always;"></div>

# Registro de Revisões
| Versão |    Data    |    Notas de revisão     | Responsável |
| :----: | :--------: | :---------------------: | :---------: |
|  1.0   | 03/04/2026 | Elaboração do Documento |   Rafael    |
|  2.0   | 05/08/2026 | Atualização conforme implementação atual |   Rafael    |

<div style="page-break-after: always;"></div>
<!--# Sumário
- [[#1. Introdução|1 Introdução]]
	- [[#1.1 Objetivo|1.1 Objetivo]]
	- [[#1.2 Definições, acrônimos e abreviações|1.2 Definições, acrônimos e abreviações]]
- [[#2. Premissas|2 Premissas]]
- [[#3. Definição de Escopo|3. Definição de Escopo]]
	- [[#3.1 Dentro do Escopo|3.1 Dentro do Escopo]]
	- [[#3.2 Fora de Escopo|3.2 Fora de Escopo]]
- [[#4. Detalhamento Técnico|4. Detalhamento Técnico]]
	- [[#4.1 Arquitetura|4.1 Arquitetura]]
	- [[#4.1 Arquitetura#4.2 Fluxo de Dados|4.2 Fluxo de Dados]]

<div style="page-break-after: always;"></div>-->

# 1. Introdução
## 1.1 Objetivo
Este documento tem como objetivo descrever a metodologia e as soluções técnicas que serão adotadas na construção do DataRural.
## 1.2 Definições, acrônimos e abreviações

| Sigla/Termo  |                                                         Significado                                                          |
| :----------: | :--------------------------------------------------------------------------------------------------------------------------: |
|  DataRural   |                                    Plataforma de repositório e compartilhamento de datasets                                    |
|     CSV      |                         formato de arquivo para planilhas onde os valores são separados por vírgulas                         |
|   Markdown   |                Linguagem de marcação leve que permite adicionar elementos de formatação a documentos de texto                |
|  Adonis.js   | Framework específico para Node.js que fornece ferramentas prontas para criar aplicações web com organização e produtividade. |
|  Inertia.js  | Protocolo que permite construir SPAs modernas usando frameworks de servidor clássicos, conectando o AdonisJS ao React sem necessidade de API separada. |
|    React     |                                      Biblioteca front-end com foco em criar interfaces                                       |
|  Shadcn UI   |                                             Conjunto de componentes web modular                                              |
| Tailwind CSS |                           Framework CSS utilitário para estilização rápida e responsiva                                      |
|   Turborepo  |            Ferramenta de build para monorepos JavaScript/TypeScript com cache inteligente e execução paralela                 |
|     pnpm     |                        Gerenciador de pacotes rápido e eficiente com suporte nativo a workspaces                             |
|    JSZip     |                        Biblioteca JavaScript para criar, ler e editar arquivos .zip                                          |
|     SSR      |                    Server-Side Rendering — renderização de páginas no lado do servidor                                        |

# 2. Premissas
Temos como premissas para o escopo inicial do DataRural:
- O upload inicial de *datasets* será limitado a arquivos no formato CSV, sendo responsabilidade do usuário garantir a integridade e padronização mínima dos dados enviados.
# 3. Definição de Escopo
## 3.1 Dentro do Escopo
Este documento entrará em detalhes sobre as seguintes questões:
- Tecnologias utilizadas no projeto;
- Integrações entre essas tecnologias;
## 3.2 Fora de Escopo
Está fora do escopo desse documento fornecer justificativas para as soluções encontradas. Também não será abordada a modelagem do banco de dados.
# 4. Detalhamento Técnico
## 4.1 Arquitetura
A solução é estruturada como um **monorepo** gerenciado com **Turborepo** e **pnpm workspaces**, adotando uma arquitetura fullstack que prioriza a integridade dos dados e a performance na entrega de grandes volumes de informação.

- **Fullstack (AdonisJS + Inertia.js + React)**: A aplicação principal reside em `apps/web` e utiliza o framework AdonisJS integrado ao React via Inertia.js, eliminando a necessidade de uma API REST separada para o frontend. O servidor renderiza as páginas com suporte a SSR (Server-Side Rendering).
- **UI Compartilhada**: Os componentes de interface são mantidos em um pacote compartilhado (`packages/ui`) baseado em Shadcn UI, Radix UI e Tailwind CSS v4, garantindo uma interface modular, customizável e responsiva.
- **Armazenamento de Arquivos**: O gerenciamento de arquivos dos datasets é feito pelo AdonisJS Drive em conjunto com o pacote `@jrmc/adonis-attachment`, que cuida do armazenamento no sistema de arquivos local (`storage/uploads`) e da associação dos anexos aos modelos.
- **Versionamento de Dados**: O versionamento é implementado por meio dos modelos `DatasetVersion` e `DatasetVersionFile`, que rastreiam cada versão e seus respectivos arquivos. Um arquivo `README.md` é gerado automaticamente para cada versão.
- **Banco de dados**: PostgreSQL executado em container Docker, acompanhado de pgAdmin para administração e Mailpit para captura de e-mails em desenvolvimento.

### 4.1.1 Estrutura do Monorepo

```
Raiz/
├── apps/
│   └── web/               # Aplicação AdonisJS + Inertia + React
│       ├── app/
│       │   ├── auth/       # Módulo de autenticação
│       │   ├── common/     # Utilitários e modelos base
│       │   ├── core/       # Hooks, middleware, políticas, UI base
│       │   ├── dataset/    # Módulo de datasets
│       │   ├── groups/     # Módulo de grupos (times)
│       │   ├── marketing/  # Páginas de marketing
│       │   └── users/      # Módulo de usuários
│       ├── config/         # Configurações do AdonisJS
│       ├── database/       # Schemas e migrações
│       ├── resources/      # Views Edge e assets estáticos
│       └── storage/        # Arquivos enviados pelos usuários
├── packages/
│   ├── ui/                 # Componentes compartilhados (Shadcn UI)
│   ├── eslint-config/      # Configuração compartilhada de lint
│   └── typescript-config/  # Configuração compartilhada de TypeScript
└── docker-compose.yaml
```

### 4.2 Fluxo de Dados
O fluxo de informações no DataRural segue regras estritas de acesso e otimização de leitura:

- **Ingestão de Dados**: Ao realizar o upload, a API processa os metadados (nome, licença, visibilidade, área, unidade, período, região, tags) para o banco de dados. Os arquivos físicos são entregues ao sistema de attachments do AdonisJS Drive, que os armazena em `storage/uploads`. Um arquivo `README.md` é gerado automaticamente no diretório da versão dentro de `storage/datasets`.
- **Consumo de Datasets**: 
    - **Públicos**: Acessíveis via rota pública, permitindo visualização e download por usuários não autenticados.
    - **Privados/Grupos**: O acesso é validado por middleware que verifica a associação do usuário logado ao grupo proprietário do dataset, respeitando os papéis de membro (owner, admin, editor).
- **Visualização (Preview)**: Para garantir a performance, o sistema gera uma versão de preview dos arquivos para ler e exibir apenas as primeiras linhas dos CSVs (até 30 linhas), evitando o carregamento total de arquivos extensos no navegador.
- **Download**: O sistema suporta o download individual de arquivos, do README, ou de todos os arquivos de uma versão empacotados em um arquivo ZIP (via JSZip).
