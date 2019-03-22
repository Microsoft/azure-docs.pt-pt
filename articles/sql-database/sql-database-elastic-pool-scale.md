---
title: Dimensionar os recursos do conjunto elástico - base de dados SQL do Azure | Documentos da Microsoft
description: Esta página descreve os recursos de dimensionamento para conjuntos elásticos na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: d8aaf51c836a8e88c4e9b92798067167cd044e72
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58015364"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Dimensionar recursos de conjunto elástico na base de dados do Azure SQL

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para conjuntos elásticos e bases de dados agrupadas na base de dados do Azure SQL.

## <a name="change-compute-resources-vcores-or-dtus"></a>Recursos de computação de alteração (vCores ou DTUs)

Após escolher inicialmente o número de vCores ou eDTUs, pode dimensionar um conjunto elástico ou reduzir verticalmente dinamicamente com base na experiência real utilizando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), o [da CLI do Azure ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), ou o [API de REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto da alteração do tamanho de computação de camada ou ao redimensionar o serviço

Alterar o serviço de camada ou de computação tamanho de um conjunto elástico segue um padrão semelhante para bases de dados individuais e envolve principalmente o serviço, efetuando os seguintes passos:

1. Criar nova instância de computação do conjunto elástico  

    É criada uma nova instância de computação do conjunto elástico com o escalão de serviço pedido e o tamanho de computação. Para algumas combinações de camada de serviços e as alterações do tamanho de computação, uma réplica de cada base de dados tem de ser criada a nova instância de computação que envolve a cópia de dados e pode influenciar fortemente da latência geral. Independentemente disso, as bases de dados permanecem online durante este passo e ligações continuam a ser direcionado para as bases de dados na instância de computação do original.

2. Mude o encaminhamento de ligações a nova instância de computação

    Ligações existentes para as bases de dados na instância de computação original são removidas. Quaisquer novas ligações são estabelecidas para as bases de dados na nova instância de computação. Para algumas combinações de camada de serviços e as alterações do tamanho de computação, ficheiros de base de dados desligados e voltar a ligar durante a troca.  Seja como for, o comutador pode resultar numa interrupção do serviço breve onde as bases de dados não estão disponíveis em geral para menos de 30 segundos e, muitas vezes, apenas alguns segundos. Se existirem de longa execução transações em execução quando as ligações são ignoradas, a duração deste passo pode demorar mais tempo para recuperar transações abortadas. [Acelerado de recuperação de base de dados](sql-database-accelerated-database-recovery.md) pode reduzir o impacto de abortar transações de longa execução.

> [!IMPORTANT]
> Nenhum dado seja perdido durante qualquer passo no fluxo de trabalho.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latência de alterar o tamanho de computação de camada ou ao redimensionar o serviço

A latência de alterar a camada de serviços ou rescale o tamanho de computação de um único banco de dados ou conjunto elástico é parametrizada da seguinte forma:

|Camada de serviços|Base de dados única básica,</br>Standard (S0-S1)|Conjunto elástico básico,</br>Standard (S2-S12), </br>Hiperescala, </br>Base de dados única finalidade geral ou conjunto elástico|Base de dados única Premium ou críticas para a empresa ou o conjunto elástico|
|:---|:---|:---|:---|
|**Base de dados única básica,</br> Standard (S0-S1)**|&bull; &nbsp;Latência de tempo constante independente do espaço utilizado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|
|**Conjunto elástico básico, </br>Standard (S2-S12), </br>Hiperescala, </br>base de dados para fins gerais ou conjunto elástico**|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|&bull; &nbsp;Latência de tempo constante independente do espaço utilizado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|
|**Base de dados única Premium ou críticas para a empresa ou o conjunto elástico**|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|

> [!NOTE]
>
> - No caso de alteração da camada de serviço ou ao redimensionar a computação de um conjunto elástico, a soma do espaço utilizado em todas as bases de dados no conjunto deve ser utilizada para calcular a estimativa.
> - No caso de mover uma base de dados de/para um conjunto elástico, apenas o espaço utilizado pela base de dados afeta a latência, não o espaço utilizado pelo conjunto elástico.
>
> [!TIP]
> Para monitorizar operações em curso, consulte: [Gerir as operações com a API de REST de SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [gerir as operações com a CLI](/cli/azure/sql/db/op), [monitorizar as operações com o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos do PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerações adicionais ao alterar escalão ou ao redimensionar o tamanho de computação de serviço

- Quando downsizing vCores ou eDTUs de um conjunto elástico, o espaço do agrupamento utilizado tem de ser menor do que o tamanho máximo permitido das eDTUs de escalão e o conjunto de serviço do destino.
- Quando ao redimensionar o vCores ou eDTUs de um conjunto elástico, aplicável um custo de armazenamento extra se (1) o tamanho máximo de armazenamento do conjunto é suportado pelo pool de destino e (2) o tamanho máximo de armazenamento excede a quantidade de armazenamento incluído de agrupamento de destino. Por exemplo, se um conjunto Standard com um tamanho máximo de 100 GB de 100 Edtus é downsized para um conjunto padrão de 50 Edtus, em seguida, um custo de armazenamento extra aplica-se uma vez que o agrupamento de destino suporta um tamanho máximo de 100 GB e a quantidade de armazenamento incluído é apenas 50 GB. Por isso, a quantidade de armazenamento extra é 100 GB – 50 GB = 50 GB. Para obter os preços do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizada é inferior à quantidade de armazenamento incluído, em seguida, isso encargos extra pode ser evitado ao reduzir o tamanho máximo da base de dados para o montante incluído.

### <a name="billing-during-rescaling"></a>Faturação durante ao redimensionar o

Será cobrado por cada hora, que uma base de dados existe ao utilizar o escalão de serviço mais elevado + tamanho aplicado durante essa hora, independentemente da utilização ou se a base de dados esteve ativo durante menos de uma hora de computação. Por exemplo, se criar uma base de dados e a eliminar passados cinco minutos, a fatura reflete um custo de hora de uma base de dados.

## <a name="change-elastic-pool-storage-size"></a>Alterar o tamanho de armazenamento do conjunto elástico

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Modelo de compras baseado em vCore

- Pode aprovisionar um armazenamento até ao limite de tamanho máximo:

  - Para o armazenamento nos escalões de serviço standard ou para fins gerais, aumentar ou diminuir o tamanho em incrementos de 10 GB
  - Para armazenamento no premium ou crítico para a empresa escalões de serviço, aumentar ou diminuir o tamanho em incrementos de 250 GB
- Armazenamento para um conjunto elástico pode ser aprovisionado por aumentar ou diminuir o tamanho máximo.
- O preço do armazenamento de um conjunto elástico é a quantidade de armazenamento, multiplicada pelo preço de unidade de armazenamento da camada de serviços. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço de eDTU de um conjunto elástico inclui uma certa quantidade de armazenamento sem custos adicionais. Pode ser aprovisionado armazenamento extra para além da quantidade incluída por um custo adicional até ao limite de tamanho máximo em incrementos de 250 GB a 1 TB e, em seguida, em incrementos de 256 GB a partir de 1 TB. Para quantidades de armazenamento incluído e limites de tamanho máximo, consulte [conjunto elástico: tamanhos de armazenamento e tamanhos de computação](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Pode aprovisionar um armazenamento extra para um conjunto elástico aumente o seu tamanho máximo utilizando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), o [da CLI do Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), ou o [REST API ](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- O preço do armazenamento extra para um conjunto elástico é a quantidade de armazenamento extra multiplicada pelo preço de unidade de armazenamento extra da camada de serviços. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="next-steps"></a>Passos Seguintes

Para geral limites de recursos, consulte [limites dos recursos baseados em vCore de base de dados SQL - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md) e [limites de recursos baseados em DTU de base de dados do SQL - conjuntos elásticos](sql-database-dtu-resource-limits-elastic-pools.md).
