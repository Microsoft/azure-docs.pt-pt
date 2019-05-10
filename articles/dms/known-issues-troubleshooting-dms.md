---
title: Artigo sobre conhecido de resolução de problemas/erros comuns associados com o serviço de migração de base de dados do Azure | Documentos da Microsoft
description: Saiba mais sobre como resolver problemas comuns problemas/erros conhecidos associados à utilização do Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/09/2019
ms.openlocfilehash: 7b470c20397aac456d34d5e3b877c7d4126d8279
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465115"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Resolver problemas de serviço de migração de base de dados do Azure e erros comuns

Este artigo descreve alguns problemas comuns e os erros que os utilizadores do Azure Database Migration Service podem se deparou com. O artigo também inclui informações sobre como resolver estes problemas e erros.

## <a name="migration-activity-in-queued-state"></a>Atividade de migração no estado de fila

Quando cria novas atividades no projeto de serviço de migração de base de dados do Azure, as atividades de permanecerem no estado de fila.

| Causa         | Resolução |
| ------------- | ------------- |
| Este problema ocorre quando a instância do serviço de migração de base de dados do Azure atingiu a capacidade máxima para tarefas em curso que executar em simultâneo. Qualquer nova atividade é colocada na fila até que a capacidade fique disponível. | Valide o serviço de migração de dados de instância tem de executar atividades entre projetos. Pode continuar a criar novas atividades que são automaticamente adicionadas à fila para execução. Assim que concluir a qualquer uma das atividades existentes em execução, a atividade em fila seguinte começa a ser executado e o estado muda para o estado de execução automaticamente. Não precisa de tomar medidas adicionais para iniciar a migração da atividade em fila.<br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Número máx. de bases de dados selecionadas para migração

O seguinte erro ocorre quando uma atividade para um projeto de migração de base de dados para mover a base de dados do Azure SQL ou uma base de dados do SQL do Azure a criar a instância gerida:

* **Erro**: Erro de validação de definições de migração","errorDetail":"o número máximo de mais do que "4" objetos de 'Database' não foi selecionada para migração."

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro apresentada quando selecionar mais de quatro bases de dados para uma atividade de migração única. No momento, cada atividade de migração está limitada a quatro bases de dados. | Selecione as bases de dados de quatro ou menos por atividade de migração. Se tiver de migrar mais de quatro bases de dados em paralelo, Aprovisione outra instância do serviço de migração de base de dados do Azure. Atualmente, cada subscrição suporta até duas instâncias de serviço de migração de base de dados do Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Erros de migração do MySQL para o MySQL do Azure com falhas de recuperação

Quando migra do MySQL para a base de dados do Azure para MySQL com o serviço de migração de base de dados do Azure, a atividade de migração falhar com o seguinte erro:

* **Erro**: Erro: Erro de migração de base de dados - a tarefa 'TaskID', foi suspensa devido a falhas de recuperação sucessivas [n].

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro pode ocorrer quando o utilizador que executa a migração está em falta a função ReplicationAdmin e/ou privilégios de cliente de replicação, a RÉPLICA de replicação e SUPER (versões anteriores ao MySQL 5.6.6).<br> <br><br><br> <br> <br> <br> <br> <br> <br> | Certifique-se de que o [privilégios pré-requisitos](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) para o utilizador conta estão configurados com precisão na instância do MySQL do Azure. Por exemplo, podem seguir os passos seguintes para criar um utilizador com o nome "migrateuser" com privilégios necessários:<br>1. Criar utilizador migrateuser@'%' identificado por 'segredo'; <br>2. a conceder a todos os privilégios no db_name.* para 'migrateuser'@'%' identificado por 'segredo'; Repita este passo para conceder acesso às bases de dados mais <br>3. a concessão subordinado de replicação no *.* para 'migrateuser'@'%' identificado por 'segredo';<br>4. cliente de replicação de a concessão no *.* para 'migrateuser'@'%' identificado por 'segredo';<br>5. esvaziar privilégios; |

## <a name="error-when-attempting-to-stop-the-azure-database-migration-service-instance"></a>Erro ao tentar parar a instância do serviço de migração de base de dados do Azure

Receber erro seguinte ao parar a instância do serviço de migração de base de dados do Azure:

* **Erro**: Falha ao parar serviço. Erro: {"error": {'code': 'InvalidRequest","mensagem":" uma ou mais atividades atualmente em execução. Para parar o serviço, aguarde até que as atividades foram concluídas ou pare essas atividades manualmente e tente novamente. "}}

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro apresentada quando a instância de serviço que está a tentar parar inclui atividades que ainda estão em execução ou presentes em projetos de migração. <br><br><br><br><br><br> | Certifique-se de que não há nenhuma atividade em execução na instância do serviço de migração de base de dados do Azure está a tentar parar. Também pode eliminar as atividades ou projetos antes de tentar parar o serviço. Os passos seguintes mostram como remover a projetos para limpar a instância de serviço de migração, eliminando todas as tarefas em execução:<br>1. Install-Module-Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. SELECT-AzureRmSubscription - SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject-Name <projectName> - ResourceGroupName <rgName> - ServiceName <serviceName> - DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-from-sql-server-to-an-azure-sql-database-managed-instance"></a>Erro ao restaurar a base de dados enquanto a instância gerida de migração do SQL Server para uma base de dados do SQL do Azure

Quando efetuar uma migração online do SQL Server para uma instância gerida da base de dados do Azure SQL, a transferência falha com o erro seguinte:

* **Erro**: Operação de restauro falhou para o Id "operationId" da operação. O código 'AuthorizationFailed', mensagem de "cliente"clientId"com o id de objeto"objectId"não tem autorização para executar a ação 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' no âmbito" /subscriptions/ subscriptionId ".".

| Causa         | Resolução    |
| ------------- | ------------- |
| Este erro indica que o principal da aplicação que está a ser utilizado para a migração online do SQL Server para uma instância gerida da base de dados do Azure SQL não tem a contribuir com permissão na subscrição. Certas chamadas de API com a instância gerida neste momento exigem essa permissão na subscrição para a operação de restauro. <br><br><br><br><br><br><br><br><br><br> | Utilize o `Get-AzureADServicePrincipal` cmdlet do PowerShell com `-ObjectId` disponíveis da mensagem de erro para listar o nome a apresentar do ID da aplicação que está a ser utilizado.<br><br> Validar as permissões para esta aplicação e certifique-se de que tem o [função de contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ao nível da subscrição. <br><br> Azure da base de dados migração serviço equipa de engenharia está a funcionar para restringir o necessário acesso a partir de atual contribuir com a função na subscrição. Se tiver um requisito de negócio que não permite a utilização de contribuir com a função, contacte o suporte do Azure para obter ajuda adicional. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Ocorreu um erro ao eliminar o NIC associados com o serviço de migração de base de dados do Azure

Ao tentar eliminar um cartão de Interface de rede associados com o serviço de migração de base de dados do Azure, a tentativa de eliminação falhar com este erro:

* **Erro**: Não é possível eliminar o NIC associado ao Azure Database Migration Service devido ao serviço DMS utilizando o NIC

| Causa         | Resolução    |
| ------------- | ------------- |
| Este problema ocorre quando a instância do serviço de migração de base de dados do Azure ainda pode estar presente e consumir o NIC. <br><br><br><br><br><br> | Para eliminar esta NIC, elimine a instância do serviço DMS que exclui automaticamente a NIC utilizada pelo serviço.<br><br> **Importante**: Certifique-se de que a instância de serviço de migração de base de dados do Azure a ser eliminada tem sem atividades em execução.<br><br> Depois de serem eliminadas todos os projetos e atividades associadas à instância do serviço de migração de base de dados do Azure, pode eliminar a instância do serviço. A NIC utilizada pela instância do serviço é limpa automaticamente como parte da eliminação de serviço. |

## <a name="connection-error-when-using-expressroute"></a>Erro de ligação ao utilizar o ExpressRoute

Ao tentar ligar à origem no Assistente de projeto de serviço de migração de base de dados do Azure, a ligação falha após o tempo limite prolongado se origem estiver a utilizar o ExpressRoute para conectividade.

| Causa         | Resolução    |
| ------------- | ------------- |
| Ao usar [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [requer](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) três pontos finais de serviço na sub-rede da rede Virtual associado ao serviço de aprovisionamento:<br> Ponto de extremidade Service Bus<br> – Ponto final de armazenamento<br> -Ponto final de base de dados (por exemplo, o ponto de extremidade do SQL, o ponto final do Cosmos DB) de destino<br><br><br><br> | [Ativar](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) os pontos de extremidade de serviço necessário para a conectividade do ExpressRoute entre a origem e o Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-database-for-mysql"></a>Erro de tempo limite ao migrar uma base de dados do MySQL para a base de dados do Azure para MySQL

Quando migra uma base de dados do MySQL para uma base de dados do Azure para MySQL instância por meio do serviço de migração de base de dados do Azure, a migração falha com o seguinte erro de tempo limite:

    * **Erro**: Erro: Erro de migração de base de dados - falha ao carregar o ficheiro - falha ao iniciar o processo de carregamento para o ficheiro "n" RetCode: SQL_ERROR SqlState: HY000 NativeError: Mensagem de 1205: [MySQL] [controlador ODBC] [mysqld] o bloqueio de aguardar o tempo limite excedido; Tente reiniciar a transação

| Causa         | Resolução    |
| ------------- | ------------- |
| Este erro ocorre quando a migração falha devido o tempo limite de espera de bloqueio durante a migração.<br><br> | Considere aumentar o valor do parâmetro de servidor **'innodb_lock_wait_timeout'**. O valor permitido mais alto é 1073741824. |

## <a name="additional-known-issues"></a>Problemas conhecidos adicionais

* [Problemas conhecidos/limitações de migração com migrações online para a BD SQL do Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Limitações de migração/problemas conhecidos com migrações online BD do Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Limitações de migração/problemas conhecidos com migrações online BD do Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="additional-resources"></a>Recursos adicionais

* [Serviço de migração de base de dados do Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)
* [Como configurar os parâmetros do servidor na base de dados do Azure para MySQL com o portal do Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* [Descrição geral da pré-requisitos para utilizar o Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
* [FAQ sobre a utilização do serviço de migração de base de dados do Azure](https://docs.microsoft.com/azure/dms/faq)
