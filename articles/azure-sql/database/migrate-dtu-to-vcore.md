---
title: Migrar de DTU para vCore
description: Migrar uma base de dados na Base de Dados Azure SQL do modelo DTU para o modelo vCore. Migrar para vCore é semelhante ao upgrade ou degradação entre os níveis standard e premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 05/28/2020
ms.openlocfilehash: b8c7671e655594456621e4489cb06191d820b134
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333159"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrar base de dados Azure SQL do modelo baseado em DTU para o modelo baseado em vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve como migrar a sua base de dados na Base de Dados Azure SQL do [modelo de compra baseado em DTU](service-tiers-dtu.md) para o modelo de compra baseado em [vCore](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Migrar uma base de dados

A migração de uma base de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante à escala entre os objetivos de serviço nos níveis de serviço Basic, Standard e Premium, com [duração](single-database-scale.md#latency) semelhante e um tempo mínimo de [paragem](scale-resources.md) no final do processo de migração. Uma base de dados migrada para o modelo de compra baseado em vCore pode ser migrada de volta para o modelo de compra baseado em DTU a qualquer momento da mesma forma, com exceção das bases de dados migradas para o nível de serviço [Hyperscale.](service-tier-hyperscale.md) 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Escolha o nível de serviço vCore e o objetivo de serviço

Para a maioria dos cenários de migração, bases de dados e piscinas elásticas nos níveis de serviço Básico e Standard mapearão para o nível de serviço [De Propósito Geral.](service-tier-general-purpose.md) Bases de dados e piscinas elásticas no nível de serviço Premium irão mapear para o nível de serviço [Business Critical.](service-tier-business-critical.md) Dependendo do cenário e dos requisitos da aplicação, o nível de serviço [hyperscale](service-tier-hyperscale.md) pode muitas vezes ser usado como o alvo de migração para bases de dados únicas em todos os níveis de serviço DTU.

Para escolher o objetivo de serviço, ou tamanho do cálculo, para a base de dados migrada no modelo vCore, pode utilizar uma regra simples mas aproximada do polegar: cada 100 DTUs nos níveis Básico ou Padrão requerem *pelo menos* 1 vCore, e cada 125 DTUs no nível Premium requer pelo *menos* 1 vCore. 

> [!TIP]
> Esta regra é aproximada porque não considera a geração de hardware utilizada para a base de dados DTU ou piscina elástica. 

No modelo DTU, qualquer geração de [hardware](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) disponível pode ser utilizada para a sua base de dados ou piscina elástica. Além disso, tem apenas controlo indireto sobre o número de vCores (CPUs lógicos), escolhendo valores DTU ou eDTU mais altos ou inferiores. 

Com o modelo vCore, os clientes devem fazer uma escolha explícita tanto da geração de hardware como do número de vCores (CPUs lógicos). O modelo DTU não oferece estas escolhas, no entanto a geração de hardware e o número de CPUs lógicos utilizados para cada base de dados e piscina elástica são expostos através de vistas dinâmicas de gestão. Isto permite determinar o objetivo de serviço vCore correspondente com mais precisão. 

A seguinte abordagem utiliza esta informação para determinar um objetivo de serviço vCore com uma alocação de recursos semelhante, para obter um nível de desempenho semelhante após a migração para o modelo vCore.

### <a name="dtu-to-vcore-mapping"></a>DTU para mapeamento vCore

Uma consulta T-SQL abaixo, quando executada no contexto de uma base de dados DTU a ser migrada, devolverá um número de vCores correspondente (possivelmente fracionário) em cada geração de hardware no modelo vCore. Ao arredondar este número para o número mais próximo de vCores disponíveis para bases de dados e [piscinas elásticas](resource-limits-vcore-elastic-pools.md) em cada geração de hardware no modelo vCore, os clientes podem escolher o objetivo de serviço vCore que é a correspondência mais próxima para a sua base de [dados](resource-limits-vcore-single-databases.md) DTU ou piscina elástica. 

Os cenários de migração da amostra utilizando esta abordagem são descritos na secção [Exemplos.](#dtu-to-vcore-migration-examples)

Execute esta consulta no contexto da base de dados a migrar, em vez de na `master` base de dados. Ao migrar uma piscina elástica, execute a consulta no contexto de qualquer base de dados na piscina.

```SQL
WITH dtu_vcore_map AS
(
SELECT TOP (1) rg.slo_name,
               CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
                    WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
               END AS dtu_hardware_gen,
               s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
               CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Fatores adicionais

Além do número de vCores (CPUs lógicos) e da geração de hardware, vários outros fatores podem influenciar a escolha do objetivo de serviço vCore:

- A consulta de mapeamento T-SQL corresponde aos objetivos de serviço DTU e vCore em termos da sua capacidade de CPU, pelo que os resultados serão mais precisos para cargas de trabalho ligadas à CPU.
- Para a mesma geração de hardware e o mesmo número de vCores, IOPS e limites de recursos de produção de registo de transações para bases de dados vCore são muitas vezes mais elevados do que para as bases de dados DTU. No caso de cargas de trabalho ligadas a IO, pode ser possível reduzir o número de vCores no modelo vCore para atingir o mesmo nível de desempenho. Os limites de recursos para as bases de dados DTU e vCore em valores absolutos são expostos na visão [sys.dm_user_db_resource_governance.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) Comparar estes valores entre a base de dados DTU a migrar e uma base de dados vCore utilizando um objetivo de serviço aproximadamente correspondente irá ajudá-lo a selecionar o objetivo de serviço vCore com mais precisão.
- A consulta de mapeamento também devolve a quantidade de memória por núcleo para a base de dados DTU ou piscina elástica a migrar, e para cada geração de hardware no modelo vCore. Garantir memória total semelhante ou superior após a migração para vCore é importante para cargas de trabalho que requerem uma grande cache de dados de memória para obter desempenho suficiente, ou cargas de trabalho que requerem grandes subsídios de memória para o processamento de consultas. Para tais cargas de trabalho, dependendo do desempenho real, pode ser necessário aumentar o número de vCores para obter memória total suficiente.
- A [utilização histórica](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dos recursos da base de dados DTU deve ser considerada na escolha do objetivo de serviço vCore. As bases de dados DTU com recursos CPU consistentemente subutilados podem necessitar de menos vCores do que o número devolvido pela consulta de mapeamento. Inversamente, as bases de dados do DTU onde uma utilização de CPU consistentemente elevada causa um desempenho inadequado da carga de trabalho pode exigir mais vCores do que devolvidos pela consulta.
- Se migrar bases de dados com padrões de utilização intermitentes ou imprevisíveis, considere a utilização do nível de computação [Serverless.](serverless-tier-overview.md)  Note que o número máximo de trabalhadores simultâneos (pedidos) em servidores é de 75% o limite no cálculo provisionado para o mesmo número de vcores máximos configurados.  Além disso, a memória máxima disponível em servidor é de 3 GB vezes o número máximo de vcores configurados; por exemplo, a memória máxima é de 120 GB quando 40 vcores máximos são configurados.   
- No modelo vCore, o tamanho máximo de base de dados suportado pode diferir dependendo da geração de hardware. Para grandes bases de dados, verifique os tamanhos máximos suportados no modelo vCore para [bases de dados individuais](resource-limits-vcore-single-databases.md) e [piscinas elásticas](resource-limits-vcore-elastic-pools.md).
- Para piscinas elásticas, os modelos [DTU](resource-limits-dtu-elastic-pools.md) e [vCore](resource-limits-vcore-elastic-pools.md) têm diferenças no número máximo de bases de dados suportadas por piscina. Isto deve ser considerado quando migram piscinas elásticas com muitas bases de dados.
- Algumas gerações de hardware podem não estar disponíveis em todas as regiões. Verifique a disponibilidade em [Gerações de Hardware.](service-tiers-vcore.md#hardware-generations)

> [!IMPORTANT]
> As diretrizes de dimensionamento DTU para vCore acima são fornecidas para ajudar na estimativa inicial do objetivo do serviço de base de dados-alvo.
>
> A configuração ideal da base de dados-alvo é dependente da carga de trabalho. Assim, a obtenção da relação preço/desempenho ideal após a migração pode exigir a flexibilidade do modelo vCore para ajustar o número de vCores, a geração de [hardware,](service-tiers-vcore.md#hardware-generations)os níveis de [serviço](service-tiers-vcore.md#service-tiers) e [de computação,](service-tiers-vcore.md#compute-tiers) bem como a afinação de outros parâmetros de configuração da base de dados, como [o grau máximo de paralelismo.](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)
> 

### <a name="dtu-to-vcore-migration-examples"></a>DTU para vCore exemplos de migração

> [!NOTE]
> Os valores nos exemplos abaixo são apenas para fins de ilustração. Os valores reais devolvidos em cenários descritos podem ser diferentes.
> 

**Migração de uma base de dados Standard S9**

A consulta de mapeamento devolve o seguinte resultado (algumas colunas não mostradas para a brevidade):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24.00|Gen5|5.40|16.800|7|24.000|5.05|

Vemos que a base de dados DTU tem 24 CPUs lógicos (vCores), com 5,4 GB de memória por vCore, e está a usar hardware Gen5. A correspondência direta com isso é uma base de dados Geral 24 vCore no hardware da Gen5, ou seja, o **objetivo de** serviço GP_Gen5_24 vCore.

**Migração de uma base de dados Standard S0**

A consulta de mapeamento devolve o seguinte resultado (algumas colunas não mostradas para a brevidade):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Gen4|0.42|0.250|7|0.425|5.05|

Vemos que a base de dados DTU tem o equivalente a 0.25 CPUs lógicos (vCores), com 0,42 GB de memória por vCore, e está a usar hardware Gen4. Os mais pequenos objetivos de serviço vCore nas gerações de hardware da Gen4 e Gen5, **GP_Gen4_1** e **GP_Gen5_2**, fornecem mais recursos computacional do que a base de dados Standard S0, pelo que não é possível uma correspondência direta. Uma vez que o hardware da Gen4 está a ser [desativado,](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)a **opção GP_Gen5_2** é preferível. Além disso, se a carga de trabalho for adequada para o nível de cálculo [serverless,](serverless-tier-overview.md) então **GP_S_Gen5_1** seria uma correspondência mais próxima.

**Migração de uma base de dados Premium P15**

A consulta de mapeamento devolve o seguinte resultado (algumas colunas não mostradas para a brevidade):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42.00|Gen5|4.86|29.400|7|42.000|5.05|

Vemos que a base de dados DTU tem 42 CPUs lógicos (vCores), com 4.86 GB de memória por vCore, e está a usar hardware Gen5. Embora não exista um objetivo de serviço vCore com 42 núcleos, o objetivo de serviço **BC_Gen5_40** é muito próximo tanto em termos de CPU como de capacidade de memória, e é uma boa combinação.

**Migração de uma piscina elástica Basic 200 eDTU**

A consulta de mapeamento devolve o seguinte resultado (algumas colunas não mostradas para a brevidade):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|Gen5|5.40|2.800|7|4.000|5.05|

Vemos que a piscina elástica DTU tem 4 CPUs lógicos (vCores), com 5,4 GB de memória por vCore, e está a usar hardware Gen5. A correspondência direta no modelo vCore é uma piscina elástica **GP_Gen5_4.** No entanto, este objetivo de serviço suporta um máximo de 200 bases de dados por pool, enquanto o pool elástico Basic 200 eDTU suporta até 500 bases de dados. Se a piscina elástica a ser migrada tiver mais de 200 bases de dados, o objetivo de serviço vCore correspondente teria de ser **GP_Gen5_6**, que suporta até 500 bases de dados.

## <a name="migrate-geo-replicated-databases"></a>Migrar bases de dados geo-replicadas

A migração do modelo baseado em DTU para o modelo de compra baseado em vCore é semelhante à atualização ou degradação das relações de geo-replicação entre bases de dados nos níveis de serviço padrão e premium. Durante a migração, não é preciso parar a geo-replicação, mas tem de seguir estas regras de sequenciação:

- Ao atualizar, primeiro deve atualizar a base de dados secundária e, em seguida, atualizar a base de dados primária.
- Ao reduzir, inverta a ordem: primeiro deve desvalorizar a base de dados primária e depois desvalorizar a segunda.

Quando estiver a utilizar a geo-replicação entre duas piscinas elásticas, recomendamos que designe uma piscina como a primária e a outra como secundária. Nesse caso, quando estiver a migrar piscinas elásticas, deve utilizar a mesma orientação de sequenciação. No entanto, se tiver piscinas elásticas que contenham bases de dados primárias e secundárias, trate a piscina com uma utilização mais elevada como a principal e siga as regras de sequenciação em conformidade.  

O quadro a seguir fornece orientações para cenários específicos de migração:

|Nível de serviço atual|Nível de serviço alvo|Tipo de migração|Ações do utilizador|
|---|---|---|---|
|Standard|Fins gerais|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o tamanho vCore apropriado como descrito acima|
|Premium|Crítico para a empresa|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o tamanho vCore apropriado como descrito acima|
|Standard|Crítico para a empresa|Atualizar|Deve migrar secundário primeiro|
|Crítico para a empresa|Standard|Mudar para uma versão anterior|Deve migrar primária primeiro|
|Premium|Fins gerais|Mudar para uma versão anterior|Deve migrar primária primeiro|
|Fins gerais|Premium|Atualizar|Deve migrar secundário primeiro|
|Crítico para a empresa|Fins gerais|Mudar para uma versão anterior|Deve migrar primária primeiro|
|Fins gerais|Crítico para a empresa|Atualizar|Deve migrar secundário primeiro|
||||

## <a name="migrate-failover-groups"></a>Migrar grupos de failover

A migração de grupos de failover com múltiplas bases de dados requer migração individual das bases de dados primárias e secundárias. Durante esse processo, aplicam-se as mesmas considerações e regras de sequenciação. Após a conversão das bases de dados para o modelo de compra baseado em vCore, o grupo de failover permanecerá em vigor com as mesmas definições de política.

### <a name="create-a-geo-replication-secondary-database"></a>Criar uma base de dados secundária de geo-replicação

Pode criar uma base de dados secundária de geo-replicação (uma geoconsuserda) apenas utilizando o mesmo nível de serviço utilizado para a base de dados primária. Para bases de dados com uma alta taxa de geração de log, recomendamos a criação do geo-secundário com o mesmo tamanho de cálculo que o principal.

Se estiver a criar um geo-secundário na piscina elástica para uma única base de dados primária, certifique-se de que `maxVCore` a configuração da piscina corresponde ao tamanho do cálculo da base de dados primária. Se estiver a criar um geo-secundário para uma primária em outra piscina elástica, recomendamos que as piscinas tenham as `maxVCore` mesmas configurações.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Utilize a cópia da base de dados para migrar de DTU para vCore

Pode copiar qualquer base de dados com um tamanho de computação baseado em DTU para uma base de dados com um tamanho de computação baseado em vCore sem restrições ou sequenciação especial, desde que o tamanho do cálculo-alvo suporte o tamanho máximo da base de dados da base de dados de origem. A cópia da base de dados cria uma imagem instantânea dos dados a partir do momento de início da operação de cópia e não sincroniza os dados entre a fonte e o alvo.

## <a name="next-steps"></a>Passos seguintes

- Para os tamanhos específicos do cálculo e as opções de tamanho de armazenamento disponíveis para bases de dados individuais, consulte [os limites de recursos baseados em SQL Database vCore para bases de dados individuais.](resource-limits-vcore-single-databases.md)
- Para os tamanhos específicos de computação e opções de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados em SQL Database vCore para piscinas elásticas.](resource-limits-vcore-elastic-pools.md)
