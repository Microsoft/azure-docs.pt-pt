---
title: Modelos de compra
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Conheça os modelos de compra que estão disponíveis para Azure SQL Database e Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 8883263d6ddb2fb8ddc809f464288fcd282531bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788830"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>Escolha entre os modelos de compra vCore e DTU - Azure SQL Database e SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A azure SQL Database e Azure SQL Managed Instance permitem-lhe facilmente adquirir uma plataforma totalmente gerida como um motor de base de dados de serviço (PaaS) que se adequa ao seu desempenho e necessidades de custos. Dependendo do modelo de implementação que escolheu para a Base de Dados Azure SQL, pode selecionar o modelo de compra que funciona para si:

- [Modelo de compra baseado em núcleo virtual (vCore)(recomendado).](service-tiers-vcore.md) Este modelo de compra proporciona uma escolha entre um nível de cálculo provisionado e um nível de computação sem servidor. Com o nível de computação a provisionado, você escolhe a quantidade exata de recursos computacional que são sempre a provisionados para a sua carga de trabalho. Com o nível de computação sem servidor, especifica a autoscalagem dos recursos computacional sobre uma gama de cálculos configuráveis. Com este nível de cálculo, também pode parar e retomar automaticamente a base de dados com base na atividade da carga de trabalho. O preço unitário vCore por unidade de tempo é mais baixo no nível de cálculo provisionado do que no nível de computação sem servidor.
- [Modelo de compra baseado em unidade de transação de dados (DTU).](service-tiers-dtu.md) Este modelo de compra fornece pacotes de computação e armazenamento agregados equilibrados para cargas de trabalho comuns.

Existem dois modelos de compra:

- [o modelo de compra baseado em vCore](service-tiers-vcore.md) está disponível tanto para [a Base de Dados Azure SQL](sql-database-paas-overview.md) como para [a Azure SQL Gerenciada.](../managed-instance/sql-managed-instance-paas-overview.md) O [nível de serviço Hyperscale](service-tier-hyperscale.md) está disponível para bases de dados únicas que estão a utilizar o [modelo de compra baseado em vCore.](service-tiers-vcore.md)
- [O modelo de compra baseado em DTU](service-tiers-dtu.md) está disponível para a Base de [Dados Azure SQL](single-database-manage.md).

A tabela e gráfico a seguir comparam e contrastam os modelos de compra baseados em VCore e DTU:

|**Modelo de compra**|**Descrição**|**Melhor para**|
|---|---|---|
|Baseado em DTU|Este modelo baseia-se numa medida agregada de recursos de computação, armazenamento e E/S. Os tamanhos da computação são expressos em DTUs para bases de dados individuais e em unidades de transação de base de dados elástica (eDTUs) para conjuntos elásticos. Para obter mais informações sobre DTUs e eDTUs, veja [O que são DTUs e eDTUs?](purchasing-models.md#dtu-based-purchasing-model).|Clientes que querem opções de recursos simples e pré-configuradas|
|Baseado em vCore|Este modelo permite-lhe escolher independentemente os recursos de computação e armazenamento. O modelo de compra baseado em vCore também lhe permite utilizar o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para o SQL Server para reduzir custos.|Clientes que valorizam flexibilidade, controlo e transparência|
||||  

![Comparação de modelos de preços](./media/purchasing-models/pricing-model.png)

Quer otimizar e economizar nos gastos na nuvem?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>Custos de computação

### <a name="provisioned-compute-costs"></a>Custos de computação provisados

No nível de cálculo previsto, o custo do cálculo reflete a capacidade total de computação prevista para a aplicação.

No nível de serviço Business Critical, alocamos automaticamente pelo menos três réplicas. Para refletir esta alocação adicional de recursos computacional, o preço no modelo de compra baseado em vCore é aproximadamente 2,7 vezes maior no nível de serviço Business Critical do que no nível de serviço De propósito geral. Da mesma forma, o preço de armazenamento mais elevado por GB no nível de serviço Business Critical reflete os limites de IO mais elevados e a menor latência do armazenamento SSD.

O custo do armazenamento de backup é o mesmo para o nível de serviço Business Critical e para o nível de serviço De Fim Geral, porque ambos os níveis usam armazenamento padrão para backups.

### <a name="serverless-compute-costs"></a>Custos de computação sem servidor

Para uma descrição de como a capacidade de cálculo é definida e os custos são calculados para o nível de cálculo sem servidor, consulte o [nível sem servidor sql Database](serverless-tier-overview.md).

## <a name="storage-costs"></a>Custos de armazenamento

Diferentes tipos de armazenamento são faturados de forma diferente. Para armazenamento de dados, é cobrado o armazenamento forentado com base na base de dados máxima ou no tamanho da piscina que seleciona. O custo não muda a menos que reduza ou aumente o máximo. O armazenamento de backup está associado a cópias de segurança automatizadas do seu caso e é atribuído dinamicamente. Aumentar o seu período de retenção de backup aumenta o armazenamento de backup que é consumido pelo seu exemplo.

Por predefinição, sete dias de cópias de segurança automatizadas das suas bases de dados são copiados para uma conta de armazenamento blob padrão de acesso de leitura (RA-GRS). Este armazenamento é utilizado por cópias de segurança completas semanais, cópias de segurança diferenciais diárias e cópias de segurança de registo de transações, que são copiadas a cada cinco minutos. O tamanho dos registos de transações depende da taxa de alteração da base de dados. Um valor mínimo de armazenamento igual a 100 por cento do tamanho da base de dados é fornecido sem custos adicionais. O consumo adicional de armazenamento de backup é cobrado em GB por mês.

Para obter mais informações sobre os preços de armazenamento, consulte a página [de preços.](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

Um núcleo virtual (vCore) representa um CPU lógico e oferece-lhe a opção de escolher entre gerações de hardware e as características físicas do hardware (por exemplo, o número de núcleos, a memória e o tamanho do armazenamento). O modelo de compra baseado em vCore dá-lhe flexibilidade, controlo, transparência do consumo individual de recursos e uma forma simples de traduzir os requisitos de carga de trabalho no local para a nuvem. Este modelo permite-lhe escolher recursos de computação, memória e armazenamento com base nas suas necessidades de carga de trabalho.

No modelo de compra baseado em vCore, pode escolher entre os níveis de serviço [De Propósito Geral](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e Critical de [Negócios](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) para A Base de Dados SQL e SqL Managed Instance.  Para bases de dados individuais, também pode escolher o [nível de serviço Hyperscale.](service-tier-hyperscale.md)

O modelo de compra baseado em vCore permite-lhe escolher independentemente os recursos de computação e armazenamento, combinar o desempenho no local e otimizar o preço. No modelo de compra baseado em vCore, você paga por:

- Os recursos de computação (escalão de serviço + número de vCores e a quantidade de memória + geração do hardware).
- O tipo e a quantidade de armazenamento de dados e registos.
- Armazenamento de backup (RA-GRS).

> [!IMPORTANT]
> Os recursos de cálculo, I/O e os dados e armazenamento de registo são cobrados por base de dados ou piscina elástica. O armazenamento de cópia de segurança é cobrado por cada base de dados. Para obter mais informações sobre os encargos de exemplo gerido da SQL, consulte [sql Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md).
> **Limitações da região:** Para a lista atual de regiões apoiadas, consulte [os produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) Para criar um caso gerido numa região que atualmente não é apoiada, [envie um pedido de apoio através do portal Azure.](quota-increase-request.md)

Se a sua base de dados consumir mais de 300 DTUs, converter-se no modelo de compra baseado em vCore pode reduzir os seus custos. Pode converter-se utilizando a sua API de eleição ou utilizando o portal Azure, sem tempo de inatividade. No entanto, a conversão não é necessária e não é feita automaticamente. Se o modelo de compra baseado em DTU satisfaça os seus requisitos de desempenho e negócio, deverá continuar a usá-lo.

Para converter do modelo de compra baseado em DTU para o modelo de compra baseado em vCore, consulte [Migrar de DTU para vCore](migrate-dtu-to-vcore.md).

## <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

Uma unidade de transação de base de dados (DTU) representa uma medida misturada de CPU, memória, leituras e escritas. O modelo de compra baseado em DTU oferece um conjunto de pacotes pré-configurados de recursos computacional e incluiu armazenamento para impulsionar diferentes níveis de desempenho da aplicação. Se preferir a simplicidade de um pacote pré-configurado e pagamentos fixos todos os meses, o modelo baseado em DTU pode ser mais adequado às suas necessidades.

No modelo de compra baseado na DTU, pode escolher entre os níveis básicos, standard e premium de serviço para Azure SQL Database. O modelo de compra baseado em DTU não está disponível para Azure SQL Managed Instance.

### <a name="database-transaction-units-dtus"></a>Unidades de transação de bases de dados (DTUs)

Para uma única base de dados com um tamanho computacional específico dentro de um nível de [serviço,](single-database-scale.md)o Azure garante um determinado nível de recursos para essa base de dados (independente de qualquer outra base de dados na nuvem Azure). Esta garantia proporciona um nível de desempenho previsível. A quantidade de recursos atribuídos a uma base de dados é calculada como um número de DTUs e é uma medida agregada de recursos de computação, armazenamento e E/S.

O rácio entre estes recursos é originalmente determinado por uma carga de trabalho de [referência de processamento de transações on-line (OLTP)](service-tiers-dtu.md) concebida para ser típica das cargas de trabalho OLTP no mundo real. Quando a sua carga de trabalho excede a quantidade de qualquer um destes recursos, a sua produção é acelerada, resultando num desempenho mais lento e em intervalos de tempo.

Os recursos utilizados pela sua carga de trabalho não afetam os recursos disponíveis para outras bases de dados na nuvem Azure. Da mesma forma, os recursos utilizados por outras cargas de trabalho não afetam os recursos disponíveis na sua base de dados.

![Caixa de delimitação](./media/purchasing-models/bounding-box.png)

Os DTUs são mais úteis para compreender os recursos relativos que são atribuídos para bases de dados em diferentes tamanhos de cálculo e níveis de serviço. Por exemplo:

- Duplicar os DTUs aumentando o tamanho do cálculo de uma base de dados equivale a duplicar o conjunto de recursos disponíveis para essa base de dados.
- Uma base de dados P11 de nível de serviço premium com 1750 DTUs fornece 350 vezes mais poder de computação DTU do que uma base de dados básica de nível de serviço com 5 DTUs.  

Para obter uma visão mais profunda do consumo de recursos (DTU) da sua carga de trabalho, utilize [insights de desempenho de consulta](query-performance-insight-use.md) para:

- Identifique as consultas de topo por CPU/duração/contagem de execução que podem potencialmente ser sintonizadas para um melhor desempenho. Por exemplo, uma consulta intensiva de E/O pode beneficiar [de técnicas de otimização na memória](../in-memory-oltp-overview.md) para melhor utilizar a memória disponível num determinado nível de serviço e tamanho de cálculo.
- Aprofundar os detalhes de uma consulta para ver o seu texto e o seu histórico de utilização de recursos.
- Aceda às recomendações de afinação de desempenho que mostram ações tomadas pelo [SqL Database Advisor](database-advisor-implement-performance-recommendations.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unidades elásticas de transação de bases de dados (eDTUs)

Para bases de dados que estão sempre disponíveis, em vez de fornecer um conjunto dedicado de recursos (DTUs) que podem nem sempre ser necessários, você pode colocar estas bases de dados em uma [piscina elástica.](elastic-pool-overview.md) As bases de dados de uma piscina elástica estão num único servidor e partilham um conjunto de recursos.

Os recursos partilhados num pool elástico são medidos por unidades elásticas de transação de bases de dados (eDTUs). As piscinas elásticas fornecem uma solução simples e rentável para gerir objetivos de desempenho para várias bases de dados que têm padrões de utilização amplamente variados e imprevisíveis. Uma piscina elástica garante que todos os recursos não podem ser consumidos por uma base de dados na piscina, garantindo ao mesmo tempo que cada base de dados na piscina tem sempre um mínimo de recursos necessários disponíveis.

Uma piscina recebe um número definido de eDTUs por um preço definido. Na piscina elástica, as bases de dados individuais podem ser autoescaladas dentro dos limites configurados. Uma base de dados sob uma carga mais pesada consumirá mais eDTUs para satisfazer a procura. As bases de dados sob cargas mais leves consumirão menos eDTUs. As bases de dados sem carga não consumirão eDTUs. Como os recursos são a provisionados para toda a piscina, em vez de por base de dados, piscinas elásticas simplificam as suas tarefas de gestão e fornecem um orçamento previsível para a piscina.

Pode adicionar eDTUs adicionais a um pool existente sem tempo de inatividade na base de dados e sem impacto nas bases de dados da piscina. Da mesma forma, se já não precisar de eDTUs extra, retire-os de uma piscina existente a qualquer momento. Também pode adicionar bases de dados ou subtrair bases de dados de um pool a qualquer momento. Para reservar eDTUs para outras bases de dados, limite o número de eDTUs que uma base de dados pode usar sob uma carga pesada. Se uma base de dados subutilizá-la de forma consistente, desloque-a para fora da piscina e configuure-a como uma única base de dados com uma quantidade previsível de recursos necessários.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinar o número de DTUs necessário por uma carga de trabalho

Se pretender migrar uma carga de trabalho virtual existente no local ou da máquina virtual SQL Server para a Base de Dados SQL, utilize a [calculadora DTU](https://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessários. Para uma carga de trabalho existente na Base de Dados SQL, utilize [insights de desempenho de consulta](query-performance-insight-use.md) para compreender o seu consumo de recursos de base de dados (DTUs) e obtenha informações mais profundas para otimizar a sua carga de trabalho. A [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) visão dinâmica de gestão (DMV) permite-lhe ver o consumo de recursos durante a última hora. A [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vista do catálogo exibe o consumo de recursos nos últimos 14 dias, mas com uma menor fidelidade de médias de cinco minutos.

### <a name="determine-dtu-utilization"></a>Determinar a utilização do DTU

Para determinar a percentagem média de utilização do DTU/eDTU em relação ao limite DTU/eDTU de uma base de dados ou de um pool elástico, utilize a seguinte fórmula:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Os valores de entrada desta fórmula podem ser obtidos a partir de [sys.dm_db_resource_stats,](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMVs. Por outras palavras, para determinar a percentagem de utilização do DTU/eDTU para o limite DTU/eDTU de uma base de dados ou de um pool elástico, escolha o maior valor percentual a partir do seguinte: `avg_cpu_percent` , e num dado `avg_data_io_percent` `avg_log_write_percent` momento.

> [!NOTE]
> O limite de DTU de uma base de dados é determinado pela CPU, lê-se, escreve e memória disponível para a base de dados. No entanto, como o motor sql Database normalmente utiliza toda a memória disponível para a sua cache de dados para melhorar o desempenho, o `avg_memory_usage_percent` valor será geralmente próximo de 100 por cento, independentemente da carga atual da base de dados. Portanto, embora a memória influencie indiretamente o limite de DTU, não é utilizada na fórmula de utilização do DTU.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Cargas de trabalho que beneficiam de um conjunto elástico de recursos

As piscinas são adequadas para bases de dados com uma média de utilização de recursos baixa e picos de utilização relativamente pouco frequentes. Para mais informações, veja [quando deve considerar uma piscina elástica SQL Database?](elastic-pool-overview.md)

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Gerações de hardware no modelo de compra baseado na DTU

No modelo de compra baseado na DTU, os clientes não podem escolher a geração de hardware utilizada para as suas bases de dados. Embora uma dada base de dados geralmente permaneça numa geração específica de hardware por muito tempo (geralmente por vários meses), existem certos eventos que podem fazer com que uma base de dados seja transferida para outra geração de hardware.

Por exemplo, uma base de dados pode ser transferida para uma geração de hardware diferente se for dimensionada para um objetivo de serviço diferente, ou se a infraestrutura atual num datacenter estiver a aproximar-se dos seus limites de capacidade, ou se o hardware atualmente utilizado estiver a ser desativado devido ao seu fim de vida.

Se uma base de dados for transferida para um hardware diferente, o desempenho da carga de trabalho pode mudar. O modelo DTU garante que o tempo de produção e resposta da carga de trabalho de referência da [DTU](./service-tiers-dtu.md#dtu-benchmark) permanecerá substancialmente idêntico à medida que a base de dados se move para uma geração diferente de hardware, desde que o seu objetivo de serviço (o número de DTUs) permaneça o mesmo.

No entanto, em todo o vasto espectro de cargas de trabalho dos clientes em execução na Base de Dados Azure SQL, o impacto da utilização de diferentes hardware para o mesmo objetivo de serviço pode ser mais pronunciado. Diferentes cargas de trabalho beneficiarão de diferentes configurações e funcionalidades de hardware. Portanto, para cargas de trabalho diferentes do benchmark DTU, é possível ver diferenças de desempenho se a base de dados passar de uma geração de hardware para outra.

Por exemplo, uma aplicação sensível à latência da rede pode ver um melhor desempenho no hardware da Gen5 vs. Gen4 devido ao uso de Rede Acelerada na Gen5, mas uma aplicação usando IO de leitura intensiva pode ver melhor desempenho no hardware da Gen4 contra Gen5 devido a uma maior relação de memória por núcleo na Gen4.

Os clientes com cargas de trabalho sensíveis a alterações de hardware ou clientes que desejem controlar a escolha da geração de hardware para a sua base de dados podem usar o modelo [vCore](service-tiers-vcore.md) para escolher a sua geração de hardware preferida durante a criação e dimensionamento da base de dados. No modelo vCore, são documentados limites de recursos de cada objetivo de serviço em cada geração de hardware, tanto para [bases de dados individuais](resource-limits-vcore-single-databases.md) como [para piscinas elásticas.](resource-limits-vcore-elastic-pools.md) Para obter mais informações sobre as gerações de hardware no modelo vCore, consulte as [gerações de Hardware](./service-tiers-vcore.md#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (PERGUNTAS Frequentes)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Preciso de tirar a minha aplicação offline para converter de um nível de serviço baseado em DTU para um nível de serviço baseado em vCore?

N.º Não precisas de desligar a aplicação. Os novos níveis de serviço oferecem um método simples de conversão on-line semelhante ao processo existente de upgrade de bases de dados do padrão para o nível de serviço premium e o contrário. Pode iniciar esta conversão utilizando o portal Azure, PowerShell, O Azure CLI, T-SQL ou a API REST. Veja [Gerir bases de dados únicas](single-database-scale.md) e [Gerir conjuntos elásticos](elastic-pool-overview.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Posso converter uma base de dados de um nível de serviço no modelo de compra baseado em vCore para um nível de serviço no modelo de compra baseado em DTU?

Sim, pode converter facilmente a sua base de dados para qualquer objetivo de desempenho suportado utilizando o portal Azure, PowerShell, O CLI Azure, T-SQL ou a API REST. Veja [Gerir bases de dados únicas](single-database-scale.md) e [Gerir conjuntos elásticos](elastic-pool-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o modelo de compra baseado em vCore, consulte [o modelo de compra baseado em vCore.](service-tiers-vcore.md)
- Para obter mais informações sobre o modelo de compra baseado na DTU, consulte o [modelo de compra baseado na DTU.](service-tiers-dtu.md)