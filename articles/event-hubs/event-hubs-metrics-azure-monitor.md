---
title: Métricas no Monitor Azure - Hubs de Eventos Azure / Microsoft Docs
description: Este artigo fornece informações sobre como usar a Monitorização Azure para monitorizar os Hubs de Eventos Azure
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 96c346f4359740fda5638dfdbe5735c5bdfce8c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162655"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Métricas de Hubs de Eventos do Azure no Azure Monitor

As métricas do Event Hubs dão-lhe o estado dos recursos do Event Hubs na sua subscrição Azure. Com um conjunto rico de dados de métricas, você pode avaliar a saúde geral dos seus centros de eventos não só ao nível do espaço de nome, mas também ao nível da entidade. Estas estatísticas podem ser importantes porque o ajudam a monitorizar o estado dos seus centros de eventos. As métricas também podem ajudar a resolver problemas de causa sem necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces unificadas de utilizador para monitorização em vários serviços Do Azure. Para mais informações, consulte [a monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e nas [métricas do Monitor Retrieve Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Monitor Azure fornece várias formas de aceder às métricas. Pode aceder às métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e soluções de análise como Log Analytics e Event Hubs. Para mais informações, consulte [os dados de Monitorização recolhidos pelo Monitor Azure](../azure-monitor/platform/data-platform.md).

As métricas são ativadas por padrão, e você pode aceder aos mais recentes 30 dias de dados. Se precisar de reter dados por um período mais longo, pode arquivar dados de métricas numa conta de Armazenamento Azure. Isto está configurado em [definições](../azure-monitor/platform/diagnostic-settings.md) de diagnóstico no Monitor Azure.


## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo no [portal Azure.](https://portal.azure.com) O exemplo que se segue mostra como visualizar pedidos bem sucedidos e pedidos de entrada ao nível da conta:

![Ver métricas de sucesso][1]

Também pode aceder às métricas diretamente através do espaço de nome. Para isso, selecione o seu espaço de nome e, em seguida, clique em **Métricas**. Para visualizar métricas filtradas ao alcance do centro de eventos, selecione o centro de eventos e, em seguida, clique em **Métricas**.

Para métricas que suportem dimensões, deve filtrar com o valor de dimensão desejado, como mostra o seguinte exemplo:

![Filtro com valor de dimensão][2]

## <a name="billing"></a>Faturação

A utilização de métricas no Monitor Azure é atualmente gratuita. No entanto, se utilizar soluções adicionais que ingerirem dados de métricas, poderá ser faturado por estas soluções. Por exemplo, é cobrado pelo Azure Storage se arquivar dados de métricas para uma conta de Armazenamento Azure. Também é cobrado pelo Azure se transmitir dados de métricas para registos do Monitor Azure para análise supérflua.

As seguintes métricas dão-lhe uma visão geral da saúde do seu serviço. 

> [!NOTE]
> Estamos a depreciar várias métricas à medida que são movidas com um nome diferente. Isto pode exigir que atualize as suas referências. As métricas marcadas com a palavra-chave "depreciada" não serão suportadas para avançar.

Todos os valores das métricas são enviados para o Monitor Azure a cada minuto. A granularidade do tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas do Event Hubs é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedido

Conta o número de pedidos de operações de dados e de gestão.

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| Pedidos de Entrada  | O número de pedidos feitos ao serviço Azure Event Hubs durante um período determinado. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade |
| Pedidos bem sucedidos    | O número de pedidos bem sucedidos feitos ao serviço Azure Event Hubs durante um período determinado. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade |
| Erros do servidor  | O número de pedidos não processados devido a um erro no serviço Azure Event Hubs durante um período determinado. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade |
|Erros do utilizador |O número de pedidos não processados devido a erros do utilizador durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Erros excedidos de quota |O número de pedidos excedeu a quota disponível. Consulte [este artigo](event-hubs-quotas.md) para mais informações sobre as quotas do Event Hubs.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

## <a name="throughput-metrics"></a>Métricas de entrada

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|Pedidos estrangulados |o número de pedidos que foram limitados porque a utilização das unidades de débito foi excedida.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

## <a name="message-metrics"></a>Métricas de mensagem

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|Mensagens de entrada |O número de eventos ou mensagens enviados para Os Centros de Eventos durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Mensagens de saída |O número de eventos ou mensagens recuperados dos Centros de Eventos durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Bytes de entrada |O número de bytes enviados para o serviço Azure Event Hubs durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Bytes de saída |O número de bytes recuperados do serviço Azure Event Hubs durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

## <a name="connection-metrics"></a>Métricas de ligação

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|ActiveConnections |O número de ligações ativas num espaço de nome, bem como numa entidade.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Conexões Abertas |O número de ligações abertas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Conexões fechadas |O número de ligações fechadas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

## <a name="event-hubs-capture-metrics"></a>Métricas de captura de hubs de eventos

Pode monitorizar as métricas de captura de Centros de Eventos quando ativa a funcionalidade Capture para os seus centros de eventos. As seguintes métricas descrevem o que pode monitorizar com a Captura ativada.

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|Captura de atrasos |O número de bytes que ainda estão por capturar para o destino escolhido.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Mensagens Capturadas |O número de mensagens ou eventos que são capturados para o destino escolhido durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Bytes Capturados |O número de bytes capturados para o destino escolhido durante um período determinado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

## <a name="metrics-dimensions"></a>Dimensões métricas

O Azure Event Hubs suporta as seguintes dimensões para métricas no Monitor Azure. Adicionar dimensões às suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas ao nível do espaço de nome. 

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|Nome da entidade| O Event Hubs apoia as entidades do hub do evento sob o espaço de nome.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Integração do Monitor Azure com ferramentas SIEM
Encaminhamento dos seus dados de monitorização (registos de atividade, registos de diagnóstico, etc.) para um centro de eventos com o Monitor Azure permite-lhe integrar-se facilmente com ferramentas de Informação de Segurança e Gestão de Eventos (SIEM). Para mais informações, consulte os seguintes artigos/posts de blog:

- [Stream Azure monitorizaos dados para um centro de eventos para consumo por uma ferramenta externa](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Introdução à Integração de Log Azure](../security/fundamentals/azure-log-integration-overview.md)
- [Use Azure Monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Utilizar o Azure Monitor para integrar com ferramentas de SIEM)

No cenário em que uma ferramenta SIEM consome dados de registo de um centro de eventos, se não vir mensagens recebidas ou se vir mensagens recebidas, mas sem mensagens de saída no gráfico de métricas, siga estes passos:

- Se não houver **mensagens de entrada,** significa que o serviço Azure Monitor não está a mover registos de auditoria/diagnóstico no centro do evento. Abra um bilhete de apoio com a equipa do Azure Monitor neste cenário. 
- se houver mensagens recebidas, mas **sem mensagens de saída,** significa que a aplicação SIEM não está a ler as mensagens. Contacte o fornecedor SIEM para determinar se a configuração do centro de eventos essas aplicações estão corretas.


## <a name="next-steps"></a>Passos seguintes

* Consulte a visão geral da [Monitorização Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* [Recupere as métricas](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) do Monitor Azure com a amostra .NET no GitHub. 

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

- Começar com um tutorial de Hubs de Eventos
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



