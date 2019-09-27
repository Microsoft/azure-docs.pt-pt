---
title: Restaurar um banco de dados SQL do Azure de um backup | Microsoft Docs
description: Saiba mais sobre a restauração pontual, que permite reverter um banco de dados SQL do Azure para um ponto anterior no tempo (até 35 dias).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: 11a7556954ff40183811d8e824011b818e4645df
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327560"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar um banco de dados SQL do Azure usando backups de banco de dados automático

Por padrão, os backups do banco de dados SQL são armazenados no armazenamento de BLOBs replicados geograficamente (RA-GRS). As seguintes opções estão disponíveis para recuperação de banco de dados usando [backups de banco de dados automatizados](sql-database-automated-backups.md):

- Crie um novo banco de dados no mesmo servidor de banco de dados SQL recuperado para um determinado momento dentro do período de retenção.
- Crie um banco de dados no mesmo servidor do banco de dados SQL recuperado para a hora de exclusão de um banco de dados excluído.
- Crie um novo banco de dados em qualquer servidor de banco de dados SQL na mesma região recuperada para o ponto dos backups mais recentes.
- Crie um novo banco de dados em qualquer servidor de banco de dados SQL em qualquer outra região recuperada para o ponto dos backups replicados mais recentes.

Se você configurou o [backup de retenção de longo prazo](sql-database-long-term-retention.md), também poderá criar um novo banco de dados a partir de qualquer backup EPD em qualquer servidor de banco de dados SQL.

> [!IMPORTANT]
> Não é possível substituir um banco de dados existente durante a restauração.

Ao usar a camada de serviço Standard ou Premium, um banco de dados restaurado gera um custo de armazenamento extra sob as seguintes condições:

- Restauração de P11 – P15 para S4-S12 ou P1 – P6 se o tamanho máximo do banco de dados for maior que 500 GB.
- Restauração de P1 – P6 para S4-S12 se o tamanho máximo do banco de dados for maior que 250 GB.

O custo extra é incorrido quando o tamanho máximo do banco de dados restaurado é maior do que a quantidade de armazenamento incluída na camada de serviço e no nível de desempenho do banco de dados de destino. O armazenamento extra provisionado acima do valor incluído é cobrado como extra. Para obter detalhes de preços de armazenamento extra, consulte a [página de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor que a quantidade de armazenamento incluída, você poderá evitar esse custo extra definindo o tamanho máximo do banco de dados para o valor incluído.

> [!NOTE]
> [Backups de banco de dados automatizados](sql-database-automated-backups.md) são usados quando você cria uma [cópia de banco de dados](sql-database-copy.md).

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar um banco de dados usando backups automatizados de banco de dados é afetado por vários fatores:

- O tamanho do banco de dados
- O tamanho de computação do banco de dados
- O número de logs de transações envolvidos
- A quantidade de atividade que precisa ser repetida para recuperação no ponto de restauração
- A largura de banda da rede se a restauração for para uma região diferente
- O número de solicitações de restauração simultâneas sendo processadas na região de destino

Para um banco de dados grande e/ou muito ativo, a restauração pode levar várias horas. Se houver uma interrupção prolongada em uma região, é possível que haja um grande número de solicitações de restauração geográfica sendo processadas por outras regiões. Quando há muitas solicitações, o tempo de recuperação pode aumentar para os bancos de dados nessa região. A maioria das restaurações de banco de dados é concluída em menos de 12 horas.

Para uma única assinatura, há limitações no número de solicitações de restauração simultâneas.  Essas limitações se aplicam a qualquer combinação de restaurações point-in-time, restaurações geográficas e restaurações do backup de retenção de longo prazo):

| | **Número máximo de solicitações simultâneas sendo processadas** | **Número máximo de solicitações simultâneas sendo enviadas** |
| :--- | --: | --: |
|Banco de dados individual (por assinatura)|10|60|
|Pool elástico (por pool)|4|200|
||||

Atualmente, não há um método interno para restaurar todo o servidor. O [banco de dados SQL do Azure: O script de](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) recuperação de servidor completo é um exemplo de como você pode realizar essa tarefa.

> [!IMPORTANT]
> Para recuperar usando backups automatizados, você deve ser membro da função de colaborador de SQL Server na assinatura ou ser o proprietário da assinatura [-consulte RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md). Você pode recuperar usando portal do Azure, PowerShell ou a API REST. Não é possível utilizar Transact-SQL.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Você pode restaurar um banco de dados autônomo, em pool ou de instância para um ponto anterior no tempo usando portal do Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases). A solicitação pode especificar qualquer camada de serviço ou tamanho de computação para o banco de dados restaurado. Verifique se você tem recursos suficientes no servidor para o qual você está restaurando o banco de dados. Depois de concluído, um novo banco de dados será criado no mesmo servidor que o banco de dados original. O banco de dados restaurado será cobrado com taxas normais com base em sua camada de serviço e tamanho de computação. Você não incorrerá em encargos até que a restauração do banco de dados seja concluída.

Geralmente, você restaura um banco de dados para um ponto anterior para fins de recuperação. Você pode tratar o banco de dados restaurado como uma substituição para o banco de dados original ou usá-lo como um dado de origem para atualizar o banco original.

- **Substituição de banco de dados**

  Se o banco de dados restaurado for destinado a uma substituição para o banco de dados original, você deverá especificar o tamanho de computação e a camada de serviço do banco de dados original. Em seguida, você pode renomear o banco de dados original e dar ao banco de dados restaurado o nome original usando o comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) no T-SQL.

- **Recuperação de dados**

  Se você planeja recuperar dados do banco de dado restaurado para se recuperar de um erro de usuário ou aplicativo, você precisa gravar e executar um script de recuperação de dados que extrai dados do banco de dado restaurado e aplica-se ao banco de dado original. Embora a operação de restauração possa levar muito tempo para ser concluída, o banco de dados de restauração fica visível na lista de banco de dados durante o processo de restauração. Se você excluir o banco de dados durante a restauração, a operação de restauração será cancelada e você não será cobrado pelo banco de dados que não concluiu a restauração.

Para recuperar um banco de dados único, em pool ou de instância para um ponto no tempo usando portal do Azure, abra a página do banco de dados e clique em **restaurar** na barra de ferramentas. Escolha backup origem e selecione o ponto de backup pontual do qual um novo banco de dados será criado.

![tempo de restauração pontual](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

> [!IMPORTANT]
> Para restaurar programaticamente um banco de dados de um backup, consulte [executando programaticamente a recuperação usando backups automatizados](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Restauração de banco de dados excluída

Você pode restaurar um banco de dados excluído para a hora de exclusão ou para um ponto anterior no mesmo servidor de banco de dados SQL usando portal do Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou o [REST (= Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Você pode [restaurar o banco de dados excluído no instância gerenciada usando o PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Para obter um exemplo de script do PowerShell mostrando como restaurar um banco de dados excluído, consulte [restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Se você excluir uma instância de servidor do banco de dados SQL do Azure, todos os seus bancos também serão excluídos e não poderão ser recuperados. Atualmente, não existe suporte para restaurar um servidor eliminado.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Restauração de banco de dados excluída usando o portal do Azure

Para recuperar um banco de dados excluído usando o portal do Azure, abra a página de visão geral do servidor e clique em banco de dados **excluídos** no menu de navegação.

![excluído-banco de dados-restauração](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

> [!IMPORTANT]
> Para restaurar programaticamente um banco de dados excluído, consulte [programaticamente executando a recuperação usando backups automatizados](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Georrestauro

Você pode restaurar um banco de dados SQL em qualquer servidor em qualquer região do Azure dos backups replicados geograficamente mais recentes. A restauração geográfica usa um backup replicado geograficamente como sua origem. Ele pode ser solicitado mesmo que o banco de dados ou o datacenter esteja inacessível devido a uma interrupção.

A restauração geográfica é a opção de recuperação padrão quando seu banco de dados não está disponível devido a um incidente na região de hospedagem. Você pode restaurar o banco de dados para um servidor em qualquer outra região. Há um atraso entre o momento em que um backup é feito e quando ele é replicado geograficamente para um blob do Azure em uma região diferente. Como resultado, o banco de dados restaurado pode ter até uma hora atrás do banco de dados original. A ilustração a seguir mostra a restauração do banco de dados do último backup disponível em outra região.

![restauração geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-using-azure-portal"></a>Restauração geográfica usando portal do Azure

O conceito geral para restaurar geograficamente um banco de dados do portal do Azure é executado por meio da criação de um novo banco de dados de instância única ou gerenciada e a seleção de um backup de restauração geográfica disponível na tela criar banco de dados. O banco de dados recém-criado conterá o backup com restauração geográfica.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para restaurar geograficamente um único banco de dados SQL do Azure de portal do Azure na região e no servidor de sua escolha, siga estas etapas:

1. Clique em Adicionar **+ Adicionar** no Marketplace e selecione **criar banco de dados SQL**, preencha as informações necessárias na **guia noções básicas**
2. Selecione a guia **configurações adicionais**
3. Em usar dados existentes, clique em **backup**
4. Selecione um backup na lista suspensa de backups de restauração geográfica disponíveis

![restauração geográfica de um único banco de dados SQL do Azure](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Conclua o processo de criação de um novo banco de dados. Depois que o banco de dados SQL único do Azure for criado, ele conterá o backup de restauração geográfica restaurado.

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Para restaurar geograficamente a instância gerenciada do banco de dados de portal do Azure para uma instância gerenciada existente em uma região de sua escolha, selecione uma instância gerenciada na qual você deseja que um banco de dados seja restaurado e siga estas etapas:

1. Clique em **+ novo banco de dados**
2. Digite um nome de banco de dados desejado
3. Em usar dados existentes selecione a opção **backup**
4. Selecione um backup na lista suspensa de backups de restauração geográfica disponíveis

![banco de dados de instância gerenciada de restauração geográfica](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Conclua o processo de criação de um novo banco de dados. Depois que o banco de dados de instância for criado, ele conterá o backup de restauração geográfica restaurado.

### <a name="geo-restore-using-powershell"></a>Restauração geográfica usando o PowerShell

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para o script do PowerShell mostrando como executar a restauração geográfica para um único banco de dados SQL do Azure, consulte [usar o PowerShell para restaurar um banco de dados do SQL Azure individual para um ponto anterior no tempo](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Para o script do PowerShell mostrando como executar a restauração geográfica para um banco de dados de instância gerenciada, consulte [usar o PowerShell para restaurar um banco de dados de instância gerenciada para outra região geográfica](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considerações sobre restauração geográfica

Atualmente, não há suporte para a restauração pontual em um secundário geográfico. A restauração pontual só pode ser feita em um banco de dados primário. Para obter informações detalhadas sobre como usar a restauração geográfica para se recuperar de uma interrupção, consulte [recuperar de uma interrupção](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> A restauração geográfica é a solução de recuperação de desastre mais básica disponível no banco de dados SQL. Ele depende de backups replicados geograficamente criados automaticamente com RPO = 1 hora e o tempo de recuperação estimado de até 12 horas. Ele não garante que a região de destino terá a capacidade de restaurar seus bancos de dados após uma interrupção regional, pois será provável que um aumento preciso da demanda. Para um aplicativo não comercialmente crítico que usa bancos de dados relativamente pequenos, a restauração geográfica é uma solução de recuperação de desastre apropriada. Para aplicativos críticos para os negócios que usam grandes bancos de dados e devem garantir a continuidade dos negócios, você deve usar [grupos de failover automático](sql-database-auto-failover-group.md). Ele oferece um RPO e RTO muito menores, e a capacidade é sempre garantida. Para obter mais informações sobre as opções de continuidade de negócios, consulte [visão geral da continuidade dos negócios](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Executando programaticamente a recuperação usando backups automatizados

Conforme discutido anteriormente, além de portal do Azure, a recuperação de banco de dados pode ser realizada programaticamente usando Azure PowerShell ou a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm têm uma grande extensão idêntica.

- Para restaurar um banco de dados autônomo ou em pool, consulte [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém uma base de dados eliminada que pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém uma cópia de segurança georredundante de uma base de dados. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura uma base de dados SQL. |

  > [!TIP]
  > Para obter um exemplo de script do PowerShell que mostra como executar uma restauração pontual de um banco de dados, consulte [restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).

- Para restaurar um banco de dados Instância Gerenciada, consulte [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Obtém uma ou mais instâncias gerenciadas. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Obtém um banco de dados de instância. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaura um banco de dados de instância. |

### <a name="rest-api"></a>API REST

Para restaurar um banco de dados individual ou em pool usando a API REST:

| API | Descrição |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restaura um banco de dados |
| [Obter o status de criação ou atualização do banco de dados](https://docs.microsoft.com/rest/api/sql/operations) |Retorna o status durante uma operação de restauração |

### <a name="azure-cli"></a>CLI do Azure

- Para restaurar um banco de dados individual ou em pool usando CLI do Azure, consulte [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).
- Para restaurar uma instância gerenciada usando CLI do Azure, consulte [AZ SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Resumo

Os backups automáticos protegem seus bancos de dados contra erros de usuário e aplicativo, exclusão acidental de banco de dados e interrupções prolongadas. Esse recurso interno está disponível para todas as camadas de serviço e tamanhos de computação.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral e cenários de continuidade de negócios, consulte [visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do banco [](sql-database-automated-backups.md)de dados SQL
- Para saber mais sobre retenção de longo prazo, confira [retenção de longo prazo](sql-database-long-term-retention.md).
- Para saber mais sobre as opções de recuperação mais rápidas, consulte [replicação geográfica ativa](sql-database-active-geo-replication.md) ou [grupos de failover automático](sql-database-auto-failover-group.md).
