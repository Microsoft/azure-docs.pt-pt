---
title: Crie recipientes e bases de dados Azure Cosmos em modo de escala automática.
description: Conheça os benefícios, use casos e como fornecer bases de dados e recipientes Azure Cosmos em modo de escala automática.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 533cd8fa69c01b8a36ff5e314ce61a4b624e62ec
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655818"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Crie recipientes e bases de dados Azure Cosmos com entrada em escala automática

O Azure Cosmos DB permite-lhe definir a produção normal (manual) ou auto-escala aprovisionada nas suas bases de dados e contentores. Este artigo descreve os benefícios e utiliza os casos de produção de escala automática. 

A produção de escala automática é adequada para cargas de trabalho críticas de missão que têm padrões de tráfego variáveis ou imprevisíveis, e requerem SLAs em alto desempenho e escala. 

Com escala automática, o Azure Cosmos DB **escala automaticamente e instantaneamente a entrada (RU/s)** da sua base de dados ou contentor com base na utilização, sem afetar a disponibilidade, latência, produção ou desempenho da carga de trabalho. 

## <a name="benefits-of-autoscale"></a>Benefícios da escala automática

As bases de dados e contentores da Azure Cosmos que estão configurados com a produção de escala automática têm os seguintes benefícios:

* **Simples:** A escala automática remove a complexidade da gestão de RU/s com scripts personalizados ou capacidade de escala manual. 

* **Escalável:** Bases de dados e contentores escalam automaticamente a entrada prevista conforme necessário. Não há nenhuma perturbação nas ligações, aplicações ou impacto do cliente na Azure Cosmos DB SLAs.

* **Rentável:** A escala automática ajuda a otimizar o uso do RU/s e o uso de custos, reduzindo-se quando não está a ser utilizado. Só pagas os recursos que as tuas cargas de trabalho precisam por hora.

* **Altamente disponível:** Bases de dados e contentores que utilizam a escala automática utilizam o mesmo backend Azure Cosmos DB altamente disponível para garantir a durabilidade dos dados e a elevada disponibilidade.

## <a name="use-cases-of-autoscale"></a>Utilizar caixas de escala automática

Os casos de utilização da escala automática incluem:

* **Cargas de trabalho variáveis ou imprevisíveis:** Quando as suas cargas de trabalho têm picos variáveis ou imprevisíveis no uso, a escala automática ajuda a escalar automaticamente para cima e para baixo com base no uso. Exemplos incluem sites de retalho que têm diferentes padrões de tráfego dependendo da sazonalidade; Cargas de trabalho iOT que têm picos em vários momentos durante o dia; linha de aplicações empresariais que vêem o uso máximo algumas vezes por mês ou ano, e muito mais. Com escala automática, já não é necessário fornecer manualmente capacidade máxima ou média. 

* **Novas aplicações:** Se estiver a desenvolver uma nova aplicação e não tiver a certeza sobre a entrada (RU/s) de que necessita, a escala automática facilita o início. Pode começar com o ponto de entrada em escala automática de 400 - 4000 RU/s, monitorizar o seu uso e determinar o RU/s certo ao longo do tempo.

* **Aplicações pouco utilizadas:** Se tiver uma aplicação que só é usada por algumas horas várias vezes por dia, semana ou mês — como uma aplicação de baixo volume/web/blog site — a escala automática ajusta a capacidade de manusear o uso máximo e reduz a escala quando termina. 

* **Cargas de trabalho** de desenvolvimento e teste: Se você ou a sua equipa usarem bases de dados e contentores da Azure Cosmos durante o horário de trabalho, mas não precisarem deles em noites ou fins de semana, a escala automática ajuda a poupar custos, reduzindo para um mínimo quando não estão a ser utilizados. 

* **Cargas de trabalho/consultas de produção programadas:** Se tiver uma série de pedidos, operações ou consultas programadas que pretende executar durante períodos de inatividade, pode fazê-lo facilmente com escala automática. Quando necessitar de executar a carga de trabalho, a entrada escalará automaticamente para o que é necessário e escalará depois. 

Construir uma solução personalizada para estes problemas não só requer uma enorme quantidade de tempo, como também introduz complexidade na configuração ou código da sua aplicação. A escala automática permite que os cenários acima referidos saisse da caixa e remova a necessidade de dimensionamento personalizado ou manual da capacidade. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Como funciona a escala automática de produção de produção

Ao configurar recipientes e bases de dados com escala automática, especifice a entrada máxima `Tmax` necessária. Azure Cosmos DB escala a entrada `T` tal `0.1*Tmax <= T <= Tmax` . Por exemplo, se fixar a potência máxima para 20.000 RU/s, a entrada escalará entre 2000 e 20.000 RU/s. Como a escala é automática e instantânea, em qualquer momento, pode consumir até ao provisionado `Tmax` sem demora. 

A cada hora, será cobrado para a maior entrada `T` do sistema escalado dentro de uma hora.

O ponto de entrada para a potência máxima de escala automática `Tmax` começa em 4000 RU/s, que escala entre 400 - 4000 RU/s. Pode definir `Tmax` em incrementos de 1000 RU/s e alterar o valor a qualquer momento.  

## <a name="enable-autoscale-on-existing-resources"></a>Ativar a escala automática dos recursos existentes ##
Utilize o [portal Azure](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) para permitir a escala automática numa base de dados ou recipiente existente. Pode alternar entre a escala automática e a produção normal (manual) a qualquer momento. Consulte esta [documentação](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) para mais informações.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Limites de produção e armazenamento para a escala automática

Por qualquer valor, a base de `Tmax` dados ou o recipiente podem armazenar um total de `0.01 * Tmax GB` . Após este valor de armazenamento ser atingido, o máximo de RU/s será automaticamente aumentado com base no novo valor de armazenamento, sem qualquer impacto na sua aplicação. 

Por exemplo, se começar com um RU/s máximo de 50.000 RU/s (escalas entre 5000 - 50.000 RU/s), pode armazenar até 500 GB de dados. Se ultrapassar os 500 GB - por exemplo, o armazenamento é agora de 600 GB, o novo máximo ru/s será de 60.000 RU/s (escalas entre 6000 - 60.000 RU/s).

Quando utilizar a entrada de nível de base de dados com escala automática, pode ter os primeiros 25 recipientes a partilhar um RU/s máximo de 4000 (escalas entre 400 e 4000 RU/s), desde que não exceda os 40 GB de armazenamento. Consulte esta [documentação](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) para mais informações.

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Comparação – recipientes configurados com entrada manual vs autoescala
Para mais detalhes, consulte esta [documentação](how-to-choose-offer.md) sobre como escolher entre a produção padrão (manual) e a entrada em escala automática.  

|| Recipientes com entrada padrão (manual)  | Recipientes com entrada em escala automática |
|---------|---------|---------|
| **Entrada prevista (RU/s)** | Provisões manualmente. | Escalado automaticamente e instantaneamente com base nos padrões de utilização da carga de trabalho. |
| **Limitação da taxa dos pedidos/operações (429)**  | Pode acontecer, se o consumo exceder a capacidade prevista. | Não acontecerá se consumir RU/s dentro da gama de entrada de escala automática que definiu.    |
| **Planeamento de capacidade** |  Tens de fazer planeamento de capacidades e fornecer o fluxo exato de que precisas. |    O sistema cuida automaticamente do planeamento de capacidades e da gestão da capacidade. |
| **Preços** | Paga-se pelo RU/s manualmente previsto por hora, utilizando a [taxa normal (manual) RU/s por hora](https://azure.microsoft.com/pricing/details/cosmos-db/). | Paga-se por hora o sistema de RU/s mais elevado, escalado até dentro de uma hora. <br/><br/> Para contas de região de escrita única, você paga os RU/s usados de hora em hora, utilizando a [taxa de RU/s por hora de autoescala](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>Para contas com várias regiões de escrita, não há custo extra para a escala automática. Paga-se a entrada utilizada de hora em hora utilizando a mesma [taxa de RU/s por hora multi-master](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Mais adequado para tipos de carga de trabalho** |  Cargas de trabalho previsíveis e estáveis|   Cargas de trabalho imprevisíveis e variáveis  |

## <a name="next-steps"></a>Passos seguintes

* Reveja o [FAQ](autoscale-faq.md)de escala automática .
* Aprenda a escolher entre a [entrada manual e](how-to-choose-offer.md)a entrada automática .
* Aprenda a fornecer entrada em [escala automática numa base de dados ou contentor da Azure Cosmos.](how-to-provision-autoscale-throughput.md)
* Saiba mais sobre [a partilha](partition-data.md) em Azure Cosmos DB.


