---
title: Base de dados do SQL do Azure adquirir modelos | Documentos da Microsoft
description: Saiba mais sobre os modelos de compras que estão disponíveis para a base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431364"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Escolher entre o vCore e a DTU modelos de compra

Base de dados SQL do Azure permite-lhe facilmente comprar uma plataforma totalmente gerida como um mecanismo de banco de dados do serviço (PaaS) que se adeque às suas necessidades de desempenho e custo. Consoante o modelo de implementação que escolheu para a base de dados do Azure SQL, pode selecionar o modelo de compra que funcione para si:

- [Núcleo virtual (vCore)-modelo de compra baseado em](sql-database-service-tiers-vcore.md) (recomendado). Este modelo de compra permite escolher entre uma camada de computação aprovisionada e uma camada de computação sem servidor (pré-visualização). Com o escalão de computação aprovisionada, é possível escolher a quantidade exata de recursos de computação que sempre são aprovisionados para a sua carga de trabalho. Com o escalão de computação sem servidor, é possível especificar o dimensionamento automático dos recursos de computação através de um intervalo de computação configuráveis. Com este escalão de computação, pode colocar em pausa e retomar a base de dados com base na atividade de carga de trabalho também automaticamente. O preço unitário de vCore por unidade de tempo é mais baixo e na camada de computação aprovisionada no que na camada de computação sem servidor.
- [Unidade de transação de base de dados (DTU)-modelo de compra baseado em](sql-database-service-tiers-dtu.md). Este modelo de compra fornece pacotes de computação e armazenamento agrupadas, com balanceamento de cargas de trabalho comuns.

Diferentes modelos de compras estão disponíveis para diferentes modelos de implementação de base de dados do Azure SQL:

- O [base de dados individual](sql-database-single-databases-manage.md) e [conjunto elástico](sql-database-elastic-pool.md) opções de implementação na [SQL Database do Azure](sql-database-technical-overview.md) oferecem ambos o [o modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- O [instância gerida](sql-database-managed-instance.md) opção de implementação na base de dados do Azure SQL oferece apenas a [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- O [camada de serviços de hiperescala](sql-database-service-tier-hyperscale.md) está disponível para bases de dados individuais que estão a utilizar o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

O gráfico e tabela seguintes comparam e contrastar a baseado em vCore e os modelos de compras baseado em DTU:

|**Modelo de compra**|**Descrição**|**Melhor para**|
|---|---|---|
|modelo baseado em DTU|Este modelo baseia-se numa medida integrados em recursos de computação, armazenamento e e/s. Tamanhos de computação são expressos em DTUs para bases de dados individuais e em unidades de transação de bases de dados elásticas (eDTUs) para conjuntos elásticos. Para obter mais informações sobre DTUs e eDTUs, veja [quais são DTUs e eDTUs?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Ideal para clientes que desejam simples, pré-configurada opções de recursos.|
|modelo baseado em vCore|Este modelo permite-lhe escolher independentemente a recursos de computação e armazenamento. O modelo de compra baseado em vCore também permite que use [benefício híbrido do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter poupanças de custos.|Melhor para os clientes que o valor de flexibilidade, controlo e transparência.|
||||  

![comparação de modelo de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Os custos de computação

### <a name="provisioned-compute-costs"></a>Custos de computação aprovisionada

Na camada de computação aprovisionada, o custo de computação reflete a capacidade de total de computação é aprovisionada para a aplicação.

Na camada de serviço críticos de negócio, podemos atribuir automaticamente, pelo menos, 3 réplicas. Para refletir esta adicional a alocação de recursos de computação, o preço no modelo de compra baseado em vCore é aproximadamente 2.7 x superior na camada de serviço críticos de negócio, que é da camada de serviço de fins gerais. Da mesma forma, o maior preço do armazenamento por GB na camada de serviço críticos de negócio reflete a e/s elevado e baixa latência de armazenamento SSD.

O custo de armazenamento de cópia de segurança é o mesmo para a camada de serviços críticos de negócio e a camada de serviços de fins gerais, uma vez que ambas as camadas de utilizar o armazenamento standard.

### <a name="serverless-compute-costs"></a>Custos de computação sem servidor

Para uma descrição de como a capacidade de computação é definida e os custos são calculados para o escalão de computação sem servidor, consulte [base de dados SQL sem servidor (pré-visualização)](sql-database-serverless.md).

## <a name="storage-costs"></a>Custos de armazenamento

Diferentes tipos de armazenamento são cobrados de forma diferente. Para o armazenamento de dados, que lhe é cobrada para o armazenamento aprovisionado com base no tamanho máximo de base de dados ou um conjunto de que selecionar. O custo não altera a menos que reduzir ou aumentar esse máximo. Armazenamento de cópia de segurança está associado a cópias de segurança automáticas da sua instância e podem é alocado dinamicamente. Aumentar o período de retenção de cópia de segurança aumentar o armazenamento de cópia de segurança que é sua instância consome.

Por predefinição, 7 dias de cópias de segurança automatizadas das bases de dados são copiados para uma conta de armazenamento de BLOBs padrão do armazenamento georredundante de acesso de leitura (RA-GRS). Este armazenamento é utilizado por cópias de segurança completas semanais, cópias de segurança diferenciais diárias e backups de log de transação, o que são copiados a cada 5 minutos. O tamanho dos logs de transação depende da taxa de alteração da base de dados. Uma quantidade mínima de armazenamento igual a 100 por cento do tamanho da base de dados é fornecida sem custos adicionais. O consumo de armazenamento de cópia de segurança adicional é cobrado em GB por mês.

Para obter mais informações sobre os preços de armazenamento, consulte a [preços](https://azure.microsoft.com/pricing/details/sql-database/single/) página.

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

Um núcleo virtual (vCore) representa uma CPU lógica e oferece-lhe a opção de escolher entre gerações de hardware e as características físicas do hardware (por exemplo, o número de núcleos, memória e o tamanho de armazenamento). O modelo de compra baseado em vCore oferece-lhe flexibilidade, controlo, transparência de consumo de recursos individuais, e uma forma direta de traduzir no local requisitos de carga de trabalho para a cloud. Este modelo permite-lhe escolher os recursos de computação, memória e armazenamento com base nas suas necessidades de carga de trabalho.

No modelo de compra baseado em vCore, pode escolher entre o [fins gerais](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [crítico para a empresa](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) camadas para de serviço [bases de dados únicas](sql-database-single-database-scale.md), [ conjuntos elásticos](sql-database-elastic-pool.md), e [instâncias geridas](sql-database-managed-instance.md). Para bases de dados individuais, também pode escolher o [camada de serviços de hiperescala](sql-database-service-tier-hyperscale.md).

O modelo de compra baseado em vCore permite que escolha os recursos de computação e armazenamento, combine o desempenho no local e otimizar o preço de forma independente. No modelo de compra baseado em vCore, paga para:

- Os recursos de computação (camada de serviço + o número de vCores e a quantidade de memória + a geração de hardware).
- O tipo e a quantidade de armazenamento de dados e de registo.
- Armazenamento de cópias de segurança (RA-GRS).

> [!IMPORTANT]
> Recursos de computação, e/s e armazenamento de dados e de registo são cobrados por base de dados ou conjunto elástico. Armazenamento de cópia de segurança é cobrado por cada base de dados. Para obter mais informações sobre as cobranças de instância gerida, veja [instâncias geridas](sql-database-managed-instance.md).
> **Limitações de região:** Para a lista atualizada de regiões suportadas, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar uma instância gerida numa região que atualmente não é suportada [enviar um pedido de suporte através do portal do Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Se a sua base de dados individual ou um conjunto elástico consome mais de 300 DTUs, a conversão para o modelo de compra baseado em vCore pode reduzir seus custos. Pode converter com a API de escolha ou ao utilizar o portal do Azure, sem períodos de indisponibilidade. No entanto, a conversão não é obrigatória e não é feita automaticamente. Se o modelo de compra baseado em DTU cumpre os requisitos de negócios e desempenho, deve continuar a utilizá-lo.

Para converter o modelo de compra baseado em DTU para o modelo de compra baseado em vCore, selecione o tamanho de computação, utilizando as seguintes regras básicas:

- Cada 100 DTUs no escalão standard, requerem, pelo menos, 1 vCore na camada de serviço de fins gerais.
- Cada 125 DTUs no escalão premium requerem, pelo menos, 1 vCore na camada de serviço críticos de negócio.

## <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

Uma unidade de transação de base de dados (DTU) representa uma medida combinada de CPU, memória, leituras e escritas. O modelo de compra baseado em DTU oferece um conjunto de pacotes de recursos de computação pré-configurados e armazenamento para impulsionar diferentes níveis de desempenho do aplicativo incluído. Se preferir a simplicidade de pagamentos fixos e de um pacote pré-configurado com todos os meses, o modelo baseado em DTU poderá ser mais adequado para as suas necessidades.

No modelo de compra baseado em DTU, pode escolher entre o básico, standard e escalões de serviço premium para os dois [bases de dados únicas](sql-database-single-database-scale.md) e [conjuntos elásticos](sql-database-elastic-pool.md). Não está disponível para o modelo de compra baseado em DTU [instâncias geridas](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unidades de transação de base de dados (DTUs)

Para uma base de dados num específico de computação tamanho dentro de um [escalão de serviço](sql-database-single-database-scale.md), Microsoft garante um determinado nível de recursos para essa base de dados (independentemente de qualquer outro banco de dados na cloud do Azure). Essa garantia fornece um nível previsível de desempenho. A quantidade de recursos alocados para uma base de dados é calculada como um número de DTUs e é uma medida integrados em recursos de computação, armazenamento e e/s.

O rácio entre esses recursos originalmente é determinado por um [carga de trabalho do processamento de transações online (OLTP) benchmark](sql-database-benchmark-overview.md) concebido para ser típica das cargas de trabalho do mundo real OLTP. Quando a carga de trabalho excede a quantidade de qualquer um desses recursos, o débito é limitado, resultando em desempenho mais lento e de tempos limite.

Os recursos utilizados pela sua carga de trabalho não tenham impacto sobre os recursos disponíveis para outros bancos de dados do SQL na cloud do Azure. Da mesma forma, os recursos utilizados por outras cargas de trabalho não tenham impacto sobre os recursos disponíveis para a base de dados SQL.

![caixa delimitadora](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTUs são mais úteis para compreender os recursos relativos que são alocados para bases de dados SQL do Azure em diferentes tamanhos de computação e de escalões de serviço. Por exemplo:

- Duplicar as DTUs aumentando o tamanho de computação de uma base de dados equivale a duplicar o conjunto de recursos disponíveis para essa base de dados.
- Uma base do premium service escalão P11 com 1750 DTUs fornece 350 x DTU mais capacidade computacional que uma base de dados de escalão de serviço básico com 5 DTUs.  

Para obter informações mais aprofundadas sobre o consumo de recursos (DTUS) da sua carga de trabalho, utilize [informações de desempenho de consulta](sql-database-query-performance.md) para:

- Identificar as consultas principais por contagem de CPU/duração/execução que potencialmente pode ser otimizada para um melhor desempenho. Por exemplo, uma consulta de e/S intensivas pode se beneficiar [técnicas de otimização de memória](sql-database-in-memory.md) para garantir uma melhor utilização da memória disponível numa determinada camada de serviço e tamanho de computação.
- Desagregar os detalhes de uma consulta para ver o texto e o respetivo histórico de utilização de recursos.
- Aceder às recomendações de ajuste de desempenho de mensagens em fila que mostram as ações tomadas pelo [Assistente de base de dados SQL](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unidades de transação de bases de dados elásticas (eDTUs)

Para bases de dados SQL que estão sempre disponíveis, em vez disso, que fornecem um conjunto dedicado de recursos (DTUs), que sempre não podem ser necessários, pode colocar esses bancos de dados para um [conjunto elástico](sql-database-elastic-pool.md). As bases de dados num conjunto elástico são num único servidor de base de dados do Azure SQL e partilham um conjunto de recursos.

Os recursos partilhados num conjunto elástico são avaliados por unidades de transação de bases de dados elásticas (eDTUs). Os conjuntos elásticos fornecem uma solução simples e rentável para gerir os objetivos de desempenho de várias bases de dados que tenham padrões de utilização extremamente variáveis e imprevisíveis. Um conjunto elástico garante que todos os recursos não podem ser consumidos por um banco de dados no agrupamento, garantindo que cada base de dados no conjunto tem sempre uma quantidade mínima de recursos necessários disponíveis.

Um conjunto é atribuído um número definido de eDTUs por um preço definido. O conjunto elástico, bases de dados individuais podem ser dimensionamento automaticamente dentro dos limites configurados. Uma base de dados sob uma carga mais pesada irá consumir mais eDTUs para responder à procura. Bases de dados sob cargas mais leves consumirá menos eDTUs. Bases de dados com qualquer carga não serão não consomem eDTUs. Uma vez que recursos estão aprovisionados para o conjunto completo, em vez de por base de dados, conjuntos elásticos simplificam as tarefas de gestão e fornecem um orçamento previsível para o conjunto.

Pode adicionar as eDTUs adicionais a um conjunto existente sem períodos de indisponibilidade da base de dados e sem qualquer impacto nas bases de dados no conjunto. Da mesma forma, se não precisa mais eDTUs adicionais, removê-los de um conjunto existente em qualquer altura. Também pode adicionar bases de dados ou subtrair bases de dados de um agrupamento em qualquer altura. Para reservar eDTUs para outras bases de dados, limite o número de eDTUs que uma base de dados pode utilizar com uma carga pesada. Se uma base de dados consistentemente underuses recursos, movê-lo para fora do conjunto e configurá-la como uma base de dados com uma quantidade previsível dos recursos necessários.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinar o número de DTUs necessárias para uma carga de trabalho

Se pretender migrar um existente no local ou carga de trabalho de máquina virtual de SQL Server para o SQL Database do Azure, utilize o [Calculadora de DTU](https://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessários. Para uma carga de trabalho de base de dados do Azure SQL existente, utilize [informações de desempenho de consulta](sql-database-query-performance.md) para compreender o consumo de recursos de base de dados (DTUs) e obter informações mais aprofundadas para otimizar a sua carga de trabalho. O [sys resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) vista de gestão dinâmica (DMV) permite-lhe ver o consumo de recursos para a última hora. O [resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) vista de catálogo exibe o consumo de recursos para os últimos 14 dias, mas a uma fidelidade inferior das médias de cinco minutos.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Cargas de trabalho que tiram partido de um conjunto elástico de recursos

Os conjuntos são adequados para bases de dados com uma média de utilização de recursos de baixa e picos de utilização relativamente raros. Base de dados SQL avalia a utilização de recursos histórica de bases de dados num servidor de base de dados SQL existente e recomenda a configuração de conjunto adequada no portal do Azure automaticamente. Para obter mais informações, consulte [quando deve pensar num conjunto elástico da base de dados SQL?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Perguntas mais frequentes (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>É necessário colocar a minha aplicação offline para converter de uma camada de serviços baseados em DTU para um escalão de serviço baseado em vCore?

Não. Não precisa de colocar o aplicativo offline. Os novos escalões de serviço oferecem um método de conversão online simples, que é semelhante ao processo existente de atualização de bases de dados do padrão para o escalão de serviço premium e o caminho inverso. Pode começar esta conversão utilizando o portal do Azure, PowerShell, a CLI do Azure, T-SQL ou a API REST. Ver [gerir bases de dados individuais](sql-database-single-database-scale.md) e [gerir conjuntos elásticos](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Posso converter a uma base de dados de um escalão de serviço no modelo de compra baseado em vCore para um escalão de serviço no modelo de compra baseado em DTU?

Sim, pode converter facilmente sua base de dados em qualquer objetivo de desempenho suportados ao utilizar o portal do Azure, PowerShell, a CLI do Azure, T-SQL ou a API REST. Ver [gerir bases de dados individuais](sql-database-single-database-scale.md) e [gerir conjuntos elásticos](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre o modelo de compra baseado em vCore, consulte [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- Para obter mais informações sobre o modelo de compra baseado em DTU, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
