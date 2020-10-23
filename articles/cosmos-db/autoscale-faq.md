---
title: Perguntas frequentes sobre a produção de autoescala abastada na Azure Cosmos DB
description: Perguntas frequentes sobre a produção de autoescala prevista para bases de dados e contentores DB da Azure Cosmos
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/10/2020
ms.openlocfilehash: 5905471dad5cf4e2e8191894af52c503c23e9036
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277971"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Perguntas frequentes sobre a produção de autoescala abastada na Azure Cosmos DB

Com a produção de autoescala, a Azure Cosmos DB irá gerir e escalar automaticamente o RU/s da sua base de dados ou contentor com base na utilização. Este artigo responde a perguntas comumente sobre autoescala.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Qual é a diferença entre "piloto automático" e "autoescala" em Azure Cosmos DB?
"Autoscale" ou "autoscale provisioned throughput" é o nome atualizado para a funcionalidade, anteriormente conhecida como "piloto automático". Com o lançamento atual da autoescala, adicionámos novas funcionalidades, incluindo a capacidade de definir max RU/s personalizado e suporte programático. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>O que acontece com bases de dados ou contentores criados no modelo anterior de nível de piloto automático?
Os recursos criados com o modelo de nível anterior são automaticamente suportados com o novo modelo máximo ru/s personalizado de autoescalação. O limite superior do nível torna-se o novo máximo RU/s, o que resulta na mesma gama de escala. 

Por exemplo, se selecionou previamente o nível que escalou entre 400 a 4000 RU/s, a base de dados ou o contentor mostrará agora como tendo um MÁXIMO RU/s de 4000 RU/s, que escala entre 400 a 4000 RU/s. A partir daqui, pode alterar o máximo RU/s para um valor personalizado para se adequar à sua carga de trabalho. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>Quão rapidamente irá escalar automaticamente com base em picos de tráfego?
Com a autoescala, o sistema escala a potência (RU/s) `T` para cima ou para baixo dentro do `0.1 * Tmax` `Tmax` alcance, com base no tráfego de entrada. Como o escalonamento é automático e instantâneo, em qualquer momento, pode consumir até ao provisionado `Tmax` sem demora. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Como determino a que RU/s o sistema é atualmente dimensionado?
Utilize [métricas do Azure Monitor](how-to-choose-offer.md#measure-and-monitor-your-usage) para monitorizar tanto o max RU/s de autoescala a provisionado como a potência de corrente (RU/s) para a qual o sistema é dimensionado. 

### <a name="what-is-the-pricing-for-autoscale"></a>Qual é o preço da autoescala?
A cada hora, será cobrado para a maior produção `T` do sistema escalado dentro de uma hora. Se o seu recurso não tiver pedidos durante a hora ou não tiver escala `0.1 * Tmax` além, será cobrado pelo mínimo de `0.1 * Tmax` . Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) da Azure Cosmos DB para obter mais detalhes. 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Como é que a autoescala aparece na minha conta?
Nas contas da região de escrita única, a taxa de autoescala por 100 RU/s é de 1,5x a taxa de produção normalizada (manual). Na sua conta, verá o medidor de produção padrão existente. A quantidade deste contador será multiplicada por 1,5. Por exemplo, se o maior RU/s do sistema escalado em uma hora fosse 6000 RU/s, você seria cobrado 60 * 1,5 = 90 unidades do medidor durante essa hora.

Em contas com várias regiões de escrita, a taxa de autoescala por 100 RU/s é a mesma que a taxa para a produção de várias regiões de escrita padrão (manual). Na sua conta, verá o medidor de múltiplas regiões de escrita existente. Uma vez que as tarifas são as mesmas, se utilizar a autoescala, verá a mesma quantidade que com a produção padrão.

### <a name="does-autoscale-work-with-reserved-capacity"></a>Funciona em autoescalação com capacidade reservada?
Sim. Ao adquirir capacidade reservada para contas com múltiplas regiões de escrita, o desconto de reserva para recursos de autoescala é aplicado ao uso do seu contador com um rácio de 1,5 * o [rácio da região específica.](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region) 

A capacidade reservada da região multi-escrita funciona da mesma forma para a produção de escala automática e padrão (manual) a nível. Ver capacidade reservada da [Azure Cosmos DB](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>A autoescala funciona com o free tier?
Sim. No nível livre, pode utilizar a produção de escala automática num recipiente. O suporte para bases de dados de produção partilhadas por escala automática com max RU/s personalizado ainda não está disponível. Veja como [funciona a faturação de nível livre com autoescala.](understand-your-bill.md#billing-examples-with-free-tier-accounts)

### <a name="is-autoscale-supported-for-all-apis"></a>A autoescala suportada para todas as APIs?
Sim, a autoescala é suportada para todas as APIs: Core (SQL), Gremlin, Table, Cassandra e API para o MongoDB.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>O autoscale é suportado para contas de escrita multi-região?
Sim. Os RU/s max estão disponíveis em cada região que é adicionada à conta DB Azure Cosmos. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Como posso permitir a autoescala em novas bases de dados ou contentores?
Consulte este artigo sobre [como permitir a autoescala.](how-to-provision-autoscale-throughput.md)

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Posso permitir a autoescala numa base de dados existente ou num contentor?
Sim. Pode também alternar entre a potência de autoescala e a produção normalizada (manual) conforme necessário. Atualmente, para todas as APIs, só é possível utilizar o [portal Azure](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) para fazer estas operações.

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Como funciona a migração entre o trabalho de produção auto-escala e padrão (manual) aussitado?
Conceptualmente, mudar o tipo de produção é um processo em duas fases. Em primeiro lugar, envia um pedido para alterar as definições de produção para utilizar a produção automática ou manual. Em ambos os casos, o sistema determinará e definirá automaticamente um valor RU/s inicial, com base nas definições de produção e armazenamento atuais. Durante este passo, não será aceite qualquer valor RU/s fornecido pelo utilizador. Em seguida, após a atualização estar concluída, pode [alterar o RU/s](#can-i-change-the-max-rus-on-the-database-or-container) de acordo com a sua carga de trabalho. 

**Migração da produção padrão (manual) prevista para autoescala**

Para um recipiente, utilize a seguinte fórmula para estimar o autoscale max RU/s inicial: ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)`` , arredondado para os 1000 RU/s mais próximos. O max RU/s de autoescala inicial pode variar dependendo da configuração da sua conta.

Exemplo #1: Suponha que tem um recipiente com 10.000 RU/s manual de produção e 25 GB de armazenamento. Quando ativar a autoescala, o max RU/s de autoescala inicial será: 10.000 RU/s, que irá escalar entre 1000 e 10.000 RU/s. 

Exemplo #2: Suponha que tem um recipiente com 50.000 RU/s manual de produção e 2500 GB de armazenamento. Quando ativar a autoescala, o max RU/s de autoescala inicial será: 250.000 RU/s, que irá escalar entre 25.000 e 250.000 RU/s. 

**Migração de autoescala para produção normalizada (manual) aussada**

A produção manual inicial será igual à atual autoescalada max RU/s. 

Exemplo: Suponha que tenha uma base de dados ou um recipiente de autoescala com RU/s máximo de 20.000 RU/s (balanças entre 2000 e 20.000 RU/s). Quando atualizar para utilizar o rendimento manual a provisionado, a produção inicial será de 20.000 RU/s. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Existe suporte Azure CLI ou PowerShell para gerir bases de dados ou contentores com autoescala?
Atualmente, só é possível criar e gerir recursos com autoescala a partir do portal Azure, .NET V3 SDK, Java V4 SDK e Azure Resource Manager. O suporte em Azure CLI, PowerShell e outros SDKs ainda não está disponível.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>A autoescala suportada para bases de dados de produção partilhadas?
Sim, a autoescala é suportada para bases de dados de produção partilhadas. Para ativar esta funcionalidade, selecione autoscale e a opção **de produção de Provisão** ao criar a base de dados. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Qual é o número de contentores permitidos por base de dados de produção partilhada quando a autoescalada está ativada?
A Azure Cosmos DB aplica um máximo de 25 contentores numa base de dados de produção partilhada, que se aplica a bases de dados com produção automática ou padrão (manual). 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>Qual é o impacto da autoescala no nível de consistência da base de dados?
Não há qualquer impacto da autoescala no nível de consistência da base de dados.
Consulte o artigo [níveis de consistência](consistency-levels.md) para obter mais informações sobre os níveis de consistência disponíveis.

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual é o limite de armazenamento associado a cada opção MAX RU/s?  
O limite de armazenamento em GB para cada RU/s máximo é: Max RU/s de base de dados ou contentor /100. Por exemplo, se o max RU/s for de 20.000 RU/s, o recurso pode suportar 200 GB de armazenamento. Consulte o artigo [limites de autoescalação](provision-throughput-autoscale.md#autoscale-limits) para as opções de max RU/s disponíveis e para as opções de armazenamento. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>O que acontece se eu exceder o limite de armazenamento associado à minha produção máxima?
Se o limite de armazenamento associado à potência máxima da base de dados ou do contentor for ultrapassado, a Azure Cosmos DB aumentará automaticamente a potência máxima para o próximo RU/s mais elevado que possa suportar esse nível de armazenamento.

Por exemplo, se começar com um RU/s máximo de 50.000 RU/s (balanças entre 5000 - 50.000 RU/s), pode armazenar até 500 GB de dados. Se exceder os 500 GB, por exemplo, se o armazenamento for agora de 600 GB, a nova RU/s máxima será de 60 000 RU/s (que dimensiona entre 6000 e 60 000 RU/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Posso alterar o max RU/s na base de dados ou no contentor? 
Sim. Consulte este [artigo](how-to-provision-autoscale-throughput.md) sobre como alterar o max RU/s. Quando se altera o max RU/s, dependendo do valor solicitado, esta pode ser uma operação assíncronea que pode demorar algum tempo a ser concluída (pode ser até 4-6 horas, dependendo do RU/s selecionado)

#### <a name="increasing-the-max-rus"></a>Aumentando o máximo RU/s
Quando envia um pedido para aumentar o max RU/s, `Tmax` dependendo do máximo RU/s selecionado, o serviço fornece mais recursos para suportar o máximo máximo RU/s. Enquanto isto estiver a acontecer, a sua carga de trabalho e operações existentes não serão afetadas. O sistema continuará a escalar a sua base de dados ou contentor entre o anterior `0.1*Tmax` até que a nova gama de `Tmax` escalas de para estar `0.1*Tmax_new` `Tmax_new` pronta.

#### <a name="lowering-the-max-rus"></a>Baixando o máximo RU/s
Quando baixar o máximo RU/s, o valor mínimo a que pode fixá-lo é: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` , arredondado para os 1000 RU/s mais próximos. 

Exemplo #1: Suponha que tem um recipiente de autoescalação com RU/s máximo de 20.000 RU/s (balanças entre 2000 - 20.000 RU/s) e 50 GB de armazenamento. O valor mínimo mais baixo a que se pode definir max RU/s é: MAX(4000, 20.000 / 10, **50 * 100**) = 5000 RU/s (balanças entre 500 - 5000 RU/s).

Exemplo #2: Suponha que tem um recipiente de autoescalação com RU/s máximo de 100.000 RU/s e 100 GB de armazenamento. Agora, você escala max RU/s até 150.000 RU/s (escalas entre 15.000 - 150.000 RU/s). O valor mínimo mais baixo que pode agora definir max RU/s é: MAX (4000, **150.000 / 10**, 100 * 100) = 15.000 RU/s (balanças entre 1500 - 15.000 RU/s). 

Para uma base de dados de produção partilhada, quando baixar o máximo RU/s, o valor mínimo a que pode defini-lo é: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` , arredondado para os 1000 RU/s mais próximos.  

As fórmulas e exemplos acima referidos referem-se ao máximo de autoescala máximo de escala automática que se pode definir, e distingue-se do alcance do `0.1 * Tmax` `Tmax` sistema automaticamente. Não importa o que é o MÁXIMO RU/s, o sistema irá sempre escalar entre `0.1 * Tmax` `Tmax` . 

### <a name="how-does-ttl-work-with-autoscale"></a>Como funciona a TTL com autoescala?
Com a autoescala, as operações de TTL não afetam a escala de RU/s. Quaisquer RUs consumidas devido à TTL não fazem parte do RU/s faturado do contentor de autoescala. 

Por exemplo, suponha que tenha um recipiente de autoescala com 400 - 4000 RU/s:
- Hora 1: T=0: O recipiente não tem utilização (sem pedidos de TTL ou carga de trabalho). O RU/s faturado é 400 RU/s.
- Hora 1: T=1: TTL está ativado.
- Hora 1: T=2: O recipiente começa a receber pedidos, que consomem 1000 RU em 1 segundo. Há também 200 RUs de valor de TTL que precisam de acontecer. O RU/s faturado ainda é 1000 RU/s. Independentemente de quando ocorrem os TTLs, não afetarão a lógica de escala automática.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual é o mapeamento entre as divisórias max RU/s e físicas?
Quando selecionar pela primeira vez o max RU/s, a Azure Cosmos DB providenciará: Max RU/s / 10.000 RU/s = # de divisórias físicas. Cada [divisória física](partitioning-overview.md#physical-partitions) pode suportar até 10.000 RU/s e 50 GB de armazenamento. À medida que o tamanho do armazenamento aumenta, a Azure Cosmos DB dividirá automaticamente as divisórias para adicionar mais divisórias físicas para lidar com o aumento do armazenamento, ou aumentará o máximo RU/s se o armazenamento [exceder o limite associado](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

O máximo RU/s da base de dados ou do recipiente é dividido uniformemente em todas as divisórias físicas. Assim, a produção total de qualquer partição física pode escalar para é: Max RU/s de base de dados ou recipiente / # divisórias físicas. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>O que acontece se os pedidos de entrada excederem o máximo RU/s da base de dados ou do contentor?
Se o RU/s consumido globalmente exceder o máximo RU/s da base de dados ou do contentor, os pedidos que excedam o máximo RU/s serão estrangulados e devolverão um código de estado 429. Os pedidos que resultem em utilização mais de 100% normalizada também serão acelerados. A utilização normalizada é definida como o máximo da utilização ru/s em todas as divisórias físicas. Por exemplo, suponha que a sua potência máxima seja de 20.000 RU/s e que tem duas divisórias físicas, P_1 e P_2, cada uma capaz de escalar para 10.000 RU/s. Num dado segundo, se P_1 utilizou 6000 RUs, e P_2 8000 RUs, a utilização normalizada é MAX (6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0,8.

> [!NOTE]
> O cliente Azure Cosmos DB SDKs e ferramentas de importação de dados (Azure Data Factory, biblioteca executora a granel) voltam automaticamente a tentar em 429s, por isso, ocasionalmente, 429s estão bem. Um número elevado sustentado de 429s pode indicar que precisa aumentar o máximo RU/s ou rever a sua estratégia de partição para uma [partição quente](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Ainda é possível ver 429s (aceleração/limitação de taxa) quando a autoescala está ativada? 
Sim. É possível ver erros 429 em dois cenários. Em primeiro lugar, quando o RU/s consumido globalmente exceder o máximo RU/s da base de dados ou do contentor, o serviço irá acelerar os pedidos em conformidade. 

Em segundo lugar, se houver uma partição quente, ou seja, um valor-chave de partição lógica que tenha uma quantidade desproporcionalmente maior de pedidos em comparação com outros valores-chave de partição, é possível que a partição física subjacente exceda o seu orçamento RU/s. Como melhor prática, para evitar partições frequentes, [escolha uma boa chave de partição](partitioning-overview.md#choose-partitionkey) que resulte numa distribuição uniforme tanto do armazenamento como do débito. 

Por exemplo, se selecionar a opção de produção máxima de 20.000 RU/s e tiver 200 GB de armazenamento, com quatro divisórias físicas, cada divisória física pode ser autodimensionada até 5000 RU/s. Se houver uma partição quente numa chave de partição lógica específica, verá 429s quando a partição física subjacente reside em mais de 5000 RU/s, ou seja, excede 100% de utilização normalizada.


## <a name="next-steps"></a>Passos seguintes

* Saiba como permitir a [autoescalação numa base de dados ou contentor Azure Cosmos DB](how-to-provision-autoscale-throughput.md).
* Conheça os [benefícios da produção aussitada com autoescala.](provision-throughput-autoscale.md#benefits-of-autoscale)
* Saiba mais sobre [divisórias lógicas e físicas.](partitioning-overview.md)
                        
