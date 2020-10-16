---
title: Questões comuns - Azure Database Migration Service
description: Saiba como resolver problemas/erros conhecidos associados à utilização do Serviço de Migração da Base de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: f0ec9d2a3794ea910339b4d329bb28f23c5a76b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297363"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Resolução de problemas problemas comuns problemas do Serviço de Migração de Bases de Dados de Azure

Este artigo descreve algumas questões e erros comuns que os utilizadores do Azure Database Migration Service podem encontrar. O artigo também inclui informações sobre como resolver estas questões e erros.

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

## <a name="migration-activity-in-queued-state"></a>Atividade migratória em estado de fila

Quando cria novas atividades num projeto do Azure Database Migration Service, as atividades permanecem em fila.

| Causa         | Resolução |
| ------------- | ------------- |
| Esta questão acontece quando a instância do Serviço de Migração da Base de Dados Azure atingiu a capacidade máxima para tarefas em curso que simultaneamente executam. Qualquer nova atividade é em fila até que a capacidade fique disponível. | Validar a instância do Serviço de Migração de Dados tem atividades em todos os projetos. Pode continuar a criar novas atividades que são automaticamente adicionadas à fila para execução. Assim que qualquer uma das atividades de execução existentes estiver concluída, a próxima atividade em fila começa a funcionar e o estado muda automaticamente para o estado de funcionamento. Não precisa de tomar nenhuma ação adicional para começar a migração de atividades em fila.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Número máximo de bases de dados selecionadas para migração

O seguinte erro ocorre ao criar uma atividade para um projeto de migração de bases de dados para se deslocar para a Base de Dados Azure SQL ou uma Instância Gerida Azure SQL:

* **Erro**: Erro de validação de configurações de migração", "errorDetail":"Mais do que o número máximo '4' objetos de 'Bases de Dados' foram selecionados para migração."

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro aparece quando selecionou mais de quatro bases de dados para uma única atividade de migração. Atualmente, cada atividade migratória está limitada a quatro bases de dados. | Selecione quatro ou menos bases de dados por atividade migratória. Se precisar de migrar mais de quatro bases de dados em paralelo, disposi outra instância do Serviço de Migração da Base de Dados Azure. Atualmente, cada subscrição suporta até duas instâncias do Serviço de Migração da Base de Dados Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Erros para a migração do MySQL para a Azure MySQL com falhas de recuperação

Quando migra do MySQL para a Base de Dados Azure para o MySQL utilizando o Azure Database Migration Service, a atividade de migração falha com o seguinte erro:

* **Erro**: Erro de migração da base de dados - Tarefa 'TaskID' foi suspenso devido a [n] falhas de recuperação sucessivas.

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro pode ocorrer quando o utilizador que faz a migração faltar a função e/ou privilégios de REPLICATION CLIENT, REPLICATION REPLICATION e SUPER (versões mais cedo do que o MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Certifique-se de que os [privilégios pré-requisitos](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) para a conta de utilizador estão configurados com precisão na Base de Dados Azure para a instância MySQL. Por exemplo, podem ser seguidos os seguintes passos para criar um utilizador chamado "migrador" com privilégios necessários:<br>1. CRIAR MIGRATEUSER@'%» DO UTILIZADOR IDENTIFICADO POR «segredo»; <br>2. Conceder todos os privilégios em db_name.* a 'migrateuser'@'%' identificado por 'segredo'; repetir este passo para conceder acesso em mais bases de dados <br>3. Conceder a réplica do escravo em *.* a 'migrateuser'@'%» identificado por 'segredo';<br>4. Conceder cliente de replicação em *.* a 'migrateuser'@'%» identificado por 'segredo';<br>5. Privilégios de descarga; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Erro ao tentar parar o Serviço de Migração da Base de Dados de Azure

Recebe o seguinte erro ao parar a instância do Serviço de Migração da Base de Dados Azure:

* **Erro**: O serviço falhou em parar. Erro: {'error':{'code':'InvalidRequest','message':'Uma ou mais atividades estão atualmente a ser executadas. Para interromper o serviço, aguarde até que as atividades tenham terminado ou pare as atividades manualmente e tente novamente.}} }}

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro aparece quando a instância de serviço que está a tentar parar inclui atividades que ainda estão em execução ou presentes em projetos de migração. <br><br><br><br><br><br> | Certifique-se de que não existem atividades em execução no caso do Serviço de Migração da Base de Dados Azure que está a tentar parar. Também pode apagar as atividades ou projetos antes de tentar parar o serviço. As seguintes etapas ilustram como remover os projetos de limpeza da instância do serviço de migração, eliminando todas as tarefas em execução:<br>1. Install-Module -Nome AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -Nome de Assinatura \<subName> " <br> 4. Remove-AzureRmDataMigrationProject -Nome \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Erro ao tentar iniciar o Serviço de Migração da Base de Dados de Azure

Recebe o seguinte erro ao iniciar a instância do Serviço de Migração da Base de Dados Azure:

* **Erro**: O serviço falha no arranque. Erro: {'errorDetail':'O serviço não foi iniciado, contacte o suporte da Microsoft»}

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro aparece quando a instância anterior falhou internamente. Este erro ocorre raramente, e a equipa de engenharia está ciente disso. <br> | Elimine a instância do serviço que não pode iniciar e, em seguida, forre um novo para substituí-lo. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Erro restaurando a base de dados enquanto migrava SQL para Azure SQL DB caso gerido

Quando realiza uma migração on-line do SQL Server para Azure SQL Managed Instance, o corte falha com o seguinte erro:

* **Erro**: Restaurar a operação falhou para o ID de funcionamento 'operationId'. Código 'AutorizaçõesFailed', Mensagem 'ClienteId' com id de objeto 'objectId' não tem autorização para realizar ação 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' sobre âmbito '/subscrições/subscriçãoId'.'.

| Causa         | Resolução    |
| ------------- | ------------- |
| Este erro indica que o principal da aplicação utilizado para a migração on-line do SQL Server para o SQL Managed Instance não tem permissão para a subscrição. Algumas chamadas da API com Instância Gerida neste momento requerem esta permissão na subscrição para a operação de restauro. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Utilize o `Get-AzureADServicePrincipal` cmdlet PowerShell com `-ObjectId` a mensagem de erro disponível para listar o nome de visualização do ID da aplicação que está a ser utilizado.<br><br> Validar as permissões a esta aplicação e garantir que tem a [função de contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ao nível da subscrição. <br><br> A Equipa de Engenharia de Serviços de Migração da Azure Database está a trabalhar para restringir o acesso necessário à função de contribuição atual na subscrição. Se tiver um requisito de negócio que não permita o uso de papel de contribuição, contacte o suporte da Azure para obter ajuda adicional. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Erro ao eliminar o NIC associado ao Serviço de Migração da Base de Dados Azure

Quando tenta eliminar um Cartão de Interface de Rede associado ao Serviço de Migração da Base de Dados Azure, a tentativa de eliminação falha com este erro:

* **Erro**: Não é possível eliminar o NIC associado ao Serviço de Migração da Base de Dados Azure devido ao serviço DMS que utiliza o NIC

| Causa         | Resolução    |
| ------------- | ------------- |
| Esta questão acontece quando a instância do Serviço de Migração da Base de Dados Azure ainda pode estar presente e consumir o NIC. <br><br><br><br><br><br><br><br> | Para eliminar este NIC, elimine a instância de serviço DMS que elimina automaticamente o NIC utilizado pelo serviço.<br><br> **Importante**: Certifique-se de que a instância do Serviço de Migração da Base de Dados Azure a ser eliminada não tem atividades de funcionamento.<br><br> Depois de todos os projetos e atividades associados à instância do Serviço de Migração da Base de Dados Azure forem eliminados, pode eliminar a instância de serviço. O NIC utilizado pela instância de serviço é automaticamente limpo como parte da eliminação de serviço. |

## <a name="connection-error-when-using-expressroute"></a>Erro de ligação ao utilizar o ExpressRoute

Quando tenta ligar à origem no assistente de projeto do serviço de Migração de Base de Dados do Azure, a ligação falhará após expirar o tempo limite prolongado se a origem estiver a utilizar o ExpressRoute para fins de conectividade.

| Causa         | Resolução    |
| ------------- | ------------- |
| Ao utilizar o [ExpressRoute](https://azure.microsoft.com/services/expressroute/), o Serviço de Migração da Base de [Dados](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) Azure requer a prestação de três pontos finais de serviço na sub-rede de Rede Virtual associada ao serviço:<br> -- Ponto final do autocarro de serviço<br> -- Ponto final de armazenamento<br> -- Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB)<br><br><br><br><br> | [Ativar](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) os pontos finais de serviço necessários para a conectividade ExpressRoute entre a fonte e o Serviço de Migração da Base de Dados Azure. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Bloqueie o erro de tempo de espera ao migrar uma base de dados MySQL para Azure DB para o MySQL

Quando migra uma base de dados MySQL para uma base de dados Azure para o caso MySQL através do Serviço de Migração da Base de Dados Azure, a migração falha com o seguinte erro de tempo de espera de espera:

* **Erro**: Erro de migração da base de dados - Não conseguiu carregar o ficheiro - Falhou no início do processo de carga para o ficheiro 'n' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 Mensagem: [MySQL][ODBC Driver][mysqld] Lock wait timeout excedido; tentar reiniciar transação

| Causa         | Resolução    |
| ------------- | ------------- |
| Este erro ocorre quando a migração falha devido ao tempo de espera do bloqueio durante a migração. | Considere aumentar o valor do parâmetro do servidor **'innodb_lock_wait_timeout'.** O valor mais alto permitido é 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Erro de ligação ao servidor SQL de origem ao utilizar uma porta dinâmica ou uma instância nomeada

Quando tenta ligar o Serviço de Migração da Base de Dados Azure à fonte do SQL Server que funciona em casos ou numa porta dinâmica, a ligação falha com este erro:

* **Erro**: -1 - A ligação SQL falhou. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Verifique se o nome da instância está correto e que o SQL Server está configurado para permitir ligações remotas. (fornecedor: SqL Network Interfaces, erro: 26 - Servidor/Instância de Localização de Erros Especificado)

| Causa         | Resolução    |
| ------------- | ------------- |
| Este problema ocorre quando a origem SQL Server exemplo que O Serviço de Migração de Bases de Dados Azure tenta ligar-se a uma porta dinâmica ou está a usar uma instância nomeada. O serviço SQL Server Browser ouve a porta UDP 1434 para ligações de entrada a um caso nomeado ou quando utiliza uma porta dinâmica. A porta dinâmica pode mudar cada vez que o serviço SQL Server reinicia. Pode verificar a porta dinâmica atribuída a uma instância através da configuração da rede no SqL Server Configuration Manager.<br><br><br> |Verifique se o Serviço de Migração da Base de Dados Azure pode ligar-se ao serviço de navegador de servidor SQL de origem na porta UDP 1434 e à instância do SQL Server através da porta TCP atribuída dinamicamente, conforme aplicável. |

## <a name="additional-known-issues"></a>Questões adicionais conhecidas

* [Questões conhecidas/limitações de migração com migrações online para Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Questões conhecidas/limitações de migração com migrações on-line para Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Questões conhecidas/limitações de migração com migrações on-line para Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passos seguintes

* Ver o artigo [Serviço de Migração de Bases de Dados Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Ver o artigo [Como configurar os parâmetros do servidor na Base de Dados Azure para o MySQL utilizando o portal Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Ver o artigo [Visão geral dos pré-requisitos para a utilização do Serviço de Migração da Base de Dados Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consulte as FAQ sobre a [utilização do Serviço de Migração da Base de Dados Azure.](https://docs.microsoft.com/azure/dms/faq)
