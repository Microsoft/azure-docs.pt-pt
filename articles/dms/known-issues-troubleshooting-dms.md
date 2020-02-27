---
title: Questões comuns - Serviço de Migração de Bases de Dados Azure
description: Saiba como resolver problemas conhecidos e erros associados à utilização do Serviço de Migração de Bases de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649112"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Problemas problemas problemas com um serviço de migração de bases de dados azure comuns e erros

Este artigo descreve alguns problemas e erros comuns que os utilizadores do Serviço de Migração da Base de Dados Azure podem encontrar. O artigo também inclui informações sobre como resolver estas questões e erros.

## <a name="migration-activity-in-queued-state"></a>Atividade migratória em estado de fila

Quando se criam novas atividades num projeto do Serviço de Migração de Bases de Dados Azure, as atividades permanecem em estado de fila.

| Causa         | Resolução |
| ------------- | ------------- |
| Esta questão acontece quando a instância do Serviço de Migração de Bases de Dados Azure atingiu a capacidade máxima para tarefas em curso que simultaneamente executam. Qualquer nova atividade fica na fila até que a capacidade fique disponível. | Validar a instância do Serviço de Migração de Dados tem atividades em curso em projetos. Pode continuar a criar novas atividades que são automaticamente adicionadas à fila para a execução. Assim que qualquer uma das atividades de execução existentes estiver concluída, a próxima atividade em fila começa a funcionar e o estado muda automaticamente para o estado de execução. Não é preciso tomar medidas adicionais para começar a migração da atividade em fila.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Número máximo de bases de dados selecionadas para migração

O seguinte erro ocorre ao criar uma atividade para um projeto de migração de bases de dados para a mudança para a Base de Dados Azure SQL ou para uma instância gerida pela Base de Dados Azure SQL:

* **Erro**: Erro de validação de definições de migração", "errorDetail":"Foram selecionados mais do que objetos max number '4' de 'Bases de Dados' para migração."

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro mostra quando selecionou mais de quatro bases de dados para uma única atividade migratória. Atualmente, cada atividade migratória está limitada a quatro bases de dados. | Selecione quatro ou menos bases de dados por atividade de migração. Se precisar de migrar mais de quatro bases de dados em paralelo, provisão de outra instância do Serviço de Migração de Bases de Dados Azure. Atualmente, cada subscrição suporta até duas instâncias do Serviço de Migração de Bases de Dados Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Erros na migração mySQL para O MiSQL Azure com falhas de recuperação

Quando migra do MySQL para a Base de Dados Azure para o MySQL utilizando o Serviço de Migração de Bases de Dados Azure, a atividade de migração falha com o seguinte erro:

* **Erro**: Erro de migração da base de dados - Task 'TaskID' foi suspenso devido a falhas de recuperação sucessivas.

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro pode ocorrer quando o utilizador que está a fazer a migração está em falta na função replicação Admin e/ou privilégios de REPLICATION CLIENT, REPLICATION REPLICA, e SUPER (versões anteriores a MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Certifique-se de que os [privilégios pré-requisitos](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) para a conta de utilizador estão configurados com precisão na base de dados Azure para a instância MySQL. Por exemplo, podem ser seguidos os seguintes passos para criar um utilizador chamado "migrador" com privilégios exigidos:<br>1. CRIAR migrateuser@'% do utilizador identificado por «segredo»; <br>2. Conceder todos os privilégios sobre db_name.* a 'migrateuser'@'%' identificados por 'segredo'; repetir este passo para conceder acesso em mais bases de dados <br>3. Conceder escravo de replicação em *.* para 'migrateuser'@'%' identificado por 'segredo';<br>4. Grant cliente de replicação em *.* para 'migrateuser'@'%' identificado por 'segredo';<br>5. Privilégios de descarga; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Erro ao tentar parar o Serviço de Migração da Base de Dados Azure

Recebe um erro seguinte ao parar a instância do Serviço de Migração de Bases de Dados Azure:

* **Erro**: O serviço falhou em parar. Erro: {'error':{'code':'InvalidRequest','message':'Uma ou mais atividades estão atualmente a ser executadas. Para parar o serviço, aguarde até que as atividades tenham concluído ou pare essas atividades manualmente e tente novamente.'}}

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro mostra quando a instância de serviço que está a tentar parar inclui atividades que ainda estão em execução ou presentes em projetos de migração. <br><br><br><br><br><br> | Certifique-se de que não existem atividades em execução no caso do Serviço de Migração de Bases de Dados Azure que está a tentar parar. Também pode eliminar as atividades ou projetos antes de tentar parar o serviço. As seguintes etapas ilustram como remover projetos para limpar a instância do serviço de migração, eliminando todas as tarefas de execução:<br>1. Instalação-Módulo -Nome AzureRM.DataMigration <br>2. Conta login-AzureRm <br>3. Select-AzureRmSubscription -SubscriptionName "\<subName>" <br> 4. Remover-AzureRmDataMigrationProject -Name \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Erro ao tentar iniciar o Serviço de Migração da Base de Dados Azure

Recebe um erro seguinte ao iniciar a instância do Serviço de Migração de Bases de Dados Azure:

* **Erro**: O serviço não arranca. Erro: {'errorDetail':'O serviço falhou no arranque, contacte o suporte da Microsoft'}

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro mostra-se quando a instância anterior falhou internamente. Este erro raramente ocorre, e a equipa de engenharia está ciente disso. <br> | Elimine a instância do serviço que não pode iniciar e, em seguida, forre um novo para substituí-lo. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Error restaurar base de dados durante a migração do SQL para o Caso gerido do SQL DB

Quando realiza uma migração on-line do SQL Server para uma instância gerida pela Base de Dados Azure SQL, a cutover falha com o seguinte erro:

* **Erro**: Restabelecer a operação falhou no funcionamento Id 'operationId'. Código 'AuthorizationFailed', Mensagem 'Client 'clientId' com id de objeto 'objectId' não tem autorização para realizar a ação 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' sobre o âmbito '/subscrições/subscrições/subscriçõesId'.».

| Causa         | Resolução    |
| ------------- | ------------- |
| Este erro indica que o principal de aplicação utilizado para a migração on-line do SQL Server para uma instância gerida pela Base de Dados Azure SQL não tem permissão de contribuição para a subscrição. Algumas chamadas da API com Instância Gerida exigem esta permissão na subscrição da operação de restauro. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Utilize o `Get-AzureADServicePrincipal` cmdlet PowerShell com `-ObjectId` disponíveis na mensagem de erro para listar o nome de visualização do ID da aplicação que está a ser utilizado.<br><br> Valide as permissões a esta aplicação e certifique-se de que tem o papel de [contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ao nível da subscrição. <br><br> A Equipa de Engenharia de Serviços de Migração da Base de Dados Azure está a trabalhar para restringir o acesso necessário ao papel atual de contribuição na subscrição. Se tiver um requisito de negócio que não permita o uso da função de contribuição, contacte o suporte do Azure para obter ajuda adicional. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Erro ao apagar NIC associado ao Serviço de Migração da Base de Dados Azure

Quando tenta eliminar um Cartão de Interface de Rede associado ao Serviço de Migração de Bases de Dados Azure, a tentativa de eliminação falha com este erro:

* **Erro**: Não pode excluir o NIC associado ao Serviço de Migração de Bases de Dados Azure devido ao serviço DMS utilizando o NIC

| Causa         | Resolução    |
| ------------- | ------------- |
| Esta questão acontece quando a instância do Serviço de Migração da Base de Dados Azure ainda pode estar presente e consumir o NIC. <br><br><br><br><br><br><br><br> | Para eliminar este NIC, elimine a instância de serviço DMS que elimina automaticamente o NIC utilizado pelo serviço.<br><br> **Importante**: Certifique-se de que a instância do Serviço de Migração da Base de Dados Azure que está a ser eliminada não tem atividades de funcionamento.<br><br> Depois de todos os projetos e atividades associados à instância do Serviço de Migração da Base de Dados Azure serem eliminados, pode eliminar a instância de serviço. O NIC utilizado pela instância de serviço é automaticamente limpo como parte da eliminação do serviço. |

## <a name="connection-error-when-using-expressroute"></a>Erro de ligação ao utilizar o ExpressRoute

Quando tenta ligar à origem no assistente de projeto do serviço de Migração de Base de Dados do Azure, a ligação falhará após expirar o tempo limite prolongado se a origem estiver a utilizar o ExpressRoute para fins de conectividade.

| Causa         | Resolução    |
| ------------- | ------------- |
| Ao utilizar o [ExpressRoute,](https://azure.microsoft.com/services/expressroute/)o Serviço de Migração da Base de Dados Azure requer o fornecimento de três [pontos](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) finais de serviço na subnet da Rede Virtual associada ao serviço:<br> -- Ponto final do ônibus de serviço<br> -- Ponto final de armazenamento<br> -- Ponto final da base de dados alvo (por exemplo, ponto final sQL, ponto final cosmos DB)<br><br><br><br><br> | [Ative](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) os pontos finais de serviço necessários para a conectividade ExpressRoute entre fonte e serviço de migração de bases de dados Azure. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Bloqueie o erro de tempo de espera ao migrar uma base de dados MySQL para O BD para o MySQL

Quando migra uma base de dados MySQL para uma base de dados Azure para a instância MySQL através do Serviço de Migração de Bases de Dados Azure, a migração falha com o seguinte erro de tempo de espera:

* **Erro**: Erro de migração de base de dados - Ficheiro de carga falhado - Não conseguiu iniciar o processo de carga para o ficheiro 'n' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 Mensagem: [MySQL][Condutor oDBC][mysqld] Lock tempo de espera ultrapassado; tentar reiniciar transação

| Causa         | Resolução    |
| ------------- | ------------- |
| Este erro ocorre quando a migração falha devido ao tempo de espera do bloqueio durante a migração. | Considere aumentar o valor do parâmetro do servidor **'innodb_lock_wait_timeout'.** O valor mais elevado permitido é 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Erro de ligação à fonte SQL Server ao utilizar porta dinâmica ou instância nomeada

Quando tenta ligar o Serviço de Migração de Bases de Dados Azure à fonte do SQL Server que funciona em instância ou porta dinâmica, a ligação falha com este erro:

* **Erro**: -1 - Ligação SQL falhou. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Verifique se o nome da instância está correto e que o Servidor SQL está configurado para permitir ligações remotas. (fornecedor: Interfaces de rede SQL, erro: 26 - Erro localizar servidor/instância especificado)

| Causa         | Resolução    |
| ------------- | ------------- |
| Este problema acontece quando a fonte SQL Server instância que o Serviço de Migração de Bases de Dados Azure tenta ligar-se a uma porta dinâmica ou está usando uma instância nomeada. O serviço SQL Server Browser ouve a porta UDP 1434 para ligações de entrada a uma instância nomeada ou quando se utiliza uma porta dinâmica. A porta dinâmica pode mudar cada vez que o serviço SQL Server reinicia. Pode verificar a porta dinâmica atribuída a uma instância através da configuração da rede no SQL Server Configuration Manager.<br><br><br> |Verifique se o Serviço de Migração de Bases de Dados Azure pode ligar-se ao serviço de navegador SQL Server de origem na porta UDP 1434 e à instância do Servidor SQL através da porta TCP dinamicamente atribuída, conforme aplicável. |

## <a name="additional-known-issues"></a>Questões conhecidas adicionais

* [Questões conhecidas/limitações de migração com migrações online para base de dados Azure SQL](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Questões conhecidas/limitações de migração com migrações online para Base de Dados Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Questões conhecidas/limitações de migração com migrações online para Base de Dados Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passos seguintes

* Ver o artigo Serviço de Migração de Bases de [Dados Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Ver o artigo Como configurar parâmetros de servidor na Base de [Dados Azure para MySQL utilizando o portal Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Ver o artigo [Visão geral dos pré-requisitos para a utilização](https://docs.microsoft.com/azure/dms/pre-reqs)do Serviço de Migração de Bases de Dados Azure .
* Consulte as [FAQ sobre a utilização](https://docs.microsoft.com/azure/dms/faq)do Serviço de Migração de Bases de Dados Azure .
