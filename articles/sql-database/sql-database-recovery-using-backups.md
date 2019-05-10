---
title: Restaurar uma base de dados SQL do Azure a partir de uma cópia de segurança | Documentos da Microsoft
description: Saiba mais sobre o restauro de ponto no tempo, o que permite-lhe reverter uma base de dados do SQL do Azure para um ponto anterior no tempo (até 35 dias).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/30/2019
ms.openlocfilehash: 47bf59adb33f3685b31430c652b31880d383833e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232654"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar uma base de dados SQL do Azure com cópias de segurança da base de dados automatizada

Por predefinição, as cópias de segurança da base de dados SQL são armazenadas no armazenamento de BLOBs georreplicado (RA-GRS). As seguintes opções estão disponíveis para recuperação de base de dados com [cópias de segurança da base de dados automatizadas](sql-database-automated-backups.md):

- Crie uma nova base de dados no mesmo servidor de base de dados SQL recuperado para um determinado ponto no tempo dentro do período de retenção.
- Crie uma base de dados no mesmo servidor de base de dados SQL recuperado para o tempo de eliminação de uma base de dados eliminada.
- Crie uma nova base de dados em qualquer servidor de base de dados SQL na mesma região recuperado para o ponto de cópias de segurança mais recentes.
- Crie uma nova base de dados em qualquer servidor de base de dados SQL em qualquer outra região recuperado para o ponto das cópias de segurança replicados mais recentes.

Se configurou [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md), também pode criar uma nova base de dados a partir de qualquer cópia de segurança LTR em qualquer servidor de base de dados SQL.

> [!IMPORTANT]
> Não é possível substituir a base de dados existente durante o restauro.

Ao utilizar o escalão de serviço Standard ou Premium, uma base de dados restaurada implica um custo de armazenamento extra nas seguintes condições:

- Restauro de P11-P15 para S4-S12 ou P1 – P6 se o tamanho máximo da base de dados for superior a 500 GB.
- Restauro de P1 – P6 para S4-S12 se o tamanho máximo da base de dados for superior a 250 GB.

O extra custo é icurred quando o tamanho máximo da base de dados restaurada é maior do que a quantidade de armazenamento incluída com o nível de desempenho e a camada de serviço do destino da base de dados. O armazenamento extra aprovisionado acima da quantidade incluída é cobrado extra. Para detalhes de armazenamento extra de preços, consulte a [base de dados SQL página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizado for inferior à quantidade de armazenamento incluída, isso pode ser evitado custos adicionais associados ao definir o tamanho máximo da base de dados para o montante incluído.

> [!NOTE]
> [Cópias de segurança da base de dados automatizadas](sql-database-automated-backups.md) são utilizadas quando cria uma [cópia da base de dados](sql-database-copy.md).

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar uma base de dados com cópias de segurança da base de dados automatizada é afetado por diversos fatores:

- O tamanho da base de dados
- O tamanho de computação da base de dados
- O número de registos de transações envolvidos
- A quantidade de atividade que tem de ser reproduzido para recuperar para o ponto de restauro
- A largura de banda de rede se o restauro estiver numa região diferente
- O número de pedidos de restauro em simultâneo a ser processado na região de destino

Para uma base de dados grandes e/ou muito ativo, o restauro pode demorar várias horas. Se houver um interrupção prolongada numa região, é possível que há um grande número de pedidos de restauro geográfico que está a ser processados pelo noutras regiões. Quando existem muitos pedidos, pode aumentar o tempo de recuperação para bases de dados nessa região. A maioria das bases de dados restaura concluídas em menos de 12 horas.

Para uma única subscrição, existem limitações sobre o número de pedidos de restauro em simultâneo.  Essas limitações aplicam-se a qualquer combinação de ponto no tempo restauros, geo restauros e restaurações de cópia de segurança de retenção longo prazo):

| | **N. º máximo de pedidos simultâneos a ser processado** | **N. º máximo de pedidos simultâneos a ser submetida** |
| :--- | --: | --: |
|Base de dados (por subscrição)|10|60|
|Conjunto elástico (por conjunto)|4|200|
||||

Atualmente não há um método incorporado para restaurar o servidor completo. O [base de dados SQL do Azure: Total de recuperação do servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script é um exemplo de como é possível executar esta tarefa.

> [!IMPORTANT]
> Para recuperar ao utilizar cópias de segurança automáticas, tem de ser um membro da função de contribuinte do SQL Server na subscrição ou ser o proprietário da subscrição - veja [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md). Pode recuperar através do portal do Azure, do PowerShell ou da API REST. Não é possível utilizar Transact-SQL.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Pode restaurar uma autónoma, agrupada, ou instância de base de dados para um ponto anterior no tempo, com o portal do Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), ou o [REST API](https://docs.microsoft.com/rest/api/sql/databases). O pedido pode especificar qualquer camada de serviço ou tamanho da base de dados restaurada de computação. Certifique-se de que tem recursos suficientes no servidor ao qual está a restaurar a base de dados. Depois de concluído, será criada uma nova base de dados no mesmo servidor da base de dados original. A base de dados restaurada será cobrado às tarifas normais com base no respetivo escalão de serviço e o tamanho de computação. Que não incorra em custos até que o restauro de base de dados esteja concluído.

Geralmente, restaurar uma base de dados para um ponto anterior para fins de recuperação. Pode tratar a base de dados restaurada como um substituto para a base de dados original ou utilizá-la como uma origem de dados para atualizar a base de dados original.

- **Substituição de base de dados**

  Se a base de dados restaurada destina-se como um substituto para a base de dados original, deve especificar o tamanho de computação da base de dados orinal e escalão de serviço. Em seguida, pode mudar o nome da base de dados original e dar a base de dados restaurada ao nome original utilizando o [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) comando em T-SQL.

- **Recuperação de dados**

  Se pretender recuperar dados de base de dados restaurada para recuperar a partir de um erro de utilizador ou aplicação, terá de escrever e executar um script de recuperação de dados que extrai dados de base de dados restaurada e aplica-se à base de dados original. Embora a operação de restauro pode demorar muito tempo a concluir, a base de dados de restauro está visível na lista de base de dados em todo o processo de restauro. Se eliminar a base de dados durante o restauro, a operação de restauro, será cancelada e não será cobrada para a base de dados que não foi possível concluir o restauro.

Para recuperar uma única, agrupada, ou de instância da base de dados para um ponto no tempo, com o portal do Azure, abra a página da base de dados e clique em **restaurar** na barra de ferramentas.

![ponto de restauro anterior no tempo](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Para restaurar uma base de dados por meio de programação a partir de uma cópia de segurança, consulte o artigo [através de recuperação de desempenho por meio de programação de cópias de segurança automatizadas](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Restauro de base de dados eliminada

Pode restaurar uma base de dados eliminada para a hora de eliminação ou um ponto anterior no tempo no mesmo servidor de base de dados SQL com o portal do Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), ou o [REST (createMode = restaurar)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Pode [restauro eliminado a base de dados na instância gerida com o PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Para um script do PowerShell de exemplo que mostra como restaurar uma base de dados eliminada, consulte [restaurar uma base de dados SQL com o PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Se eliminar uma instância de servidor de base de dados do Azure SQL, todas as suas bases de dados também são eliminados e não podem ser recuperados. Atualmente não há suporte para restaurar um servidor foi eliminado.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Restauro de base de dados eliminada através do portal do Azure

Para recuperar uma base de dados eliminada através do portal do Azure, abra a página do seu servidor e na área de operações, clique em **bases de dados eliminadas**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Para restaurar programaticamente uma base de dados eliminada, consulte [através de recuperação de desempenho por meio de programação de cópias de segurança automatizadas](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Georrestauro

Pode restaurar uma base de dados SQL em qualquer servidor em qualquer região do Azure a partir de cópias de segurança georreplicado mais recentes. O restauro geográfico utiliza uma cópia de segurança georreplicado como origem. Ele pode ser solicitado, mesmo se a base de dados ou o Centro de dados não está acessível devido a uma falha.

Georrestauro encontra-se a opção de recuperação predefinida quando a sua base de dados está indisponível devido a um incidente na região de alojamento. Pode restaurar a base de dados para um servidor em qualquer outra região. Existe um atraso entre quando é feita uma cópia de segurança e quando é georreplicado a Azure blob numa região diferente. Como resultado, a base de dados restaurada pode ser por trás da base de dados do orignal até uma hora. A ilustração seguinte mostra o restauro da base de dados da cópia de segurança disponível última noutra região.

![Restauro geográfico](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Para um script do PowerShell de exemplo que mostra como efetuar um georrestauro, consulte [restaurar uma base de dados SQL com o PowerShell](scripts/sql-database-restore-database-powershell.md).

Restauro de ponto no tempo na geo-secundária não é atualmente suportado. Restauro de ponto no tempo pode ser feito apenas numa base de dados primária. Para obter informações detalhadas sobre como utilizar o restauro geográfico para recuperar a partir de uma falha, consulte [recuperar a partir de um período de indisponibilidade](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Georrestauro encontra-se a solução de recuperação de desastre mais básica, disponível na base de dados SQL. Ele conta com criados automaticamente cópias de segurança georreplicado com RPO = 1 hora e o tempo de recuperação estimado de até 12 horas. Não garantem que tenham a capacidade para restaurar os bancos de dados após um ourage regional porque um aumento sharp de procura, será provável que a região de destino. Para não críticas aplicativo de negócios que utilizar bases de dados relativamente pequenos, o restauro geográfico é uma solução de recuperação após desastre apropriado. Para busniess aplicativos críticos que utilizam bases de dados grandes e devem garantir a continuidade do negócio, deve usar [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md). Oferece um RPO e RTO muito mais baixo e a capacidade é sempre garantida. Para obter mais informações sobre as opções de continuidade do negócio, veja [descrição geral da continuidade do negócio](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Georrestauro com o portal do Azure

Para uma base de dados durante o restauro geográfico seus [período de retenção do modelo baseado em DTU](sql-database-service-tiers-dtu.md) ou [período de retenção do modelo baseado em vCore](sql-database-service-tiers-vcore.md) com o portal do Azure, abra a página de bases de dados SQL e, em seguida, clique em **adicionar** . Na **selecionar origem** caixa de texto, selecione **cópia de segurança**. Especifique a cópia de segurança a partir da qual pretende efetuar a recuperação, na região e no servidor da sua preferência.

> [!Note]
> Georrestauro com o portal do Azure não está disponível na instância gerida. Utilize o PowerShell.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>A execução através de programação de recuperação com cópias de segurança automáticas

Como foi discutido anteriormente, além de portal do Azure, a recuperação de base de dados pode ser executada por meio de programação com o Azure PowerShell ou a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- Para restaurar um autónoma ou de bases de dados agrupadas, consulte [restauro AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém uma base de dados eliminada que pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém uma cópia de segurança georredundante de uma base de dados. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura uma base de dados SQL. |

  > [!TIP]
  > Para um script do PowerShell de exemplo que mostra como executar um restauro de ponto no tempo de uma base de dados, consulte [restaurar uma base de dados SQL com o PowerShell](scripts/sql-database-restore-database-powershell.md).

- Para restaurar uma base de dados de instância gerida, consulte [restauro AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Obtém um ou mais instâncias geridas. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Obtém uma instância de bases de dados. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaura uma base de dados de instância. |

### <a name="rest-api"></a>API REST

Para restaurar uma base de dados individual ou agrupada através da API REST:

| API | Descrição |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restaura uma base de dados |
| [Começar a criar ou atualizar o estado da base de dados](https://docs.microsoft.com/rest/api/sql/operations) |Devolve o estado durante uma operação de restauro |

### <a name="azure-cli"></a>CLI do Azure

- Para restaurar uma base de dados individual ou agrupada com a CLI do Azure, consulte [restauro do az sql db](/cli/azure/sql/db#az-sql-db-restore).
- Para restaurar uma instância gerida com a CLI do Azure, consulte [restauro de midb az sql](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Resumo

Cópias de segurança automáticas proteger as bases de dados de utilizador e erros de aplicações, a eliminação acidental de base de dados e interrupções prolongadas. Esta capacidade incorporada está disponível para todos os escalões de serviço e tamanhos de computação.

## <a name="next-steps"></a>Passos Seguintes

- Para uma visão geral de continuidade de negócio e cenários, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md).
- Para saber mais sobre SQL do Azure, base de dados automatizada de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre a retenção de longa duração, veja [retenção a longo prazo](sql-database-long-term-retention.md).
- Para saber mais sobre as opções de recuperação mais rápidas, veja [georreplicação ativa](sql-database-active-geo-replication.md) ou [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md).
