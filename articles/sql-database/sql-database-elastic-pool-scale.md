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
ms.date: 02/07/2019
ms.openlocfilehash: 2726c1fd08414f112035d5378fc7e395ca7ace4c
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959090"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Dimensionar recursos de conjunto elástico na base de dados do Azure SQL

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para conjuntos elásticos e bases de dados agrupadas na base de dados do Azure SQL.

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>modelo de compra baseado em vCore: Alterar o tamanho de armazenamento do conjunto elástico

- Pode aprovisionar um armazenamento até ao limite de tamanho máximo:

  - Para o armazenamento nos escalões de serviço standard ou para fins gerais, aumentar ou diminuir o tamanho em incrementos de 10 GB
  - Para armazenamento no premium ou crítico para a empresa escalões de serviço, aumentar ou diminuir o tamanho em incrementos de 250 GB
- Armazenamento para um conjunto elástico pode ser aprovisionado por aumentar ou diminuir o tamanho máximo.
- O preço do armazenamento de um conjunto elástico é a quantidade de armazenamento, multiplicada pelo preço de unidade de armazenamento da camada de serviços. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>modelo de compra baseado em vCore: Recursos (vCores) de computação do conjunto elástico de alteração

Pode aumentar ou diminuir o tamanho de computação a um conjunto elástico com base no recurso às suas necessidades com o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), o [da CLI do Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), ou o [ REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

- Quando ao redimensionar o vCores num conjunto elástico, ligações de base de dados rapidamente são ignoradas. Este comportamento é o mesmo comportamento que ocorre quando ao redimensionar o DTUs para uma base de dados. Para obter detalhes sobre a duração e o impacto da queda de conexão para uma base de dados durante as operações ao redimensionar, consulte [(DTUs) de recursos de computação de alteração](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus).
- A duração a redimensionar vCores de agrupamento pode depender da quantidade total de espaço de armazenamento utilizado por todas as bases de dados no conjunto. Em geral, a latência ao redimensionar calcula a média 90 minutos ou menos por 100 GB. Por exemplo, se o total de espaço utilizado por todas as bases de dados no conjunto é 200 GB, em seguida, a latência esperada ao redimensionar o conjunto é de 3 horas ou menos. Em alguns casos no escalão Standard ou Basic, a latência ao redimensionar pode ser menos de cinco minutos, independentemente da quantidade de espaço utilizado.
- Em geral, a duração para alterar os vCores min por vCores de base de dados ou máximo por base de dados é de cinco minutos ou menos.
- Quando downsizing vCores de agrupamento, o espaço do agrupamento utilizado tem de ser menor do que o tamanho máximo permitido dos vCores de escalão e o conjunto de serviço do destino.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>Modelo de compra baseado em DTU: Alterar o tamanho de armazenamento do conjunto elástico

- O preço de eDTU de um conjunto elástico inclui uma certa quantidade de armazenamento sem custos adicionais. Pode ser aprovisionado armazenamento extra para além da quantidade incluída por um custo adicional até ao limite de tamanho máximo em incrementos de 250 GB a 1 TB e, em seguida, em incrementos de 256 GB a partir de 1 TB. Para quantidades de armazenamento incluído e limites de tamanho máximo, consulte [conjunto elástico: tamanhos de armazenamento e tamanhos de computação](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Pode aprovisionar um armazenamento extra para um conjunto elástico aumente o seu tamanho máximo utilizando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), o [da CLI do Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), ou o [REST API ](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- O preço do armazenamento extra para um conjunto elástico é a quantidade de armazenamento extra multiplicada pelo preço de unidade de armazenamento extra da camada de serviços. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>Modelo de compra baseado em DTU: Recursos (eDTUs) de computação do conjunto elástico de alteração

Pode aumentar ou diminuir os recursos disponíveis para um conjunto elástico com base no recurso às suas necessidades com o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), o [da CLI do Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), ou o [ REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

- Quando ao redimensionar o conjunto de eDTUs, ligações de base de dados rapidamente são ignoradas. Este comportamento é o mesmo comportamento que ocorre quando ao redimensionar o DTUs para uma base de dados. Para obter detalhes sobre a duração e o impacto da queda de conexão para uma base de dados durante as operações ao redimensionar, consulte [(DTUs) de recursos de computação de alteração](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus).
- A duração a redimensionar eDTUs do conjunto pode confiar na quantidade total de espaço de armazenamento utilizado por todas as bases de dados no conjunto. Em geral, a latência ao redimensionar calcula a média 90 minutos ou menos por 100 GB. Por exemplo, se o total de espaço utilizado por todas as bases de dados no conjunto é 200 GB, em seguida, a latência esperada ao redimensionar o conjunto é de 3 horas ou menos. Em alguns casos no escalão Standard ou Basic, a latência ao redimensionar pode ser menos de cinco minutos, independentemente da quantidade de espaço utilizado.
- Em geral, a duração para alterar o mínimo de eDTUs por base de dados ou o número máximo de eDTUs por base de dados é de cinco minutos ou menos.
- Quando downsizing eDTUs para um conjunto elástico, o espaço do agrupamento utilizado tem de ser menor do que o tamanho máximo permitido das eDTUs de escalão e o conjunto de serviço do destino.
- Quando ao redimensionar o eDTUs para um conjunto elástico, aplicável um custo de armazenamento extra se (1) o tamanho máximo de armazenamento do conjunto é suportado pelo pool de destino e (2) o tamanho máximo de armazenamento excede a quantidade de armazenamento incluído de agrupamento de destino. Por exemplo, se um conjunto Standard com um tamanho máximo de 100 GB de 100 Edtus é downsized para um conjunto padrão de 50 Edtus, em seguida, um custo de armazenamento extra aplica-se uma vez que o agrupamento de destino suporta um tamanho máximo de 100 GB e a quantidade de armazenamento incluído é apenas 50 GB. Por isso, a quantidade de armazenamento extra é 100 GB – 50 GB = 50 GB. Para obter os preços do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizada é inferior à quantidade de armazenamento incluído, em seguida, isso encargos extra pode ser evitado ao reduzir o tamanho máximo da base de dados para o montante incluído.

## <a name="next-steps"></a>Passos Seguintes

Para geral limites de recursos, consulte [limites dos recursos baseados em vCore de base de dados SQL - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md) e [limites de recursos baseados em DTU de base de dados do SQL - conjuntos elásticos](sql-database-dtu-resource-limits-elastic-pools.md).
