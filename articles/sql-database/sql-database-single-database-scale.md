---
title: Dimensionar recursos de banco de dados individual-banco de dados SQL do Azure
description: Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para um único banco de dados no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 7f3e0c6a5f2d3594e8a03ed4034248b1fd43c73d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687172"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Dimensionar recursos de banco de dados individual no banco de dados SQL do Azure

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para um banco de dados SQL do Azure na camada de computação provisionada. Como alternativa, a [camada de computação sem servidor](sql-database-serverless.md) fornece o dimensionamento automático de computação e as listas por segundo para computação usada.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

## <a name="change-compute-size-vcores-or-dtus"></a>Alterar o tamanho da computação (vCores ou DTUs)

Depois de escolher inicialmente o número de vCores ou DTUs, você pode dimensionar um banco de dados individual verticalmente com base na experiência real usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), o [PowerShell](/powershell/module/az.sql/set-azsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az-sql-db-update)ou a [API REST ](https://docs.microsoft.com/rest/api/sql/databases/update).

O vídeo a seguir mostra a alteração dinâmica da camada de serviço e o tamanho da computação para aumentar as DTUs disponíveis para um único banco de dados.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto da alteração da camada de serviço ou redimensionamento do tamanho da computação

Alterar a camada de serviço ou o tamanho da computação envolve principalmente o serviço executando as seguintes etapas:

1. Criar nova instância de computação para o banco de dados  

    Uma nova instância de computação é criada com a camada de serviço e o tamanho de computação solicitados. Para algumas combinações de camada de serviço e alterações de tamanho de computação, uma réplica do banco de dados deve ser criada na nova instância de computação que envolve a cópia de dados e pode influenciar de forma forte a latência geral. Independentemente do, o banco de dados permanece online durante essa etapa, e as conexões continuam a ser direcionadas para o banco de dados na instância de computação original.

2. Alternar o roteamento de conexões para a nova instância de computação

    As conexões existentes com o banco de dados na instância de computação original são descartadas. Todas as novas conexões são estabelecidas com o banco de dados na nova instância de computação. Para algumas combinações de camada de serviço e alterações de tamanho de computação, os arquivos de banco de dados são desanexados e reanexados durante o comutador.  Independentemente disso, a opção pode resultar em uma breve interrupção do serviço quando o banco de dados não está disponível geralmente por menos de 30 segundos e geralmente por apenas alguns segundos. Se houver transações de longa execução em execução quando as conexões forem descartadas, a duração dessa etapa poderá levar mais tempo para recuperar as transações anuladas. A [recuperação de banco de dados acelerada](sql-database-accelerated-database-recovery.md) pode reduzir o impacto da anulação de transações de longa execução.

> [!IMPORTANT]
> Nenhum dado é perdido durante qualquer etapa no fluxo de trabalho. Verifique se você implementou alguma [lógica de repetição](sql-database-connectivity-issues.md) nos aplicativos e componentes que estão usando o banco de dados SQL do Azure enquanto a camada de serviço é alterada.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latência da alteração da camada de serviço ou redimensionamento do tamanho da computação

A latência estimada para alterar a camada de serviço ou redimensionar o tamanho de computação de um único banco de dados ou pool elástico é parametrizada da seguinte maneira:

|Camada de serviços|Banco de dados individual básico,</br>Padrão (S0-S1)|Pool elástico básico,</br>Standard (S2-S12), </br>Em hiperescala </br>Uso Geral banco de dados individual ou pool elástico|Banco de dados único ou pool elástico Premium ou Comercialmente Crítico|
|:---|:---|:---|:---|
|**Banco de dados individual básico,</br> Standard (S0-S1)**|&bull; &nbsp;latência de tempo constante independente do espaço usado</br>&bull; &nbsp;normalmente, menos de 5 minutos|&bull; &nbsp;latência proporcional ao espaço de banco de dados usado devido à cópia de dado</br>&bull; &nbsp;normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;latência proporcional ao espaço de banco de dados usado devido à cópia de dado</br>&bull; &nbsp;normalmente, menos de 1 minuto por GB de espaço usado|
|**Pool elástico básico, </br>Standard (S2-S12), </br>hiperescala </br>Uso Geral banco de dados individual ou pool elástico**|&bull; &nbsp;latência proporcional ao espaço de banco de dados usado devido à cópia de dado</br>&bull; &nbsp;normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;latência de tempo constante independente do espaço usado</br>&bull; &nbsp;normalmente, menos de 5 minutos|&bull; &nbsp;latência proporcional ao espaço de banco de dados usado devido à cópia de dado</br>&bull; &nbsp;normalmente, menos de 1 minuto por GB de espaço usado|
|**Banco de dados único ou pool elástico Premium ou Comercialmente Crítico**|&bull; &nbsp;latência proporcional ao espaço de banco de dados usado devido à cópia de dado</br>&bull; &nbsp;normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;latência proporcional ao espaço de banco de dados usado devido à cópia de dado</br>&bull; &nbsp;normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;latência proporcional ao espaço de banco de dados usado devido à cópia de dado</br>&bull; &nbsp;normalmente, menos de 1 minuto por GB de espaço usado|

> [!TIP]
> Para monitorar as operações em andamento, consulte: [gerenciar operações usando a API REST do SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [gerenciar operações usando a CLI](/cli/azure/sql/db/op), [monitorar operações usando o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos do PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [ Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Cancelando alterações na camada de serviço ou operações de redimensionamento de computação

Uma operação de alteração de camada de serviço ou redimensionamento de computação pode ser cancelada.

#### <a name="azure-portal"></a>Portal do Azure

Na folha visão geral do banco de dados, navegue até **notificações** e clique no bloco indicando que há uma operação em andamento:

![Operação em andamento](media/sql-database-single-database-scale/ongoing-operations.png)

Em seguida, clique no botão rotulado **cancelar esta operação**.

![Cancelar operação em andamento](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Em um prompt de comando do PowerShell, defina o `$ResourceGroupName`, `$ServerName`e `$DatabaseName`e, em seguida, execute o seguinte comando:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerações adicionais ao alterar a camada de serviço ou redimensionar o tamanho da computação

- Se você estiver atualizando para uma camada de serviço ou tamanho de computação superior, o tamanho máximo do banco de dados não aumentará a menos que você especifique explicitamente um tamanho maior (MaxSize).
- Para fazer o downgrade de um banco de dados, o espaço usado do banco de dados deve ser menor do que o tamanho máximo permitido da camada de serviço de destino e do tamanho de computação.
- Ao fazer o downgrade de **Premium** para a camada **Standard** , um custo de armazenamento extra se aplicará se (1) o tamanho máximo do banco de dados tiver suporte no tamanho de computação de destino e (2) o tamanho máximo exceder o valor de armazenamento incluído do tamanho de computação de destino. Por exemplo, se um banco de dados P1 com um tamanho máximo de 500 GB for reduzido para S3, um custo de armazenamento extra será aplicado, pois o S3 dá suporte a um tamanho máximo de 1 TB e seu valor de armazenamento incluído é de apenas 250 GB. Portanto, o valor de armazenamento extra é 500 GB – 250 GB = 250 GB. Para obter o preço do armazenamento extra, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor que a quantidade de armazenamento incluída, esse custo extra poderá ser evitado, reduzindo o tamanho máximo do banco de dados para o valor incluído.
- Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para a camada de serviço e o tamanho de computação desejados antes de atualizar o banco de dados primário (orientação geral para o melhor desempenho). Ao atualizar para um diferente, primeiro é necessário atualizar o banco de dados secundário.
- Ao fazer downgrade de um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, faça o downgrade de seus bancos de dados primários para a camada de serviço e o tamanho de computação desejados antes de fazer downgrade do banco de dados secundário (orientação geral para o melhor desempenho). Ao fazer downgrade para uma edição diferente, primeiro é necessário fazer downgrade do banco de dados primário.
- As ofertas de serviço de restauro diferem entre os vários escalões de serviço. Se você estiver fazendo downgrade para a camada **básica** , haverá um período de retenção de backup menor. Consulte [backups do banco de dados SQL do Azure](sql-database-automated-backups.md).
- As novas propriedades para a base de dados não são aplicadas até que as alterações estejam concluídas.

### <a name="billing-during-compute-rescaling"></a>Cobrança durante o redimensionamento de computação

Você será cobrado por cada hora de existência de um banco de dados usando a camada de serviço mais alta + o tamanho de computação aplicado durante essa hora, independentemente do uso ou se o banco de dados esteve ativo por menos de uma hora. Por exemplo, se você criar um banco de dados individual e excluí-lo cinco minutos depois, sua fatura refletirá uma cobrança por uma hora de banco de dados.

## <a name="change-storage-size"></a>Alterar o tamanho do armazenamento

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser provisionado até o limite de tamanho máximo usando incrementos de 1 GB. O armazenamento de dados mínimo configurável é de 5 GB
- O armazenamento de um único banco de dados pode ser provisionado aumentando ou diminuindo seu tamanho máximo usando o [portal do Azure](https://portal.azure.com), o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), o [PowerShell](/powershell/module/az.sql/set-azsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az-sql-db-update)ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- O banco de dados SQL aloca automaticamente 30% de armazenamento adicional para os arquivos de log e 32 GB por vCore para TempDB, mas não para exceder 384GB. O TempDB está localizado em um SSD anexado em todas as camadas de serviço.
- O preço do armazenamento para um banco de dados individual é a soma das quantidades de armazenamento de dado e armazenamento de log multiplicada pelo preço unitário de armazenamento da camada de serviço. O custo do TempDB está incluído no preço vCore. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço de DTU para um banco de dados individual inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além do valor incluído pode ser provisionado por um custo adicional até o limite de tamanho máximo em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para valores de armazenamento incluídos e limites de tamanho máximo, consulte [banco de dados individual: tamanhos de armazenamento e tamanhos de computação](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- O armazenamento extra para um único banco de dados pode ser provisionado aumentando seu tamanho máximo usando o portal do Azure, o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), o [PowerShell](/powershell/module/az.sql/set-azsqldatabase), o [CLI do Azure](/cli/azure/sql/db#az-sql-db-update)ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- O preço do armazenamento extra para um único banco de dados é a quantidade de armazenamento extra multiplicada pelo preço unitário de armazenamento extra da camada de serviço. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Restrições de P11 e P15 quando o tamanho máximo for maior que 1 TB

Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto: Leste da China, Norte da China, Alemanha central, Alemanha nordeste, Oeste EUA Central, regiões de US DoD e central do governo dos EUA. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB. As seguintes considerações e limitações se aplicam aos bancos de dados P11 e P15 com um tamanho máximo maior que 1 TB:

- Se o tamanho máximo de um banco de dados P11 ou P15 fosse definido como um valor maior que 1 TB, ele só poderá ser restaurado ou copiado para um banco de dados P11 ou P15.  Subsequentemente, o banco de dados pode ser redimensionado para um tamanho de computação diferente, desde que a quantidade de espaço alocado no momento da operação de redimensionamento não exceda os limites de tamanho máximo do novo tamanho de computação.
- Para cenários de replicação geográfica ativa:
  - Configurando uma relação de replicação geográfica: se o banco de dados primário for P11 ou P15, os secundários também deverão ser P11 ou P15; o tamanho de computação inferior é rejeitado como secundários, pois eles não são capazes de dar suporte a mais de 1 TB.
  - Atualizando o banco de dados primário em um relacionamento de replicação geográfica: alterar o tamanho máximo para mais de 1 TB em um banco de dados primário dispara a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. As limitações de região para a opção mais de 1 TB se aplicam. Se o secundário estiver em uma região que não dá suporte a mais de 1 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de importação/exportação para carregar bancos de dados P11/P15 com mais de 1 TB. Use SqlPackage. exe para [importar](sql-database-import.md) e [Exportar](sql-database-export.md) dados.

## <a name="next-steps"></a>Passos seguintes

Para obter os limites de recursos gerais, consulte [limites de recursos baseados em vCore do banco de dados SQL-bancos](sql-database-vcore-resource-limits-single-databases.md) de dados individuais e [limites de recursos baseados em DTU do SQL Database-pools elásticos](sql-database-dtu-resource-limits-single-databases.md).
