---
title: Modelos de compra
description: Conheça os modelos de compra disponíveis para a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/01/2020
ms.openlocfilehash: 0b2eafeec27cb92ccb191ec902e8bf1d581a3b4a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587299"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Escolha entre os modelos de compra vCore e DTU

A Base de Dados Azure SQL permite-lhe facilmente adquirir uma plataforma totalmente gerida como um motor de base de dados de serviço (PaaS) que se adequa às suas necessidades de desempenho e custos. Dependendo do modelo de implementação escolhido para a Base de Dados SQL azure, pode selecionar o modelo de compra que funciona para si:

- [Modelo de compra baseado em núcleo virtual (vCore)](sql-database-service-tiers-vcore.md) (recomendado). Este modelo de compra proporciona uma escolha entre um nível de computação provisionado e um nível de computação sem servidor. Com o nível de cálculo provisionado, você escolhe a quantidade exata de recursos computacionais que são sempre provisionados para a sua carga de trabalho. Com o nível de computação sem servidor, especifica a autoscalcificação dos recursos computacionais numa gama de cálculo configurável. Com este nível de cálculo, também pode parar automaticamente e retomar a base de dados com base na atividade da carga de trabalho. O preço unitário vCore por unidade de tempo é mais baixo no nível de cálculo provisionado do que no nível de computação sem servidor.
- [Unidade de transações de base de dados (modelo de compra baseado em DTU).](sql-database-service-tiers-dtu.md) Este modelo de compra fornece pacotes de cálculo e armazenamento agregados equilibrados para cargas de trabalho comuns.

Diferentes modelos de compra estão disponíveis para diferentes modelos de implementação da Base de Dados Azure SQL:

- A base de [dados única](sql-database-single-databases-manage.md) e as opções de implantação de [piscinas elásticas](sql-database-elastic-pool.md) na Base de Dados [Azure SQL](sql-database-technical-overview.md) oferecem tanto o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) como o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- A opção de implementação de [instânciagerida](sql-database-managed-instance.md) na Base de Dados Azure SQL oferece apenas o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- O [nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md) está disponível para bases de dados únicas que estão a utilizar o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

A tabela e o gráfico seguinte comparam e contrastam os modelos de compra baseados em VCore e os modelos de compra baseados em DTU:

|**Modelo de compra**|**Descrição**|**O melhor para**|
|---|---|---|
|modelo baseado em DTU|Este modelo baseia-se numa medida agregada de recursos computacionais, de armazenamento e de I/O. Os tamanhos computacionais são expressos em DTUs para bases de dados únicas e em unidades de transação de base de dados elásticas (eDTUs) para piscinas elásticas. Para mais informações sobre DTUs e eDTUs, consulte [o que são DTUs e eDTUs?](sql-database-purchase-models.md#dtu-based-purchasing-model)|O melhor para os clientes que querem opções simples e de recursos reconfigurados.|
|modelo baseado em vCore|Este modelo permite-lhe escolher de forma independente os recursos de computação e armazenamento. O modelo de compra baseado em vCore também permite utilizar o [Azure Hybrid Benefit para o SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter poupanças de custos.|O melhor para os clientes que valorizam a flexibilidade, o controlo e a transparência.|
||||  

![comparação de modelos de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Custos de computação

### <a name="provisioned-compute-costs"></a>Custos de cálculo provisionados

No nível de cálculo provisionado, o custo do cálculo reflete a capacidade total do cálculo que está prevista para a aplicação.

No nível de serviço Business Critical, alocamos automaticamente pelo menos 3 réplicas. Para refletir esta alocação adicional de recursos computacionais, o preço no modelo de compra baseado em vCore é aproximadamente 2,7x mais alto no nível de serviço Business Critical do que no nível de serviço De Propósito Geral. Da mesma forma, o preço de armazenamento mais elevado por GB no nível de serviço Business Critical reflete os limites de IO mais elevados e a menor latência do armazenamento SSD.

O custo do armazenamento de backup é o mesmo para o nível de serviço Business Critical e para o nível de serviço General Purpose porque ambos os níveis utilizam armazenamento padrão para backups.

### <a name="serverless-compute-costs"></a>Custos de computação sem servidor

Para uma descrição de como a capacidade da computação é definida e os custos são calculados para o nível de computação sem servidor, consulte o Servidor de Base de [Dados SQL](sql-database-serverless.md).

## <a name="storage-costs"></a>Custos de armazenamento

Diferentes tipos de armazenamento são cobrados de forma diferente. Para armazenamento de dados, é cobrado pelo armazenamento provisionado com base na base de dados máxima ou tamanho da piscina que selecionar. O custo não muda a menos que reduza ou aumente o máximo. O armazenamento de backup está associado a cópias de segurança automatizadas da sua instância e é atribuído dinamicamente. Aumentar o seu período de retenção de cópias de segurança aumenta o armazenamento de reserva que é consumido pela sua instância.

Por padrão, 7 dias de cópias automáticas das suas bases de dados são copiadas para uma conta de armazenamento padrão blob de acesso de leitura (RA-GRS). Este armazenamento é utilizado semanalmente por cópias completas semanais, cópias diárias de backups diferenciais e cópias de registo de transações, que são copiadas a cada 5 minutos. O tamanho dos registos de transações depende da taxa de alteração da base de dados. Um valor mínimo de armazenamento igual a 100% do tamanho da base de dados não é fornecido sem custos adicionais. O consumo adicional de armazenamento de reserva é cobrado em GB por mês.

Para mais informações sobre os preços de armazenamento, consulte a página [de preços.](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

Um núcleo virtual (vCore) representa um CPU lógico e oferece-lhe a opção de escolher entre gerações de hardware e as características físicas do hardware (por exemplo, o número de núcleos, a memória e o tamanho do armazenamento). O modelo de compra baseado em vCore dá-lhe flexibilidade, controlo, transparência do consumo de recursos individuais e uma forma simples de traduzir os requisitos de carga de trabalho no local para a nuvem. Este modelo permite-lhe escolher os recursos de computação, memória e armazenamento com base nas suas necessidades de carga de trabalho.

No modelo de compra baseado em vCore, pode escolher entre os níveis de serviço [De Propósito Geral](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e Business [Critical](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) para bases de [dados únicas,](sql-database-single-database-scale.md) [piscinas elásticas](sql-database-elastic-pool.md)e [instâncias geridas.](sql-database-managed-instance.md) Para bases de dados individuais, também pode escolher o [nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md).

O modelo de compra baseado em vCore permite-lhe escolher de forma independente os recursos de computação e armazenamento, corresponder ao desempenho no local e otimizar o preço. No modelo de compra baseado em vCore, paga-se por:

- Recursos computacionais (o nível de serviço + o número de vCores e a quantidade de memória + a geração de hardware).
- O tipo e a quantidade de dados e armazenamento de registo.
- Armazenamento de cópia de segurança (RA-GRS).

> [!IMPORTANT]
> Os recursos computacionais, O/O, e os dados e armazenamento de registo são cobrados por base de dados ou piscina elástica. O armazenamento de cópia de segurança é cobrado por cada base de dados. Para obter mais informações sobre acusações de exemplo [geridas, consulte casos geridos](sql-database-managed-instance.md).
> **Limitações da região:** Para a lista atual de regiões apoiadas, consulte [os produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) Para criar uma instância gerida numa região que atualmente não é suportada, envie um pedido de [apoio através do portal Azure.](quota-increase-request.md)

Se a sua única base de dados ou piscina elástica consumir mais de 300 DTUs, a conversão para o modelo de compra baseado em vCore pode reduzir os seus custos. Pode converter utilizando a sua API de eleição ou utilizando o portal Azure, sem tempo de inatividade. No entanto, a conversão não é necessária e não é feita automaticamente. Se o modelo de compra baseado em DTU cumprir os seus requisitos de desempenho e negócio, deverá continuar a utilizá-lo.

Para converter do modelo de compra baseado em DTU para o modelo de compra baseado em vCore, selecione o tamanho da computação utilizando as seguintes regras do polegar:

- Cada 100 DTUs no nível padrão requer pelo menos 1 vCore no nível de serviço de Propósito Geral.
- Cada 125 DTUs no nível premium requer pelo menos 1 vCore no nível de serviço Business Critical.

## <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

Uma unidade de transação de base de dados (DTU) representa uma medida misturada de CPU, memória, leituras e escritos. O modelo de compra baseado em DTU oferece um conjunto de pacotes pré-reconfigurados de recursos computacionais e incluiu armazenamento para impulsionar diferentes níveis de desempenho da aplicação. Se preferir a simplicidade de um pacote pré-configurado e pagamentos fixos todos os meses, o modelo baseado em DTU pode ser mais adequado às suas necessidades.

No modelo de compra baseado em DTU, pode escolher entre os níveis básicos, standard e premium de serviço tanto para bases de [dados individuais](sql-database-single-database-scale.md) como [para piscinas elásticas](sql-database-elastic-pool.md). O modelo de compra baseado em DTU não está disponível para [casos geridos](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unidades de transação de bases de dados (DTUs)

Para uma única base de dados com um tamanho de cálculo específico dentro de um nível de [serviço,](sql-database-single-database-scale.md)a Microsoft garante um certo nível de recursos para essa base de dados (independente de qualquer outra base de dados na nuvem Azure). Esta garantia proporciona um nível de desempenho previsível. A quantidade de recursos atribuídos a uma base de dados é calculada como uma série de DTUs e é uma medida agregada de recursos de computação, armazenamento e I/O.

O rácio entre estes recursos é originalmente determinado por uma carga de trabalho de referência de processamento de [transações on-line (OLTP)](sql-database-benchmark-overview.md) projetada para ser típica das cargas de trabalho oLTP no mundo real. Quando a sua carga de trabalho excede a quantidade de qualquer um destes recursos, a sua entrada é acelerada, resultando em desempenho mais lento e intervalos.

Os recursos utilizados pela sua carga de trabalho não afetam os recursos disponíveis para outras bases de dados SQL na nuvem Azure. Da mesma forma, os recursos utilizados por outras cargas de trabalho não afetam os recursos disponíveis na sua base de dados SQL.

![caixa de delimitação](./media/sql-database-what-is-a-dtu/bounding-box.png)

As DTUs são mais úteis para entender os recursos relativos que são atribuídos para bases de dados Azure SQL em diferentes tamanhos de computação e níveis de serviço. Por exemplo:

- Duplicar as DTUs aumentando o tamanho da computação de uma base de dados equivale a duplicar o conjunto de recursos disponíveis para essa base de dados.
- Uma base de dados de nível de serviço Premium P11 com 1750 DTUs fornece 350x mais poder de computação DTU do que uma base de dados de nível de serviço básico com 5 DTUs.  

Para obter uma visão mais profunda do consumo de recursos (DTU) da sua carga de trabalho, utilize insights de desempenho [de consulta](sql-database-query-performance.md) para:

- Identifique as principais consultas por CPU/contagem de duração/execução que possam potencialmente ser sintonizadas para um melhor desempenho. Por exemplo, uma consulta intensiva em I/O pode beneficiar de [técnicas de otimização na memória](sql-database-in-memory.md) para fazer melhor uso da memória disponível num determinado nível de serviço e tamanho de cálculo.
- Aprofundar os detalhes de uma consulta para ver o seu texto e a sua história de utilização de recursos.
- Aceda a recomendações de afinação de desempenho que mostrem as ações tomadas pelo [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unidades de transação de base de dados elásticas (EDTUs)

Para bases de dados SQL que estão sempre disponíveis, em vez de fornecer um conjunto dedicado de recursos (DTUs) que podem nem sempre ser necessários, pode colocar essas bases de dados num [pool elástico](sql-database-elastic-pool.md). As bases de dados de um pool elástico estão num único servidor de base de dados Azure SQL e partilham um conjunto de recursos.

Os recursos partilhados num pool elástico são medidos por unidades de transação elásticas de base de dados (eDTUs). As piscinas elásticas fornecem uma solução simples e rentável para gerir objetivos de desempenho para várias bases de dados que têm padrões de utilização amplamente variados e imprevisíveis. Um conjunto elástico garante que todos os recursos não podem ser consumidos por uma base de dados na piscina, garantindo ao mesmo tempo que cada base de dados da piscina tem sempre uma quantidade mínima de recursos necessários disponíveis.

Uma piscina é dada um número definido de eDTUs por um preço definido. Na piscina elástica, as bases de dados individuais podem ser automaticamente dimensionadas dentro dos limites configurados. Uma base de dados sob uma carga mais pesada consumirá mais EDTUs para satisfazer a procura. As bases de dados sob cargas mais leves consumirão menos EDTUs. Bases de dados sem carga não consumirão EDTUs. Como os recursos são aprovisionados para todo o pool, em vez de por base de dados, os pools elásticos simplificam as suas tarefas de gestão e fornecem um orçamento previsível para a piscina.

Pode adicionar eDTUs adicionais a uma piscina existente sem tempo de paragem na base de dados e sem impacto nas bases de dados da piscina. Da mesma forma, se já não precisar de eDTUs extra, remova-os de uma piscina existente a qualquer momento. Também pode adicionar bases de dados ou subtrair bases de dados de uma piscina a qualquer momento. Para reservar eDTUs para outras bases de dados, limite o número de EDTUs que uma base de dados pode utilizar sob uma carga pesada. Se uma base de dados subutiliza sistematicamente os recursos, mova-os para fora da piscina e configure-os como uma única base de dados com uma quantidade previsível de recursos necessários.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinar o número de DTUs necessários por uma carga de trabalho

Se pretender migrar uma carga de trabalho virtual existente no local ou no SQL Server para a Base de Dados Azure SQL, utilize a [calculadora DTU](https://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessários. Para uma carga de trabalho de base de dados Azure SQL existente, use [insights de desempenho de consulta](sql-database-query-performance.md) para entender o seu consumo de recursos de base de dados (DTUs) e obtenha informações mais profundas para otimizar a sua carga de trabalho. A visão dinâmica de gestão (DMV) do [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) permite-lhe visualizar o consumo de recursos durante a última hora. A vista de catálogo [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) exibe o consumo de recursos nos últimos 14 dias, mas com uma menor fidelidade de cinco minutos.

### <a name="determine-dtu-utilization"></a>Determinar a utilização do DTU

Para determinar a percentagem média de utilização dTU/eDTU em relação ao limite DTU/eDTU de uma base de dados ou de um pool elástico, utilize a seguinte fórmula:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Os valores de entrada para esta fórmula podem ser obtidos a partir de [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database), e [dMVs sys.elastic_pool_resource_stats.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) Por outras palavras, para determinar a percentagem de utilização da DTU/eDTU em relação ao limite DTU/eDTU de uma base de dados ou de um pool elástico, escolha o maior valor percentual do seguinte: `avg_cpu_percent`, `avg_data_io_percent`e `avg_log_write_percent` num dado momento.

> [!NOTE]
> O limite dTU de uma base de dados é determinado pela CPU, lê-se, escreve e memória disponível na base de dados. No entanto, uma vez que o motor de base de dados Do SQL Server normalmente utiliza toda a memória disponível para a sua cache de dados para melhorar o desempenho, o valor `avg_memory_usage_percent` geralmente será perto de 100% independentemente da carga atual da base de dados. Portanto, embora a memória influencie indiretamente o limite de DTU, não é utilizada na fórmula de utilização do DTU.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Cargas de trabalho que beneficiam de um conjunto elástico de recursos

As piscinas são adequadas para bases de dados com uma média de utilização de recursos baixa e picos de utilização relativamente pouco frequentes. Para mais informações, consulte [Quando deve considerar um elástico SQL Database?](sql-database-elastic-pool.md)

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (Perguntas Frequentes)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Preciso de desligar a minha aplicação para me converter de um nível de serviço baseado em DTU para um nível de serviço baseado em VCore?

Não. Não precisas de desligar a aplicação. Os novos níveis de serviço oferecem um método simples de conversão on-line que é semelhante ao processo existente de upgrade de bases de dados do padrão para o nível de serviço premium e o contrário. Pode iniciar esta conversão utilizando o portal Azure, PowerShell, o Azure CLI, T-SQL ou o REST API. Ver [Gerir bases de dados individuais](sql-database-single-database-scale.md) e gerir [piscinas elásticas](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Posso converter uma base de dados de um nível de serviço no modelo de compra baseado em vCore para um nível de serviço no modelo de compra baseado em DTU?

Sim, pode facilmente converter a sua base de dados em qualquer objetivo de desempenho suportado utilizando o portal Azure, PowerShell, o Azure CLI, T-SQL ou o REST API. Ver [Gerir bases de dados individuais](sql-database-single-database-scale.md) e gerir [piscinas elásticas](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o modelo de compra baseado em vCore, consulte o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- Para obter mais informações sobre o modelo de compra baseado em DTU, consulte o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
