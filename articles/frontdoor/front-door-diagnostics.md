---
title: Serviço de porta de entrada do Azure - métricas e registo | Documentos da Microsoft
description: Este artigo ajuda-o a compreender as diferentes métricas e registos de acesso que suporta o serviço de porta de entrada do Azure
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
ms.openlocfilehash: d409d451385ba1f88189b12cf372845e70a87429
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726375"
---
# <a name="monitoring-metrics-for-front-door"></a>Métricas de monitorização para a porta de entrada

Ao utilizar o serviço de porta de entrada do Azure, pode monitorizar os recursos das seguintes formas:
* [Registos](#diagnostic-logging): Permitir que os registos de desempenho, acesso e outros dados a ser guardado ou consumidos a partir de um recurso para efeitos de monitorização.

* [Métricas](#metrics): Gateway de aplicação atualmente tem sete métricas para ver os contadores de desempenho.

## <a name="metrics"></a>Métricas

As métricas são uma funcionalidade para determinados recursos do Azure, onde pode ver contadores de desempenho no portal. Para a porta da frente, as métricas seguintes estão disponíveis:

| Métrica | Nome a apresentar de métrica | Unidade | Dimensões | Descrição |
| --- | --- | --- | --- | --- |
| RequestCount | Contagem de pedidos | Contagem | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de pedidos de cliente servido por porta de entrada.  |
| RequestSize | Tamanho do pedido | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como pedidos de clientes para a porta de entrada. |
| ResponseSize | Tamanho de resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como respostas da porta de entrada para os clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O tempo calculado a partir do quando o pedido de cliente foi recebido por porta da frente até que o cliente confirmados o último byte de resposta de porta de entrada. |
| BackendRequestCount | Contagem de pedidos de back-end | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de pedidos enviados do porta de entrada para o back-ends. |
| BackendRequestLatency | Latência de pedido de back-end | Milissegundos | Back-end | O tempo calculado a partir do quando o pedido foi enviado pela porta de entrada para o back-end até que a porta da frente recebido o último byte de resposta de back-end. |
| BackendHealthPercentage | Percentagem de estado de funcionamento do back-end | Percentagem | Back-end</br>BackendPool | A percentagem de estado de funcionamento com êxito as sondas de porta de entrada para o back-ends. |
| WebApplicationFirewallRequestCount | Contagem de pedidos de Firewall de aplicação Web | Contagem | PolicyName</br>RuleName</br>Ação | O número de pedidos de clientes processados pela segurança de camada de aplicativo de porta de entrada. |

## <a name="activity-log"></a>Registos de atividades

Registos de atividades fornecem informações sobre as operações que foram executadas em sua porta de entrada. Utilizar registos de atividades, pode determinar o "o quê, quem e quando" para quaisquer operações (PUT, POST, DELETE) efetuadas nos desde seu início de escrita.

> [!NOTE]
> Os registos de atividades não incluem operações de leitura (GET) nem operações executadas no Portal do Azure ou com as APIs de Gestão originais.

Pode aceder aos registos de atividade em sua porta de entrada ou aceder aos registos de todos os seus recursos do Azure no Azure Monitor. 

Para ver registos de atividades:

1. Selecione a sua instância de porta de entrada.
2. Clique em **Registo de atividades**.

    ![registo de atividade](./media/front-door-diagnostics/activity-log.png)

3. Selecione o âmbito de filtragem pretendido e clique em **Aplicar**.

## <a name="diagnostic-logging"></a>Registos de diagnóstico
Os registos de diagnóstico fornecem informações avançadas sobre operações e erros que são importantes para auditoria, bem como para fins de resolução de problemas. Os registos de diagnóstico diferem dos registos de atividades. Os registos de atividades fornecem informações aprofundadas sobre as operações executadas nos recursos do Azure. Os registos de diagnóstico fornecem informações aprofundadas sobre as operações executadas pelo recurso. Saiba mais sobre [registos de diagnóstico do Azure Monitor](..\azure-monitor\platform\diagnostic-logs-overview.md). 

Para configurar os registos de diagnóstico para a porta de entrada:

1. Selecione a instância de serviço APIM.
2. Clique em **Definições de diagnóstico**.

    ![registos de diagnóstico](./media/front-door-diagnostics/diagnostic-log.png)

3. Clique em **Ativar diagnósticos**. Pode arquivar registos de diagnósticos juntamente com métricas para uma conta de armazenamento, transmiti-los para um Hub de eventos ou enviá-los para registos do Azure Monitor. 

Atualmente, o serviço de porta de entrada do Azure fornece diagnóstico pedido de registos (batches criados de hora a hora) sobre a individual API tendo cada entrada o esquema seguinte:

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

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
