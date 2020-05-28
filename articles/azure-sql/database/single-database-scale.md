---
title: Dimensionar recursos de base de dados única
description: Este artigo descreve como escalar os recursos de cálculo e armazenamento disponíveis para uma única base de dados na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/30/2020
ms.openlocfilehash: cbd15e2356e9ceb781d7314cb9a0114d2d47d412
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041779"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Dimensionar recursos de base de dados individuais na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve como escalar os recursos de cálculo e armazenamento disponíveis para uma Base de Dados Azure SQL no nível de cálculo provisionado. Alternativamente, o nível de [computação sem servidor](serverless-tier-overview.md) fornece a escala automática computacional e as contas por segundo para a computação utilizada.

Depois de inicialmente escolher o número de vCores ou DTUs, pode escalar uma única base de dados para cima ou para baixo dinamicamente com base na experiência real utilizando o [portal Azure,](single-database-manage.md#azure-portal) [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)o [Azure CLI,](/cli/azure/sql/db#az-sql-db-update)ou o [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

O vídeo seguinte mostra a alteração dinâmica do nível de serviço e do tamanho da computação para aumentar as DTUs disponíveis para uma única base de dados.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](file-space-manage.md).

## <a name="impact"></a>Impacto

A alteração do nível de serviço ou do tamanho da computação envolve principalmente o serviço que executa os seguintes passos:

1. Criar nova instância de cálculo para a base de dados  

    Uma nova instância computacional é criada com o nível de serviço solicitado e tamanho da computação. Para algumas combinações de nível de serviço e alterações no tamanho da computação, uma réplica da base de dados deve ser criada no novo caso do cálculo, que envolve copiar dados e pode influenciar fortemente a latência geral. Independentemente disso, a base de dados permanece on-line durante este passo, e as ligações continuam a ser direcionadas para a base de dados na instância de cálculo original.

2. Altere o encaminhamento de ligações para uma nova instância de cálculo

    As ligações existentes à base de dados na instância de cálculo original são retiradas. Quaisquer novas ligações são estabelecidas na base de dados no novo caso do cálculo. Para algumas combinações de nível de serviço e alterações no tamanho da computação, os ficheiros de base de dados são separados e religados durante o interruptor.  Independentemente disso, o interruptor pode resultar numa breve interrupção de serviço quando a base de dados está geralmente indisponível por menos de 30 segundos e muitas vezes por apenas alguns segundos. Se houver transações de longo prazo a decorrer quando as ligações forem retiradas, a duração deste passo pode demorar mais tempo para recuperar as transações abortadas. [A recuperação acelerada da base](../accelerated-database-recovery.md) de dados pode reduzir o impacto de abortar transações de longo prazo.

> [!IMPORTANT]
> Não se perdem dados durante qualquer passo no fluxo de trabalho. Certifique-se de que implementou alguma lógica de [repetição](troubleshoot-common-connectivity-issues.md) nas aplicações e componentes que estão a utilizar a Base de Dados Azure SQL enquanto o nível de serviço é alterado.

## <a name="latency"></a>Latência

A latência estimada para alterar o nível de serviço, escalar o tamanho da computação de uma única base de dados ou piscina elástica, mover uma base de dados para dentro/para fora de uma piscina elástica, ou mover uma base de dados entre piscinas elásticas é parametrizada da seguinte forma:

|Camada de serviços|Base de dados única básica,</br>Padrão (S0-S1)|Piscina elástica básica,</br>Padrão (S2-S12), </br>Hiperescala, </br>Base de dados única de propósito geral ou piscina elástica|Premium ou Business Critical única base de dados ou piscina elástica|
|:---|:---|:---|:---|
|**Base de dados única básica, </br> Standard (S0-S1)**|&bull;&nbsp;Latência de tempo constante independente do espaço utilizado</br>&bull;&nbsp;Tipicamente, menos de 5 minutos|&bull;Latência proporcional ao espaço de base de &nbsp; dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;Latência proporcional ao espaço de base de &nbsp; dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Piscina elástica básica, </br> Standard (S2-S12), </br> Hiperescala, Base de </br> Dados Única de Propósito Geral ou piscina elástica**|&bull;Latência proporcional ao espaço de base de &nbsp; dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência de tempo constante independente do espaço utilizado</br>&bull;&nbsp;Tipicamente, menos de 5 minutos|&bull;Latência proporcional ao espaço de base de &nbsp; dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Premium ou Business Critical única base de dados ou piscina elástica**|&bull;Latência proporcional ao espaço de base de &nbsp; dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;Latência proporcional ao espaço de base de &nbsp; dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;Latência proporcional ao espaço de base de &nbsp; dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|

> [!NOTE]
> Adicionalmente, para as bases de dados Standard (S2-S12) e General Purpose, a latência para mover uma base de dados dentro/fora de uma piscina elástica ou entre piscinas elásticas será proporcional ao tamanho da base de dados se a base de dados estiver a utilizar o armazenamento premium file Share[(PFS).](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
>
> Para determinar se uma base de dados está a utilizar o armazenamento de PFS, execute a seguinte consulta no contexto da base de dados. Se o valor na coluna AccountType `PremiumFileStorage` estiver, a base de dados está a utilizar o armazenamento de PFS.
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Para monitorizar as operações em curso, consulte: [Gerir as operações utilizando o SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), [Gerir as operações utilizando cli,](/cli/azure/sql/db/op) [monitorizar as operações utilizando o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Cancelamento de alterações

Uma operação de alteração de nível de serviço ou de reescalonamento de cálculo pode ser cancelada.

### <a name="azure-portal"></a>Portal do Azure

Na lâmina de visão geral da base de dados, navegue para **Notificações** e clique no azulejo indicando que existe uma operação em curso:

![Operação em curso](./media/single-database-scale/ongoing-operations.png)

Em seguida, clique no botão rotulado **Cancele esta operação**.

![Cancelar operação em curso](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

A partir de um pedido de comando PowerShell, desloque o `$resourceGroupName` , `$serverName` `$databaseName` e, em seguida, executar o seguinte comando:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Considerações adicionais

- Se estiver a atualizar para um nível de serviço mais elevado ou tamanho de computação, o tamanho máximo da base de dados não aumenta a menos que especifique explicitamente um tamanho maior (tamanho máximo).
- Para desvalorizar uma base de dados, o espaço utilizado na base de dados deve ser inferior ao tamanho máximo permitido do nível de serviço-alvo e do tamanho da computação.
- Ao degradar-se do **Premium** para o nível **Standard,** aplica-se um custo de armazenamento extra se ambos (1) o tamanho máximo da base de dados for suportado no tamanho da computação-alvo, e (2) o tamanho máximo exceder a quantidade de armazenamento incluída do tamanho da computação-alvo. Por exemplo, se uma base de dados P1 com um tamanho máximo de 500 GB for reduzido para S3, então um custo de armazenamento extra aplica-se uma vez que o S3 suporta um tamanho máximo de 1 TB e o seu valor de armazenamento incluído é de apenas 250 GB. Assim, o valor extra de armazenamento é de 500 GB – 250 GB = 250 GB. Para obter preços de armazenamento extra, consulte o preço da Base de [Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizado for inferior ao valor de armazenamento incluído, então este custo extra pode ser evitado reduzindo o tamanho máximo da base de dados para o valor incluído.
- Ao atualizar uma base de dados com [geo-replicação](active-geo-replication-configure-portal.md) ativada, atualize as suas bases de dados secundárias para o nível de serviço pretendido e calcule o tamanho da computação antes de atualizar a base de dados primária (orientação geral para melhor desempenho). Ao atualizar para uma edição diferente, é um requisito que a base de dados secundária seja atualizada primeiro.
- Ao degradar uma base de dados com [geo-replicação](active-geo-replication-configure-portal.md) ativada, desagrade as suas bases de dados primárias para o nível de serviço pretendido e para o tamanho da computação antes de degradar a base de dados secundária (orientação geral para melhor desempenho). Ao degradar-se para uma edição diferente, é um requisito que a base de dados primária seja desclassificada primeiro.
- As ofertas de serviço de restauro diferem entre os vários escalões de serviço. Se estiver a degradar-se para o nível **Básico,** existe um período de retenção de backup mais baixo. Ver Backups de Base de [Dados Azure SQL](automated-backups-overview.md).
- As novas propriedades para a base de dados não são aplicadas até que as alterações estejam concluídas.

## <a name="billing"></a>Faturação

É cobrado por cada hora que existe uma base de dados utilizando o maior nível de serviço + tamanho de computação que se aplica durante essa hora, independentemente do uso ou se a base de dados esteve ativa por menos de uma hora. Por exemplo, se criar uma única base de dados e a eliminar cinco minutos depois, a sua conta reflete uma carga por uma hora de base de dados.

## <a name="change-storage-size"></a>Alterar o tamanho do armazenamento

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser provisionado até ao limite de tamanho máximo de armazenamento de dados utilizando incrementos de 1 GB. O armazenamento mínimo de dados configuráveis é de 1 GB. Consulte as páginas de documentação limite de recursos para [bases de dados individuais](resource-limits-vcore-single-databases.md) e [piscinas elásticas](resource-limits-vcore-elastic-pools.md) para os limites máximos de armazenamento de dados em cada objetivo de serviço.
- O armazenamento de dados para uma única base de dados pode ser provisionado aumentando ou diminuindo o seu tamanho máximo utilizando o [portal Azure](https://portal.azure.com), [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az-sql-db-update)ou [REST API](https://docs.microsoft.com/rest/api/sql/databases/update). Se o valor máximo de tamanho for especificado em bytes, deve ser um múltiplo de 1 GB (1073741824 bytes).
- A quantidade de dados que podem ser armazenados nos ficheiros de dados de uma base de dados é limitada pelo tamanho máximo de armazenamento de dados configurado. Além desse armazenamento, a Base de Dados Azure SQL atribui automaticamente mais 30% de armazenamento a ser utilizado para o registo de transações.
- A Base de Dados Azure SQL atribui automaticamente 32 GB por vCore para a base de `tempdb` dados. `tempdb`encontra-se localizado no armazenamento local de SSD em todos os níveis de serviço.
- O preço de armazenamento de uma única base de dados ou de um pool elástico é a soma dos valores de armazenamento de dados e de armazenamento de registos de transações multiplicados pelo preço unitário de armazenamento do nível de serviço. O custo está `tempdb` incluído no preço. Para mais detalhes sobre o preço de armazenamento, consulte o preço da Base de [Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço dTU para uma única base de dados inclui uma certa quantidade de armazenamento sem custos adicionais. Armazenamento extra para além do valor incluído pode ser provisionado para um custo adicional até o limite de tamanho máximo em incrementos de 250 GB até 1 TB, e, em seguida, em incrementos de 256 GB para além de 1 TB. Para valores de armazenamento incluídos e limites de tamanho máximo, consulte [base de dados única: tamanhos de armazenamento e tamanhos de cálculo](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- O armazenamento extra para uma única base de dados pode ser provisionado aumentando o seu tamanho máximo utilizando o portal Azure, [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [o Azure CLI](/cli/azure/sql/db#az-sql-db-update)ou o [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- O preço do armazenamento extra para uma única base de dados é o valor extra de armazenamento multiplicado pelo preço extra de armazenamento do nível de serviço. Para mais detalhes sobre o preço do armazenamento extra, consulte o preço da Base de [Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](file-space-manage.md).

### <a name="geo-replicated-database"></a>Base de dados geo-replicada

Para alterar o tamanho da base de dados de uma base de dados secundária replicada, altere o tamanho da base de dados primária. Esta alteração será então replicada e implementada na base de dados secundária.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Restrições P11 e P15 quando o tamanho máximo é superior a 1 TB

Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China Leste, China North, Alemanha Central, Alemanha Nordeste, Oeste Dos EUA, regiões do DoD dos EUA e Central do Governo dos EUA. Nestas regiões, o máximo de armazenamento no nível Premium está limitado a 1 TB. As seguintes considerações e limitações aplicam-se às bases de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se o tamanho máximo de uma base de dados P11 ou P15 alguma vez tiver sido fixado num valor superior a 1 TB, então só pode ser restaurado ou copiado para uma base de dados P11 ou P15.  Posteriormente, a base de dados pode ser redimensionada para um tamanho de cálculo diferente, desde que a quantidade de espaço atribuído no momento da operação de rescaling não exceda os limites máximos de tamanho do novo tamanho do cálculo.
- Para cenários de geo-replicação ativa:
  - Criação de uma relação de geo-replicação: Se a base de dados primária for P11 ou P15, o secundário deve também ser P11 ou P15; o tamanho inferior da computação é rejeitado como secundário, uma vez que não são capazes de suportar mais de 1 TB.
  - Atualizar a base de dados primária numa relação de geo-replicação: Alterar o tamanho máximo para mais de 1 TB numa base de dados primária desencadeia a mesma alteração na base de dados secundária. Ambas as atualizações devem ser bem sucedidas para que a alteração nas primárias entre em vigor. Aplicam-se as limitações da região para a opção mais de 1-TB. Se o secundário estiver numa região que não suporta mais de 1 TB, o primário não é atualizado.
- Não é suportado o serviço de importação/exportação para o carregamento de bases de dados P11/P15 com mais de 1 TB. Utilize sqlPackage.exe para [importar](database-import.md) e [exportar](database-export.md) dados.

## <a name="next-steps"></a>Próximos passos

Para obter limites globais de recursos, consulte os limites de [recursos baseados na Base de Dados Azure SQL vCore - bases](resource-limits-vcore-single-databases.md) de dados únicas e limites de recursos baseados em DTU baseados em Base de Dados [Azure SQL - bases](resource-limits-dtu-single-databases.md)de dados únicas .
