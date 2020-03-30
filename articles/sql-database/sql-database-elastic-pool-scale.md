---
title: Dimensionar recursos de conjuntos elásticos
description: Esta página descreve recursos de escala para piscinas elásticas na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462603"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Dimensione recursos de piscina elástica na Base de Dados Azure SQL

Este artigo descreve como escalar os recursos de cálculo e armazenamento disponíveis para piscinas elásticas e bases de dados reunidas na Base de Dados Azure SQL.

## <a name="change-compute-resources-vcores-or-dtus"></a>Alterar os recursos computacionais (vCores ou DTUs)

Depois de inicialmente escolher o número de vCores ou eDTUs, pode escalar uma piscina elástica para cima ou para baixo dinamicamente com base na experiência real utilizando o [portal Azure,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)o [Azure CLI,](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)ou o [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto da mudança do nível de serviço ou do tamanho da computação de rescaling

A alteração do nível de serviço ou do tamanho da computação de uma piscina elástica segue um padrão semelhante ao das bases de dados individuais e envolve principalmente o serviço que executa os seguintes passos:

1. Criar nova instância computacional para a piscina elástica  

    Uma nova instância de cálculo para a piscina elástica é criada com o nível de serviço solicitado e tamanho da computação. Para algumas combinações de nível de serviço e alterações no tamanho da computação, uma réplica de cada base de dados deve ser criada no novo caso de cálculo que envolve copiar dados e pode influenciar fortemente a latência geral. Independentemente disso, as bases de dados permanecem online durante este passo, e as ligações continuam a ser direcionadas para as bases de dados na instância de cálculo original.

2. Altere o encaminhamento de ligações para uma nova instância de cálculo

    As ligações existentes às bases de dados na instância de cálculo original são retiradas. Quaisquer novas ligações são estabelecidas nas bases de dados no novo caso do cálculo. Para algumas combinações de nível de serviço e alterações no tamanho da computação, os ficheiros de base de dados são separados e religados durante o interruptor.  Independentemente disso, o interruptor pode resultar numa breve interrupção de serviço quando as bases de dados estão geralmente indisponíveis por menos de 30 segundos e muitas vezes por apenas alguns segundos. Se houver transações de longo prazo a decorrer quando as ligações forem retiradas, a duração deste passo pode demorar mais tempo para recuperar as transações abortadas. [A recuperação acelerada da base](sql-database-accelerated-database-recovery.md) de dados pode reduzir o impacto de abortar transações de longo prazo.

> [!IMPORTANT]
> Não se perdem dados durante qualquer passo no fluxo de trabalho.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latência de alteração do nível de serviço ou tamanho da computação de rescaling

A latência estimada para alterar o nível de serviço ou redimensionar o tamanho do cálculo de uma única base de dados ou piscina elástica é parametrizada da seguinte forma:

|Camada de serviços|Base de dados única básica,</br>Padrão (S0-S1)|Piscina elástica básica,</br>Padrão (S2-S12), </br>Hiperescala, </br>Base de dados única de propósito geral ou piscina elástica|Premium ou Business Critical única base de dados ou piscina elástica|
|:---|:---|:---|:---|
|**Base de</br> dados única básica, Standard (S0-S1)**|&bull;&nbsp;Latência de tempo constante independente do espaço utilizado</br>&bull;&nbsp;Tipicamente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Piscina elástica </br>básica, Standard (S2-S12), </br>Hiperescala, Base de </br>Dados Única de Propósito Geral ou piscina elástica**|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência de tempo constante independente do espaço utilizado</br>&bull;&nbsp;Tipicamente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Premium ou Business Critical única base de dados ou piscina elástica**|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|

> [!NOTE]
>
> - No caso de alterar o nível de serviço ou a computação de rescaling para uma piscina elástica, a soma do espaço utilizado em todas as bases de dados da piscina deve ser utilizada para calcular a estimativa.
> - No caso de mover uma base de dados para/a partir de uma piscina elástica, apenas o espaço utilizado pela base de dados tem impacto na latência, e não no espaço utilizado pela piscina elástica.
>
> [!TIP]
> Para monitorizar as operações em curso, consulte: [Gerir as operações utilizando o SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), [Gerir as operações utilizando cli,](/cli/azure/sql/db/op) [monitorizar as operações utilizando o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerações adicionais ao alterar o nível de serviço ou o tamanho da computação de rescaling

- Ao reduzir os vCores ou eDTUs para uma piscina elástica, o espaço utilizado na piscina deve ser menor do que o tamanho máximo permitido do nível de serviço-alvo e da piscina eDTUs.
- Ao escalonar as EDTUs para uma piscina elástica, aplica-se um custo extra de armazenamento se (1) o tamanho máximo de armazenamento da piscina for suportado pela piscina-alvo, e (2) o tamanho máximo de armazenamento excede a quantidade de armazenamento incluída da piscina alvo. Por exemplo, se uma piscina standard de 100 eDTU com um tamanho máximo de 100 GB for reduzido para uma piscina standard de 50 eDTU, então um custo de armazenamento extra se aplica, uma vez que a piscina alvo suporta um tamanho máximo de 100 GB e o seu valor de armazenamento incluído é de apenas 50 GB. Assim, o valor extra de armazenamento é de 100 GB – 50 GB = 50 GB. Para obter preços de armazenamento extra, consulte os preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizado for inferior ao valor de armazenamento incluído, então este custo extra pode ser evitado reduzindo o tamanho máximo da base de dados para o valor incluído.

### <a name="billing-during-rescaling"></a>Faturação durante o rescaling

É cobrado por cada hora que existe uma base de dados utilizando o maior nível de serviço + tamanho de computação que se aplica durante essa hora, independentemente do uso ou se a base de dados esteve ativa por menos de uma hora. Por exemplo, se criar uma única base de dados e a eliminar cinco minutos depois, a sua conta reflete uma carga por uma hora de base de dados.

## <a name="change-elastic-pool-storage-size"></a>Alterar o tamanho do armazenamento da piscina elástica

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser provisionado até ao limite máximo de tamanho:

  - Para armazenamento nos níveis de serviço padrão ou geral, aumente ou diminua o tamanho em incrementos de 10 GB
  - Para armazenamento nos níveis de serviço críticos premium ou empresariais, aumente ou diminua o tamanho em incrementos de 250 GB
- O armazenamento de uma piscina elástica pode ser provisionado aumentando ou diminuindo o seu tamanho máximo.
- O preço de armazenamento de uma piscina elástica é o valor de armazenamento multiplicado pelo preço unitário de armazenamento do nível de serviço. Para mais detalhes sobre o preço do armazenamento extra, consulte os preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço do eDTU para uma piscina elástica inclui uma certa quantidade de armazenamento sem custos adicionais. Armazenamento extra para além do valor incluído pode ser provisionado para um custo adicional até o limite de tamanho máximo em incrementos de 250 GB até 1 TB, e, em seguida, em incrementos de 256 GB para além de 1 TB. Para valores de armazenamento incluídos e limites de tamanho máximo, consulte [piscina elástica: tamanhos de armazenamento e tamanhos](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)de cálculo.
- O armazenamento extra para uma piscina elástica pode ser provisionado aumentando o seu tamanho máximo utilizando o [portal Azure,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)o [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)ou o [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- O preço do armazenamento extra para uma piscina elástica é o valor extra de armazenamento multiplicado pelo preço extra de armazenamento do nível de serviço. Para mais detalhes sobre o preço do armazenamento extra, consulte os preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](sql-database-file-space-management.md).

## <a name="next-steps"></a>Passos seguintes

Para os limites globais de recursos, consulte [os limites de recursos baseados na Base de Dados SQL vCore - piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md) e [limites de recursos baseados em DTU baseados na Base de Dados SQL - piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md).
