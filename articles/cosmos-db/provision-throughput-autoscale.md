---
title: Crie contentores e bases de dados Azure Cosmos em modo de autoescalação.
description: Conheça os benefícios, use casos e como providenciar bases de dados e contentores da Azure Cosmos em modo de autoescalação.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: ba0dd347c4ee2cb41b34c2fc34f1848a7295dc3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368669"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Criar contentores e bases de dados da Azure Cosmos com produção de escala automática
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Na Azure Cosmos DB, pode configurar o rendimento padrão (manual) ou autoescalado nas suas bases de dados e contentores. A produção provisitada por autoescalação na Azure Cosmos DB **permite-lhe escalar a produção (RU/s) da sua base de dados ou contentor automaticamente e instantaneamente**. A produção é dimensionada com base na utilização, sem afetar a disponibilidade, latência, produção ou desempenho da carga de trabalho.

A produção de autoescalação é adequada para cargas de trabalho críticas de missão que têm padrões de tráfego variáveis ou imprevisíveis, e requerem SLAs em alto desempenho e escala. Este artigo descreve os benefícios e os casos de utilização do débito aprovisionado de dimensionamento automático.

## <a name="benefits-of-autoscale"></a>Benefícios da autoescala

As bases de dados e os contentores Azure Cosmos que estão configurados com produção de escala automática têm os seguintes benefícios:

* **Simples:** A autoescala remove a complexidade da gestão ru/s com scripts personalizados ou capacidade de escala manual. 

* **Escalável:** As bases de dados e os contentores escalam automaticamente a produção prevista, se necessário. Não há nenhuma perturbação nas ligações do cliente, aplicações ou impacto para Azure Cosmos DLAs.

* **Rentável:** A autoescala ajuda a otimizar a utilização do RU/s e o uso de custos, reduzindo-o quando não está a ser utilizado. Só paga os recursos de que as suas cargas de trabalho precisam por hora. De todas as horas num mês, se definir automaticamente max RU/s(Tmax) e utilizar a quantidade total Tmax por 66% das horas ou menos, economizará com autoescala. Para saber mais, consulte como escolher entre o [artigo de produção padrão (manual) e autoescala.](how-to-choose-offer.md)

* **Altamente disponível:** Bases de dados e contentores que utilizam autoescalação utilizam o mesmo backend Azure Cosmos DB distribuído globalmente, tolerante a falhas, altamente disponível para garantir a durabilidade dos dados e alta disponibilidade.

## <a name="use-cases-of-autoscale"></a>Utilizar casos de autoescala

Os casos de utilização de autoescala incluem:

* **Cargas de trabalho variáveis ou imprevisíveis:** Quando as suas cargas de trabalho têm picos variáveis ou imprevisíveis na utilização, a autoescala ajuda a escalar automaticamente para cima e para baixo com base na utilização. Exemplos incluem sites de retalho que têm diferentes padrões de tráfego dependendo da sazonalidade; Cargas de trabalho IOT que têm picos em vários momentos durante o dia; linha de aplicações de negócio que vêem o pico de uso algumas vezes por mês ou ano, e muito mais. Com a autoescala, já não é necessário provisão manual para a capacidade máxima ou média. 

* **Novas aplicações:** Se está a desenvolver uma nova aplicação e não tem a certeza sobre a produção (RU/s) de que necessita, a autoescala facilita o seu início. Pode começar pelo ponto de entrada de escala automática de 400 - 4000 RU/s, monitorizar a sua utilização e determinar o RU/s certo ao longo do tempo.

* **Aplicações pouco utilizadas:** Se tiver uma aplicação que é usada apenas por algumas horas várias vezes por dia, semana ou mês - como uma aplicação/site de baixo volume/web/blog - a autoescala ajusta a capacidade de lidar com o pico de utilização e escalas quando acaba. 

* **Cargas de trabalho de desenvolvimento e de ensaio:** Se você ou a sua equipa utilizarem bases de dados e contentores da Azure Cosmos durante o horário de trabalho, mas não precisarem delas em noites ou fins de semana, a autoescala ajuda a poupar custos, reduzindo-os ao mínimo quando não estão a ser utilizados. 

* **Cargas/consultas de produção programadas:** Se tiver uma série de pedidos, operações ou consultas agendadas que pretende executar durante os períodos de marcha lenta, pode fazê-lo facilmente com autoescala. Quando precisar de executar a carga de trabalho, a produção irá automaticamente escalar para o que é necessário e reduzir-se depois. 

Construir uma solução personalizada para estes problemas não só requer uma enorme quantidade de tempo, como também introduz complexidade na configuração ou código da sua aplicação. A autoescala permite os cenários acima da caixa e elimina a necessidade de dimensionamento personalizado ou manual de capacidade. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Como funciona a produção de autoescala

Ao configurar contentores e bases de dados com autoescala, especifique a potência máxima `Tmax` necessária. Azure Cosmos DB escala a produção `T` tal `0.1*Tmax <= T <= Tmax` . Por exemplo, se definir a produção máxima para 20.000 RU/s, a produção irá escalar entre 2000 e 20.000 RU/s. Como o escalonamento é automático e instantâneo, em qualquer momento, pode consumir até ao provisionado `Tmax` sem demora. 

A cada hora, será cobrado para a maior produção `T` do sistema escalado dentro de uma hora.

O ponto de entrada para a potência máxima de autoescala `Tmax` começa em 4000 RU/s, que escala entre 400 - 4000 RU/s. Pode definir `Tmax` incrementos de 1000 RU/s e alterar o valor a qualquer momento.  

## <a name="enable-autoscale-on-existing-resources"></a>Permitir a autoescala dos recursos existentes

Utilize o [portal Azure](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container), [modelo de Gestor de Recursos Azure](how-to-provision-autoscale-throughput.md#azure-resource-manager), [CLI](how-to-provision-autoscale-throughput.md#azure-cli) ou [PowerShell](how-to-provision-autoscale-throughput.md#azure-powershell) para permitir a autoescalação numa base de dados ou num recipiente existente. Pode alternar entre a potência de autoescala e a produção normalizada (manual) a qualquer momento. Consulte esta [documentação](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) para obter mais informações.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Limites de produção e armazenamento para autoescalação

Por qualquer `Tmax` valor, a base de dados ou o contentor podem armazenar um total de `0.01 * Tmax GB` . Depois de atingir esta quantidade de armazenamento, a RU/s máxima aumenta automaticamente com base no novo valor de armazenamento, sem afetar a aplicação. 

Por exemplo, se começar com uma RU/s máxima de 50 000 RU/s (que dimensiona entre 5000 e 50 000 RU/s), poderá armazenar até 500 GB de dados. Se exceder os 500 GB, por exemplo, se o armazenamento for agora de 600 GB, a nova RU/s máxima será de 60 000 RU/s (que dimensiona entre 6000 e 60 000 RU/s).

Quando utilizar o débito ao nível da base de dados com o dimensionamento automático, pode ter os primeiros 25 contentores a partilhar uma RU/s máxima de dimensionamento automático de 4000 (que dimensiona entre 400 e 4000 RU/s), desde que não exceda os 40 GB de armazenamento. Consulte esta [documentação](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) para obter mais informações.

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Comparação – contentores configurados com produção manual vs autoescala
Para obter mais detalhes, consulte esta [documentação](how-to-choose-offer.md) sobre como escolher entre a produção padrão (manual) e a produção de autoescalação.  

|| Contentores com produção padrão (manual)  | Contentores com produção de autoescala |
|---------|---------|---------|
| **Produção provisão (RU/s)** | A provisionado manualmente. | Escalado automaticamente e instantaneamente com base nos padrões de utilização da carga de trabalho. |
| **Limitação das taxas de pedidos/operações (429)**  | Pode acontecer, se o consumo exceder a capacidade a provisionada. | Não acontecerá se consumir RU/s dentro da gama de produção de escala automática que definiu.    |
| **Planeamento de capacidade** |  Tens de fazer planeamento de capacidades e providenciar o rendimento exato de que precisas. |    O sistema cuida automaticamente do planeamento de capacidades e da gestão da capacidade. |
| **Preços** | Paga-se os RU/s manualmente a provisionados por hora, utilizando a [taxa standard (manual) RU/s por hora](https://azure.microsoft.com/pricing/details/cosmos-db/). | Paga-se por hora o maior RU/s que o sistema aumentou até dentro de uma hora. <br/><br/> Para contas de uma única região de escrita, você paga os RU/s utilizados por hora, utilizando a [taxa de RU/s de escala automática por hora](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>Para contas com várias regiões de escrita, não há qualquer custo extra para a autoescala. Você paga a produção usada por hora usando a mesma [taxa de escrita de várias regiões RU/s por hora](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Mais adequado para tipos de carga de trabalho** |  Cargas de trabalho previsíveis e estáveis|   Cargas de trabalho imprevisíveis e variáveis  |

## <a name="next-steps"></a>Passos seguintes

* Reveja as [FAQ de escala automática.](autoscale-faq.md)
* Saiba como escolher entre a [produção manual e autoescala.](how-to-choose-offer.md)
* Saiba como providenciar a [produção de autoescalação numa base de dados ou contentor Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Saiba mais sobre [a partição](partitioning-overview.md) em Azure Cosmos DB.


