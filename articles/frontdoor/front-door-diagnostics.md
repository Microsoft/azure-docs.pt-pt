---
title: Monitorização de métricas e registos no serviço de porta de entrada do Azure | Documentos da Microsoft
description: Este artigo descreve as diferentes métricas e registos de acesso que suporta o serviço de porta de entrada do Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009757"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitorização de métricas e registos no serviço de porta de entrada do Azure

Ao utilizar o serviço de porta de entrada do Azure, pode monitorizar os recursos das seguintes formas:

- **Métricas**. Gateway de aplicação atualmente tem sete métricas para ver os contadores de desempenho.
- **Logs**. Registos de diagnóstico e de atividade permitem que o desempenho, acesso e outros dados a ser guardado ou consumidos a partir de um recurso para efeitos de monitorização.

### <a name="metrics"></a>Métricas

As métricas são uma funcionalidade para determinados recursos do Azure que lhe permitem ver contadores de desempenho no portal. Seguem-se as métricas de porta de entrada disponíveis:

| Métrica | Nome a apresentar de métrica | Unidade | Dimensões | Descrição |
| --- | --- | --- | --- | --- |
| RequestCount | Número de Pedidos | Contagem | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de pedidos de cliente servido por porta de entrada.  |
| RequestSize | Tamanho do pedido | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como pedidos de clientes para a porta de entrada. |
| ResponseSize | Tamanho de resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como respostas da porta de entrada para os clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O tempo calculado no pedido de cliente recebidos pela porta da frente até que o cliente confirmados o último byte de resposta de porta de entrada. |
| BackendRequestCount | Contagem de pedidos de back-end | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de pedidos enviados do porta de entrada para o back-ends. |
| BackendRequestLatency | Latência de pedido de back-end | Milissegundos | Back-end | O tempo calculado a partir do quando o pedido foi enviado pela porta de entrada para o back-end até que a porta da frente recebido o último byte de resposta de back-end. |
| BackendHealthPercentage | Percentagem de estado de funcionamento do back-end | Percentagem | Back-end</br>BackendPool | A percentagem de estado de funcionamento com êxito as sondas de porta de entrada para o back-ends. |
| WebApplicationFirewallRequestCount | Contagem de pedidos de Firewall de aplicação Web | Contagem | PolicyName</br>RuleName</br>Ação | O número de pedidos de clientes processados pela segurança de camada de aplicativo de porta de entrada. |

## <a name="activity-log"></a>Registos de atividades

Registos de atividades fornecem informações sobre as operações efetuadas no serviço de porta de entrada. Também determinam a quê, quem e quando, por qualquer escrita (put, post ou delete) efetuadas nos serviços de porta de entrada.

>[!NOTE]
>Registos de Atividades não incluem operações de leitura (get). Também não incluem operações que efetuar ao utilizar o portal do Azure ou a API de gestão original.

Registos de atividades de acesso no seu serviço de porta de entrada ou de todos os registos de recursos do Azure no Azure Monitor. Para ver registos de atividades:

1. Selecione a sua instância de porta de entrada.
2. Selecione **registo de atividades**.

    ![Registo de atividades](./media/front-door-diagnostics/activity-log.png)

3. Escolha um âmbito de filtragem e, em seguida, selecione **aplicar**.

## <a name="diagnostic-logging"></a>Registos de diagnóstico
Os registos de diagnóstico fornecem informações avançadas sobre operações e erros que são importantes para auditoria e resolução de problemas. Os registos de diagnóstico diferem dos registos de atividades.

Registos de atividades fornecem informações sobre operações realizadas em recursos do Azure. Os registos de diagnóstico fornecem informações aprofundadas de operações executadas pelo recurso. Para obter mais informações, consulte [registos de diagnóstico do Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md).

![Registos de diagnósticos](./media/front-door-diagnostics/diagnostic-log.png)

Para configurar os registos de diagnóstico para o seu serviço de porta de entrada:

1. Selecione o seu serviço de porta de entrada do Azure.

2. Escolher **das definições de diagnóstico**.

3. Selecione **ativar os diagnósticos**. Arquivar registos de diagnósticos juntamente com métricas para uma conta de armazenamento, transmiti-los para um hub de eventos ou enviá-los para registos do Azure Monitor.

Atualmente, o serviço de porta de entrada fornece registos de diagnósticos (batches criados de hora a hora). Os registos de diagnóstico fornecem pedidos de API individuais tendo cada entrada o esquema seguinte:

| Propriedade  | Descrição |
| ------------- | ------------- |
| ClientIp | O endereço IP do cliente que efetuou o pedido. |
| ClientPort | A porta IP do cliente que efetuou o pedido. |
| HttpMethod | Método HTTP usado pelo pedido. |
| HttpStatusCode | O código de estado HTTP devolvido do proxy. |
| HttpStatusDetails | Resultado de estado no pedido. Significado deste valor de cadeia de caracteres pode ser encontrado numa tabela de referência de estado. |
| HttpVersion | Tipo de ligação ou a pedido. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de pedido e o corpo do pedido. |
| RequestUri | URI do pedido recebido. |
| ResponseBytes | Bytes enviados pelo servidor de back-end, como a resposta.  |
| RoutingRuleName | O nome da regra de encaminhamento que correspondem a solicitação. |
| SecurityProtocol | A versão do protocolo TLS/SSL utilizado pelo pedido ou nulo se sem encriptação. |
| TimeTaken | O período de tempo que a ação demorou em milissegundos. |
| UserAgent | O tipo de navegador que o cliente utilizou |
| TrackingReference | A cadeia de caracteres de referência exclusivo que identifica um pedido servido por porta de entrada, também enviado como cabeçalho X-Azure-Ref ao cliente. É necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |

## <a name="next-steps"></a>Passos Seguintes

- [Criar um perfil de porta de entrada](quickstart-create-front-door.md)
- [Como funciona a porta de entrada](front-door-routing-architecture.md)
