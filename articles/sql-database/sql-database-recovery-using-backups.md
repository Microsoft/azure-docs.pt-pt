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
ms.date: 02/08/2019
ms.openlocfilehash: b4b514350e36e9fe71e7c5cb5cd978764e0108d5
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317191"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar uma base de dados SQL do Azure com cópias de segurança da base de dados automatizada

Por predefinição, as cópias de segurança da base de dados SQL são armazenadas no armazenamento de BLOBs georreplicado (RA-GRS). As seguintes opções estão disponíveis para recuperação de base de dados com [cópias de segurança da base de dados automatizadas](sql-database-automated-backups.md):

- Crie uma nova base de dados no mesmo servidor de base de dados SQL recuperado para um determinado ponto no tempo dentro do período de retenção.
- Crie uma base de dados no mesmo servidor de base de dados SQL recuperado para o tempo de eliminação de uma base de dados eliminada.
- Crie uma nova base de dados em qualquer servidor de base de dados SQL na mesma região recuperado para o ponto de cópias de segurança mais recentes.
- Crie uma nova base de dados em qualquer servidor de base de dados SQL em qualquer outra região recuperado para o ponto das cópias de segurança replicados mais recentes.

Se configurou [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md) também pode criar uma nova base de dados a partir de qualquer cópia de segurança LTR em qualquer servidor de base de dados SQL em qualquer região.

> [!IMPORTANT]
> Não é possível substituir a base de dados existente durante o restauro.

Ao utilizar o escalão de serviço Standard ou Premium, uma base de dados restaurada implica um custo de armazenamento extra nas seguintes condições:

- Restauro de P11-P15 para S4-S12 ou P1 – P6 se o tamanho máximo da base de dados for superior a 500 GB.
- Restauro de P1 – P6 para S4-S12 se o tamanho máximo da base de dados for superior a 250 GB.

O extra custo é porque o tamanho máximo da base de dados restaurada é maior do que a quantidade de armazenamento incluída para o tamanho de computação, e qualquer armazenamento extra aprovisionado acima da quantidade incluída é cobrado extra. Para detalhes de armazenamento extra de preços, consulte a [base de dados SQL página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizada é inferior à quantidade de armazenamento incluída, em seguida, isso encargos extra pode ser evitado ao reduzir o tamanho máximo da base de dados para o montante incluído.

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

Para uma base de dados muito grandes e/ou Active Directory, o restauro pode demorar várias horas. Se houver um interrupção prolongada numa região, é possível que há um grande número de pedidos de restauro geográfico que está a ser processados pelo noutras regiões. Quando existem muitos pedidos, pode aumentar o tempo de recuperação para bases de dados nessa região. A maioria das bases de dados restaura concluídas em menos de 12 horas.

Para uma única subscrição, existem algumas limitações no número de pedidos de restauro em simultâneo (incluindo o ponto de restauro para um tempo, o restauro geográfico e o restauro da cópia de segurança de retenção longo prazo) que está a ser submetidas e prosseguiram:

| | **N. º máximo de pedidos simultâneos a ser processado** | **N. º máximo de pedidos simultâneos a ser submetida** |
| :--- | --: | --: |
|Base de dados (por subscrição)|10|60|
|Conjunto elástico (por conjunto)|4|200|
||||

Não é uma funcionalidade incorporada em massa de restauro. O [base de dados SQL do Azure: Total de recuperação do servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script é um exemplo de uma maneira de realizar essa tarefa.

> [!IMPORTANT]
> Para recuperar ao utilizar cópias de segurança automáticas, tem de ser um membro da função de contribuinte do SQL Server na subscrição ou ser o proprietário da subscrição - veja [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md). Pode recuperar através do portal do Azure, do PowerShell ou da API REST. Não é possível utilizar Transact-SQL.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Pode restaurar uma autónoma, agrupada, ou instância de base de dados para um ponto anterior no tempo como uma nova base de dados no mesmo servidor com o portal do Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), ou o [REST API](https://docs.microsoft.com/rest/api/sql/databases). Uma base de dados pode ser restaurada a qualquer camada de serviço ou tamanho de computação. Certifique-se de que tem recursos suficientes no servidor ao qual está a restaurar a base de dados. Depois de concluído, a base de dados restaurada é uma base de dados online, normal e totalmente acessível. A base de dados restaurada é cobrada às tarifas normais com base no respetivo escalão de serviço e o tamanho de computação. Que não incorra em custos até que o restauro de base de dados esteja concluído.

Geralmente, restaurar uma base de dados para um ponto anterior para fins de recuperação. Ao fazê-lo, pode tratar a base de dados restaurada como um substituto para a base de dados original ou utilizá-lo a obter dados a partir e, em seguida, atualizar a base de dados original.

- **Substituição de base de dados**

  Se a base de dados restaurada destina-se como um substituto para a base de dados original, deve verificar o tamanho de computação e/ou camada de serviços, conforme adequada e dimensionar a base de dados, se necessário. Pode mudar o nome da base de dados original e, em seguida, dar a base de dados restaurada ao nome original utilizando o [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) comando em T-SQL.

- **Recuperação de dados**

  Se pretender recuperar dados de base de dados restaurada para recuperar a partir de um erro de utilizador ou aplicação, terá de escrever e executar os scripts de recuperação de dados necessários para extrair dados do banco de dados restaurado para a base de dados original. Embora a operação de restauro pode demorar muito tempo a concluir, a base de dados de restauro está visível na lista de base de dados em todo o processo de restauro. Se eliminar a base de dados durante o restauro, a operação de restauro foi cancelada e não lhe são cobradas da base de dados que não foi possível concluir o restauro.

Para recuperar uma única, agrupada, ou de instância da base de dados para um ponto no tempo, com o portal do Azure, abra a página da base de dados e clique em **restaurar** na barra de ferramentas.

![ponto de restauro anterior no tempo](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Para restaurar uma base de dados por meio de programação a partir de uma cópia de segurança, consulte o artigo [através de recuperação de desempenho por meio de programação de cópias de segurança automatizadas](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Restauro de base de dados eliminada

Pode restaurar uma base de dados eliminada para a hora de eliminação de uma base de dados eliminada no mesmo servidor de base de dados SQL com o portal do Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), ou o [REST (createMode = restaurar)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Pode [restauro eliminado a base de dados na instância gerida com o PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). Pode restaurar uma base de dados eliminada para um ponto anterior no tempo durante a utilização de retenção [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase).

> [!TIP]
> Para um script do PowerShell de exemplo que mostra como restaurar uma base de dados eliminada, consulte [restaurar uma base de dados SQL com o PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Se eliminar uma instância de servidor de base de dados do Azure SQL, todas as suas bases de dados também são eliminados e não podem ser recuperados. Atualmente não há suporte para restaurar um servidor foi eliminado.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Restauro de base de dados eliminada através do portal do Azure

Para recuperar uma base de dados eliminada, com o portal do Azure durante a respetiva [período de retenção do modelo baseado em DTU](sql-database-service-tiers-dtu.md) ou [período de retenção do modelo baseado em vCore](sql-database-service-tiers-vcore.md) com o portal do Azure, abra a página do seu servidor e, no Área de operações, clique em **bases de dados eliminadas**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Para restaurar programaticamente uma base de dados eliminada, consulte [através de recuperação de desempenho por meio de programação de cópias de segurança automatizadas](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Georrestauro

Pode restaurar uma base de dados SQL em qualquer servidor em qualquer região do Azure a partir de cópias de segurança georreplicado mais recentes. O restauro geográfico utiliza uma cópia de segurança georredundante como origem e pode ser usado para recuperar uma base de dados, mesmo que a base de dados ou o Centro de dados não está acessível devido a uma falha.

> [!Note]
> O restauro geográfico não está disponível na instância gerida.

Georrestauro encontra-se a opção de recuperação predefinida quando a sua base de dados está indisponível devido a um incidente na região onde está hospedado o banco de dados. Se um incidente em grande escala nos resultados da região na indisponibilidade da sua aplicação de base de dados, pode restaurar uma base de dados das cópias de segurança georreplicado para um servidor em qualquer outra região. Existe um atraso entre quando é feita uma cópia de segurança e quando é georreplicado a Azure blob numa região diferente. Este atraso pode demorar até uma hora, por isso, se ocorrer um desastre, pode haver até à perda de dados de uma hora. A ilustração seguinte mostra o restauro da base de dados da cópia de segurança disponível última noutra região.

![Restauro geográfico](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Para um script do PowerShell de exemplo que mostra como efetuar um georrestauro, consulte [restaurar uma base de dados SQL com o PowerShell](scripts/sql-database-restore-database-powershell.md).

Restauro de ponto no tempo na geo-secundária não é atualmente suportado. Restauro de ponto no tempo pode ser feito apenas numa base de dados primária. Para obter informações detalhadas sobre como utilizar o restauro geográfico para recuperar a partir de uma falha, consulte [recuperar a partir de um período de indisponibilidade](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Recuperação a partir de cópias de segurança é a mais básica das soluções de recuperação após desastre disponíveis no banco de dados SQL com o objetivo de ponto de recuperação mais longo (RPO) e o tempo de recuperação de estimativa (ERT). Para soluções que utilizar bases de dados de tamanho pequeno (camada de serviços por exemplo, Basic ou conjuntos elásticos de bases de dados de inquilinos de tamanho pequeno), o restauro geográfico com frequência é uma solução de DR razoável com um ERT de até 12 horas (geralmente muito menos). Para soluções com grandes bancos de dados e necessitam de recuperação menores vezes, deve considerar o uso [georreplicação ativa](sql-database-active-geo-replication.md) ou [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md). Replicação geográfica activa oferece um RPO de muito mais baixo e ERT uma vez que ele apenas requer que inicie uma ativação pós-falha para uma secundária replicada de forma contínua. Os grupos de ativação pós-falha automática permitem a ativação pós-falha automática para um grupo de bases de dados. Para obter mais informações sobre as opções de continuidade do negócio, veja [descrição geral da continuidade do negócio](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Georrestauro com o portal do Azure

Para uma base de dados durante o restauro geográfico seus [período de retenção do modelo baseado em DTU](sql-database-service-tiers-dtu.md) ou [período de retenção do modelo baseado em vCore](sql-database-service-tiers-vcore.md) com o portal do Azure, abra a página de bases de dados SQL e, em seguida, clique em **adicionar** . Na **selecionar origem** caixa de texto, selecione **cópia de segurança**. Especifique a cópia de segurança a partir da qual pretende efetuar a recuperação, na região e no servidor da sua preferência.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>A execução através de programação de recuperação com cópias de segurança automáticas

Como foi discutido anteriormente, além de portal do Azure, a recuperação de base de dados pode ser executada por meio de programação com o Azure PowerShell ou a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Para restaurar um autónoma ou de bases de dados agrupadas, consulte [AzSqlDatabase de restauro](https://docs.microsoft.com/powershell/module/az.sql/restore-azurermsqldatabase)

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém uma base de dados eliminada que pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém uma cópia de segurança georredundante de uma base de dados. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura uma base de dados SQL. |

  > [!TIP]
  > Para um script do PowerShell de exemplo que mostra como executar um restauro de ponto no tempo de uma base de dados, consulte [restaurar uma base de dados SQL com o PowerShell](scripts/sql-database-restore-database-powershell.md).

- Para restaurar uma base de dados de instância gerida, consulte [restaurodepontonotempodade uma base de dados do Azure instância gerida SQL biblioteca do PowerShell do azurerm. SQL](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../point-in-time-restore-of-a-database-on-azure-sql-managed-instance-using-Az-sql-powershell-library/)

### <a name="rest-api"></a>API REST

Para restaurar uma base de dados individual ou agrupada através da API REST:

| API | Descrição |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restaura uma base de dados |
| [Começar a criar ou atualizar o estado da base de dados](https://docs.microsoft.com/rest/api/sql/operations) |Devolve o estado durante uma operação de restauro |

### <a name="azure-cli"></a>CLI do Azure

Para restaurar uma base de dados individual ou agrupada com a CLI do Azure, consulte [restauro do az sql db](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-restore).

## <a name="summary"></a>Resumo

Cópias de segurança automáticas proteger as bases de dados de utilizador e erros de aplicações, a eliminação acidental de base de dados e interrupções prolongadas. Esta capacidade incorporada está disponível para todos os escalões de serviço e tamanhos de computação.

## <a name="next-steps"></a>Passos Seguintes

- Para uma visão geral de continuidade de negócio e cenários, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md).
- Para saber mais sobre SQL do Azure, base de dados automatizada de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre a retenção de longa duração, veja [retenção a longo prazo](sql-database-long-term-retention.md).
- Para saber mais sobre as opções de recuperação mais rápidas, veja [georreplicação ativa](sql-database-active-geo-replication.md) ou [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md).
