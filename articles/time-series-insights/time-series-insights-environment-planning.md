---
title: Planear a escala do seu ambiente do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como a seguir as melhores práticas quando planear um ambiente do Azure Time Series Insights, incluindo a capacidade de armazenamento, retenção de dados, capacidade de entrada, monitorização e negócio de recuperação após desastre (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: f73f814321abbb75624ac18c9191c69a99cfe925
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64693581"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planejar seu ambiente do Azure Time Series Insights

Este artigo descreve como planejar seu ambiente do Azure Time Series Insights com base na sua taxa de entrada esperada e os requisitos de retenção de dados.

## <a name="video"></a>Vídeo

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Neste vídeo, vamos abordar a retenção de dados do Time Series Insights e como planejá-la.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Melhores práticas

Para começar a utilizar com o Time Series Insights, é melhor se souber a quantidade de dados esperado para enviar por push ao minuto, bem como a forma como tempo de que necessita armazenar os dados.  

Para obter mais informações sobre a retenção e capacidade para ambos os SKUs de informações de série de tempo, consulte [preços de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Considere os seguintes atributos melhor plano para o ambiente para o sucesso de longo prazo:

- Capacidade de armazenamento
- Período de retenção de dados
- Capacidade de entrada
- Formatação de seus eventos
- Assegurando que tem dados de referência no local

## <a name="understand-storage-capacity"></a>Compreender a capacidade de armazenamento

Por predefinição, o Time Series Insights retém os dados com base na quantidade de armazenamento que aprovisionou (quantidade de vezes de unidades de armazenamento por unidade) e de entrada.

## <a name="understand-data-retention"></a>Compreender a retenção de dados

Pode configurar o seu ambiente de Time Series Insights **período de retenção de dados** definição, habilitando até 400 dias de retenção.  O Time Series Insights tem dois modos, um que otimiza para garantir que seu ambiente tem os dados mais atualizados (por predefinição) e outro que otimiza para garantir a retenção de limites são cumpridos, onde entrada está em pausa se a capacidade de armazenamento geral para o ambiente for atingido.  Pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal do Azure.

Pode configurar um máximo de 400 dias de retenção de dados no seu ambiente do Time Series Insights.

## <a name="configure-data-retention"></a>Configurar a retenção de dados

1. Na [portal do Azure](https://portal.azure.com), selecione o seu ambiente do Time Series Insights.

2. Na **página de ambiente do Time Series Insights**, no **definições** título, selecione **configurar**. 

3. Na **período de retenção de dados (em dias)** , introduza um valor de 1 como 400.

   ![Configurar retenção](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Compreender a capacidade de entrada

A outra área para se concentrar para o planejamento é a capacidade de entrada, que é um derivativo da alocação de minuto. 

De uma perspectiva de limitação, um pacote de dados ingressed com um tamanho de pacote de 32 KB é tratado como 32 eventos, cada 1 KB em tamanho normal. O tamanho máximo do evento permitido é de 32 KB; pacotes de dados superiores a 32 KB são truncados.

A tabela seguinte resume a capacidade de entrada para cada SKU:

|SKU  |Contagem de eventos por mês, por unidade  |Por mês, por unidade de tamanho de eventos  |Contagem de eventos por minuto, por unidade  | Tamanho por minuto, por unidade   |
|---------|---------|---------|---------|---------|
|S1     |   30 milhões     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milhões    |   300 GB   | 7,200   | 7.200 KB  |

Pode aumentar a capacidade de um S1 ou S2 SKU de 10 unidades num único ambiente. Não é possível migrar de um ambiente de S1 para S2 ou de um ambiente de S2 para um S1.

Para a capacidade de entrada, deve determinar primeiro a entrada total que exigir numa base por mês. Em seguida, determine o que sua por minuto às suas necessidades, como se trata de onde limitação e latência reproduzir uma função.

Se tiver um pico na sua entrada de dados em menos de 24 horas de duração, o Time Series Insights pode "catch-up" a uma taxa de entrada de 2 x preços listados acima. 

Por exemplo, se tiver um único S1 SKU e os dados de entrada a uma taxa de 720 eventos por minuto e o pico durante menos de 1 hora a uma taxa de eventos de 1440 ou menos, não haveria nenhuma latência perceptível ao seu ambiente. No entanto, se exceder 1440 eventos por minuto durante mais de uma hora, provavelmente teriam latência para dados que são visualizados e estão disponíveis para consulta no seu ambiente. 

Talvez não saiba com antecedência a quantidade de dados esperado enviar por push. Neste caso, pode encontrar a telemetria de dados para [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e [Event Hubs do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) no portal do Azure. Este telemetria pode ajudá-lo a determinar como aprovisionar o seu ambiente. Utilize o **métricas** página no portal do Azure, a respetiva origem do evento ver a respetiva telemetria. Se compreender suas métricas de origem do evento, pode planear com mais eficiência e aprovisionar o seu ambiente do Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

- Confirme a sua capacidade de entrada for superior a sua tarifa por minuto média e de que o seu ambiente é grande o suficiente para lidar com a sua entrada antecipada equivalente a 2x sua capacidade para menos de 1 hora.

- Se ocorrerem de picos de entrada que pela última vez durante mais de 1 hora, utilize a taxa de pico como sua média e aprovisionar um ambiente com capacidade para lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Mitigar limitação e latência

Para obter informações sobre como impedir a limitação e latência, consulte [minimizar a latência e limitação](time-series-insights-environment-mitigate-latency.md).

## <a name="shaping-your-events"></a>Formatação de seus eventos

É importante certificar-se de que a forma como os eventos são enviados para o TSI suporta o tamanho do ambiente do que se estiver a aprovisionar (por outro lado, pode mapear o tamanho do ambiente para o número de eventos lê do TSI e o tamanho de cada evento).  Da mesma forma, é importante pensar sobre os atributos que pode querer segmentar e filtrar por ao consultar os seus dados.  Com isso em mente, sugerimos a analisar o JSON shaping secção do nosso [documentação de eventos de envio](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).  É em direção à parte inferior da página.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Assegurando que tem dados de referência no local

Um conjunto de dados de referência é uma coleção de itens que aumentam os eventos da origem de evento. O mecanismo de entrada do Time Series Insights une cada evento da origem de evento com a linha de dados correspondente no seu conjunto de dados de referência. Esse evento aumentado fica então disponível para consulta. Esta associação baseia-se as colunas de chave primária definidas no seu conjunto de dados de referência.

Tenha em atenção que os dados de referência não está associada retroativamente. Isso significa que apenas os dados de entrada atuais e futuras são correspondidos e associados ao conjunto de datas de referência, assim que ele foi configurado e carregado.  Se pretender enviar muitos dados históricos para TSI e não carregar ou criar dados de referência no TSI em primeiro lugar, em seguida, poderá ter de Refazer seu trabalho (dica, não divertidos).  

Para saber mais sobre como criar, carregar e gerir os seus dados de referência no TSI, aceda ao nosso [documentação de conjunto de dados de referência](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="business-disaster-recovery"></a>Recuperação após desastre de negócio

Como um serviço do Azure, o Time Series Insights fornece elevada disponibilidade (HA) com redundâncias ao nível da região do Azure, sem qualquer trabalho adicional necessário para a solução. A plataforma Microsoft Azure também inclui recursos para ajudar a criar soluções com capacidades de recuperação (DR) após desastre ou entre regiões de disponibilidade. Se quiser fornecer global, entre regiões de elevada disponibilidade para dispositivos ou utilizadores, tirar partido destas funcionalidades de DR do Azure. O artigo [orientações técnicas de continuidade do negócio de Azure](../resiliency/resiliency-technical-guidance.md) descreve as funcionalidades incorporadas no Azure para a continuidade do negócio e de DR. O [recuperação após desastre e elevada disponibilidade para aplicações do Azure](https://docs.microsoft.com/azure/architecture/resiliency/index) documento fornece orientações de arquitetura sobre estratégias para aplicações do Azure alcançar a HA e DR.

O Azure Time Series Insights não tem recuperação após desastre de negócio internas (BCDR).
Por predefinição no IoT Hub do Azure e Hubs de eventos tem recuperação incorporada.

Para saber mais sobre as políticas BCDR do IoT Hub, aceda [aqui](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr).  

Para saber mais sobre as políticas BCDR do hub de eventos, aceda [aqui](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).

No entanto, os clientes que necessitam de BCDR ainda podem implementar uma estratégia de recuperação usando o seguinte método.
Ao criar um ambiente do Time Series Insights segundo uma cópia de segurança do Azure região e enviar eventos para esse ambiente secundário da origem de eventos primário, tirando partido de um segundo grupo de consumidores dedicado e diretrizes BCDR essa origem de evento.  

1. Crie o ambiente na segunda região.  Mais sobre como criar um ambiente do Time Series Insights [aqui](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Criar um segundo grupo de consumidores dedicado para a origem do evento e ligar essa origem de evento para o novo ambiente.  Certifique-se de que designar o grupo de consumidores dedicado, segundo.  Pode saber mais sobre isso ao seguir qualquer um [documentação do IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) ou [documentação do hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access).
1. Se a região primária ficam Inativos durante um incidente de desastres, mude ao longo de operações para o ambiente do Time Series Insights cópia de segurança.  

É **importante ter em conta** durante qualquer cenário de ativação pós-falha pode haver um atraso antes do TSI pode começar novamente mensagens de processamento: Isto pode fazer com que um pico no processamento de mensagens. Para obter mais informações dar uma olhada [neste documento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [adicionar uma origem de evento do Hub de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) para Time Series Insights.

- Saiba mais sobre como [configurar uma origem de evento do IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).