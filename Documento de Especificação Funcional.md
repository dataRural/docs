![Logo UFRRJ](media/ufrrj.png)
<h1 style="text-align: center; font-size: 2.5rem; color: #284b8c">Documento de Especificação Funcional</h1>
<h2 style="text-align: center; font-size: 2rem; color: #284b8c">DataRural</h2>

<div style="page-break-after: always;"></div>

# Registro de Revisões
| Versão |    Data    |    Notas de revisão     | Responsável |
| :----: | :--------: | :---------------------: | :---------: |
|  1.0   | 01/04/2026 | Elaboração do Documento |   Rafael    |
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
- [[#4. Regras de Negócio|4. Regras de Negócio]]
	- [[#4.1 Requisitos Funcionais|4.1 Requisitos Funcionais]]
	- [[#4.2 Regras de Negócio|4.2 Regras de Negócio]]
 
<div style="page-break-after: always;"></div>-->

# 1. Introdução
## 1.1 Objetivo
Este documento tem como objetivo descrever os Requisitos Funcionais para o DataRural.
## 1.2 Definições, acrônimos e abreviações

| Sigla/Termo |                                 Significado                                  |
| :---------: | :--------------------------------------------------------------------------: |
|  DataRural  |        Plataforma de repositório e compartilhamento de datasets              |
|     CSV     | Formato de arquivo para planilhas onde os valores são separados por vírgulas  |
|    Grupo    | Conjunto de usuários com papéis definidos para colaboração em datasets        |
# 2. Premissas
Temos como premissas para o escopo inicial do DataRural:
- O upload inicial de *datasets* será limitado a arquivos no formato CSV, sendo responsabilidade do usuário garantir a integridade e padronização mínima dos dados enviados.
# 3. Definição de Escopo
## 3.1 Dentro do Escopo
Este documento visa abordar as regras de funcionamento geral do sistema, assim como suas características principais.
## 3.2 Fora de Escopo
Está fora do escopo desse documento abordar questões específicas sobre a arquitetura do projeto.
# 4. Regras de Negócio
## 4.1 Requisitos Funcionais

| Código |                                         Requisito Funcional                                         |
| :----: | :-------------------------------------------------------------------------------------------------: |
|  RF01  | O sistema deve permitir o upload de *datasets* em formato CSV                                       |
|  RF02  | O sistema deve permitir o upload de múltiplos arquivos CSV por versão do dataset                     |
|  RF03  | O sistema deve permitir a criação de grupos de usuários com papéis (dono, admin, editor, visualizador) |
|  RF04  | O sistema deve permitir a criação de *datasets* privados                                            |
|  RF05  | O sistema deve permitir alternar a visibilidade (público/privado) de um dataset                     |
|  RF06  | O sistema deve permitir o versionamento de *datasets*                                               |
|  RF07  | O sistema deve permitir a restauração de versões anteriores de um dataset                           |
|  RF08  | O sistema deve permitir a exclusão lógica (soft delete) de versões                                  |
|  RF09  | O sistema deve permitir a descrição e o licenciamento de *datasets* publicados                      |
|  RF10  | O sistema deve permitir a classificação de datasets por metadados (área, unidade, período, região, tags) |
|  RF12  | O sistema deve permitir o download individual de arquivos de um dataset                             |
|  RF13  | O sistema deve permitir o download de todos os arquivos de uma versão em formato ZIP                |
|  RF14  | O sistema deve permitir o registro e autenticação de usuários                                       |
|  RF16  | O sistema deve permitir a edição de perfil do usuário (nome, bio, avatar, instituição, localização) |
|  RF17  | O sistema deve permitir que usuários curtam datasets                                                |
|  RF18  | O sistema deve permitir que usuários favoritem datasets para acesso rápido                          |
|  RF19  | O sistema deve permitir a exploração e busca de datasets com filtro por área temática               |
|  RF20  | O sistema deve exibir uma prévia das primeiras linhas dos arquivos CSV de um dataset                |
|  RF22  | O sistema deve permitir a atribuição de datasets a grupos                                          |
|  RF23  | O sistema deve permitir a exclusão de datasets pelo proprietário ou membro autorizado do grupo      |
|  RF24  | O sistema deve oferecer um painel administrativo para gestão de usuários e áreas temáticas          |
|  RF25  | O sistema deve registrar trilha de auditoria para alterações nas entidades principais               |
|  RF26  | O sistema deve oferecer suporte a internacionalização (i18n)                                        |
|  RF27  | O sistema deve exibir perfis públicos de usuários                                                   |
## 4.2 Regras de Negócio
- O sistema deve limitar a publicação de *datasets* a usuários autenticados
- O sistema deve permitir que usuários não registrados visualizem e façam download de *datasets* públicos
- Todo *dataset* publicado deve possuir uma licença associada
- *Datasets* não publicados devem ser visíveis apenas pelo usuário proprietário ou membros do grupo associado
- Nomes de *datasets* devem ser únicos no sistema
- Cada arquivo CSV enviado deve ter no máximo 25 MB
- Membros de grupo com papel de dono, admin ou editor podem editar datasets do grupo
- Apenas membros com papel de dono ou admin podem excluir versões de um dataset do grupo
- A exclusão de versões é lógica (soft delete), preservando os dados no sistema