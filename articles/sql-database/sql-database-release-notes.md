---
title: Release Notes (Notas de Lançamento)
description: Conheça as novas funcionalidades e melhorias no serviço de base de dados Azure SQL e na documentação da Base de Dados Azure SQL
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: sstein
ms.openlocfilehash: 3e5069c779cee0700bff6b2236f3cd36547fd623
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659614"
---
# <a name="sql-database-release-notes"></a>Notas de lançamento da Base de Dados SQL

Este artigo lista as funcionalidades da Base de Dados SQL que estão atualmente em pré-visualização pública. Para atualizações e melhorias da Base de Dados SQL, consulte [as atualizações do serviço SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Para atualizações e melhorias a outros serviços do Azure, consulte [as atualizações do Serviço.](https://azure.microsoft.com/updates)

## <a name="features-in-public-preview"></a>Características na pré-visualização pública

### <a name="single-database"></a>[Base de dados única](#tab/single-database)

| Funcionalidade | Detalhes |
| ---| --- |
| Novas gerações de hardware da série Fsv2 e da série M| Para obter informações, consulte gerações de [Hardware](sql-database-service-tiers-vcore.md#hardware-generations).|
| Recuperação acelerada da base de dados com bases de dados únicas e piscinas elásticas | Para obter informações, consulte [A Recuperação acelerada da Base de Dados](sql-database-accelerated-database-recovery.md).|
|Contagem aproximada distinta|Para obter informações, consulte [Aproximada count distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modo de lote na Rowstore (sob o nível de compatibilidade 150)|Para obter informações, consulte [o Modo Lote na Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Deteção e classificação de dados  |Para obter informações, consulte a Base de Dados Azure SQL e a Descoberta de dados do [Armazém de Dados SQL & classificação](sql-database-data-discovery-and-classification.md).|
| Tarefas de base de dados elástica | Para obter informações, consulte [Criar, configurar e gerir trabalhos elásticos.](elastic-jobs-overview.md) |
| Consultas elásticas | Para obter informações, consulte a visão geral da [consulta elástica](sql-database-elastic-query-overview.md). |
| Transações elásticas | [Transações distribuídas através](sql-database-elastic-transactions-overview.md)de bases de dados em nuvem . |
|Feedback da concessão da memória (modo de linha) (sob o nível de compatibilidade 150)|Para obter informações, consulte [Memory Grant Feedback (Modo de Linha)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor de consulta no portal Azure |Para obter informações, consulte [Use o editor de consulta SQL do portal Azure para ligar e consultar dados](sql-database-connect-query-portal.md).|
| Serviços R / machine learning com bases de dados únicas e piscinas elásticas |Para obter informações, consulte [Serviços de Aprendizagem Automática na Base de Dados Azure SQL](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Análise de SQL|Para obter informações, consulte [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Compilação Diferida Variável de Tabela (sob o nível de compatibilidade 150)|Para obter informações, consulte [A Compilação Diferida variável da tabela](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Instância gerida](#tab/managed-instance)

| Funcionalidade | Detalhes |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Conjuntos de instâncias</a> | Uma forma conveniente e rentável de migrar casos SQL mais pequenos para a nuvem. |
| <a href="https://aka.ms/managed-instance-aadlogins">Diretores de servidores AD de nível de instância (logins)</a> | Crie logins ao nível do servidor utilizando a <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">declaração create LOGIN from EXTERNAL PROVIDER.</a> |
| [Replicação transacional](sql-database-managed-instance-transactional-replication.md) | Replicar as alterações das suas tabelas para outras bases de dados colocadas em instâncias geridas, bases de dados únicas ou casos do Servidor SQL, ou atualizar as suas tabelas quando algumas linhas são alteradas em outras instâncias geridas ou instância do Servidor SQL. Para obter informações, consulte a [replicação da Configuração numa](replication-with-sql-database-managed-instance.md)base de dados de instância gerida por 1den . |
| Deteção de ameaças |Para obter informações, consulte a deteção de [ameaças configure na base de dados Azure SQL gerida](sql-database-managed-instance-threat-detection.md).|
| Retenção de cópia de segurança de longa duração | Para obter informações, consulte a configuração de retenção de reserva a [longo prazo na base de dados Azure SQL gerida](sql-database-managed-instance-long-term-backup-retention-configure.md), que está atualmente em pré-visualização pública limitada. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Instância gerida - novas funcionalidades e questões conhecidas

### <a name="managed-instance-h2-2019-updates"></a>Atualizações de instância gerida H2 2019

- [Configuração da sub-rede ajudada ao serviço](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Uma forma segura e conveniente de gerir a configuração da sub-rede onde controla o tráfego de dados enquanto a instância gerida garante o fluxo ininterrupto de tráfego de gestão
- [A encriptação transparente de dados (TDE) com bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) permite o cenário de trazer a sua própria chave (BYOK) para proteção de dados em repouso e permite que as organizações separem os deveres de gestão de chaves e dados.
- [Os grupos de falha automática](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) permitem-lhe replicar todas as bases de dados desde a instância primária até uma instância secundária noutra região.
- Configure o seu comportamento de instância gerida com [bandeiras de traçoglobal](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Atualizações de instância gerida H1 2019

As seguintes funcionalidades estão ativadas no modelo de implementação de instâncias geridas em H1 2019:
  - Suporte para subscrições com <a href="https://aka.ms/sql-mi-visual-studio-subscribers">crédito mensal Azure para assinantes do Estúdio Visual</a> e [limites regionais aumentados.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
  - Suporte para <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Crie casos com <a href="https://aka.ms/managed-instance-collation">colagem ao nível do servidor</a> e <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso horário</a> à sua escolha.
  - As instâncias geridas estão agora protegidas com <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall incorporado.</a>
  - Configure casos para utilizar [pontos finais públicos](sql-database-managed-instance-public-endpoint-configure.md), [Proxy sobrepor](sql-database-connectivity-architecture.md#connection-policy) a ligação para obter um melhor desempenho de rede, <a href="https://aka.ms/four-cores-sql-mi-update">4 vCores na geração de hardware Gen5</a> ou configurar a retenção de <a href="https://aka.ms/managed-instance-configurable-backup-retention">backup até 35 dias</a> para restaurar o tempo ponto-a-tempo. [A retenção de backup a longo prazo](sql-database-long-term-retention.md#managed-instance-support) (até 10 anos) está atualmente em pré-visualização pública limitada.  
  - As novas funcionalidades permitem-lhe restaurar a sua base de dados para outro centro de <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">dados utilizando powerShell,</a>base de [dados de renome,](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) [eliminar cluster virtual](sql-database-managed-instance-delete-virtual-cluster.md).
  - O novo [papel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) integrado do contribuinte permite a separação do dever (SoD) o cumprimento dos princípios de segurança e o cumprimento das normas empresariais.
  - A instância gerida está disponível nas seguintes regiões do Governo azure para ga (EUA Gov Texas, EUA Gov Arizona) bem como na China North 2 e China East 2. Também está disponível nas seguintes regiões públicas: Australia Central, Australia Central 2, Brasil Sul, França Sul, Eau Central, Emirados Emirados Unidos Norte, África do Sul Norte, África do Sul, África do Sul Oeste.

### <a name="known-issues"></a>Problemas conhecidos

|Problema  |Data descoberta  |Estado  |Data resolvida  |
|---------|---------|---------|---------|
|[Restaurar a cópia de segurança manual sem a CHECKSUM pode falhar](#restoring-manual-backup-without-checksum-might-fail)|Maio de 2020|Tem Sem-teto| |
|[Agente torna-se insensível ao modificar, desativar ou permitir empregos existentes](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maio de 2020|Atenuado automaticamente| |
|[Permissões em grupo de recursos não aplicadas à Instância Gerida](#permissions-on-resource-group-not-applied-to-managed-instance)|Fev 2020|Tem Sem-teto| |
|[Limitação da falha manual via portal para grupos de failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Tem Sem-teto| |
|[As funções do Agente SQL precisam de permissões de EXECUÇÃ explícitas para logins não-sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dez 2019|Tem Sem-teto| |
|[Os trabalhos do Agente SQL podem ser interrompidos pelo reinício do processo do Agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dez 2019|Resolvido|Mar 2020|
|[Os logins e utilizadores da AAD não são suportados no SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Sem Sem Suver| |
|[Não são aplicados limites de memória OLTP na memória](#in-memory-oltp-memory-limits-are-not-applied)|Out 2019|Tem Sem-teto| |
|[Erro errado devolvido ao tentar remover um ficheiro que não está vazio](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Out 2019|Tem Sem-teto| |
|[Alterar nível de serviço e criar operações de instância são bloqueadas por restauro de base de dados em curso](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Set 2019|Tem Sem-teto| |
|[O Governador de Recursos no nível de serviço Business Critical pode ter de ser reconfigurado após o fracasso](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Set 2019|Tem Sem-teto| |
|[Os diálogos do Corretor de Serviços cross-database devem ser reinicializados após a atualização do nível de serviço](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Ago 2019|Tem Sem-teto| |
|[A ipersonificação dos tipos de login Azure AD não é suportada](#impersonification-of-azure-ad-login-types-is-not-supported)|Jul 2019|Sem Sem Suver| |
|[@queryparâmetro não suportado em sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Abr 2019|Sem Sem Suver| |
|[A replicação transacional deve ser reconfigurada após a geofalha](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Sem Sem Suver| |
|[Base de dados temporária é utilizada durante o funcionamento do RESTAURO](#temporary-database-is-used-during-restore-operation)||Tem Sem-teto| |
|[Estrutura e conteúdo TEMPDB é recriado](#tempdb-structure-and-content-is-re-created)| |Sem Sem Suver| |
|[Exceder espaço de armazenamento com pequenos ficheiros de base de dados](#exceeding-storage-space-with-small-database-files)| |Tem Sem-teto| |
|[Valores GUIA mostrados em vez de nomes de bases de dados](#guid-values-shown-instead-of-database-names) ||Tem Sem-teto| |
|[Os registos de erro não são persistidos](#error-logs-arent-persisted)||Sem Sem Suver| |
|[Módulos CLR e servidores ligados às vezes não conseguem fazer referência a um endereço IP local](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)| |Tem Sem-teto| |
|[O âmbito de transação em duas bases de dados dentro da mesma instância não é suportado](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)| |Resolvido|Mar 2020|
|Consistência da base de dados não verificada utilizando o DBCC CHECKDB após restaurar a base de dados do Armazenamento De Blob Azure.| |Resolvido|Nov 2019|
|A restauração da base de dados ponto-a-tempo do nível Business Critical para o nível de Propósito Geral não terá sucesso se a base de dados de origem contiver objetos OLTP na memória.| |Resolvido|Out 2019|
|Recurso de correio de base de dados com servidores de correio externos (não-Azure) utilizando ligação segura| |Resolvido|Out 2019|
|Bases de dados contidas não suportadas em instância gerida| |Resolvido|Ago 2019|

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Restaurar a cópia de segurança manual sem a CHECKSUM pode falhar

Em certas circunstâncias, a cópia manual de bases de dados que foi feita em instância gerida sem a CHECKSUM pode não ser restaurada. Neste caso, tente restaurar o backup até ser bem sucedido.

**Seleção**: Pegue cópias de segurança manuais de bases de dados em caso de gestão com a CHECKUM ativada.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agente torna-se insensível ao modificar, desativar ou permitir empregos existentes

Em certas circunstâncias, modificar um trabalho existente, desativar ou permitir que possa fazer com que o agente fique sem resposta. O problema é automaticamente atenuado após a deteção, resultando no reinício do processo do agente.

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Permissões no grupo de recursos não aplicadas a instância gerida

A função RBAC do colaborador de instância gerida quando aplicada a um grupo de recursos (RG) não é aplicada à Instância Gerida e não tem qualquer efeito.

**Supor :** Configurar função de Colaborador de Instância Gerida para utilizadores ao nível da subscrição.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitação da falha manual via portal para grupos de failover

Se o grupo failover se estende por instâncias em diferentes subscrições do Azure ou grupos de recursos, a falha manual não pode ser iniciada a partir da instância primária no grupo failover.

**Suposição :** Iniciar a falha através do portal a partir da instância geo-secundária.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>As funções do Agente SQL precisam de permissões de EXECUÇÃ explícitas para logins não-sysadmin

Se os logins não sysadmin a qualquer uma das funções de base de dados fixas do [Agente SQL,](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)existe uma questão em que devem ser concedidas permissões explícitas de EXECUTa aos procedimentos master stored para que estes logins funcionem. Se este problema for encontrado, será mostrada a mensagem de erro "A permissão EXECUTE foi negada no objeto <object_name> (Microsoft SQL Server, Error: 229)".

**Suposição de suposição**: Uma vez adicionados logins a qualquer uma das funções de base de dados fixas do Agente SQL: SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole, para cada um dos logins adicionados a estas funções executa o script T-SQL abaixo para conceder explicitamente permissões EXECUTE aos procedimentos armazenados listados.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Os trabalhos do Agente SQL podem ser interrompidos pelo reinício do processo do Agente

**(Resolvido em março de 2020)** O Agente SQL cria uma nova sessão sempre que o trabalho é iniciado, aumentando gradualmente o consumo de memória. Para evitar atingir o limite interno de memória que bloquearia a execução de trabalhos programados, o processo de agente será reiniciado assim que o seu consumo de memória atingir o limiar. Pode resultar na interrupção da execução de postos de trabalho em funcionamento no momento do recomeço.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Não são aplicados limites de memória OLTP na memória

O nível de serviço Business Critical não aplicará corretamente [limites máximos de memória para objetos otimizados](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) pela memória em alguns casos. A instância gerida pode permitir que a carga de trabalho utilize mais memória para operações OLTP em memória, o que pode afetar a disponibilidade e a estabilidade da ocorrência. As consultas oLTP na memória que estão a atingir os limites podem não falhar imediatamente. Esta questão será resolvida em breve. As consultas que usam mais memória OLTP de memória falharão mais cedo se atingirem os [limites](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Suposições:** [Monitorize o uso de armazenamento OLTP na memória](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) utilizando o Estúdio de Gestão de [Servidores SQL](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) para garantir que a carga de trabalho não está a utilizar mais do que a memória disponível. Aumente os limites de memória que dependem do número de vCores, ou otimize a sua carga de trabalho para usar menos memória.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erro errado devolvido ao tentar remover um ficheiro que não está vazio

SQL Server/Managed Instance [não permite que o utilizador deixe cair um ficheiro que não está vazio](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se tentar remover um ficheiro de dados não vazio utilizando `ALTER DATABASE REMOVE FILE` a declaração, o erro `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` não será devolvido imediatamente. A Instância Gerida continuará a tentar retirar o ficheiro e a operação falhará após 30min com `Internal server error` .

**Seleção**: Retire o conteúdo do ficheiro utilizando `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` o comando. Se este for o único ficheiro no grupo de ficheiros, terá de eliminar os dados da tabela ou partição associada a este grupo de ficheiros antes de encolher o ficheiro e, opcionalmente, carregar esses dados noutra tabela/partição.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Alterar nível de serviço e criar operações de instância são bloqueadas por restauro de base de dados em curso

A declaração em curso, o processo de migração do Serviço de Migração de Dados e a restauração do tempo incorporado bloquearão a atualização do nível de `RESTORE` serviço ou a redimensionação da instância existente e criarão novas instâncias até que o processo de restabelecimento termine. O processo de restauro bloqueará estas operações nas instâncias geridas e piscinas de exemplo na mesma subnet onde o processo de restauro está em execução. Os casos, por exemplo, as piscinas não são afetados. Criar ou alterar as operações de nível de serviço não falhará ou o tempo de tempo - procederão assim que o processo de restauro estiver concluído ou cancelado.

**Supor:** Aguarde até que o processo de restauro termine ou cancele o processo de restauro se a criação ou atualização da operação de nível de serviço tiver maior prioridade.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>O Governador de Recursos no nível de serviço Business Critical pode ter de ser reconfigurado após o fracasso

[A](/sql/relational-databases/resource-governor/resource-governor) funcionalidade Do Governador de Recursos que lhe permite limitar os recursos atribuídos à carga horária do utilizador pode classificar incorretamente alguma carga de trabalho do utilizador após a falha ou alteração do nível de serviço iniciado pelo utilizador (por exemplo, a alteração do tamanho máximo vCore ou do tamanho de armazenamento de instâncias max).

**Supor :** Execute `ALTER RESOURCE GOVERNOR RECONFIGURE` periodicamente ou como parte do Trabalho do Agente SQL que executa a tarefa SQL quando a instância começa se estiver a utilizar [o Governor de Recursos](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Os diálogos do Corretor de Serviços cross-database devem ser reinicializados após a atualização do nível de serviço

Os diálogos do Corretor de Serviços cross-database deixarão de entregar as mensagens aos serviços noutras bases de dados após a alteração da operação de nível de serviço. As mensagens não se **perdem** e podem ser encontradas na fila do remetente. Qualquer alteração do vCores ou tamanho de armazenamento de instâncias em Instância Gerida, fará com que o valor na visão de bases de `service_broke_guid` [dados sys.bases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) de dados seja alterado para todas as bases de dados. Qualquer criado usando a declaração DO `DIALOG` [DIALOG BEGIN](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) que referencia corretores de serviços em outra base de dados deixará de entregar mensagens ao serviço alvo.

**Supor:** Pare qualquer atividade que utilize conversas de diálogo de serviço seleção de serviços antes de atualizar o nível de serviço e reinicializá-las depois. Se restam mensagens que não sejam entregues após a alteração do nível de serviço, leia as mensagens da fila de origem e reenvie-as para a fila do alvo.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>A ipersonificação dos tipos de login Azure AD não é suportada

Não é suportada a utilização `EXECUTE AS USER` ou `EXECUTE AS LOGIN` dos seguintes diretores da AAD:
-    Utilizadores de AAD aliased. Neste caso, devolve-se o seguinte `15517` erro.
- Logins aAD e utilizadores com base em aplicações aAD ou diretores de serviço. Neste caso, são devolvidos os seguintes erros `15517` e `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparâmetro não suportado em sp_send_db_mail

O `@query` parâmetro no procedimento [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) não funciona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A replicação transacional deve ser reconfigurada após a geofalha

Se a Replicação Transacional estiver ativada numa base de dados num grupo de falhas automáticas, o administrador de instância gerido deve limpar todas as publicações sobre as primárias antigas e reconfigurá-las nas novas primárias após uma falha em outra região. Consulte a [Replicação](sql-database-managed-instance-transact-sql-information.md#replication) para mais detalhes.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Os logins e utilizadores da AAD não são suportados no SSDT

As Ferramentas de Dados do Servidor SQL não suportam totalmente os logins e utilizadores do diretório Azure Ative.

### <a name="temporary-database-is-used-during-restore-operation"></a>Base de dados temporária é utilizada durante o funcionamento do RESTAURO

Quando uma base de dados está a restaurar em Caso Gerido, o serviço de restauro criará primeiro uma base de dados vazia com o nome pretendido para atribuir o nome na instância. Após algum tempo, esta base de dados será retirada e a restauração da base de dados real será iniciada. A base de dados que está em *estado de Restauração* terá um valor GUID aleatório em vez de nome. O nome temporário será alterado para o nome desejado especificado em comunicado assim que o processo de `RESTORE` restauro estiver concluído. Na fase inicial, o utilizador pode aceder à base de dados vazia e até criar tabelas ou carregar dados nesta base de dados. Esta base de dados temporária será retirada quando o serviço de restauro começar a segunda fase.

**Seleção**: Não aceda à base de dados que está a restaurar até ver que a restauração está concluída.

### <a name="tempdb-structure-and-content-is-re-created"></a>Estrutura e conteúdo TEMPDB é recriado

A `tempdb` base de dados é sempre dividida em 12 ficheiros de dados e a estrutura do ficheiro não pode ser alterada. O tamanho máximo por ficheiro não pode ser alterado e não é possível adicionar novos ficheiros `tempdb` a . `Tempdb`é sempre recriada como base de dados vazia quando a instância começa ou falha, e quaisquer alterações introduzidas `tempdb` não serão preservadas.

### <a name="exceeding-storage-space-with-small-database-files"></a>Exceder espaço de armazenamento com pequenos ficheiros de base de dados

`CREATE DATABASE`, `ALTER DATABASE ADD FILE` e as declarações podem falhar porque a instância pode atingir o limite de armazenamento `RESTORE DATABASE` azure.

Cada instância gerida pelo General Purpose tem até 35 TB de armazenamento reservado para o espaço Disco Premium Azure. Cada ficheiro de base de dados é colocado num disco físico separado. Os tamanhos do disco podem ser de 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não utilizado no disco não é carregado, mas a soma total dos tamanhos do Disco Premium Azure não pode exceder 35 TB. Em alguns casos, um caso gerido que não precisa de 8 TB no total pode exceder o limite de 35 TB Azure no tamanho do armazenamento devido à fragmentação interna.

Por exemplo, uma instância gerida pelo General Purpose pode ter um ficheiro grande com 1,2 TB de tamanho colocado num disco de 4 TB. Também pode ter 248 ficheiros com tamanho de 1 GB cada um que são colocados em discos separados de 128 GB. Neste exemplo:

- O tamanho total de armazenamento de disco atribuído é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O espaço total reservado para bases de dados na instância é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Este exemplo ilustra que, em certas circunstâncias, devido a uma distribuição específica de ficheiros, uma instância gerida pode atingir o limite de 35 TB reservado para um Disco Premium Azure anexo quando não se pode esperar.

Neste exemplo, as bases de dados existentes continuam a funcionar e podem crescer sem qualquer problema, desde que não sejam adicionados novos ficheiros. Novas bases de dados não podem ser criadas ou restauradas porque não há espaço suficiente para novas unidades de disco, mesmo que o tamanho total de todas as bases de dados não atinja o limite de tamanho da instância. O erro que foi devolvido neste caso não é claro.

Pode [identificar o número de ficheiros restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) utilizando as vistas do sistema. Se atingir este limite, tente [esvaziar e apagar alguns dos ficheiros mais pequenos utilizando a declaração do DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou mude para o [nível Business Critical, que não tem este limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valores GUIA mostrados em vez de nomes de bases de dados

Várias visualizações do sistema, contadores de desempenho, mensagens de erro, XEvents e entradas de registo de erros apresentam identificadores de base de dados GUID em vez dos nomes reais da base de dados. Não confie nestes identificadores GUID porque são substituídos por nomes reais de bases de dados no futuro.

**Solução:** Utilize a visão sys.databases para resolver o nome real da base de dados a partir do nome da base de dados física, especificado sob a forma de identificadores de base de dados GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Os registos de erro não são persistidos

Os registos de erro disponíveis em instância gerida não são persistentes, e o seu tamanho não está incluído no limite máximo de armazenamento. Os registos de erros podem ser apagados automaticamente se ocorrer em falha. Pode haver lacunas no histórico de registo de erros porque a Managed Instance foi movida várias vezes em várias máquinas virtuais.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O âmbito de transação em duas bases de dados dentro da mesma instância não é suportado

**(Resolvido em março de 2020)** A `TransactionScope` classe em .NET não funciona se duas consultas forem enviadas para duas bases de dados dentro do mesmo âmbito de transação:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Supor (não necessário desde março de 2020):** Utilize [sqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para utilizar outra base de dados num contexto de ligação em vez de utilizar duas ligações.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Módulos CLR e servidores ligados às vezes não conseguem fazer referência a um endereço IP local

Os módulos CLR colocados numa instância gerida e servidores ligados ou consultas distribuídas que referenciam uma instância atual às vezes não conseguem resolver o IP de uma instância local. Este erro é uma questão transitória.

**Supor:** Utilize ligações de contexto num módulo CLR, se possível.

## <a name="updates"></a>Atualizações

Para obter uma lista de atualizações e melhorias da Base de Dados SQL, consulte [as atualizações do serviço SQL Database](https://azure.microsoft.com/updates/?product=sql-database).

Para atualizações e melhorias em todos os serviços do Azure, consulte [as atualizações do Serviço.](https://azure.microsoft.com/updates)

## <a name="contribute-to-content"></a>Contribuir para o conteúdo

Para contribuir para a documentação da Base de Dados Azure SQL, consulte o Guia de [Contribuinte do Docs](https://docs.microsoft.com/contribute/).
