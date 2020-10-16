---
title: Novidades
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Conheça as novas funcionalidades e melhorias de documentação para a Azure SQL Database & SQL Managed Instance.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: sstein
ms.openlocfilehash: 027a816e846996aa7c61a1747327128f9a0feed0
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079212"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Quais as novidades na Base de Dados Azure SQL & SQL Managed Instance?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo lista as funcionalidades Azure SQL Database e Azure SQL Managed Instance que estão atualmente em pré-visualização pública. Para atualizações e melhorias de exemplos de bases de dados sql e sql geridos, consulte a [Base de Dados SQL & atualizações do serviço sql Managed Instance](https://azure.microsoft.com/updates/?product=sql-database). Para atualizações e melhorias a outros serviços Azure, consulte [as atualizações do Serviço](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Novidades

A documentação da Azure SQL Database e da Azure SQL Managed Instance foi dividida em secções separadas. Também atualizámos a forma como nos referimos a um caso gerido da *Azure SQL Database gerido instância* para *Azure SQL Managed Instance*.

Fizemos isto porque algumas funcionalidades e funcionalidades variam muito entre uma única base de dados e uma instância gerida, e tornou-se cada vez mais desafiante explicar nuances complexas entre a Base de Dados Azure SQL e a Azure SQL Managed Instance em artigos partilhados individuais.

Este esclarecimento entre os diferentes produtos Azure SQL deve simplificar e simplificar o processo de trabalho com o motor de base de dados SQL Server em Azure, quer se trate de uma única base de dados gerida na Base de Dados Azure SQL, um caso gerido totalmente fledged que hospeda várias bases de dados em Azure SQL Managed Instance, ou o familiar produto sql Server alojado numa máquina virtual em Azure.

Considere-se que se trata de um trabalho em curso e que nem todos os artigos foram atualizados. Por exemplo, a documentação para declarações de Transact-SQL (T-SQL), procedimentos armazenados e muitas funcionalidades partilhadas entre a Azure SQL Database e a Azure SQL Managed Instance ainda não estão completas, por isso agradecemos a sua paciência enquanto continuamos a clarificar o conteúdo. 

Esta tabela proporciona uma comparação rápida para a mudança na terminologia: 


|**Novo termo**  | **Prazo anterior**  |**Explicação** |
|---------|---------|---------|
|**Instância Gerida do SQL no Azure** | Azure SQL Database *gerido instância*| Azure SQL Managed Instance é o seu próprio produto dentro da família Azure SQL, em vez de apenas uma opção de implementação dentro da Base de Dados Azure SQL. | 
|**Base de Dados SQL do Azure**|Base de dados *única* Azure SQL| Salvo especificação explícita em contrário, o nome do produto Azure SQL Database inclui bases de dados únicas e bases de dados implantadas numa piscina elástica. |
|**Base de Dados SQL do Azure**|*Piscina elástica* Azure SQL Database| Salvo especificação explícita em contrário, o nome do produto Azure SQL Database inclui bases de dados únicas e bases de dados implantadas numa piscina elástica.  |
|**Base de Dados SQL do Azure** |Base de Dados SQL do Azure | Embora o termo permaneça o mesmo, agora só se aplica a uma única base de dados e implantações elásticas de piscinas, e não inclui instâncias geridas. |
| **SQL do Azure**| N/D | Isto refere-se à família dos produtos de motores de base de dados SQL Server que estão disponíveis em Azure: Azure SQL Database, Azure SQL Managed Instance e SQL Server em VMs Azure. | 

## <a name="features-in-public-preview"></a>Características na pré-visualização pública

### <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/single-database)

| Funcionalidade | Detalhes |
| ---| --- |
| Recuperação acelerada da base de dados com bases de dados únicas e piscinas elásticas | Para obter informações, consulte [a Recuperação acelerada da base de dados.](../accelerated-database-recovery.md)|
| Deteção e classificação de dados  |Para obter informações, consulte [a Base de Dados Azure SQL e a Azure Synapse Analytics para a descoberta de dados & classificação](data-discovery-and-classification-overview.md).|
| Trabalhos de base de dados elásticos (pré-visualização) | Para obter informações, consulte [Criar, configurar e gerir trabalhos elásticos.](elastic-jobs-overview.md) |
| Consultas elásticas | Para obter informações, consulte [a visão geral da consulta elástica.](elastic-query-overview.md) |
| Transações elásticas | [Transações distribuídas através de bases de dados em nuvem.](elastic-transactions-overview.md) |
| Editor de consulta no portal Azure |Para obter informações, consulte [o editor de consulta SQL do portal Azure para ligar e consultar dados](connect-query-portal.md).|
| R serviços/machine learning com bases de dados individuais e piscinas elásticas |Para obter informações, consulte [os Serviços de Aprendizagem automática na Base de Dados Azure SQL](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Análise de SQL|Para obter informações, consulte [a Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Instância Gerida do SQL no Azure](#tab/managed-instance)

| Funcionalidade | Detalhes |
| ---| --- |
| <a href="/azure/azure-sql/database/elastic-transactions-overview">Transações distribuídas</a> | Transações distribuídas por Instâncias Geridas. |
| <a href="/azure/sql-database/sql-database-instance-pools">Conjuntos de instâncias</a> | Uma forma conveniente e económica de migrar pequenos exemplos de SQL para a nuvem. |
| <a href="https://aka.ms/managed-instance-aadlogins">Principais do servidor Azure AD ao nível de instância (logins)</a> | Crie logins de nível de instância utilizando uma declaração <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">DE CRIAÇÃO DE PROVEDOR EXTERNO.</a> |
| [Replicação transacional](../managed-instance/replication-transactional-overview.md) | Replique as alterações das suas tabelas para outras bases de dados em SQL Managed Instance, SQL Database ou SQL Server. Ou atualize as suas tabelas quando algumas linhas são alteradas em outros casos de SQL Managed Instance ou SQL Server. Para obter informações, consulte a replicação de [configuração em Azure SQL Managed Instance](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Deteção de ameaças |Para obter informações, consulte [a deteção de ameaças configure em Azure SQL Managed Instance](../managed-instance/threat-detection-configure.md).|
| Retenção de cópia de segurança de longa duração | Para obter informações, consulte [a Configure back-up retenção em Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md), que está atualmente em pré-visualização pública limitada. | 

---

## <a name="new-features"></a>Novas funcionalidades

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL Gestão de Ocorrências H2 2019 atualizações

- [A configuração da sub-rede ajudada pelo serviço](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) é uma forma segura e conveniente de gerir a configuração da sub-rede onde controla o tráfego de dados enquanto a SQL Managed Instance assegura o fluxo ininterrupto do tráfego de gestão.
- [A encriptação transparente de dados (TDE) com a Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) permite um cenário de trazer a sua própria chave (BYOK) para a proteção de dados em repouso e permite que as organizações separem os deveres de gestão para chaves e dados.
- [Os grupos de falha automática permitem-lhe](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) replicar todas as bases de dados desde a instância primária até uma instância secundária noutra região.
- [As bandeiras de traços globais](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) permitem configurar o comportamento da SQL Managed Instance.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL Gestão de Casos H1 2019 atualizações

As seguintes funcionalidades estão ativadas no modelo de implementação sql Managed Instance em H1 2019:
  - Suporte para subscrições com <a href="https://aka.ms/sql-mi-visual-studio-subscribers">crédito mensal Azure para assinantes do Estúdio Visual</a> e aumento dos [limites regionais.](../managed-instance/resource-limits.md#regional-resource-limitations)
  - Suporte para <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central. </a>
  - Crie um caso gerido com <a href="https://aka.ms/managed-instance-collation">colagem ao nível de exemplo</a> e um <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso horário</a> à sua escolha.
  - As instâncias geridas estão agora protegidas com [firewall incorporada.](../managed-instance/management-endpoint-verify-built-in-firewall.md)
  - Configure a SQL Managed Instance para utilizar [pontos finais públicos](../managed-instance/public-endpoint-configure.md), Ligação [de substituição proxy](connectivity-architecture.md#connection-policy) para obter um melhor desempenho da rede, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 vCores na geração de hardware da Gen5</a> ou <a href="https://aka.ms/managed-instance-configurable-backup-retention">retenção de backup Configure até 35 dias</a> para a restauração pontual. [A retenção de backup de longo prazo](long-term-retention-overview.md#sql-managed-instance-support) (até 10 anos) encontra-se atualmente em pré-visualização pública limitada.  
  - As novas funcionalidades <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">permitem-lhe restaurar a sua base de dados para outro centro de dados utilizando o PowerShell,</a>a base de [dados do nome,](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) [eliminar o cluster virtual.](../managed-instance/virtual-cluster-delete.md)
  - O novo [papel de contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) incorporado permite a separação do dever (SoD) o cumprimento dos princípios de segurança e o cumprimento das normas empresariais.
  - SQL Managed Instance está disponível nas seguintes regiões do Governo Azure para GA (EUA Gov Texas, EUA Gov Arizona) bem como na China Norte 2 e China East 2. Também está disponível nas seguintes regiões públicas: Austrália Central, Austrália Central 2, Brasil Sul, França Sul, UAE Central, UAE North, África do Sul Norte, África do Sul Oeste.

## <a name="known-issues"></a>Problemas conhecidos

|Problema  |Data descoberta  |Estado  |Data resolvida  |
|---------|---------|---------|---------|
|[As transações distribuídas podem ser executadas após a remoção da Instância Gerida do Grupo De Confiança do Servidor](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Out 2020|Tem Solução||
|[As transações distribuídas não podem ser executadas após a operação de dimensionamento de instâncias geridas](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Out 2020|Tem Solução||
|[INSERÇÃO A GRANEL](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql) em Azure SQL e `BACKUP` / `RESTORE` declaração em Instância Gerida não pode usar Azure AD Manage Identity para autenticar para armazenamento Azure|Sep 2020|Tem Solução||
|[Diretor de serviço não pode aceder a Azure AD e AKV](#service-principal-cannot-access-azure-ad-and-akv)|Agosto 2020|Tem Solução||
|[Restaurar a cópia de segurança manual sem o CHECKSUM pode falhar](#restoring-manual-backup-without-checksum-might-fail)|Maio de 2020|Resolvido|Junho de 2020|
|[Agente torna-se sem resposta ao modificar, desativar ou permitir empregos existentes](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maio de 2020|Resolvido|Junho de 2020|
|[Permissões sobre grupo de recursos não aplicados à SQL Gestd Instance](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Fev 2020|Tem Solução||
|[Limitação do failover manual através do portal para grupos de failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Tem Solução||
|[As funções do SQL Agent precisam de permissões EXECUTE explícitas para inícios de sessão não sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dez 2019|Tem Solução||
|[Os trabalhos do Agente SQL podem ser interrompidos pelo reinício do processo do Agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dez 2019|Resolvido|Mar 2020|
|[Logins E utilizadores AZure AD não são suportados em SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Sem solução||
|[Os limites de memória OLTP na memória não são aplicados](#in-memory-oltp-memory-limits-are-not-applied)|Out 2019|Tem Solução||
|[Erro errado devolvido ao tentar remover um ficheiro que não está vazio](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Out 2019|Tem Solução||
|[Alterar o nível de serviço e criar operações de instância são bloqueados pela restauração da base de dados em curso](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Tem Solução||
|[O governador de recursos no nível de serviço critical empresarial pode ter de ser reconfigurado após o failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Tem Solução||
|[Os diálogos de corretor de serviço de base cruzada devem ser reinicializados após a atualização do nível de serviço](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Ago 2019|Tem Solução||
|[A personificação dos tipos de login AZure AD não é suportada](#impersonation-of-azure-ad-login-types-is-not-supported)|Jul 2019|Sem solução||
|[@query parâmetro não suportado em sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Abr 2019|Sem solução||
|[A replicação transacional deve ser reconfigurada após a geo-falha](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Sem solução||
|[Base de dados temporária é utilizada durante a operação RESTORE](#temporary-database-is-used-during-restore-operation)||Tem Solução||
|[A estrutura e o conteúdo TEMPDB são recriados](#tempdb-structure-and-content-is-re-created)||Sem solução||
|[Excedendo o espaço de armazenamento com pequenos ficheiros de base de dados](#exceeding-storage-space-with-small-database-files)||Tem Solução||
|[Valores GUID apresentados em vez de nomes de bases de dados](#guid-values-shown-instead-of-database-names)||Tem Solução||
|[Os registos de erro não persistem](#error-logs-arent-persisted)||Sem solução||
|[O âmbito de transação em duas bases de dados dentro da mesma instância não é suportado](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Tem Solução|Mar 2020|
|[Módulos CLR e servidores ligados às vezes não podem referenciar um endereço IP local](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Tem Solução||
|Consistência da base de dados não verificada utilizando dBCC CHECKDB após a restauração da base de dados do Azure Blob Storage.||Resolvido|Nov 2019|
|A base de dados pontual restaurada do nível Business Critical para o nível de Finalidade Geral não será bem sucedida se a base de dados de origem contiver objetos OLTP na memória.||Resolvido|Out 2019|
|Funcionalidade de correio de base de dados com servidores de correio externos (não-Azure) utilizando ligação segura||Resolvido|Out 2019|
|Bases de dados contidas não suportadas em SQL Gestd Instance||Resolvido|Ago 2019|

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>As transações distribuídas podem ser executadas após a remoção da Instância Gerida do Grupo De Confiança do Servidor

[Os Grupos de Confiança do Servidor](https://docs.microsoft.com/azure/azure-sql/managed-instance/server-trust-group-overview) são utilizados para estabelecer confiança entre Instâncias Geridas que é pré-requisito para a execução de [transações distribuídas](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview). Depois de remover a Instância Gerida do Server Trust Group ou de eliminar o grupo, poderá ainda ser capaz de executar transações distribuídas. Existe uma solução alternativa que pode aplicar para ter certeza de que as transações distribuídas são desativadas e que é [falha manual iniciada pelo utilizador](https://docs.microsoft.com/azure/azure-sql/managed-instance/user-initiated-failover) em Casos Geridos.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>As transações distribuídas não podem ser executadas após a operação de dimensionamento de instâncias geridas

As operações de dimensionamento de instâncias geridas que incluem a alteração do nível de serviço ou o número de vCores irão redefinir as definições do Grupo Server Trust no backend e [desativar as transações distribuídas](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview)em execução . Como uma solução alternativa, elimine e crie o novo [Server Trust Group](https://docs.microsoft.com/azure/azure-sql/managed-instance/server-trust-group-overview) no portal Azure.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>Inserção a granel e backup/restauro não podem utilizar identidade gerida para aceder ao armazenamento do Azure

A declaração de inserção a granel não pode ser utilizada `DATABASE SCOPED CREDENTIAL` com identidade gerida para autenticar para o armazenamento da Azure. Como solução alternativa, mude para a autenticação SIGNATURE DE ACESSO PARTILHADO. O exemplo a seguir não funcionará no Azure SQL (base de dados e instância gerida):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Solução alternativa**: Utilize [a Assinatura de Acesso Partilhado para autenticar para armazenamento](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage).

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Diretor de serviço não pode aceder a Azure AD e AKV

Em algumas circunstâncias, pode existir um problema com o Diretor de Serviços usado para aceder aos serviços Azure AD e Azure Key Vault (AKV). Como resultado, este problema tem impacto na utilização da autenticação AZure AD e da Encriptação transparente da Base de Dados (TDE) com SQL Managed Instance. Isto pode ser experimentado como um problema de conectividade intermitente, ou não ser capaz de executar declarações tais são CREATE LOGIN/USER FROM EXTERNAL PROVIDER ou EXECUTE AS LOGIN/USER. A configuração do TDE com a chave gerida pelo cliente numa nova Azure SQL Managed Instance também pode não funcionar em algumas circunstâncias.

**Solução alternativa**: Para evitar que este problema ocorra na sua SQL Managed Instance antes de executar quaisquer comandos de atualização, ou caso já tenha experimentado este problema após os comandos de atualização, aceda ao portal Azure, aceda ao sql Managed Instance [Ative Directory admin.](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell#azure-portal) Verifique se consegue ver a mensagem de erro "A Instância Gerida precisa de um Diretor de Serviço para aceder ao Diretório Ativo do Azure. Clique aqui para criar um Diretor de Serviço". Caso tenha encontrado esta mensagem de erro, clique nela e siga as instruções passo a passo fornecidas até que este erro tenha sido resolvido.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Restaurar a cópia de segurança manual sem o CHECKSUM pode falhar

Em certas circunstâncias, a cópia de segurança manual das bases de dados que foi feita numa instância gerida sem o CHECKSUM pode não ser restaurada. Nesses casos, recava novamente a reserva até teres sucesso.

**Solução alternativa**: Faça cópias de segurança manuais de bases de dados em casos geridos com checksum ativados.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agente torna-se sem resposta ao modificar, desativar ou permitir empregos existentes

Em certas circunstâncias, modificar, desativar ou permitir um trabalho existente pode fazer com que o agente não responda. O problema é automaticamente atenuado após a deteção, resultando num reinício do processo do agente.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Permissões sobre grupo de recursos não aplicados à SQL Gestd Instance

Quando o papel de Azure do Contribuinte de Instância Gerida SQL é aplicado a um grupo de recursos (RG), não é aplicado à SQL Managed Instance e não tem efeito.

**Solução alternativa**: Crie um papel de contribuinte de instância gerida SQL para os utilizadores ao nível da subscrição.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitação do failover manual através do portal para grupos de failover

Se um grupo de failover se estende por instâncias em diferentes subscrições ou grupos de recursos do Azure, a falha manual não pode ser iniciada a partir da instância primária do grupo de failover.

**Solução alternativa**: Iniciar a falha através do portal a partir da instância geoconsusídua.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>As funções do SQL Agent precisam de permissões EXECUTE explícitas para inícios de sessão não sysadmin

Se os logins não-sysadmin forem adicionados a quaisquer [funções de base de dados fixas do SqL Agent,](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)existe um problema em que as permissões explícitas do EXECUTE devem ser concedidas aos procedimentos armazenados para que estes logins funcionem. Se este problema for encontrado, será mostrada a mensagem de erro "A permissão EXECUTE foi negada no <object_name> do objeto (Microsoft SQL Server, Error: 229)" será mostrada.

**Solução alternativa**: Uma vez adicionados logins a uma função de base de dados fixa SQL Agent (SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole), para cada um dos logins adicionados a estas funções, execute o script abaixo do T-SQL para conceder explicitamente permissões EXECUTE aos procedimentos armazenados listados.

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

**(Resolvido em março de 2020)** O SqL Agent cria uma nova sessão cada vez que um trabalho é iniciado, aumentando gradualmente o consumo de memória. Para evitar atingir o limite de memória interna, o que bloquearia a execução de trabalhos programados, o processo do Agente será reiniciado assim que o seu consumo de memória atingir o limiar. Pode resultar na interrupção da execução de postos de trabalho em execução no momento do reinício.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Os limites de memória OLTP na memória não são aplicados

O nível de serviço Business Critical não aplicará corretamente [os limites máximos de memória para objetos otimizados](../managed-instance/resource-limits.md#in-memory-oltp-available-space) pela memória em alguns casos. A SQL Managed Instance pode permitir que a carga de trabalho utilize mais memória para operações OLTP na memória, o que pode afetar a disponibilidade e a estabilidade do caso. As consultas em memória do OLTP que estão a atingir os limites podem não falhar imediatamente. Esta questão será resolvida em breve. As consultas que usam mais memória da memória ALTP falharão mais cedo se atingirem os [limites](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Solução alternativa**: [Monitorize a utilização do armazenamento OLTP na memória](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-monitor-space) utilizando o [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) para garantir que a carga de trabalho não está a utilizar mais do que a memória disponível. Aumente os limites de memória que dependem do número de vCores, ou otimize a sua carga de trabalho para utilizar menos memória.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erro errado devolvido ao tentar remover um ficheiro que não está vazio

Sql Server e SQL Managed Instance [não permitem que um utilizador deixe cair um ficheiro que não esteja vazio](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se tentar remover um ficheiro de dados sem qualquer data utilizando uma `ALTER DATABASE REMOVE FILE` declaração, o erro `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` não será imediatamente devolvido. A SQL Managed Instance continuará a tentar largar o ficheiro, e a operação falhará após 30 minutos com `Internal server error` .

**Resumo :** Remova o conteúdo do ficheiro utilizando o `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` comando. Se este for o único ficheiro do grupo de ficheiros, precisaria de eliminar dados da tabela ou partição associadas a este grupo de ficheiros antes de encolher o ficheiro e carregar opcionalmente estes dados noutra tabela/partição.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Alterar o nível de serviço e criar operações de instância são bloqueados pela restauração da base de dados em curso

A declaração em `RESTORE` curso, o processo de migração do Serviço de Migração de Dados e a restauração em tempo útil incorporada bloquearão a atualização de um nível de serviço ou o redimensionamento da instância existente e criarão novas instâncias até que o processo de restauro termine. 

O processo de restauro bloqueará estas operações nas instâncias geridas e nas piscinas de instâncias na mesma sub-rede onde o processo de restauro está em curso. Os casos, por exemplo, as piscinas não são afetadas. Criar ou alterar operações de nível de serviço não falhará ou irá esgotar o tempo. Prosseguirão assim que o processo de restauro estiver concluído ou cancelado.

**Solução alternativa**: Aguarde até que o processo de restauro termine, ou cancele o processo de restauro se a operação de criação ou de nível de serviço de atualização tiver maior prioridade.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>O governador de recursos no nível de serviço critical empresarial pode ter de ser reconfigurado após o failover

A funcionalidade [DeTerador](/sql/relational-databases/resource-governor/resource-governor) de Recursos que lhe permite limitar os recursos atribuídos à carga de trabalho do utilizador pode classificar incorretamente alguma carga de trabalho do utilizador após a falha ou uma alteração do nível de serviço iniciado pelo utilizador (por exemplo, a alteração do tamanho de armazenamento de instância máxima vCore ou max).

**Solução :** Executar `ALTER RESOURCE GOVERNOR RECONFIGURE` periodicamente ou como parte de um trabalho de Agente SQL que executa a tarefa SQL quando a instância começar se estiver a utilizar [o Governador de Recursos](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Os diálogos de corretor de serviço de base cruzada devem ser reinicializados após a atualização do nível de serviço

Os diálogos de corretor de serviços de base cruzada deixarão de entregar as mensagens aos serviços noutras bases de dados após a alteração da operação do nível de serviço. As mensagens não se *perdem*e podem ser encontradas na fila do remetente. Qualquer alteração do tamanho de armazenamento de vCores ou instância em SQL Managed Instance fará com que um `service_broke_guid` valor na [visualização de sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) seja alterado para todas as bases de dados. Qualquer `DIALOG` criação utilizando uma declaração [DE DIÁLOGO BEGIN](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) que faz referência a Corretores de Serviço noutra base de dados deixará de entregar mensagens ao serviço-alvo.

**Solução alternativa**: Pare qualquer atividade que utilize conversas de diálogo de corretor de serviços de base cruzada antes de atualizar um nível de serviço e reiniciá-las depois. Se houver mensagens restantes que não são entregues após uma alteração do nível de serviço, leia as mensagens da fila de origem e reencade-as para a fila do alvo.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>A personificação dos tipos de login AZure AD não é suportada

A personificação utilizando `EXECUTE AS USER` ou `EXECUTE AS LOGIN` dos seguintes principais do Azure Ative (Azure AD) não é suportada:
-   Utilizadores azure azure. Neste caso, é devolvido o seguinte erro: `15517` .
- Logins E utilizadores AZURE AD com base em aplicações AD Azure ou diretores de serviço. Neste caso, são devolvidos os seguintes erros: `15517` e `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parâmetro não suportado em sp_send_db_mail

O `@query` parâmetro do procedimento [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) não funciona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A replicação transacional deve ser reconfigurada após a geo-falha

Se a replicação transacional estiver ativada numa base de dados num grupo de falha automática, o administrador da SQL Managed Instance deve limpar todas as publicações sobre a antiga primária e reconfigurá-las na nova primária após a ocorrência de uma falha noutra região. Para obter mais informações, consulte [Replication](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Logins E utilizadores AZure AD não são suportados em SSDT

As Ferramentas de Dados do Servidor SQL não suportam totalmente os logins e utilizadores AZure AD.

### <a name="temporary-database-is-used-during-restore-operation"></a>Base de dados temporária é utilizada durante a operação RESTORE

Quando uma base de dados está a restaurar em SQL Managed Instance, o serviço de restauro criará primeiro uma base de dados vazia com o nome pretendido para alocar o nome no caso. Após algum tempo, esta base de dados será abandonada, e o restabelecimento da base de dados será iniciado. 

A base de dados que está em *Restaurar* o estado terá temporariamente um valor GUID aleatório em vez de nome. O nome temporário será alterado para o nome pretendido especificado na `RESTORE` declaração assim que o processo de restauro terminar. 

Na fase inicial, um utilizador pode aceder à base de dados vazia e até criar tabelas ou carregar dados nesta base de dados. Esta base de dados temporária será abandonada quando o serviço de restauro iniciar a segunda fase.

**Solução:** Não aceda à base de dados que está a restaurar até ver se a restauração está concluída.

### <a name="tempdb-structure-and-content-is-re-created"></a>A estrutura e o conteúdo TEMPDB são recriados

A `tempdb` base de dados é sempre dividida em 12 ficheiros de dados, e a estrutura do ficheiro não pode ser alterada. O tamanho máximo por ficheiro não pode ser alterado e os novos ficheiros não podem ser adicionados `tempdb` . `Tempdb` é sempre recriada como uma base de dados vazia quando a instância começa ou falha, e quaisquer alterações feitas `tempdb` não serão preservadas.

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com pequenos ficheiros de base de dados

`CREATE DATABASE`, `ALTER DATABASE ADD FILE` e `RESTORE DATABASE` as declarações podem falhar porque a instância pode atingir o limite de Armazenamento Azure.

Cada instância de Finalidade Geral de SQL Managed Instance tem até 35 TB de armazenamento reservado para o espaço disco Azure Premium. Cada ficheiro de base de dados é colocado num disco físico separado. Os tamanhos do disco podem ser de 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não construído no disco não é carregado, mas a soma total dos tamanhos do Disco Azure Premium não pode exceder 35 TB. Em alguns casos, um caso gerido que não precise de 8 TB no total pode exceder o limite de 35 TB Azure no tamanho do armazenamento devido à fragmentação interna.

Por exemplo, uma instância de finalidade geral de SQL Managed Instance pode ter um ficheiro grande que é 1.2 TB em tamanho colocado em um disco de 4-TB. Também pode ter 248 ficheiros que são 1 GB cada e que são colocados em discos separados de 128 GB. Neste exemplo:

- O tamanho total de armazenamento de disco atribuído é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O espaço reservado total para bases de dados no caso é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Este exemplo ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de ficheiros, um caso de SQL Managed Instance pode atingir o limite de 35-TB que é reservado para um Disco Azure Premium anexado quando não se pode esperar.

Neste exemplo, as bases de dados existentes continuam a funcionar e podem crescer sem qualquer problema, desde que não sejam adicionados novos ficheiros. As novas bases de dados não podem ser criadas ou restauradas porque não há espaço suficiente para novas unidades de disco, mesmo que o tamanho total de todas as bases de dados não atinja o limite de tamanho de instância. O erro que foi devolvido neste caso não é claro.

Pode [identificar o número de ficheiros restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) utilizando as vistas do sistema. Se atingir este limite, tente [esvaziar e apagar alguns dos ficheiros mais pequenos utilizando a declaração DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou mudar para o [nível Business Critical, que não tem este limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valores GUID apresentados em vez de nomes de bases de dados

Várias visualizações do sistema, contadores de desempenho, mensagens de erro, XEvents e entradas de registo de erros exibem identificadores de base de dados GUID em vez dos nomes reais da base de dados. Não confie nestes identificadores GUID porque são substituídos por nomes de bases de dados reais no futuro.

**Resumo :** Utilize o visualização sys.databases para resolver o nome da base de dados real a partir do nome da base de dados física, especificado sob a forma de identificadores de base de dados GUID:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Os registos de erro não persistem

Os registos de erro que estão disponíveis em SQL Managed Instance não persistem e o seu tamanho não está incluído no limite máximo de armazenamento. Os registos de erro podem ser automaticamente apagados se ocorrerem falhas. Pode haver lacunas no histórico de registos de erros porque o SQL Managed Instance foi movido várias vezes em várias máquinas virtuais.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O âmbito de transação em duas bases de dados dentro da mesma instância não é suportado

**(Resolvido em março de 2020)** A `TransactionScope` classe em .NET não funciona se duas consultas forem enviadas para duas bases de dados no mesmo caso no mesmo âmbito de transação:

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

**Solução alternativa (não necessária desde março de 2020)**: Utilize [sqlConnection.ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para utilizar outra base de dados num contexto de ligação em vez de utilizar duas ligações.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Módulos CLR e servidores ligados às vezes não podem referenciar um endereço IP local

Os módulos CLR em SQL Managed Instance e servidores ligados ou consultas distribuídas que referem um caso atual às vezes não conseguem resolver o IP de um caso local. Este erro é um problema transitório.

**Solução alternativa**: Utilize ligações de contexto num módulo CLR, se possível.

## <a name="updates"></a>Atualizações

Para obter uma lista de atualizações e melhorias da Base de Dados [SQL, consulte as atualizações do serviço de bases de dados SQL](https://azure.microsoft.com/updates/?product=sql-database).

Para atualizações e melhorias em todos os serviços Azure, consulte [as atualizações do Serviço](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuir para o conteúdo

Para contribuir para a documentação do Azure SQL, consulte o [guia de contribuintes docs](https://docs.microsoft.com/contribute/).
