---
title: Entender a retenção de dados em seu ambiente – Azure Time Series Insight | Microsoft Docs
description: Este artigo descreve duas configurações que controlam a retenção de dados em seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 2c68c64202efec6d7dab745b6e1bdc029fa3976f
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561313"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Entender a retenção de dados no Azure Time Series Insights

Este artigo descreve duas configurações principais que afetam a retenção de dados em seu ambiente de Azure Time Series Insights.

## <a name="video"></a>Vídeo

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>O vídeo a seguir resume Time Series Insights retenção de dados e como planejar para ele.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Cada um de seus ambientes de Azure Time Series Insights tem uma configuração que controla o **tempo de retenção de dados**. O valor se estende de 1 a 400 dias. Os dados são excluídos com base na capacidade de armazenamento do ambiente ou na duração da retenção, o que vier primeiro.

Além disso, seu ambiente de Azure Time Series Insights tem uma configuração de **comportamento de limite de armazenamento excedido** . Ele controla o comportamento de entrada e limpeza quando a capacidade máxima de um ambiente é atingida. Há dois comportamentos para escolher ao configurá-lo:

- **Limpar dados antigos** (padrão)  
- **Pausar entrada**

> [!NOTE]
> Por padrão, ao criar um novo ambiente, a retenção é configurada para **limpar dados antigos**. Essa configuração pode ser alternada conforme necessário após a hora de criação usando o portal do Azure, na página **Configurar** do ambiente de time Series insights.
> * Para obter informações sobre como configurar políticas de retenção, leia [Configurando a retenção em time Series insights](time-series-insights-how-to-configure-retention.md).

Ambas as políticas de retenção de dados são descritas mais detalhadamente abaixo.

## <a name="purge-old-data"></a>Limpar dados antigos

- **Limpar dados antigos** é a configuração padrão para ambientes de Azure Time Series insights.  
- **Limpar dados antigos** é preferencial quando os usuários desejam sempre ver seus *dados mais recentes* em seu ambiente de time Series insights.
- A configuração **limpar dados antigos** *limpa* os dados depois que os limites do ambiente (tempo de retenção, tamanho ou contagem, o que ocorrer primeiro) são atingidos. A retenção é definida como 30 dias por padrão.
- Os dados ingeridos mais antigos são limpos primeiro (a abordagem "primeiro a entrar primeiro a sair").

### <a name="example-one"></a>Exemplo um

Considere um ambiente de exemplo com comportamento de retenção **continuar entrada e limpar dados antigos**:

O **tempo de retenção de dados** é definido como 400 dias. A **capacidade** é definida para a unidade S1, que contém 30 GB de capacidade total. Vamos supor que os dados de entrada sejam acumulados a 500 MB por dia em média. Esse ambiente pode reter apenas 60 dias de dados de acordo com a taxa de dados de entrada, uma vez que a capacidade máxima é atingida em 60 dias. Os dados de entrada são acumulados como: 500 MB por dia x 60 dias = 30 GB.

No dia 61 º, o ambiente mostra os dados mais recentes, mas limpa os dados mais antigos, com mais de 60 dias. A limpeza deixa espaço para o novo streaming de dados, para que novos dados possam continuar a ser explorados. Se o usuário quiser manter os dados por mais tempo, eles poderão aumentar o tamanho do ambiente adicionando mais unidades ou podem enviar por push menos dados.  

### <a name="example-two"></a>Exemplo dois

Considere que um ambiente também configurou o comportamento de retenção para **continuar a entrada e limpar os dados antigos**. Neste exemplo, o **tempo de retenção de dados** é definido como um valor inferior de 180 dias. A **capacidade** é definida para a unidade S1, que contém 30 GB de capacidade total. Para armazenar dados por todos os 180 dias, a entrada diária não pode exceder 0,166 GB (166 MB) por dia.  

Sempre que a taxa diária de entrada do ambiente excede 0,166 GB por dia, os dados não podem ser armazenados por 180 dias, já que alguns dados são limpos. Considere esse mesmo ambiente durante um período de tempo ocupado. Suponha que a taxa de entrada do ambiente possa aumentar para uma média de 0,189 GB por dia. Nesse período de tempo ocupado, cerca de 158 dias de dados são retidos (30 GB/0,189 = 158,73 dias de retenção). Esse tempo é menor do que o período de retenção de dados desejado.

## <a name="pause-ingress"></a>Pausar entrada

- A configuração **Pausar entrada** foi projetada para garantir que os dados não sejam limpos se os limites de tamanho e contagem forem atingidos antes do período de retenção.  
- **Pausar a entrada** fornece tempo adicional para os usuários aumentarem a capacidade de seu ambiente antes de os dados serem limpos devido à violação do período de retenção.
- Ele ajuda a proteger você contra perda de dados, mas pode criar uma oportunidade para a perda de seus dados mais recentes se a entrada estiver em pausa além do período de retenção da origem do evento.
- No entanto, depois que a capacidade máxima de um ambiente é atingida, o ambiente pausa a entrada de dados até que as seguintes ações adicionais ocorram:

   - Você aumenta a capacidade máxima do ambiente para adicionar mais unidades de escala, conforme descrito em [como dimensionar seu ambiente de time Series insights](time-series-insights-how-to-scale-your-environment.md).
   - O período de retenção de dados é atingido e os dados são limpos, trazendo o ambiente abaixo de sua capacidade máxima.

### <a name="example-three"></a>Exemplo três

Considere um ambiente com o comportamento de retenção configurado para **pausar a entrada**. Neste exemplo, o **período de retenção de dados** é configurado para 60 dias. A **capacidade** é definida como três (3) unidades de S1. Suponha que esse ambiente tenha entrada de dados de 2 GB por dia. Nesse ambiente, a entrada é pausada quando a capacidade máxima é atingida.

Nesse momento, o ambiente mostra o mesmo conjunto de dados até que a entrada seja retomada ou até que a **entrada continuar** esteja habilitada (o que limparia os mais antigos para liberar espaço para novos dados).

Quando a entrada for retomada:

- Os fluxos de dados na ordem em que foram recebidos pela origem do evento
- Os eventos são indexados com base em seu carimbo de data/hora, a menos que você tenha excedido as políticas de retenção na origem do evento. Para obter mais informações sobre configuração de retenção de origem de evento, [perguntas frequentes sobre hubs de eventos](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Você deve definir alertas para fornecer aviso para ajudar a evitar que a entrada seja pausada. A perda de dados é possível porque a retenção padrão é 1 dia para as fontes de eventos do Azure. Portanto, quando a entrada estiver em pausa, você provavelmente perderá os dados mais recentes, a menos que uma ação adicional seja executada. Você deve aumentar a capacidade ou alternar o comportamento para **limpar dados antigos** para evitar o potencial de perda de dados.

Nos hubs de eventos afetados, considere ajustar a propriedade de **retenção de mensagem** para minimizar a perda de dados ao pausar a entrada ocorre em time Series insights.

[![retenção de mensagens do hub de eventos.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Se nenhuma propriedade estiver configurada na origem do evento (`timeStampPropertyName`), Time Series Insights usa como padrão o carimbo de data/hora de chegada no Hub de eventos como o eixo X. Se `timeStampPropertyName` estiver configurado para ser algo mais, o ambiente procurará o `timeStampPropertyName` configurado no pacote de dados quando os eventos forem analisados.

Leia [como dimensionar seu ambiente de time Series insights](time-series-insights-how-to-scale-your-environment.md) para dimensionar seu ambiente para acomodar capacidade adicional ou aumentar a duração da retenção.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como configurar ou alterar as configurações de retenção de dados, examine [Configurando a retenção em time Series insights](time-series-insights-how-to-configure-retention.md).

- Saiba mais sobre a [redução da latência no Azure Time Series insights](time-series-insights-environment-mitigate-latency.md).
