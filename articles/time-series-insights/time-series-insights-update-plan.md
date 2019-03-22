---
title: Planejar seu ambiente de pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Planeje seu ambiente de pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 251e95744f57d9b5e42df9bdc3743f4880ff5381
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077001"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planejar seu ambiente de pré-visualização do Azure Time Series Insights

Este artigo descreve as melhores práticas para planear e começar a utilizar rapidamente a pré-visualização do Azure Time Series Insights.

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas para o planejamento e preparação

Para começar a utilizar com o Time Series Insights, é melhor se compreender:

* O que obtém quando aprovisiona um ambiente de pré-visualização do Time Series Insights.
* São as suas propriedades de IDs de série de tempo e Timestamp.
* O que é o novo modelo de série de tempo e como criar seus próprios.
* Como enviar eventos com eficiência em JSON. 
* Opções de recuperação do Time Series Insights negócios após desastre.

O Time Series Insights emprega um modelo de negócio pay as you go. Para obter mais informações sobre custos e a capacidade, consulte [preços de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-time-series-insights-preview-environment"></a>O ambiente de pré-visualização do Time Series Insights

Quando aprovisiona um ambiente de pré-visualização do Time Series Insights, cria dois recursos do Azure:

* Ambiente de pré-visualização de informações de série de tempo
* Conta do Azure Storage para fins gerais V1

Para começar, terá de três itens adicionais:
 
- A [o modelo de série de tempo](./time-series-insights-update-tsm.md) 
- Um [origem de eventos ligado ao Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [Eventos a ser encaminhados para a origem do evento](./time-series-insights-send-events.md) que estão ambos mapeados para o modelo e estão no formato JSON válido 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Configurar as propriedades de IDs de série de tempo e Timestamp

Para criar um novo ambiente do Time Series Insights, selecione uma série de tempo de ID. Ao fazê-lo então, atua como uma partição lógica para os seus dados. Como observado, lembre-se de que tem os seus IDs de série de tempo pronto.

> [!IMPORTANT]
> Os IDs de série de tempo são *imutável* e *não é possível alterar mais tarde*. Verifique se cada um deles antes de seleção final e utilizar pela primeira vez.

Pode selecionar até três (3) chaves para distinguir exclusivamente os seus recursos. Para obter mais informações, leia [melhores práticas para a escolha de um ID de série de tempo](./time-series-insights-update-how-to-id.md) e [armazenamento e de entrada](./time-series-insights-update-storage-ingress.md).

A propriedade Timestamp também é importante. Pode designar esta propriedade quando adicionar origens de eventos. Cada origem de evento tem uma propriedade Timestamp opcional que utilizou para origens de eventos de controle ao longo do tempo. Timestamp valores são sensíveis a maiúsculas e minúsculas e têm de ser formatados a especificação individuais de cada origem de evento.

> [!TIP]
> Verifique se os requisitos de formatação e análise para origens de evento.

Quando deixado em branco, será utilizada a hora de colocar em fila de eventos de uma origem de evento que o evento Timestamp. Se tiver de enviar dados históricos ou eventos em lote, personalizando a propriedade Timestamp é mais útil à predefinição de tempo de colocar em fila de eventos. Para obter mais informações, leia sobre [como adicionar origens de eventos no IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). 

## <a name="understand-the-time-series-model"></a>Compreender a série de tempo modelo

Agora pode configurar o modelo de série de tempo de seu ambiente de Time Series Insights. O novo modelo torna mais fácil localizar e analisar dados de IoT. Ele permite que a organização, manutenção e enriquecimento de dados de séries de tempo e ajuda a preparar conjuntos de dados de preparado para o consumidor. O modelo utiliza **IDs de série de tempo**, que mapeiam para uma instância que associa o recurso exclusivo de variáveis, conhecidas como tipos e hierarquias. Saiba mais sobre a nova [modelo de série de tempo](./time-series-insights-update-tsm.md).

O modelo é dinâmico, pelo que podem ser criado em qualquer altura. Para começar rapidamente a utilizar, crie e carregue-o antes de enviar por push dados para o Time Series Insights. Para criar o seu modelo, veja [utilizam o modelo de série de tempo](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o modelo de série de tempo é mapeado para um modelo de recurso existente ou o sistema ERP já em vigor. Se não tiver um modelo existente, uma experiência de utilizador criados previamente é [fornecida](https://github.com/Microsoft/tsiclient) para começar a trabalhar rapidamente. Para prever como um modelo pode ajudá-lo, veja a [ambiente de demonstração de exemplo](https://insights.timeseries.azure.com/preview/demo). 

## <a name="shape-your-events"></a>Moldar seus eventos

Pode verificar a maneira que eventos são enviados para o Time Series Insights. O ideal é que os eventos são desnormalizados bem e eficiente.

Uma boa regra prática:

* Metadados de Store no seu modelo de série de tempo
* Modo de série de tempo, campos de instância e eventos incluem apenas as informações necessárias, tais como:
  * ID de Série de Tempo
  * Carimbo de data/hora

Para obter mais informações, consulte [moldar eventos](./time-series-insights-send-events.md#json).

## <a name="business-disaster-recovery"></a>Recuperação após desastre de negócio

O Time Series Insights é um serviço de elevada disponibilidade que utiliza as redundâncias ao nível da região do Azure. Não é necessária configuração para utilizar estas funcionalidades inerentes. A plataforma Microsoft Azure também inclui recursos para ajudar a criar soluções com capacidades de recuperação após desastre ou entre regiões de disponibilidade. Para fornecer global, entre regiões de elevada disponibilidade para dispositivos ou utilizadores, tirar partido destas funcionalidades de recuperação após desastre do Azure. 

Para obter informações sobre as funcionalidades incorporadas no Azure para a continuidade do negócio e recuperação após desastre (BCDR), consulte [orientações técnicas de continuidade de negócio no Azure](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance). Para obter orientações de arquitetura sobre estratégias para aplicações do Azure alcançar a elevada disponibilidade e recuperação após desastre, consulte o documento sobre [recuperação após desastre e elevada disponibilidade para aplicações do Azure](https://docs.microsoft.com/azure/architecture/resiliency/index).

> [!NOTE]
> 
>  O Time Series Insights não tem BCDR incorporada.
> Por predefinição, o armazenamento do Azure, o IoT Hub do Azure e o Event Hubs do Azure têm recuperação incorporada.

Para saber mais, leia sobre:

* [Redundância de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Recuperação de desastres de elevada disponibilidade do IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Políticas do Hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

Se necessitar de BCDR, ainda é possível implementar uma estratégia de recuperação. Crie um ambiente de Time Series Insights segundo numa região do Azure de cópia de segurança. Envie eventos para este ambiente secundário da origem de evento primário. Utilize um segundo grupo de consumidores dedicado e diretrizes BCDR essa origem de evento.

Siga estes passos para criar e utilizar um ambiente do Time Series Insights secundário.

1. Crie um ambiente numa segunda região. Para obter mais informações, consulte [ambientes do Time Series Insights](./time-series-insights-get-started.md).
1. Crie um segundo grupo de consumidores dedicado para a origem do evento. Ligue-se essa origem de evento para o novo ambiente. Certifique-se de que designar o segundo grupo de consumidores dedicado. Para obter mais informações, consulte a [documentação do IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) ou o [documentação do Hub de eventos](./time-series-insights-data-access.md).
1. Se a região primária é afetada durante um incidente de desastres, redirecionar operações para o ambiente do Time Series Insights cópia de segurança.

> [!IMPORTANT]
> * Tenha em atenção que poderá encontrar um atraso na eventualidade de uma ativação pós-falha.
> * Ativação pós-falha também pode fazer com que um pico momentânea no processamento de mensagens, como operações são reencaminhadas.
> * Para obter mais informações, consulte [minimizar a latência no Time Series Insights](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais, leia sobre:

- [Armazenamento de pré-visualização do Time Series Insights do Azure e de entrada](./time-series-insights-update-storage-ingress.md)
- [Modelação de dados](./time-series-insights-update-tsm.md)