---
title: Métricas em Azure Monitor - Azure Event Hubs | Microsoft Docs
description: Este artigo fornece informações sobre como usar o Azure Monitoring para monitorizar os Hubs de Eventos do Azure
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a53f69030e8333dfe659b163b83983615212281f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573163"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Métricas de Hubs de Eventos do Azure no Azure Monitor

As métricas do Event Hubs dão-lhe o estado dos recursos do Event Hubs na sua subscrição do Azure. Com um rico conjunto de dados de métricas, você pode avaliar a saúde geral dos seus centros de eventos não só ao nível do espaço de nome, mas também ao nível da entidade. Estas estatísticas podem ser importantes, uma vez que o ajudam a monitorizar o estado dos seus centros de eventos. As métricas também podem ajudar a resolver problemas de causa de raiz sem precisar de contactar o suporte do Azure.

O Azure Monitor fornece interfaces de utilizador unificadas para monitorização em vários serviços Azure. Para obter mais informações, consulte [monitorar no Microsoft Azure](../azure-monitor/overview.md) e nas [métricas do Monitor De Recuperação Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias formas de aceder às métricas. Pode aceder às métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e soluções de análise como Log Analytics e Event Hubs. Para obter mais informações, consulte [os dados de monitorização recolhidos pelo Azure Monitor](../azure-monitor/data-platform.md).

As métricas são ativadas por padrão, e pode aceder aos últimos 30 dias de dados. Se precisar de manter os dados por um período mais longo, pode arquivar dados de métricas numa conta de Armazenamento Azure. Esta definição pode ser configurada em [definições de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) no Monitor Azure.


## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar métricas ao longo do tempo no [portal Azure](https://portal.azure.com). O exemplo a seguir mostra como visualizar pedidos bem sucedidos e pedidos de entrada ao nível da conta:

![Ver métricas de sucesso][1]

Também pode aceder às métricas diretamente através do espaço de nomes. Para tal, selecione o seu espaço de nome e, em seguida, selecione **Métricas**. Para apresentar métricas filtradas no âmbito do centro de eventos, selecione o centro de eventos e, em seguida, selecione **Métricas**.

Para as dimensões de suporte das métricas, deve filtrar com o valor de dimensão pretendido, como mostra o seguinte exemplo:

![Filtro com valor de dimensão][2]

## <a name="billing"></a>Faturação

A utilização de métricas no Azure Monitor é atualmente gratuita. No entanto, se utilizar outras soluções que ingerem dados de métricas, poderá ser faturado por estas soluções. Por exemplo, é faturado pelo Azure Storage se arquivar dados de métricas numa conta de Armazenamento Azure. Também é faturado pelo Azure se transmitir dados de métricas para registos do Azure Monitor para análise avançada.

As seguintes métricas dão-lhe uma visão geral da saúde do seu serviço. 

> [!NOTE]
> Estamos a depreciar várias métricas à medida que são movidas com um nome diferente. Isto pode exigir que atualize as suas referências. As métricas marcadas com a palavra-chave "precedida" não serão suportadas para a frente.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual são apresentados os valores das métricas. O intervalo de tempo suportado para todas as métricas do Event Hubs é de 1 minuto.

## <a name="azure-event-hubs-metrics"></a>Métricas Azure Event Hubs
Para obter uma lista de métricas suportadas pelo serviço, consulte [os Hubs de Eventos Azure](../azure-monitor/essentials/metrics-supported.md#microsofteventhubnamespaces)

> [!NOTE]
> Quando ocorre um erro do utilizador, o Azure Event Hubs atualiza a métrica **de Erros** do Utilizador, mas não regista qualquer outra informação de diagnóstico. Por isso, é necessário capturar detalhes sobre os erros do utilizador nas suas aplicações. Ou também pode converter a telemetria gerada quando as mensagens são enviadas ou recebidas em insights de aplicação. Por exemplo, consulte [Tracking with Application Insights](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights).

## <a name="azure-monitor-integration-with-siem-tools"></a>Integração do Azure Monitor com ferramentas SIEM
Encaminhar os seus dados de monitorização (registos de atividade, registos de diagnóstico, e assim por diante.) para um centro de eventos com o Azure Monitor permite-lhe integrar-se facilmente com ferramentas de Gestão de Informação de Segurança e Gestão de Eventos (SIEM). Para mais informações, consulte os seguintes artigos/posts de blog:

- [Stream Azure monitorizando dados para um centro de eventos para consumo por uma ferramenta externa](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
- [Introdução à Integração de Registos Azure](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Use Azure Monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Utilizar o Azure Monitor para integrar com ferramentas de SIEM)

No cenário em que uma ferramenta SIEM consome dados de registo a partir de um centro de eventos, se não vir mensagens recebidas ou se vir mensagens recebidas mas nenhuma mensagem de saída no gráfico de métricas, siga estes passos:

- Se não houver **mensagens recebidas,** significa que o serviço Azure Monitor não está a mover registos de auditoria/diagnósticos para o centro de eventos. Abra um bilhete de apoio com a equipa do Azure Monitor neste cenário. 
- se houver mensagens recebidas, mas **sem mensagens de saída,** significa que a aplicação SIEM não está a ler as mensagens. Contacte o fornecedor SIEM para determinar se a configuração do centro de eventos dessas aplicações está correta.


## <a name="next-steps"></a>Passos seguintes

* Consulte a [visão geral da Monitorização do Azure](../azure-monitor/overview.md).
* [Recupere as métricas do Monitor Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub. 

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

- Começa com um tutorial de Eventos Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
