---
title: Base de dados do SQL do Azure adquirir modelos | Documentos da Microsoft
description: Saiba mais sobre o modelo de modelos de compra que são bases de dados disponíveis no serviço de base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 0e9001111d6aa48f0dad69a2fb3b2186bfc37ab7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010506"
---
# <a name="azure-sql-database-purchasing-models"></a>Base de dados do SQL do Azure modelos de compra

Base de dados SQL do Azure permite-lhe comprar facilmente o motor de base de dados de PaaS totalmente gerido que se adeque às suas necessidades de desempenho e custo. Consoante o modelo de implementação de base de dados do Azure SQL, pode selecionar o modelo de compra que atende às suas necessidades:

- [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) (recomendado) que permite-lhe escolher a quantidade exata de capacidade de armazenamento e computação de que precisa para a sua carga de trabalho.
- [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) onde pode escolher agrupados com balanceamento de cargas de trabalho comuns de pacotes de computação e armazenamento.

Diferentes modelos de compras estão disponíveis em modelos de implementação de base de dados do Azure SQL:

- O [base de dados individual](sql-database-single-databases-manage.md) e [conjunto elástico](sql-database-elastic-pool.md) opções de implementação na [SQL Database do Azure](sql-database-technical-overview.md) oferecem ambos o [o modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- O [instância gerida](sql-database-managed-instance.md) só oferece a opção de implementação na base de dados do Azure SQL a [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> O [camada de serviços de hiperescala (pré-visualização)](sql-database-service-tier-hyperscale.md) está em pré-visualização pública apenas para bases de dados individuais, utilizando o modelo de compra de vCore.

O gráfico e tabela seguintes comparam e contrastar esses dois modelos de compras.

|**Modelo de compra**|**Descrição**|**Melhor para**|
|---|---|---|
|modelo baseado em DTU|Este modelo baseia-se numa medida integrados em recursos de computação, armazenamento e e/s. Computação tamanhos são expressos em termos de unidades de transação de base de dados (DTUs) para bases de dados únicas e unidades de transação da base de dados elástica (eDTUs) para conjuntos elásticos. Para obter mais informações sobre DTUs e eDTUs, veja [quais são DTUs e eDTUs?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Ideal para clientes que desejam simples, pré-configurado opções de recursos.|
|modelo baseado em vCore|Este modelo permite-lhe escolher independentemente a recursos de computação e armazenamento. O modelo de compra baseado em vCore também permite que use [benefício híbrido do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter poupanças de custos.|Melhor para os clientes que o valor de flexibilidade, controlo e transparência.|
||||  

![modelo de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Os custos de computação

O custo de computação reflete a capacidade de total de computação é aprovisionada para a aplicação. Na camada de serviço críticos de negócio, podemos atribuir automaticamente, pelo menos, 3 réplicas. Para refletir esta adicional a alocação de recursos de computação, o preço no modelo de compra baseado em vCore é aproximadamente de 2,7 x maiores na camada de serviço crítico comercial que na camada de serviço de fins gerais. Pela mesma razão, o maior preço do armazenamento por GB na camada de serviço críticos de negócio reflete a e/s elevado e baixa latência de armazenamento SSD. Ao mesmo tempo, o custo de armazenamento de cópia de segurança não é diferente entre estas camadas de duas serviço porque em ambos os casos, podemos usar uma classe de armazenamento standard.

## <a name="storage-costs"></a>Custos de armazenamento

Diferentes tipos de armazenamento são cobrados de forma diferente. Para o armazenamento de dados, é-lhe cobrada o com base no tamanho máximo de base de dados ou um conjunto de que selecionar o armazenamento aprovisionado. O custo não altera a menos que reduzir ou aumentar esse máximo. Armazenamento de cópia de segurança está associado a cópias de segurança automáticas da sua instância e podem é alocado dinamicamente. Se aumentar o período de retenção de cópias de segurança, está a aumentar o armazenamento de cópias de segurança que a sua instância consome. 

7 dias de cópias de segurança automatizadas das suas bases de dados são copiados para o armazenamento de blobs Standard RA-GRS por predefinição. O armazenamento é utilizado por cópias de segurança completas semanais, cópias de segurança diferenciais diárias e cópias de segurança de registo de transações copiadas a cada 5 minutos. O tamanho do registo de transação depende da taxa de alteração da base de dados. É dada uma quantidade mínima de armazenamento igual a 100% do tamanho da base de dados sem custos extra. O consumo adicional do armazenamento de cópias de segurança será cobrado em GB/mês.

Para obter mais informações sobre os preços de armazenamento, consulte a [preços](https://azure.microsoft.com/pricing/details/sql-database/single/) página. 

## <a name="vcore-based-purchasing-model"></a>Modelo de compras baseado em vCore

Um núcleo virtual representa a CPU lógica oferecida com a opção de escolher entre gerações de hardware e as características físicas de hardware (por exemplo, número de núcleos, memória, o tamanho de armazenamento). O modelo de compra baseado em vCore dá-lhe flexibilidade, controlo, transparência de consumo de recursos individuais e uma forma direta de traduzir os requisitos de carga de trabalho no local para a cloud. Este modelo permite-lhe escolher a computação, memória e armazenamento com base nas suas necessidades de carga de trabalho. No modelo de compra baseado em vCore, pode escolher entre [fins gerais](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [crítico para a empresa](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) camadas para de serviço [bases de dados únicas](sql-database-single-database-scale.md), [ conjuntos elásticos](sql-database-elastic-pool.md), e [instâncias geridas](sql-database-managed-instance.md). Para bases de dados individuais, também pode escolher o [camada de serviços de hiperescala (pré-visualização)](sql-database-service-tier-hyperscale.md).

O modelo de compra baseado em vCore permite que escolha os recursos de computação e armazenamento, combine o desempenho no local e otimizar o preço de forma independente. No modelo de compra baseado em vCore, os clientes pagam pelo:

- Computação (camada de serviços + número de vCores e a quantidade de memória + geração de hardware)
- Tipo e a quantidade de armazenamento de dados e de registo
- Armazenamento de cópias de segurança (RA-GRS)

> [!IMPORTANT]
> Computação, IOs, dados e armazenamento de registo é cobrada por base de dados ou conjunto elástico. Armazenamento de cópias de segurança é cobrado por cada base de dados. Para obter mais informações sobre as cobranças de instância gerida, veja [instâncias geridas](sql-database-managed-instance.md).
> **Limitações de região:** Para a lista atualizada de regiões suportadas, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se quiser criar uma instância gerida na região que não é atualmente suportado, pode [enviar o pedido de suporte através do portal do Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).
.

Se a sua base de dados individual ou um conjunto elástico consome mais de 300 DTUs, a conversão para o modelo de compra baseado em vCore pode reduzir o custo. Se optar por converter, pode converter a utilizar a API de escolha ou no portal do Azure, sem períodos de indisponibilidade. No entanto, a conversão não é necessária e não é feita automaticamente. Se o modelo de compra baseado em DTU cumpre os requisitos de negócios e desempenho, deve continuar a utilizá-lo. Se optar por converter o modelo de compra baseado em DTU para o modelo de compra baseado em vCore, selecione o tamanho de computação com as seguintes regras básicas: 

- Cada 100 DTUS no escalão Standard requer, pelo menos, 1 vCore na camada de fins gerais
- Cada 125 DTU no escalão Premium requer, pelo menos, 1 vCore na camada de negócio críticos

## <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

As unidades de transação de base de dados (DTU) representa uma medida combinada de CPU, memória, lê e escreve. O modelo de compra baseado em DTU oferece um conjunto de pacotes de recursos de computação pré-configurados e armazenamento para impulsionar diferentes níveis de desempenho do aplicativo incluído. Os clientes que preferem a simplicidade de um pacote pré-configurado e pagamentos fixos a cada mês, pode encontrar o modelo baseado em DTU mais adequado às suas necessidades. No modelo de compra baseado em DTU, os clientes podem escolher entre **básica**, **padrão**, e **premium** escalões de serviço para ambos [bases de dadosúnicas](sql-database-single-database-scale.md) e [conjuntos elásticos](sql-database-elastic-pool.md). Este modelo de compra não está disponível no [instâncias geridas](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unidades de transação de base de dados (DTUs)

Para uma base de dados num específico de computação tamanho dentro de um [escalão de serviço](sql-database-single-database-scale.md), Microsoft garante um determinado nível de recursos para essa base de dados (independentemente de qualquer outro banco de dados na cloud do Azure), fornecendo um nível previsível de desempenho. A quantidade de recursos é calculada como um número de unidades de transação de base de dados ou DTUs e é uma medida integrados em recursos de computação, armazenamento e e/s. A relação entre estes recursos foi originalmente determinada por uma [carga de trabalho do OLTP benchmark](sql-database-benchmark-overview.md), projetado para ser típica das cargas de trabalho do mundo real OLTP. Quando a carga de trabalho excede a quantidade de qualquer um desses recursos, o débito é limitados - resultante no tempos limite e desempenho mais lento. Os recursos utilizados pela sua carga de trabalho não afetam os recursos disponíveis para outros bancos de dados do SQL na cloud do Azure e os recursos utilizados por outras cargas de trabalho não afetam os recursos disponíveis para a base de dados SQL.

![caixa delimitadora](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTUs são mais úteis para compreender o nível relativo de recursos entre bases de dados SQL do Azure em diferentes tamanhos de computação e de escalões de serviço. Por exemplo, duplicar as DTUs aumentando o tamanho de computação de uma base de dados equivale a duplicar o conjunto de recursos disponíveis para essa base de dados. Por exemplo, uma base de dados Premium P11 com 1750 DTUs fornece 350 x mais potência de computação de DTUs do que uma base de dados básica com cinco DTUs.  

Para obter informações mais aprofundadas sobre o consumo de recursos (DTUS) da sua carga de trabalho, utilize [consultar informações de desempenho](sql-database-query-performance.md) para:

- Identificar as consultas principais por contagem de CPU/duração/execução que potencialmente pode ser otimizada para um melhor desempenho. Por exemplo, uma consulta de intensiva de e/s pode beneficiar do uso da opção [técnicas de otimização de memória](sql-database-in-memory.md) para garantir uma melhor utilização da memória disponível numa determinada camada de serviço e tamanho de computação.
- Desagregar os detalhes de uma consulta, ver o texto e o histórico de utilização de recursos.
- Recomendações que mostram as ações realizadas por de otimização de desempenho de acesso [Assistente de base de dados SQL](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unidades de transação de base de dados elástica (eDTUs)

Em vez disso, apenas fornecer um conjunto dedicado de recursos (DTUs) que pode não ser sempre necessária para uma base de dados do SQL que estará sempre disponível, pode colocar as bases de dados num [conjunto elástico](sql-database-elastic-pool.md) num servidor de base de dados SQL que partilham um conjunto de recursos entre essas bases de dados. Os recursos partilhados num conjunto elástico são avaliados por unidades de transação de base de dados de elásticas ou eDTUs. Os conjuntos elásticos fornecem uma solução rentável simples para gerir os objetivos de desempenho de várias bases de dados ter variáveis e padrões de utilização imprevisíveis. Um conjunto elástico garante recursos não podem ser consumidos por um banco de dados no agrupamento, enquanto garantir que cada base de dados no conjunto sempre tem uma quantidade mínima de recursos necessários disponíveis.

Um conjunto é atribuído um número definido de eDTUs por um preço definido. Dentro do conjunto elástico, é dada às bases de dados individuais a flexibilidade para se dimensionarem automaticamente dentro dos limites configurados. Uma base de dados numa carga mais pesada irá consumir mais eDTUs para responder à procura. Bases de dados sob cargas mais leves consumirá menos eDTUs. Bases de dados com qualquer carga não serão não consomem eDTUs. Aprovisionamento de recursos para o conjunto completo, vez por base de dados, tarefas de gestão são simplificadas, fornecendo um orçamento previsível para o conjunto.

Podem ser adicionais mais eDTUs a um conjunto existente sem qualquer período de indisponibilidade da base de dados e sem impacto nas bases de dados do conjunto. Do mesmo modo, se as eDTUs adicionais já não forem necessárias, podem ser removidas de um conjunto existente em qualquer momento. Pode adicionar ou subtrair bases de dados para o pool ou limitar a quantidade de eDTUs que uma base de dados pode utilizar com muita carga para reservar eDTUs para outras bases de dados. Se uma base de dados estiver a subutilizar recursos, pode movê-lo para fora do conjunto e configurá-la como uma base de dados com uma quantidade previsível dos recursos necessários.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinar o número de DTUs necessárias para uma carga de trabalho

Se estiver à procura para migrar um existente no local ou o SQL Server a carga de trabalho de máquina virtual para a base de dados do Azure SQL, pode utilizar o [Calculadora de DTU](https://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessários. Para uma carga de trabalho de base de dados do Azure SQL existente, pode usar [consultar informações de desempenho](sql-database-query-performance.md) para compreender o consumo de recursos de base de dados (DTUs) para obter informações mais aprofundadas sobre para otimizar a sua carga de trabalho. Também pode utilizar o [sys resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV para ver o consumo de recursos para a última hora. Em alternativa, a exibição de catálogo [resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) mostra o consumo de recursos para os últimos 14 dias, mas a uma fidelidade inferior das médias de cinco minutos.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Cargas de trabalho que tiram partido de um conjunto elástico de recursos

Os conjuntos são adequados para um grande número de bases de dados com padrões de utilização específicos. Para um determinado banco de dados, este padrão é caracterizado por uma média de utilização baixa com picos de utilização relativamente raros. A Base de Dados SQL avalia automaticamente o histórico de utilização de recursos de bases de dados num servidor de Base de Dados SQL existente e recomenda a configuração de conjunto adequada no portal do Azure. Para obter mais informações, consulte [When should an elastic pool be used? (Quando deve ser utilizado um conjunto elástico?)](sql-database-elastic-pool.md)

## <a name="purchase-model-frequently-asked-questions-faq"></a>Modelo de compra perguntas mais frequentes (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>É necessário colocar a minha aplicação offline para converter de uma base de dados baseado em DTU para um escalão de serviço baseado em vCore

Os novos escalões de serviço oferecem um método de conversão online simples, semelhante ao processo existente de atualização de bases de dados do escalão de serviço Standard para Premium e vice-versa. Esta conversão pode ser iniciado através do portal do Azure, PowerShell, CLI do Azure, T-SQL ou a API REST. Ver [gerir bases de dados individuais](sql-database-single-database-scale.md) e [gerir conjuntos elásticos](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-using-the-vcore-based-purchase-to-a-service-tier-using-the-dtu-based-purchasing-model"></a>Pode converter uma base de dados de um escalão de serviço com a compra baseado em vCore para um escalão de serviço com o modelo de compra baseado em DTU

Sim, pode converter facilmente sua base de dados para qualquer objetivo de desempenho suportados com o portal do Azure, PowerShell, CLI do Azure, T-SQL ou a API REST. Ver [gerir bases de dados individuais](sql-database-single-database-scale.md) e [gerir conjuntos elásticos](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Passos Seguintes

- Para o modelo de compra baseado em vCore, consulte [o modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md)
- Para o modelo de compra baseado em DTU, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
