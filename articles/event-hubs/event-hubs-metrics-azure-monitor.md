---
title: Métricas no Monitor Azure - Hubs de Eventos Azure / Microsoft Docs
description: Este artigo fornece informações sobre como utilizar a monitorização do Azure para monitorizar os Hubs de eventos do Azure
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162655"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Métricas do Azure Event Hubs no Monitor Azure

As métricas do Event Hubs dão-lhe o estado dos recursos do Event Hubs na sua subscrição Azure. Com um vasto conjunto de dados de métricas, pode avaliar o estado de funcionamento geral os hubs de eventos não apenas ao nível do espaço de nomes, mas também no nível de entidade. Essas estatísticas podem ser importantes como eles ajudam-na monitorizar o estado dos hubs de eventos. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para mais informações, consulte [a monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e nas [métricas do Monitor Retrieve Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Pode aceder às métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e soluções de análise como Log Analytics e Event Hubs. Para mais informações, consulte [os dados de Monitorização recolhidos pelo Monitor Azure](../azure-monitor/platform/data-platform.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Isto está configurado em [definições](../azure-monitor/platform/diagnostic-settings.md) de diagnóstico no Monitor Azure.


## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo no [portal Azure.](https://portal.azure.com) O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![Ver métricas de sucesso][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para isso, selecione o seu espaço de nome e, em seguida, clique em **Métricas**. Para visualizar métricas filtradas ao alcance do centro de eventos, selecione o centro de eventos e, em seguida, clique em **Métricas**.

Para as métricas que suporta dimensões, tem de filtrar com o valor de dimensão pretendida, conforme mostrado no exemplo a seguir:

![Filtrar com valor de dimensão][2]

## <a name="billing"></a>Faturação

A utilização de métricas no Monitor Azure é atualmente gratuita. No entanto, se utilizar soluções adicionais que ingerir dados de métricas, pode ser cobrado por estas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Também é cobrado pelo Azure se transmitir dados de métricas para registos do Monitor Azure para análise supérflua.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas de Hubs de eventos é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedidos

Conta o número de pedidos de operações de gestão e de dados.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Pedidos de Entrada  | O número de pedidos efetuados para o serviço de Event Hubs do Azure num determinado período. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Pedidos bem sucedidos    | O número de pedidos com êxito efetuados para o serviço de Event Hubs do Azure num determinado período. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Erros do servidor  | O número de pedidos não processadas devido a um erro no serviço de Event Hubs do Azure num determinado período. <br/><br/>Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
|Erros do utilizador |O número de pedidos não processadas devido a erros de utilizador num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros excedidos de quota |O número de pedidos excedeu a quota disponível. Consulte [este artigo](event-hubs-quotas.md) para mais informações sobre as quotas do Event Hubs.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="throughput-metrics"></a>Métricas de débito

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Pedidos estrangulados |O número de pedidos que foram limitado porque a utilização de unidades de débito foi excedida.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="message-metrics"></a>Métricas de mensagem

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens de entrada |O número de eventos ou as mensagens enviadas para os Hubs de eventos num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída |Obter o número de mensagens ou eventos dos Hubs de eventos num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes de entrada |O número de bytes enviados para o serviço de Event Hubs do Azure num determinado período.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes de saída |O número de bytes obtidos a partir do serviço de Event Hubs do Azure num determinado período.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections |O número de ligações ativas num espaço de nomes, bem como numa entidade.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões Abertas |O número de conexões abertas.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões fechadas |O número de conexões encerradas.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="event-hubs-capture-metrics"></a>Métricas de captura dos Hubs de eventos

Pode monitorizar métricas de captura de Hubs de eventos quando ativar a funcionalidade de captura para os hubs de eventos. As métricas seguintes descrevem o que pode monitorizar com a captura ativada.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Captura de atrasos |O número de bytes que ainda estão a ser capturadas para o destino escolhido.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens Capturadas |O número de mensagens ou eventos que são capturados para o destino escolhido num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes Capturados |O número de bytes que são capturados para o destino escolhido num determinado período.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

Os Hubs de eventos do Azure suporta as seguintes dimensões de métricas no Azure Monitor. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|EntityName| Os Hubs de eventos suporta as entidades do hub de eventos sob o espaço de nomes.|

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
    - [python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



