---
title: Compreenda a retenção de dados no seu ambiente - Azure Time Series Insight [ Microsoft Docs
description: Este artigo descreve duas configurações que controlam a retenção de dados no ambiente da Série De Tempo Azure Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 34cf1e91b1fe5aae516c77bf2c280dfe70000611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894758"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Compreender a retenção de dados em Insights da Série Temporal Azure

Este artigo descreve duas configurações primárias que impactam a retenção de dados no seu ambiente Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>O vídeo seguinte resume a retenção de dados da Time Series Insights e como planeá-lo.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Cada um dos seus ambientes da Série De Tempo Azure Insights tem uma definição que controla o tempo de **retenção**de dados . O valor estende-se de 1 a 400 dias. Os dados são eliminados com base na capacidade de armazenamento do ambiente ou na duração da retenção, o que vier em primeiro lugar.

Além disso, o seu ambiente Azure Time Series Insights tem um limite de armazenamento ultrapassado a definição de **comportamento.** Controla o comportamento de ingresso e purga quando a capacidade máxima de um ambiente é alcançada. Há dois comportamentos para escolher ao configurar:

- **Expurgar dados antigos** (padrão)  
- **Pausa ingresso**

> [!NOTE]
> Por padrão, ao criar um novo ambiente, a retenção é configurada para **purgar dados antigos**. Esta definição pode ser toggled conforme necessário após o tempo de criação usando o portal Azure, na página **de Configuração** do ambiente Time Series Insights.
> * Para obter informações sobre como configurar as políticas de retenção, leia [a retenção de Configuração em Insights da Série De Tempo](time-series-insights-how-to-configure-retention.md).

Ambas as políticas de retenção de dados são descritas em maior detalhe abaixo.

## <a name="purge-old-data"></a>Expurgar dados antigos

- **Expurgar dados antigos** é a definição padrão para ambientes Azure Time Series Insights.  
- A purga de **dados antigos** é preferível quando os utilizadores querem ter sempre os seus *dados mais recentes* no seu ambiente Time Series Insights.
- A **definição** de dados *antigos* purga dados uma vez que os limites do ambiente (tempo de retenção, tamanho ou contagem, o que vier em primeiro lugar) são atingidos. A retenção é definida para 30 dias por padrão.
- Os dados mais antigos ingeridos são purgados primeiro (a abordagem "First In First out").

### <a name="example-one"></a>Exemplo um

Considere um ambiente de exemplo com comportamento de retenção **Continue a inserção e expurgue dados antigos:**

O tempo de **retenção** de dados está definido para 400 dias. **A capacidade** é definida para a unidade S1, que contém 30 GB de capacidade total. Vamos assumir que os dados de entrada acumulam-se a 500 MB por dia, em média. Este ambiente só pode reter 60 dias de dados dada a taxa de dados de entrada, uma vez que a capacidade máxima é atingida em 60 dias. Os dados de entrada acumulam-se como: 500 MB por dia x 60 dias = 30 GB.

No 61º dia, o ambiente mostra os dados mais recentes, mas expurga os dados mais antigos, com mais de 60 dias. A purga abre espaço para o novo streaming de dados, para que novos dados possam continuar a ser explorados. Se o utilizador quiser reter os dados por mais tempo, pode aumentar o tamanho do ambiente adicionando unidades adicionais ou pode empurrar menos dados.  

### <a name="example-two"></a>Exemplo dois

Considere um ambiente também configurado comportamento de retenção **Continue a ingresso e expurgue dados antigos.** Neste exemplo, o tempo de **retenção** de dados é definido para um valor mais baixo de 180 dias. **A capacidade** é definida para a unidade S1, que contém 30 GB de capacidade total. Para armazenar dados durante os 180 dias completos, a entrada diária não pode exceder 0,166 GB (166 MB) por dia.  

Sempre que a taxa diária de ingresso deste ambiente exceda 0,166 GB por dia, os dados não podem ser armazenados durante 180 dias, uma vez que alguns dados são expurgados. Considere este mesmo ambiente durante um período de tempo ocupado. Assuma que a taxa de ingresso do ambiente pode aumentar para uma média de 0,189 GB por dia. Nesse período de tempo ocupado, são retidos cerca de 158 dias de dados (30GB/0,189 = 158,73 dias de retenção). Este tempo é menor do que o prazo de retenção de dados desejado.

## <a name="pause-ingress"></a>Pausa ingresso

- A definição de **entrada de pausa** foi concebida para garantir que os dados não são purgados se os limites de tamanho e contagem forem atingidos antes do seu período de retenção.  
- **A pausa** proporciona tempo adicional para que os utilizadores aumentem a capacidade do seu ambiente antes de os dados forem expurgados devido à violação do período de retenção.
- Ajuda a protegê-lo da perda de dados, mas pode criar uma oportunidade para a perda dos seus dados mais recentes se a entrada for interrompida para além do período de retenção da sua fonte de evento.
- No entanto, uma vez atingida a capacidade máxima de um ambiente, o ambiente pausa a entrada de dados até que ocorram as seguintes ações adicionais:

   - Aumenta a capacidade máxima do ambiente para adicionar mais unidades de escala, conforme descrito em [Como escalar o seu ambiente](time-series-insights-how-to-scale-your-environment.md)Time Series Insights .
   - O período de retenção de dados é atingido e os dados são purgados, colocando o ambiente abaixo da sua capacidade máxima.

### <a name="example-three"></a>Exemplo três

Considere um ambiente com comportamento de retenção configurado para parar a **entrada**. Neste exemplo, o período de **retenção** de Dados está configurado para 60 dias. **A capacidade** está definida para três (3) unidades de S1. Assuma que este ambiente tem entrada de dados de 2 GB todos os dias. Neste ambiente, a entrada é interrompida uma vez que a capacidade máxima é alcançada.

Nessa altura, o ambiente mostra o mesmo conjunto de dados até que a entrada retome ou até **que a entrada continue** ativada (o que iria expurgar dados mais antigos para dar espaço a novos dados).

Quando a entrada recomeçar:

- Fluxos de dados na ordem que foi recebido por fonte do evento
- Os eventos são indexados com base no seu carimbo de tempo, a menos que tenha excedido as políticas de retenção na sua fonte de evento. Para mais informações sobre a configuração de retenção de fontes de [eventos, Event Hubs FAQ](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Deve definir alertas para fornecer aviso para ajudar a evitar que a entrada seja interrompida. A perda de dados é possível uma vez que a retenção por defeito é de 1 dia para fontes do evento Azure. Portanto, uma vez que a entrada é interrompida, você provavelmente perde os dados mais recentes a menos que sejam tomadas medidas adicionais. Deve aumentar a capacidade ou mudar de comportamento para **expurgar dados antigos** para evitar o potencial de perda de dados.

Nos Hubs de Eventos impactados, considere ajustar a propriedade de Retenção de **Mensagens** para minimizar a perda de dados quando a entrada de pausa ocorre em Time Series Insights.

[![Retenção de mensagens no centro do evento.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Se nenhuma propriedade estiver configurada`timeStampPropertyName`na fonte do evento ( ), time Series Insights predefica-se com o carimbo de chegada no centro de eventos como o eixo X. Se `timeStampPropertyName` estiver configurado para ser outra coisa, `timeStampPropertyName` o ambiente procura o configurado no pacote de dados quando os eventos são analisados.

Leia [Como escalar o seu ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para escalar o seu ambiente para acomodar capacidade adicional ou aumentar o comprimento de retenção.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre configurar ou alterar definições de retenção de dados, reveja [a retenção de Configuração em Insights da Série De Tempo](time-series-insights-how-to-configure-retention.md).

- Saiba sobre [a latência mitigante em Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).
