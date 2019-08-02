---
title: Modelos de compra do banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os modelos de compra que estão disponíveis para o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 98d257c28ab5ff2cf902c0b8205ac8918ccf4d45
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567015"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Escolha entre os modelos vCore e compra de DTU

O banco de dados SQL do Azure permite que você compre facilmente um mecanismo de banco de dados PaaS (plataforma como serviço) totalmente gerenciado que atenda às suas necessidades de desempenho e custo. Dependendo do modelo de implantação escolhido para o banco de dados SQL do Azure, você pode selecionar o modelo de compra que funciona para você:

- [Modelo de compra baseado no núcleo virtual (vCore)](sql-database-service-tiers-vcore.md) (recomendado). Esse modelo de compra fornece uma opção entre uma camada de computação provisionada e uma camada de computação sem servidor (visualização). Com a camada de computação provisionada, você escolhe a quantidade exata de recursos de computação que são sempre provisionados para sua carga de trabalho. Com a camada de computação sem servidor, você especifica o dimensionamento automático dos recursos de computação em um intervalo de computação configurável. Com essa camada de computação, você também pode pausar automaticamente e retomar o banco de dados com base na atividade de carga de trabalho. O preço unitário vCore por unidade de tempo é inferior na camada de computação provisionada do que está na camada de computação sem servidor.
- [Modelo de compra baseado em DTU (unidade de transação de banco de dados)](sql-database-service-tiers-dtu.md). Esse modelo de compra fornece computação agrupada e pacotes de armazenamento balanceados para cargas de trabalho comuns.

Modelos de compra diferentes estão disponíveis para diferentes modelos de implantação de banco de dados SQL do Azure:

- As opções de implantação de [banco de dados individual](sql-database-single-databases-manage.md) e [pool elástico](sql-database-elastic-pool.md) no [banco de dados SQL do Azure](sql-database-technical-overview.md) oferecem o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o modelo de [compra baseado em vCore](sql-database-service-tiers-vcore.md).
- A opção de implantação de [instância gerenciada](sql-database-managed-instance.md) no banco de dados SQL do Azure oferece apenas o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- A [camada de serviço](sql-database-service-tier-hyperscale.md) de hiperescala está disponível para bancos de dados individuais que estão usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

A tabela e o gráfico a seguir comparam e contrastam os modelos de compra baseados em vCore e em DTU:

|**Modelo de compra**|**Descrição**|**Melhor para**|
|---|---|---|
|modelo baseado em DTU|Esse modelo é baseado em uma medida agrupada de computação, armazenamento e recursos de e/s. Os tamanhos de computação são expressos em DTUs para bancos de dados individuais e em eDTUs (unidades de transação de banco de dados elástico) para pools elásticos. Para obter mais informações sobre DTUs e eDTUs, consulte [o que são DTUs e eDTUs?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Melhor para clientes que desejam opções de recursos simples e pré-configuradas.|
|modelo baseado em vCore|Esse modelo permite que você escolha de forma independente os recursos de computação e armazenamento. O modelo de compra baseado em vCore também permite que você use [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para obter economias de custos.|Melhor para clientes que contenham flexibilidade, controle e transparência.|
||||  

![comparação de modelo de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Custos de computação

### <a name="provisioned-compute-costs"></a>Custos de computação provisionados

Na camada de computação provisionada, o custo de computação reflete a capacidade total de computação que é provisionada para o aplicativo.

Na camada de serviço comercialmente crítica, alocamos automaticamente pelo menos 3 réplicas. Para refletir essa alocação adicional dos recursos de computação, o preço no modelo de compra baseado em vCore é de aproximadamente 2.7 x superior na camada de serviço comercialmente crítica do que está na camada de serviço de uso geral. Da mesma forma, o preço de armazenamento mais alto por GB na camada de serviço comercialmente crítico reflete a alta e/s e baixa latência do armazenamento SSD.

O custo do armazenamento de backup é o mesmo para a camada de serviço comercialmente crítica e para a camada de serviço de uso geral, pois as duas camadas usam o armazenamento padrão.

### <a name="serverless-compute-costs"></a>Custos de computação sem servidor

Para obter uma descrição de como a capacidade de computação é definida e os custos são calculados para a camada de computação sem servidor, consulte [banco de dados SQL sem servidor (versão prévia)](sql-database-serverless.md).

## <a name="storage-costs"></a>Custos de armazenamento

Diferentes tipos de armazenamento são cobrados de forma diferente. Para armazenamento de dados, você é cobrado pelo armazenamento provisionado com base no tamanho máximo do banco de dados ou do pool selecionado. O custo não é alterado, a menos que você reduza ou aumente o máximo. O armazenamento de backup é associado a backups automatizados de sua instância e é alocado dinamicamente. Aumentar o período de retenção de backup aumenta o armazenamento de backup que é consumido pela sua instância.

Por padrão, 7 dias de backups automatizados de seus bancos de dados são copiados para uma conta de armazenamento de BLOBs padrão do armazenamento com redundância geográfica de acesso de leitura (RA-GRS). Esse armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações, que são copiados a cada 5 minutos. O tamanho dos logs de transação depende da taxa de alteração do banco de dados. Um valor mínimo de armazenamento igual a 100 por cento do tamanho do banco de dados é fornecido sem custo adicional. O consumo adicional de armazenamento de backup é cobrado em GB por mês.

Para obter mais informações sobre preços de armazenamento, consulte a página de [preços](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

Um vCore (núcleo virtual) representa uma CPU lógica e oferece a opção de escolher entre gerações de hardware e as características físicas do hardware (por exemplo, o número de núcleos, a memória e o tamanho do armazenamento). O modelo de compra baseado em vCore oferece flexibilidade, controle, transparência de consumo de recursos individuais e uma maneira simples de traduzir os requisitos de carga de trabalho local para a nuvem. Esse modelo permite que você escolha recursos de computação, memória e armazenamento com base nas suas necessidades de carga de trabalho.

No modelo de compra baseado em vCore, você pode escolher entre as camadas de serviço de [finalidade geral](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e de [negócio crítico](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) para [bancos de dados individuais](sql-database-single-database-scale.md), [pools elásticos](sql-database-elastic-pool.md)e [instâncias gerenciadas](sql-database-managed-instance.md). Para bancos de dados individuais, você também pode escolher a [camada de serviço](sql-database-service-tier-hyperscale.md)de hiperescala.

O modelo de compra baseado em vCore permite que você escolha independentemente os recursos de computação e armazenamento, correspondam ao desempenho local e otimize o preço. No modelo de compra baseado em vCore, você paga por:

- Recursos de computação (a camada de serviço + o número de vCores e a quantidade de memória + a geração de hardware).
- O tipo e a quantidade de dados e armazenamento de log.
- Armazenamento de backup (RA-GRS).

> [!IMPORTANT]
> Os recursos de computação, a e/s e O armazenamento de dados e de log são cobrados por Database ou pool elástico. O armazenamento de backup é cobrado por cada banco de dados. Para obter mais informações sobre encargos de instância gerenciada, consulte [instâncias gerenciadas](sql-database-managed-instance.md).
> **Limitações de região:** Para obter a lista atual de regiões com suporte, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar uma instância gerenciada em uma região que não tem suporte atualmente, [envie uma solicitação de suporte por meio do portal do Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Se seu banco de dados individual ou pool elástico consumir mais de 300 DTUs, a conversão para o modelo de compra baseado em vCore pode reduzir os custos. Você pode converter usando a API de sua escolha ou usando o portal do Azure, sem tempo de inatividade. No entanto, a conversão não é necessária e não é feita automaticamente. Se o modelo de compra baseado em DTU atender aos seus requisitos de desempenho e de negócios, você deverá continuar a usá-lo.

Para converter do modelo de compra baseado em DTU para o modelo de compra baseado em vCore, selecione o tamanho da computação usando as seguintes regras de Thumb:

- Cada DTUs de 100 na camada Standard exige pelo menos 1 vCore na camada de serviço de uso geral.
- Cada DTUs de 125 na camada Premium exige pelo menos 1 vCore na camada de serviço comercialmente crítica.

## <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

Uma DTU (unidade de transação de banco de dados) representa uma medida combinada de CPU, memória, leituras e gravações. O modelo de compra baseado em DTU oferece um conjunto de pacotes pré-configurados de recursos de computação e armazenamento incluído para gerar diferentes níveis de desempenho do aplicativo. Se você preferir a simplicidade de um pacote pré-configurado e de pagamentos fixos a cada mês, o modelo baseado em DTU poderá ser mais adequado para suas necessidades.

No modelo de compra baseado em DTU, você pode escolher entre as camadas de serviço Basic, Standard e Premium para bancos de [dados individuais](sql-database-single-database-scale.md) e pools [elásticos](sql-database-elastic-pool.md). O modelo de compra baseado em DTU não está disponível para [instâncias gerenciadas](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unidades de transação de banco de dados (DTUs)

Para um banco de dados individual em um tamanho de computação específico dentro de uma [camada de serviço](sql-database-single-database-scale.md), a Microsoft garante um determinado nível de recursos para esse banco de dados (independente de qualquer outro banco de dados na nuvem do Azure). Essa garantia fornece um nível previsível de desempenho. A quantidade de recursos alocados para um banco de dados é calculada como um número de DTUs e é uma medida agrupada de recursos de computação, armazenamento e e/s.

A proporção entre esses recursos é determinada originalmente por uma carga de trabalho de parâmetro de [comparação de OLTP (processamento de transações online)](sql-database-benchmark-overview.md) projetada para ser típica de cargas de trabalho OLTP do mundo real. Quando sua carga de trabalho excede a quantidade de qualquer um desses recursos, sua taxa de transferência é limitada, resultando em desempenho e tempos limite mais lentos.

Os recursos usados pela sua carga de trabalho não afetam os recursos disponíveis para outros bancos de dados SQL na nuvem do Azure. Da mesma forma, os recursos usados por outras cargas de trabalho não afetam os recursos disponíveis para o banco de dados SQL.

![caixa delimitadora](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTUs são mais úteis para entender os recursos relativos alocados para bancos de dados SQL do Azure em diferentes tamanhos de computação e camadas de serviço. Por exemplo:

- Dobrar as DTUs aumentando o tamanho de computação de um banco de dados equivale a dobrar o conjunto de recursos disponíveis para esse banco de dados.
- Um banco de dados P11 da camada de serviço Premium com 1750 DTUs fornece uma capacidade de computação mais DTU de 350x do que um banco de dados de camada de serviço básico com 5 DTUs.  

Para obter uma visão mais profunda do consumo de recursos (DTU) de sua carga de trabalho, use [informações de desempenho de consulta](sql-database-query-performance.md) para:

- Identifique as principais consultas por CPU/duração/contagem de execução que podem ser ajustadas para melhorar o desempenho. Por exemplo, uma consulta com uso intensivo de e/s pode se beneficiar de [técnicas de otimização na memória](sql-database-in-memory.md) para fazer melhor uso da memória disponível em uma determinada camada de serviço e tamanho de computação.
- Aprofunde-se nos detalhes de uma consulta para exibir seu texto e seu histórico de uso de recursos.
- Acesse as recomendações de ajuste de desempenho que mostram as ações tomadas pelo [Assistente do banco de dados SQL](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>EDTUs (unidades de transação de banco de dados elástico)

Para bancos de dados SQL que estão sempre disponíveis, em vez de fornecer um conjunto dedicado de recursos (DTUs) que talvez nem sempre sejam necessários, você pode inserir esses bancos de dados em um [pool elástico](sql-database-elastic-pool.md). Os bancos de dados em um pool elástico estão em um único servidor de banco de dados SQL do Azure e compartilham um pool de recursos.

Os recursos compartilhados em um pool elástico são medidos por eDTUs (unidades de transação de banco de dados elástico). Os pools elásticos fornecem uma solução simples e econômica para gerenciar as metas de desempenho de vários bancos de dados que têm padrões de uso amplamente variados e imprevisíveis. Um pool elástico garante que todos os recursos não possam ser consumidos por um banco de dados no pool, garantindo que cada banco de dados no pool sempre tenha uma quantidade mínima de recursos necessários disponíveis.

Um pool recebe um número definido de eDTUs para um preço definido. No pool elástico, os bancos de dados individuais podem ser dimensionados automaticamente dentro dos limites configurados. Um banco de dados com carga mais pesada consumirá mais eDTUs para atender à demanda. Os bancos de dados sob cargas mais claras consumirão menos eDTUs. Bancos de dados sem carga não consumirão nenhum eDTUs. Como os recursos são provisionados para todo o pool, em vez de por banco de dados, os pools elásticos simplificam suas tarefas de gerenciamento e fornecem um orçamento previsível para o pool.

Você pode adicionar eDTUs adicionais a um pool existente sem tempo de inatividade do banco de dados e sem impacto sobre os bancos de dados no pool. Da mesma forma, se você não precisar mais de eDTUs extras, remova-os de um pool existente a qualquer momento. Você também pode adicionar bancos de dados ou subtrair bancos de dados de um pool a qualquer momento. Para reservar o eDTUs para outros bancos de dados, limite o número de eDTUs que um banco de dados pode usar em uma carga pesada. Se um banco de dados utiliza consistentemente os recursos, mova-o para fora do pool e configure-o como um banco de dados individual com uma quantidade previsível de recursos necessários.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinar o número de DTUs necessárias para uma carga de trabalho

Se você quiser migrar uma carga de trabalho de máquina virtual local ou SQL Server existente para o banco de dados SQL do Azure, use a [calculadora de DTU](https://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessárias. Para uma carga de trabalho existente do banco de dados SQL do Azure, use [informações de desempenho de consulta](sql-database-query-performance.md) para entender o consumo de recursos de banco de dados (DTUs) e obter informações mais aprofundadas para otimizar sua carga de trabalho. A DMV (exibição de gerenciamento dinâmico) [Sys. dm _db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) permite exibir o consumo de recursos na última hora. A exibição do catálogo [Sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) exibe o consumo de recursos nos últimos 14 dias, mas a uma fidelidade inferior de médias de cinco minutos.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Cargas de trabalho que se beneficiam de um pool elástico de recursos

Os pools são adequados para bancos de dados com uma média de baixa utilização de recursos e picos de utilização relativamente pouco frequentes. O banco de dados SQL avalia automaticamente o uso histórico de recursos dos bancos de dados em um servidor de banco de dados SQL existente e recomenda a configuração de pool apropriada no portal do Azure. Para obter mais informações, consulte [quando você deve considerar um pool elástico do banco de dados SQL?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Preciso colocar meu aplicativo offline para converter de uma camada de serviço baseada em DTU em uma camada de serviço baseada em vCore?

Não. Você não precisa colocar o aplicativo offline. As novas camadas de serviço oferecem um método de conversão online simples que é semelhante ao processo existente de atualização de bancos de dados do padrão para a camada de serviço Premium e o contrário. Você pode iniciar essa conversão usando o portal do Azure, o PowerShell, o CLI do Azure, o T-SQL ou a API REST. Consulte [gerenciar bancos de dados individuais](sql-database-single-database-scale.md) e [Gerenciar Pools elásticos](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Posso converter um banco de dados de uma camada de serviço no modelo de compra baseado em vCore para uma camada de serviço no modelo de compra baseado em DTU?

Sim, você pode facilmente converter seu banco de dados para qualquer objetivo de desempenho com suporte usando o portal do Azure, o PowerShell, o CLI do Azure, o T-SQL ou a API REST. Consulte [gerenciar bancos de dados individuais](sql-database-single-database-scale.md) e [Gerenciar Pools elásticos](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre o modelo de compra baseado em vCore, consulte [modelo de compra baseado em VCORE](sql-database-service-tiers-vcore.md).
- Para obter mais informações sobre o modelo de compra baseado em DTU, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
