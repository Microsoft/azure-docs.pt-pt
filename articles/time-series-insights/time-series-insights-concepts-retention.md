---
title: Compreenda a retenção de dados no seu ambiente - Azure Time Series Insight | Microsoft Docs
description: Este artigo descreve duas definições que controlam a retenção de dados no ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 26363031aea37c53cce098e2b6cbc2b4d93b918f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307083"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Compreender a retenção de dados em Azure Time Series Insights Gen1

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve duas definições primárias que impactam a retenção de dados no ambiente Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>O vídeo seguinte resume a retenção de dados da Azure Time Series Insights e como planeá-lo.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Cada um dos ambientes da Série De Tempo Azure Insights tem uma definição que controla o **tempo de retenção de dados**. O valor estende-se de 1 a 400 dias. Os dados são eliminados com base na capacidade de armazenamento do ambiente ou na duração da retenção, o que vier primeiro.

Além disso, o seu ambiente Azure Time Series Insights tem um **limite de armazenamento excedido a** definição de comportamento. Controla o comportamento de entrada e purga quando se alcança a capacidade máxima de um ambiente. Há dois comportamentos à escolha ao configurar:

- **Purgar dados antigos** (padrão)  
- **Pausa ingress**

> [!NOTE]
> Por padrão, ao criar um novo ambiente, a retenção é configurada para **purgar dados antigos**. Esta definição pode ser alternada conforme necessário após o tempo de criação utilizando o portal Azure, na página **Configure** do ambiente Azure Time Series Insights.
>
> - Para obter informações sobre como configurar políticas de retenção, leia [a retenção configurante em Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

Ambas as políticas de retenção de dados são descritas em maior detalhe abaixo.

## <a name="purge-old-data"></a>Purgar dados antigos

- **Purgar dados antigos** é a definição padrão para ambientes Azure Time Series Insights.  
- **Purgar dados antigos** é preferido quando os utilizadores querem ter sempre os seus *dados mais recentes* no seu ambiente Azure Time Series Insights.
- A definição **de dados antigos purga** *os dados* uma vez que os limites do ambiente (tempo, tamanho ou contagem de retenção, o que vier primeiro) são alcançados. A retenção está definida para 30 dias por defeito.
- Os dados ingeridos mais antigos são purgados primeiro (a abordagem "First In First out").

### <a name="example-one"></a>Exemplo um

Considere um ambiente de exemplo com comportamento de retenção Continue a entrar **e expurgar dados antigos:**

**O tempo de retenção de dados** está definido para 400 dias. **A capacidade** é definida para a unidade S1, que contém 30 GB de capacidade total. Vamos assumir que os dados de entrada acumulam-se a 500 MB por dia, em média. Este ambiente só pode reter 60 dias de dados dada a taxa de entrada de dados, uma vez que a capacidade máxima é atingida em 60 dias. Os dados de entrada acumulam-se como: 500 MB por dia x 60 dias = 30 GB.

No dia 61, o ambiente mostra os dados mais recentes, mas expurga os dados mais antigos, com mais de 60 dias. A purga abre espaço para o novo streaming de dados, para que novos dados possam continuar a ser explorados. Se o utilizador quiser reter dados por mais tempo, pode aumentar o tamanho do ambiente adicionando unidades adicionais ou pode empurrar menos dados.  

### <a name="example-two"></a>Exemplo dois

Considere um ambiente também configurado comportamento de retenção **Continue a entrar e a purgar dados antigos.** Neste exemplo, **o tempo de retenção** de dados é definido para um valor inferior de 180 dias. **A capacidade** é definida para a unidade S1, que contém 30 GB de capacidade total. Para armazenar dados durante os 180 dias completos, a entrada diária não pode exceder 0,166 GB (166 MB) por dia.  

Sempre que a taxa diária de entrada deste ambiente exceda 0,166 GB por dia, os dados não podem ser armazenados durante 180 dias, uma vez que alguns dados são purgados. Considere este mesmo ambiente durante um período de tempo ocupado. Assuma que a taxa de entrada do ambiente pode aumentar para uma média de 0,189 GB por dia. Nesse período de tempo ocupado, são retidos cerca de 158 dias de dados (30GB/0,189 = 158,73 dias de retenção). Desta vez é menos do que o prazo de retenção de dados desejado.

## <a name="pause-ingress"></a>Pausa ingress

- A definição de entrada de pausa foi concebida para garantir que os **dados** não são purgados se os limites de tamanho e contagem forem atingidos antes do período de retenção.  
- **A pausa** proporciona um tempo adicional para os utilizadores aumentarem a capacidade do seu ambiente antes de os dados serem purgados devido à violação do período de retenção.
- Ajuda a protegê-lo da perda de dados, mas pode criar uma oportunidade para a perda dos seus dados mais recentes se a entrada for interrompida para além do período de retenção da sua fonte de evento.
- No entanto, uma vez alcançada a capacidade máxima de um ambiente, o ambiente interrompe a entrada de dados até que ocorram as seguintes ações adicionais:

  - Aumenta a capacidade máxima do ambiente para adicionar mais unidades de escala, conforme descrito em [Como escalar o ambiente Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
  - O período de retenção de dados é atingido e os dados são expurgados, colocando o ambiente abaixo da sua capacidade máxima.

### <a name="example-three"></a>Exemplo três

Considere um ambiente com comportamento de retenção configurado para **parar a entrada**. Neste exemplo, o **período de retenção de dados** é configurado para 60 dias. **A capacidade** está definida para três (3) unidades de S1. Assuma que este ambiente tem ingressão de dados de 2 GB por dia. Neste ambiente, a entrada é pausada uma vez que a capacidade máxima é alcançada.

Nessa altura, o ambiente mostra o mesmo conjunto de dados até que a entrada retome ou até que a entrada continue ativada (o que iria expurgar dados mais **antigos** para dar espaço a novos dados).

Quando a entrada recomeça:

- Fluxos de dados na ordem que foi recebido por fonte de evento
- Os eventos são indexados com base na sua marca de tempo, a menos que tenha excedido as políticas de retenção na sua fonte de evento. Para mais informações sobre a configuração de retenção de fontes de [evento, O Evento Hubs FAQ](../event-hubs/event-hubs-faq.yml)

> [!IMPORTANT]
> Deve definir alertas para fornecer aviso para ajudar a evitar a pausa. A perda de dados é possível uma vez que a retenção por defeito é de 1 dia para fontes de eventos Azure. Portanto, uma vez que a entrada é pausa, você provavelmente perde os dados mais recentes, a menos que sejam tomadas medidas adicionais. Deve aumentar a capacidade ou mudar o comportamento para **purgar dados antigos** para evitar o potencial de perda de dados.

Nos Impacted Event Hubs, considere ajustar a propriedade **de Retenção de Mensagens** para minimizar a perda de dados quando ocorrer uma pausa no Azure Time Series Insights.

[![Retenção de mensagens do centro do evento.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Se nenhuma propriedade estiver configurada na fonte do evento `timeStampPropertyName` (), Azure Time Series Insights predefine o relógio de chegada ao centro de eventos como o eixo X. Se `timeStampPropertyName` for configurado para ser outra coisa, o ambiente procura o configurado `timeStampPropertyName` no pacote de dados quando os eventos são analisados.

Leia [Como escalar o ambiente Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para escalar o seu ambiente para acomodar capacidade adicional ou aumentar o comprimento de retenção.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre configurar ou alterar definições de retenção de dados, [reveja a retenção de configuração em Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Saiba mais sobre [a mitigação da latência em Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).
