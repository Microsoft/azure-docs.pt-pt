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
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
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
|Envio de registos|Sim| 
|Compressão de backup|Sim|
|Instantâneo da base de dados|Sim|
|Sempre na instância de cluster de failover<sup>1</sup>| Não aplicável. Capacidades semelhantes disponíveis |
|Sempre em grupos de disponibilidade<sup>2</sup>|As capacidades ha estão planeadas.|
|Grupos básicos de disponibilidade <sup>2</sup>|As capacidades ha estão planeadas.|
|Réplica mínima compromete disponibilidade grupo <sup>2</sup>|As capacidades ha estão planeadas.|
|Grupo de disponibilidade sem clusters|Sim|
|Página online e restauro de ficheiros|Sim|
|Indexação online|Sim|
|Resumível índice online reconstrói|Sim|
|Mudança de esquema on-line|Sim|
|Recuperação rápida|Sim|
|Backups espelhados|Sim|
|Hot add memory e CPU|Sim|
|Cópia de segurança encriptada|Sim|
|Backup híbrido para Azure (backup para URL)|Sim|

<sup>1</sup> No cenário em que há falha no pod, uma nova SQL Managed Instance iniciar-se-á e re-anexará ao volume persistente que contém os seus dados. [Saiba mais sobre os volumes persistentes da Kubernetes aqui.](https://kubernetes.io/docs/concepts/storage/persistent-volumes)

<sup>2</sup> Futuras versões fornecerão capacidades AG 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> Escalabilidade e Desempenho RDBMS  

| Funcionalidade | SQL Managed Instance preparado para o Azure Arc |
|--|--|
| Loja de Colunas | Sim |
| Binários de objetos grandes em índices de loja de colunas agrupados | Sim |
| Reconstrução do índice de colunas não-aglomerada online | Sim |
| OLTP na Memória | Sim |
| Memória Principal Persistente | Sim |
| Divisórias de tabela e índice | Sim |
| Compressão de dados | Sim |
| Resource Governor | Sim |
| Paralelos de mesa dividida | Sim |
| Uma memória de página e alocação de matriz de página grande e grande página | Sim |
| IO Governação de Recursos | Sim |
| Durabilidade atrasada | Sim |
| Otimização Automática | Sim |
| Conjuntos adaptáveis do modo de lote | Sim |
| Feedback do subsídio de memória do modo de lote | Sim |
| Execução Intercalada para Funções Avaliadas em Tabela Multi-Declaração | Sim |
| Melhorias de inserção a granel | Sim |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> Segurança RDBMS

| Funcionalidade | SQL Managed Instance preparado para o Azure Arc |
|--|--|
| Row-level security | Sim |
| Sempre Encriptado | Sim |
| Always Encrypted com Enclaves Protegidos | Não |
| Máscara de dados dinâmica | Sim |
| Auditoria básica | Sim |
| Auditoria de grãos finos | Sim |
| Encriptação transparente da base de dados | Sim |
| Funções definidas pelo utilizador | Sim |
| Bases de dados contidas | Sim |
| Encriptação para cópias de segurança | Sim |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> Gerenciabilidade de RDBMS  

| Funcionalidade | SQL Managed Instance preparado para o Azure Arc |
|--|--|
| Conexão de administração dedicada | Sim |
| Suporte de script powerShell | Sim |
| Suporte para operações de componentes de aplicação de nível de dados - extrair, implantar, atualizar, eliminar | Sim |
| Automação de políticas (verificar o horário e a mudança) | Sim |
| Coletor de dados de desempenho | Sim |
| Relatórios de desempenho padrão | Sim |
| Planeie guias e planeie congelamento para guias de planos | Sim |
| Consulta direta de pontos de vista indexados (usando sugestão NOEXPAND) | Sim |
| Manutenção automática de vistas indexadas | Sim |
| Vistas distribuídas por divisórias | Sim |
| Operações indexadas paralelas | Sim |
| Utilização automática da vista indexada por otimizador de consulta | Sim |
| Verificação paralela de consistência | Sim |

### <a name="programmability"></a><a name="Programmability"></a> Programabilidade  

| Funcionalidade | SQL Managed Instance preparado para o Azure Arc |
|--|--|
| JSON | Sim |
| Arquivo de Consultas | Sim |  |
| Temporal | Sim |  |
| Suporte nativo XML | Sim |  |
| Indexação XML | Sim |  |
| & capacidades da UPSERT | Sim |  |
| Data e hora de dados | Sim |  |
| Apoio à internacionalização | Sim |  |
| Pesquisa completa e semântica | Não |
| Especificação da linguagem em consulta | Sim |  |
| Corretor de Serviços (mensagens) | Sim |  |
| Pontos finais Transact-SQL | Sim |  |
| Graph | Sim |  |
| Machine Learning Services | Não |  |
| PolyBase | Não |


### <a name="tools"></a>Ferramentas

O Azure Arc ativou o SQL Managed Instance suportando várias ferramentas de dados que podem ajudá-lo a gerir os seus dados.

| **Ferramenta** | SQL Managed Instance preparado para o Azure Arc|
| --- | --- | --- |
| Portal Azure <sup>1</sup> | Não |
| CLI do Azure | Não |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Sim |
| Azure PowerShell | Sim |
| [Ficheiro BACPAC (exportação)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sim |
| [Ficheiro BACPAC (importação)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sim |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Sim |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Sim |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Sim |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Sim |

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