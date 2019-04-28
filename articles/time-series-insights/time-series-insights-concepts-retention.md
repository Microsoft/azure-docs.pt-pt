---
title: Compreender a retenção de dados no seu ambiente do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve as duas definições que controlam a retenção de dados no seu ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.custom: seodec18
ms.openlocfilehash: b230ac48cf2ca14c9ed988f869b5abba3e347215
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761572"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Compreender a retenção de dados no Time Series Insights

Este artigo descreve as duas definições que afetam a retenção de dados no seu ambiente do Time Series Insights (TSI).

## <a name="video"></a>Vídeo

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Neste vídeo, vamos abordar a retenção de dados do Time Series Insights e como planejá-la.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Cada ambiente de TSI tem uma definição que controla **período de retenção de dados**. Abrange o valor de 1 e 400 dias. Os dados são eliminados com base no ambiente armazenamento capacidade ou de retenção de duração (1-400), o que ocorrer primeiro.

Cada ambiente de TSI tem uma configuração adicional **limite de armazenamento excedido comportamento**. Esta definição controla o comportamento de entrada e de remoção quando for atingida a capacidade máxima de um ambiente. Existem dois comportamentos à sua escolha:
- **Remover dados antigos** (predefinição)  
- **Entrada de colocação em pausa**

> [!NOTE]
> Por predefinição, quando criar um novo ambiente, o período de retenção está configurado para **remover dados antigos**. Esta definição pode ser ativada/desativada conforme necessário depois de utilizar o portal do Azure, no momento da criação da **configurar** página do ambiente do TSI.

Para obter informações sobre comutação de comportamentos de retenção, consultar [configurar a retenção no Time Series Insights](time-series-insights-how-to-configure-retention.md).

Compare o comportamento de retenção de dados:

## <a name="purge-old-data"></a>Remover dados antigos

- Este comportamento é o comportamento predefinido para ambientes de TSI e anexos os ambientes de TSI comportamento mesmo apresentou uma, uma vez que ele iniciado para pré-visualização pública.  
- Este comportamento é preferencial quando os usuários querem ver sempre seus *dados mais recentes* em seus ambientes do TSI. 
- Este comportamento *remove* do dados uma vez o ambiente atingir os limites (tempo de retenção, de tamanho ou count, o que acontecer primeiro). Retenção está definida para 30 dias, por predefinição. 
- Os dados ingeridos mais antigos são removidos primeiro (abordagem FIFO).

### <a name="example-one"></a>Exemplo de um

Considere um ambiente de exemplo com o comportamento de retenção **Continue a entrada e remover dados antigos**: Neste exemplo, **período de retenção de dados** está definido como 400 dias. **Capacidade** está definido como unidade de S1, que contém a 30 GB de capacidade total.   Vamos supor que dados de entrada acumulem a 500 MB por dia em média. Este ambiente pode manter apenas os 60 dias de dados tendo em conta a taxa de dados de entrada, uma vez que a capacidade máxima é atingida em 60 dias. Acumulem os dados de entrada como: 500 MB cada dia x 60 dias = 30 GB.

Neste exemplo, no dia 61st, o ambiente mostra os dados mais recentes, mas remove os dados mais antigos, com mais de 60 dias. A remoção torna espaço para os novos dados de transmissão em fluxo, para que os novos dados poderão continuar a ser explorada. 

Se o utilizador pretende manter os dados mais, que podem aumentar o tamanho do ambiente ao adicionar unidades adicionais ou podem enviar por push menos dados.  

### <a name="example-two"></a>Exemplo de dois

Considere um ambiente configurado também o comportamento de retenção **Continue a entrada e remover dados antigos**. Neste exemplo, **período de retenção de dados** está definido como um valor mais baixo de 180 dias. **Capacidade** está definido como unidade de S1, que contém a 30 GB de capacidade total. Para armazenar dados de 180 dias completos, a entrada diária não pode exceder 0.166 GB (166 MB) por dia.  

Sempre que a taxa de entrada diária deste ambiente excede de 0.166 GB por dia, não não possível armazenar dados durante 180 dias, uma vez que alguns dados obtém removidos. Considere este mesmo ambiente durante um período de tempo ocupado. Assumir a que taxa de entrada do ambiente pode aumentar para um GB 0.189 média por dia. Em que período de tempo ocupado, cerca de 158 dias de dados são retidos (30GB/0.189 = 158,73 dias de retenção). Neste momento é menor que o intervalo de tempo de retenção de dados pretendido.

## <a name="pause-ingress"></a>Entrada de pausa

- Esse comportamento foi concebido para garantir que os dados não são removidos se os limites de tamanho e a contagem são percebidos antes do período de retenção.  
- Esse comportamento fornece mais tempo para os utilizadores aumentar a capacidade de seus ambientes, antes dos dados são removidos devido a violar do período de retenção
- Este comportamento ajuda a proteger contra a perda de dados, mas cria uma oportunidade para a perda dos seus dados mais recentes, se a entrada está em pausa para além do período de retenção da sua origem de evento.
- No entanto, assim que a capacidade máxima de um ambiente for atingida, o ambiente de pausa de entrada de dados até que ocorrem ações adicionais: 
   - Aumentar a capacidade máxima do ambiente. Para obter mais informações, consulte [como dimensionar o seu ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para adicionar mais unidades de escala.
   - O período de retenção de dados é atingido e os dados são removidos, levando, por conseguinte, o ambiente abaixo da capacidade máxima.

### <a name="example-three"></a>Exemplo três

Considere um ambiente com o comportamento de retenção configurado para **colocar em pausa entrada**. Neste exemplo, o **período de retenção de dados** está configurado para 60 dias. **Capacidade** está definido como 3 unidades de S1. Assumir que este ambiente tem uma entrada de dados de 2 GB por dia. Neste ambiente, entrada está em pausa quando a capacidade máxima for atingida. Nessa altura, o ambiente mostra o mesmo conjunto de dados até retoma de entrada ou até 'continuar a entrada' está ativada (que teria de remover os dados mais antigos para disponibilizar espaço para novos dados). 

Quando retoma a entrada:

- Fluxos de dados na ordem que foi recebido por origem do evento
- Os eventos são indexados com base no seu período de tempo, a menos que tenha excedido políticas de retenção na sua origem de evento. Para obter mais informações sobre a configuração de retenção de origem do evento, [FAQ dos Hubs de eventos](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Deve definir alertas para fornecer um aviso para ajudar a evitar a entrada que está a ser colocada em pausa. Perda de dados é possível, uma vez que o período de retenção predefinido é de 1 dia para origens de eventos do Azure. Portanto, depois de entrada está em pausa, provavelmente perderá os dados mais recentes, a menos que está a ser utilizada qualquer ação adicional. Tem de aumentar a capacidade ou mudar o comportamento para **remover dados antigos** para evitar a possibilidade de perda de dados.

Os Hubs de eventos afetados, considere ajustar os **retenção de mensagens** propriedade para minimizar a perda de dados quando ocorre a entrada de colocação em pausa no Time Series Insights.

![Retenção de mensagens do hub de eventos.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Se não existem propriedades são configuradas na origem de evento (timeStampPropertyName), o TSI é predefinido para o carimbo de hora da chegada ao hub de eventos, como o eixo x. Se timeStampPropertyName estiver configurado para ser algo diferente, o ambiente de procura o timeStampPropertyName configurado no pacote de dados quando os eventos são analisados. 

Se precisar de aumentar o seu ambiente verticalmente para acomodar a capacidade adicional ou para aumentar o período de retenção, veja [como dimensionar o seu ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para obter mais informações.  

## <a name="next-steps"></a>Passos Seguintes

- Para informações sobre o comportamento de retenção da mudança, reveja [configurar a retenção no Time Series Insights](time-series-insights-how-to-configure-retention.md).
