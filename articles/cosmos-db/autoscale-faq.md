---
title: Perguntas frequentes sobre a escala automática aprovisionada em Produção em Azure Cosmos DB
description: Perguntas frequentes sobre a escala automática disponibilizada para bases de dados e contentores da Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/10/2020
ms.openlocfilehash: b398f739189232f39a2fee06fc6e6ff0d53348f0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656622"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Perguntas frequentes sobre produção de escala automática no Azure Cosmos DB

Com a entrada aprovisionada em escala automática, o Azure Cosmos DB irá gerir e escalar automaticamente os RU/s da sua base de dados ou contentor com base na utilização. Este artigo responde a perguntas comumente feitas sobre escala automática.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Qual é a diferença entre "piloto automático" e "autoescala" em Azure Cosmos DB?
"Autoscale" ou "autoscale provisioned throughput" é o nome atualizado para a funcionalidade, anteriormente conhecida como "piloto automático". Com o lançamento atual da escala automática, adicionámos novas funcionalidades, incluindo a capacidade de definir max RU/s personalizado e suporte programático. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>O que acontece às bases de dados ou contentores criados no modelo anterior de nível de piloto automático?
Os recursos criados com o modelo de nível anterior são automaticamente suportados com o novo modelo ru/s máximo personalizado de escala automática. O limite superior do nível torna-se o novo MÁXIMO RU/s, o que resulta na mesma escala. 

Por exemplo, se selecionou previamente o nível que escalava entre 400 e 4000 RU/s, a base de dados ou o contentor mostrar-se-á agora como tendo um MÁXIMO RU/s de 4000 RU/s, que escala entre 400 a 4000 RU/s. A partir daqui, pode alterar o máximo de RU/s para um valor personalizado para atender à sua carga de trabalho. 

### <a name="how-quickly-will-autoscale-scale-up-and-down-based-on-spikes-in-traffic"></a>A rapidez com que escala automática vai escalar para cima e para baixo com base em picos de tráfego?
Com escala automática, o sistema escala a entrada (RU/s) `T` para cima ou para baixo dentro ou para baixo dentro do e `0.1 * Tmax` `Tmax` intervalo, com base no tráfego de entrada. Como a escala é automática e instantânea, em qualquer momento, pode consumir até ao provisionado `Tmax` sem demora. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Como determino para que o sistema está atualmente escalado?
Utilize [as métricas do Monitor Azure](how-to-choose-offer.md#measure-and-monitor-your-usage) para monitorizar tanto o max RU/s de escala automática aprovisionado como a entrada de corrente (RU/s) para a a que o sistema é dimensionado. 

### <a name="what-is-the-pricing-for-autoscale"></a>Qual é o preço para a escala automática?
A cada hora, será cobrado para a maior entrada `T` do sistema escalado dentro de uma hora. Se o seu recurso não tiver pedidos durante a hora ou não tiver escala para `0.1 * Tmax` além, será cobrado pelo mínimo de `0.1 * Tmax` . Consulte a página de [preços](https://azure.microsoft.com/pricing/details/cosmos-db/) do Azure Cosmos DB para mais detalhes. 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Como é que a escala automática aparece na minha conta?
Nas contas single-master, a taxa de escala automática por 100 RU/s é 1,5x a taxa de produção normal (manual) provisionada. Na sua conta, verá o medidor de entrada padrão existente. A quantidade deste medidor será multiplicada por 1,5. Por exemplo, se o sistema ru/s mais elevado escalado dentro de uma hora fosse 6000 RU/s, seria cobrado 60 * 1,5 = 90 unidades do medidor para essa hora.

Nas contas multi-master, a taxa de escala automática por 100 RU/s é a mesma que a taxa de produção multi-master (manual) normalizada. Na sua conta, verá o contador multi-mestre existente. Uma vez que as tarifas são as mesmas, se utilizar a escala automática, verá a mesma quantidade que com a produção padrão.

### <a name="does-autoscale-work-with-reserved-capacity"></a>A escala automática funciona com capacidade reservada?
Yes. Quando adquire uma capacidade reservada de um único mestre, o desconto de reserva para recursos de escala automática é aplicado ao uso do seu contador a uma razão de 1,5 * a [relação da região específica](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region). 

A capacidade reservada multi-master funciona da mesma forma para a produção de autoescala e standard (manual). Ver [capacidade reservada azure Cosmos DB](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>A escala automática funciona com nível livre?
Yes. Em camada livre, pode utilizar a entrada de escala automática num recipiente. O suporte para bases de dados de entrada partilhadas em escala automática com max RU/s personalizado ainda não está disponível. Veja como funciona a [faturação gratuita com escala automática](understand-your-bill.md#billing-examples-with-free-tier-accounts).

### <a name="is-autoscale-supported-for-all-apis"></a>A escala automática é suportada para todas as APIs?
Sim, a escala automática é suportada para todas as APIs: Core (SQL), Gremlin, Table, Cassandra e API para MongoDB.

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>A escala automática é suportada para contas multi-master?
Yes. Os max RU/s estão disponíveis em cada região que é adicionado à conta Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Como posso permitir a escala automática em novas bases de dados ou contentores?
Consulte este artigo sobre [como ativar a escala automática](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Posso ativar automaticamente uma base de dados ou um contentor existente?
Yes. Também pode alternar entre a escala automática e a produção normal (manual) prevista, conforme necessário. Atualmente, para todas as APIs, só pode utilizar o [portal Azure](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) para efazer estas operações.

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Como funciona a migração entre a escala automática e a produção normal (manual) prevista?
Conceptualmente, mudar o tipo de entrada é um processo em duas fases. Em primeiro lugar, envia um pedido para alterar as definições de entrada para utilizar a escala automática ou a entrada manual aprovisionada. Em ambos os casos, o sistema determinará e definirá automaticamente um valor INICIAL de RU/s, com base nas definições de entrada e armazenamento atuais. Durante este passo, não será aceite qualquer valor RU/s fornecido pelo utilizador. Em seguida, após a atualização estar completa, pode [alterar o RU/s](#can-i-change-the-max-rus-on-the-database-or-container) para se adequar à sua carga de trabalho. 

**Migração da produção standard (manual) prevista para a escala automática**

Para um recipiente, utilize a seguinte fórmula para estimar a escala automática inicial max RU/s: ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)`` , arredondada para os 1000 RU/s mais próximos. O max RU/s de escala automática inicial real pode variar dependendo da configuração da sua conta.

Exemplo #1: Suponha que tenha um recipiente com 10.000 ru/s manual de entrada aprovisionada e 25 GB de armazenamento. Quando ativar a escala automática, o max ru/s de escala automática inicial será: 10.000 RU/s, que escalará entre 1000 e 10.000 RU/s. 

Exemplo #2: Suponha que tenha um recipiente com 50.000 ru/s manual de entrada aprovisionado e 2500 GB de armazenamento. Quando ativar a escala automática, o max ru/s de escala automática inicial será: 250.000 RU/s, que escalará entre 25.000 e 250.000 RU/s. 

**Migração da escala automática para a produção normal (manual) aprovisionada**

A entrada manual inicial prevista será igual à atual escala automática max RU/s. 

Exemplo: Suponha que tenha uma base de dados ou recipiente de escala automática com max RU/s de 20.000 RU/s (escalas entre 2000 - 20.000 RU/s). Quando atualizar para utilizar a entrada manual, a entrada inicial será de 20.000 RU/s. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Existe suporte Azure CLI ou PowerShell para gerir bases de dados ou contentores com escala automática?
Atualmente, só pode criar e gerir recursos com escala automática a partir do portal Azure, .NET V3 SDK, Java V4 SDK e Azure Resource Manager. O suporte em Azure CLI, PowerShell e outros SDKs ainda não está disponível.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>A escala automática é suportada para bases de dados de produção partilhadas?
Sim, a escala automática é suportada para bases de dados de produção partilhadas. Para ativar esta funcionalidade, selecione a escala automática e a opção de **entrada de provisionamento** ao criar a base de dados. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Qual é o número de contentores permitidos por base de dados de entrada partilhada quando a escala automática está ativada?
A Azure Cosmos DB aplica um máximo de 25 contentores numa base de dados de produção partilhada, que se aplica a bases de dados com entrada automática ou standard (manual). 

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual é o limite de armazenamento associado a cada opção max RU/s?  
O limite de armazenamento em GB para cada ru/s max é: Max RU/s de base de dados ou contentor / 100. Por exemplo, se o max RU/s for de 20.000 RU/s, o recurso pode suportar 200 GB de armazenamento. Consulte o artigo [limites](provision-throughput-autoscale.md#autoscale-limits) de escala automática para as opções de max RU/s e armazenamento disponíveis. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>O que acontece se eu exceder o limite de armazenamento associado à minha entrada máxima?
Se o limite de armazenamento associado à entrada máxima da base de dados ou do contentor for ultrapassado, o Azure Cosmos DB aumentará automaticamente a potência máxima para o próximo RU/s mais elevado que possa suportar esse nível de armazenamento.

Por exemplo, se começar com um RU/s max de 50.000 RU/s (escalas entre 5000 - 50.000 RU/s), pode armazenar até 500 GB de dados. Se ultrapassar os 500 GB - por exemplo, o armazenamento é agora de 600 GB, o novo máximo ru/s será de 60.000 RU/s (escalas entre 6000 - 60.000 RU/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Posso alterar o max RU/s na base de dados ou no contentor? 
Yes. Consulte este [artigo](how-to-provision-autoscale-throughput.md) sobre como alterar o max RU/s. Quando alterar o max RU/s, dependendo do valor solicitado, esta pode ser uma operação assíncrona que pode demorar algum tempo a ser concluída (pode ser até 4-6 horas, dependendo do RU/s selecionado)

#### <a name="increasing-the-max-rus"></a>Aumentando o max RU/s
Quando envia um pedido para aumentar o max RU/s, `Tmax` dependendo do max RU/s selecionado, o serviço disponibiliza mais recursos para suportar o máximo ru/s. Enquanto isto acontece, a sua carga de trabalho e operações existentes não serão afetadas. O sistema continuará a escalar a sua base de dados ou contentor entre o anterior e até que `0.1*Tmax` a nova escala de escala esteja `Tmax` `0.1*Tmax_new` `Tmax_new` pronta.

#### <a name="lowering-the-max-rus"></a>Baixando o max RU/s
Quando baixa o max RU/s, o valor mínimo a que pode defini-lo é: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` , arredondado para os 1000 RU/s mais próximos. 

Exemplo #1: Suponha que tenha um recipiente de escala automática com max RU/s de 20.000 RU/s (escalas entre 2000 - 20.000 RU/s) e 50 GB de armazenamento. O valor mínimo mais baixo que se pode definir max RU/s é: MAX (4000, 20.000 / 10, **50 * 100**) = 5000 RU/s (escalas entre 500 - 5000 RU/s).

Exemplo #2: Suponha que tenha um recipiente de escala automática com max RU/s de 100.000 RU/s e 100 GB de armazenamento. Agora, escala-se max RU/s até 150.000 RU/s (escalas entre 15.000 - 150.000 RU/s). O valor mínimo mais baixo que pode agora definir max RU/s é: MAX (4000, **150.000 / 10,** 100 * 100) = 15.000 RU/s (escalas entre 1500 - 15.000 RU/s). 

Para uma base de dados de entradas partilhadas, quando baixa o max RU/s, o valor mínimo a que pode defini-lo é: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` , arredondado para os 1000 RU/s mais próximos.  

As fórmulas e exemplos acima referidos dizem respeito ao max RU/s de escala automática mínima que pode definir, e é distinto do alcance do `0.1 * Tmax` `Tmax` sistema automaticamente escala entre. Não importa o que seja o MAX RU/s, o sistema irá sempre escalar entre `0.1 * Tmax` `Tmax` . 

### <a name="how-does-ttl-work-with-autoscale"></a>Como funciona a TTL com escala automática?
Com a escala automática, as operações TTL não afetam a escala ção de RU/s. Quaisquer RUs consumidos devido à TTL não fazem parte do RU/s faturado do recipiente de escala automática. 

Por exemplo, suponha que tenha um recipiente de escala automática com 400 - 4000 RU/s:
- Hora 1: T=0: O recipiente não tem utilização (sem ttl ou pedidos de carga de trabalho). O RU/s faturado é de 400 RU/s.
- Hora 1: T=1: O TTL está ativado.
- Hora 1: T=2: O recipiente começa a receber pedidos, que consomem 1000 RU em 1 segundo. Há também 200 RUs de TTL que precisam de acontecer. O RU/s faturado ainda é 1000 RU/s. Independentemente de quando os TTLs ocorrem, não afetarão a lógica de escala automática.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual é o mapeamento entre o max RU/s e as divisórias físicas?
Quando selecionar pela primeira vez o max RU/s, o Azure Cosmos DB fornecerá: Max RU/s / 10.000 RU/s = # de divisórias físicas. Cada [partição física](partition-data.md#physical-partitions) pode suportar até 10.000 RU/s e 50 GB de armazenamento. À medida que o tamanho do armazenamento cresce, o Azure Cosmos DB dividirá automaticamente as divisórias para adicionar mais divisórias físicas para lidar com o aumento do armazenamento, ou aumentará o max RU/s se o armazenamento [exceder o limite associado](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

O max RU/s da base de dados ou do recipiente é dividido uniformemente em todas as divisórias físicas. Assim, a entrada total que qualquer partição física pode escalar é: Max RU/s de base de dados ou contentor / # divisórias físicas. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>O que acontece se os pedidos de entrada excederem o max RU/s da base de dados ou do contentor?
Se o RU/s globalmente consumido exceder o max RU/s da base de dados ou do contentor, os pedidos que excedam o max RU/s serão estrangulados e devolverão um código de estado 429. Os pedidos que resultem em utilização mais de 100% normalizada também serão acelerados. A utilização normalizada é definida como o máximo da utilização ru/s em todas as divisórias físicas. Por exemplo, suponha que a sua potência máxima seja de 20.000 RU/s e tenha duas divisórias físicas, P_1 e P_2, cada uma capaz de escalar para 10.000 RU/s. Num segundo, se P_1 utilizou 6000 RUs, e P_2 8000 RUs, a utilização normalizada é MAX (6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0,8.

> [!NOTE]
> O cliente DaMS e ferramentas de importação de dados da Azure Cosmos DB (Azure Data Factory, biblioteca de executora a granel) retry automaticamente em 429s, por isso ocasionalmente 429s estão bem. Um número elevado sustentado de 429s pode indicar que precisa de aumentar o max RU/s ou rever a sua estratégia de partição para uma [partição quente](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Ainda é possível ver 429s (limitação de estrangulamento/taxa) quando a escala automática está ativada? 
Yes. É possível ver 429s em dois cenários. Em primeiro lugar, quando o RU/s consumido globalmente exceder o max RU/s da base de dados ou do contentor, o serviço irá acelerar os pedidos em conformidade. 

Em segundo lugar, se houver uma divisão quente, isto é, um valor-chave de partição lógica que tenha um montante desproporcionalmente maior de pedidos em comparação com outros valores-chave de partição, é possível que a partição física subjacente exceda o seu orçamento RU/s. Como uma boa prática, para evitar divisórias quentes, escolha uma boa chave de [partição](partitioning-overview.md#choose-partitionkey) que resulte numa distribuição uniforme tanto do armazenamento como da entrada. 

Por exemplo, se selecionar a opção de entrada máxima de 20.000 RU/s e tiver 200 GB de armazenamento, com quatro divisórias físicas, cada partição física pode ser autodimensionada até 5000 RU/s. Se houver uma partição quente numa determinada chave de partição lógica, verá 429s quando a partição física subjacente em que reside excede 5000 RU/s, ou seja, excede 100% de utilização normalizada.


## <a name="next-steps"></a>Passos seguintes

* Saiba como permitir a [escala automática numa base de dados ou contentor da Azure Cosmos DB](how-to-provision-autoscale-throughput.md).
* Conheça os [benefícios da produção aprovisionada com escala automática](provision-throughput-autoscale.md#benefits-of-autoscale).
* Saiba mais sobre [divisórias lógicas e físicas.](partition-data.md)
                        