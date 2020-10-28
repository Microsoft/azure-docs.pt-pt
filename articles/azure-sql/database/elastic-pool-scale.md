---
title: Dimensionar recursos de conjuntos elásticos
description: Esta página descreve recursos de escala para piscinas elásticas na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 947d842860452425f8b30fbdaf9558c2a94a89a2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781214"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Dimensione recursos de piscina elástica na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para piscinas elásticas e bases de dados em piscinas na Base de Dados Azure SQL.

## <a name="change-compute-resources-vcores-or-dtus"></a>Alterar recursos computacional (vCores ou DTUs)

Depois de escolher inicialmente o número de vCores ou eDTUs, pode escalar uma piscina elástica para cima ou para baixo dinamicamente com base na experiência real utilizando o [portal Azure](elastic-pool-manage.md#azure-portal), [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)o [Azure CLI,](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)ou a [API REST](/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto da alteração do nível de serviço ou do tamanho do cálculo rescalante

A alteração do nível de serviço ou do tamanho do cálculo de uma piscina elástica segue um padrão semelhante ao das bases de dados individuais e envolve principalmente o serviço que executa os seguintes passos:

1. Crie uma nova instância computacional para a piscina elástica  

    Uma nova instância computacional para a piscina elástica é criada com o nível de serviço e tamanho de cálculo solicitados. Para algumas combinações de alterações de nível de serviço e tamanho de cálculo, uma réplica de cada base de dados deve ser criada no novo caso de computação que envolve copiar dados e pode influenciar fortemente a latência geral. Seja como for, as bases de dados permanecem on-line durante este passo, e as ligações continuam a ser direcionadas para as bases de dados no caso do cálculo original.

2. Mudar o encaminhamento das ligações para nova instância computacional

    As ligações existentes às bases de dados na instância de computação original são eliminadas. Quaisquer novas ligações são estabelecidas nas bases de dados no novo caso computacional. Para algumas combinações de alterações de nível de serviço e tamanho de cálculo, os ficheiros de base de dados são separados e religados durante o comutador.  Independentemente disso, o comutador pode resultar numa breve interrupção de serviço quando as bases de dados não estão disponíveis geralmente por menos de 30 segundos e muitas vezes durante apenas alguns segundos. Se houver transações em execução prolongadas quando as ligações forem retiradas, a duração deste passo pode demorar mais tempo para recuperar as transações abortadas. [A recuperação acelerada da base de dados](../accelerated-database-recovery.md) pode reduzir o impacto de abortar transações de longa duração.

> [!IMPORTANT]
> Não são perdidos dados durante qualquer etapa no fluxo de trabalho.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latência de alteração do nível de serviço ou do tamanho do cálculo rescalante

A latência estimada para alterar o nível de serviço, escalar o tamanho do cálculo de uma única base de dados ou piscina elástica, mover uma base de dados dentro/fora de uma piscina elástica, ou mover uma base de dados entre piscinas elásticas é parametrizada da seguinte forma:

|Escalão de serviço|Base de dados única básica,</br>Padrão (S0-S1)|Piscina elástica básica,</br>Standard (S2-S12), </br>Base de dados única ou piscina elástica para fins gerais|Premium ou Business Critical base de dados única ou piscina elástica|Hyperscale
|:---|:---|:---|:---|:---|
|**Base de dados única básica, </br> Standard (S0-S1)**|&bull;&nbsp;Latência do tempo constante independente do espaço utilizado</br>&bull;&nbsp;Tipicamente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Piscina elástica básica, </br> Standard (S2-S12), </br> base de dados única de finalidade geral ou piscina elástica**|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Para bases de dados individuais, latência de tempo constante independente do espaço utilizado</br>&bull;&nbsp;Normalmente, menos de 5 minutos para bases de dados individuais</br>&bull;&nbsp;Para piscinas elásticas, proporcional ao número de bases de dados|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Premium ou Business Critical base de dados única ou piscina elástica**|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Hyperscale**|N/D|N/D|N/D|&bull;&nbsp;Latência do tempo constante independente do espaço utilizado</br>&bull;&nbsp;Tipicamente, menos de 2 minutos|

> [!NOTE]
>
> - No caso de alterar o nível de serviço ou rescalar o cálculo para uma piscina elástica, a soma do espaço utilizado em todas as bases de dados da piscina deve ser utilizada para calcular a estimativa.
> - No caso de mover uma base de dados de/para uma piscina elástica, apenas o espaço utilizado pela base de dados tem impacto na latência, não no espaço utilizado pela piscina elástica.
> - Para piscinas elásticas Standard e General Purpose, a latência de mover uma base de dados dentro/fora de uma piscina elástica ou entre piscinas elásticas será proporcional ao tamanho da base de dados se a piscina elástica estiver a utilizar o armazenamento Premium File Share[(PFS).](../../storage/files/storage-files-introduction.md) Para determinar se uma piscina está a utilizar o armazenamento PFS, execute a seguinte consulta no contexto de qualquer base de dados na piscina. Se o valor na coluna AccountType for `PremiumFileStorage` `PremiumFileStorage-ZRS` ou, o pool está a utilizar o armazenamento PFS.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Para monitorizar as operações em curso, consulte: [Gerir operações utilizando a API DE REST SQL,](/rest/api/sql/operations/list) [Gerir operações utilizando operações CLI,](/cli/azure/sql/db/op) [Monitor utilizando o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerações adicionais ao alterar o nível de serviço ou rescalar o tamanho do cálculo

- Ao reduzir o tamanho de vCores ou eDTUs para uma piscina elástica, o espaço utilizado na piscina deve ser menor do que o tamanho máximo permitido do nível de serviço alvo e eDTUs de piscina.
- Ao redimensionar os EDTUs para uma piscina elástica, aplica-se um custo de armazenamento extra se (1) o tamanho máximo de armazenamento da piscina for suportado pelo pool-alvo, e (2) o tamanho máximo de armazenamento excede a quantidade de armazenamento incluída da piscina alvo. Por exemplo, se uma piscina Standard 100 eDTU com um tamanho máximo de 100 GB for reduzido para uma piscina standard de 50 eDTU, então um custo de armazenamento extra se aplica, uma vez que o pool-alvo suporta um tamanho máximo de 100 GB e o seu valor de armazenamento incluído é de apenas 50 GB. Assim, a quantidade extra de armazenamento é de 100 GB – 50 GB = 50 GB. Para preços de armazenamento extra, consulte [os preços da Base de Dados SQL.](https://azure.microsoft.com/pricing/details/sql-database/) Se a quantidade real de espaço utilizado for inferior à quantidade de armazenamento incluída, então este custo extra pode ser evitado reduzindo o tamanho máximo da base de dados para a quantidade incluída.

### <a name="billing-during-rescaling"></a>Faturação durante o rescalamento

Você é cobrado por cada hora existe uma base de dados usando o nível de serviço mais alto + tamanho de cálculo que se aplica durante essa hora, independentemente da utilização ou se a base de dados estava ativa por menos de uma hora. Por exemplo, se criar uma única base de dados e a eliminar cinco minutos depois, a sua conta reflete uma cobrança por uma hora de base de dados.

## <a name="change-elastic-pool-storage-size"></a>Mude o tamanho do armazenamento da piscina elástica

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser a provisionado até ao limite máximo de tamanho:

  - Para armazenamento nos níveis de serviço de finalidade padrão ou geral, aumente ou diminua o tamanho em incrementos de 10 GB
  - Para armazenamento nos níveis de serviço crítico premium ou empresarial, aumente ou diminua o tamanho em incrementos de 250 GB
- O armazenamento para uma piscina elástica pode ser a provisionado aumentando ou diminuindo o seu tamanho máximo.
- O preço de armazenamento de uma piscina elástica é a quantidade de armazenamento multiplicada pelo preço unitário de armazenamento do nível de serviço. Para mais informações sobre o preço do armazenamento extra, consulte [os preços da Base de Dados SQL.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço eDTU para uma piscina elástica inclui uma certa quantidade de armazenamento sem custos adicionais. O armazenamento extra para além do montante incluído pode ser previsto para um custo adicional até ao limite de tamanho máximo em incrementos de 250 GB até 1 TB, e depois em incrementos de 256 GB para além de 1 TB. Para quantidades de armazenamento incluídas e limites de tamanho máximo, consulte [piscina elástica: tamanhos de armazenamento e tamanhos de cálculo](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- O armazenamento extra para uma piscina elástica pode ser a provisionado aumentando o seu tamanho máximo utilizando o [portal Azure,](elastic-pool-manage.md#azure-portal) [o PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)o [Azure CLI,](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)ou o [REST API](/rest/api/sql/elasticpools/update).
- O preço de armazenamento extra para uma piscina elástica é a quantidade extra de armazenamento multiplicada pelo preço extra da unidade de armazenamento do nível de serviço. Para mais informações sobre o preço do armazenamento extra, consulte [os preços da Base de Dados SQL.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

## <a name="next-steps"></a>Passos seguintes

Para limites globais de recursos, consulte [os limites de recursos baseados em SQL Database vCore - piscinas elásticas](resource-limits-vcore-elastic-pools.md) e [limites de recursos baseados em DTU baseados em DTU - piscinas elásticas](resource-limits-dtu-elastic-pools.md).