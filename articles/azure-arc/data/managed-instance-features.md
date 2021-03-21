---
title: Funcionalidades e Capacidades do Arco Azure ativaram a sql Gestão de Instância
description: Funcionalidades e Capacidades do Arco Azure ativaram a sql Gestão de Instância
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c42acb69e13cc1eb0fbba3fcafaec1451bc4d77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589224"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Funcionalidades e Capacidades do Arco Azure ativaram a sql Gestão de Instância

Azure Arc habilitado SQL Managed Instance compartilham uma base de código comum com a versão mais recente estável do SQL Server. A maioria das funcionalidades padrão de idioma SQL, processamento de consultas e gestão de bases de dados são idênticas. As funcionalidades comuns entre o SQL Server e a SQL Database ou a SQL Managed Instance são:

- Características [linguísticas - Controlo de palavras-chave de linguagem de fluxo,](/sql/t-sql/language-elements/control-of-flow) [Cursors,](/sql/t-sql/language-elements/cursors-transact-sql)Tipos [de Dados,](/sql/t-sql/data-types/data-types-transact-sql) [Declarações DML,](/sql/t-sql/queries/queries) [Predicados, Números](/sql/t-sql/queries/predicates) [de sequências,](/sql/relational-databases/sequence-numbers/sequence-numbers) [Procedimentos armazenados](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)e [Variáveis.](/sql/t-sql/language-elements/variables-transact-sql)
- Características da base de dados - [Afinação automática (força de plano)](/sql/relational-databases/automatic-tuning/automatic-tuning), [Rastreio de alterações,](/sql/relational-databases/track-changes/about-change-tracking-sql-server) [colagem de](/sql/relational-databases/collations/set-or-change-the-database-collation) [dados, bases de dados contidas,](/sql/relational-databases/databases/contained-databases) [utilizadores contidos,](/sql/relational-databases/security/contained-database-users-making-your-database-portable) [compressão de](/sql/relational-databases/data-compression/data-compression) [dados, configurações de configuração de bases de dados,](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) [operações de índice online,](/sql/relational-databases/indexes/perform-index-operations-online) [partição](/sql/relational-databases/partitions/partitioned-tables-and-indexes)e [tabelas temporais](/sql/relational-databases/tables/temporal-tables) [(ver guia de arranque).](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)
- Funcionalidades de segurança - [Funções de aplicação,](/sql/relational-databases/security/authentication-access/application-roles) [mascaramento dinâmico de dados](/sql/relational-databases/security/dynamic-data-masking) [(Começar com sql Database máscara de dados dinâmicos com o portal Azure](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), [Linha Level Security](/sql/relational-databases/security/row-level-security)
- Capacidades multi-modelos - Processamento de [gráficos, dados JSON,](/sql/relational-databases/json/json-data-sql-server) [OPENXML,](/sql/t-sql/functions/openxml-transact-sql) [Espacial,](/sql/relational-databases/spatial/spatial-data-sql-server) [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)e [índices XML](/sql/t-sql/statements/create-xml-index-transact-sql). [](/sql/relational-databases/graphs/sql-graph-overview)



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Características do Arco Azure ativadas SQL Gestão De Instância

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> Alta Disponibilidade RDBMS  
  
|Funcionalidade|SQL Managed Instance preparado para o Azure Arc|
|-------------|----------------|
|Envio de registos|Yes| 
|Compressão de backup|Yes|
|Instantâneo da base de dados|Yes|
|Sempre na instância de cluster de failover<sup>1</sup>| Não aplicável. Capacidades semelhantes disponíveis |
|Sempre em grupos de disponibilidade<sup>2</sup>|As capacidades ha estão planeadas.|
|Grupos básicos de disponibilidade <sup>2</sup>|As capacidades ha estão planeadas.|
|Réplica mínima compromete disponibilidade grupo <sup>2</sup>|As capacidades ha estão planeadas.|
|Grupo de disponibilidade sem clusters|Yes|
|Página online e restauro de ficheiros|Yes|
|Indexação online|Yes|
|Resumível índice online reconstrói|Yes|
|Mudança de esquema on-line|Yes|
|Recuperação rápida|Yes|
|Backups espelhados|Yes|
|Hot add memory e CPU|Yes|
|Cópia de segurança encriptada|Yes|
|Backup híbrido para Azure (backup para URL)|Yes|

<sup>1</sup> No cenário em que há falha no pod, uma nova SQL Managed Instance iniciar-se-á e re-anexará ao volume persistente que contém os seus dados. [Saiba mais sobre os volumes persistentes da Kubernetes aqui.](https://kubernetes.io/docs/concepts/storage/persistent-volumes)

<sup>2</sup> Futuras versões fornecerão capacidades AG 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> Escalabilidade e Desempenho RDBMS  

| Funcionalidade | SQL Managed Instance preparado para o Azure Arc |
|--|--|
| Loja de Colunas | Yes |
| Binários de objetos grandes em índices de loja de colunas agrupados | Yes |
| Reconstrução do índice de colunas não-aglomerada online | Yes |
| OLTP na Memória | Yes |
| Memória Principal Persistente | Yes |
| Divisórias de tabela e índice | Yes |
| Compressão de dados | Yes |
| Resource Governor | Yes |
| Paralelos de mesa dividida | Yes |
| Uma memória de página e alocação de matriz de página grande e grande página | Yes |
| IO Governação de Recursos | Yes |
| Durabilidade atrasada | Yes |
| Otimização Automática | Yes |
| Conjuntos adaptáveis do modo de lote | Yes |
| Feedback do subsídio de memória do modo de lote | Yes |
| Execução Intercalada para Funções Avaliadas em Tabela Multi-Declaração | Yes |
| Melhorias de inserção a granel | Yes |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> Segurança RDBMS

| Funcionalidade | SQL Managed Instance preparado para o Azure Arc |
|--|--|
| Segurança ao nível da linha | Yes |
| Sempre Encriptado | Yes |
| Always Encrypted com Enclaves Protegidos | No |
| Máscara de dados dinâmica | Yes |
| Auditoria básica | Yes |
| Auditoria de grãos finos | Yes |
| Encriptação transparente da base de dados | Yes |
| Funções definidas pelo utilizador | Yes |
| Bases de dados contidas | Yes |
| Encriptação para cópias de segurança | Yes |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> Gerenciabilidade de RDBMS  

| Funcionalidade | SQL Managed Instance preparado para o Azure Arc |
|--|--|
| Conexão de administração dedicada | Yes |
| Suporte de script powerShell | Yes |
| Suporte para operações de componentes de aplicação de nível de dados - extrair, implantar, atualizar, eliminar | Yes |
| Automação de políticas (verificar o horário e a mudança) | Yes |
| Coletor de dados de desempenho | Yes |
| Relatórios de desempenho padrão | Yes |
| Planeie guias e planeie congelamento para guias de planos | Yes |
| Consulta direta de pontos de vista indexados (usando sugestão NOEXPAND) | Yes |
| Manutenção automática de vistas indexadas | Yes |
| Vistas distribuídas por divisórias | Yes |
| Operações indexadas paralelas | Yes |
| Utilização automática da vista indexada por otimizador de consulta | Yes |
| Verificação paralela de consistência | Yes |

### <a name="programmability"></a><a name="Programmability"></a> Programabilidade  

| Funcionalidade | SQL Managed Instance preparado para o Azure Arc |
|--|--|
| JSON | Yes |
| Arquivo de Consultas | Yes |  |
| Temporal | Yes |  |
| Suporte nativo XML | Yes |  |
| Indexação XML | Yes |  |
| & capacidades da UPSERT | Yes |  |
| Data e hora de dados | Yes |  |
| Apoio à internacionalização | Yes |  |
| Pesquisa completa e semântica | No |
| Especificação da linguagem em consulta | Yes |  |
| Corretor de Serviços (mensagens) | Yes |  |
| Pontos finais Transact-SQL | Yes |  |
| Graph | Yes |  |
| Machine Learning Services | No |  |
| PolyBase | No |


### <a name="tools"></a>Ferramentas

O Azure Arc ativou o SQL Managed Instance suportando várias ferramentas de dados que podem ajudá-lo a gerir os seus dados.

| **Ferramenta** | SQL Managed Instance preparado para o Azure Arc|
| --- | --- | --- |
| Portal Azure <sup>1</sup> | No |
| CLI do Azure | No |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Yes |
| Azure PowerShell | Yes |
| [Ficheiro BACPAC (exportação)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Yes |
| [Ficheiro BACPAC (importação)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Yes |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Yes |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Yes |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Yes |

<sup>1</sup> O portal Azure é utilizado apenas para visualizar Azure Arc ativado SQL Managed Instances no modo apenas de leitura durante a pré-visualização.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Funcionalidades não suportadas & Serviços

As seguintes funcionalidades e serviços não estão disponíveis para Azure Arc ativado SQL Managed Instance. O suporte destas funcionalidades será cada vez mais ativado ao longo do tempo.

| Área | Funcionalidade ou serviço não suportado |
|-----|-----|
| **Motor da Base de Dados** | Fusão de replicação |
| &nbsp; | Alongamento DB |
| &nbsp; | Consulta distribuída com ligações de terceiros |
| &nbsp; | Servidores ligados a fontes de dados que não os produtos SQL Server e Azure SQL |
| &nbsp; | Sistema alargado procedimentos armazenados (XP_CMDSHELL, etc.) |
| &nbsp; | FileTable, FILESTREAM |
| &nbsp; | Conjuntos CLR com o conjunto de permissões EXTERNAL_ACCESS ou INSEGURO |
| &nbsp; | Extensão da piscina tampão |
| **Agente do SQL Server** |  Subsistemas: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Alertas |
| &nbsp; | Cópia de Segurança Gerida |
| **Alta Disponibilidade** | Espelhamento da base de dados  |
| **Segurança** | Gestão de Chaves Extensíveis |
| &nbsp; | Autenticação AD para Servidores Ligados | 
| &nbsp; | Autenticação AD para Grupos de Disponibilidade (AGs) | 