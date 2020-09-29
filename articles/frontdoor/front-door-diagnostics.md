---
title: Métricas de monitorização e registos na Porta frontal do Azure. Microsoft Docs
description: Este artigo descreve as diferentes métricas e registos de acesso que a Porta Frontal Azure suporta
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: a1e77b5f669d1b492f2d71063a6c77bec1178696
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449283"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Métricas de monitorização e troncos na Porta frontal de Azure

Ao utilizar a Porta Frontal Azure, pode monitorizar os recursos das seguintes formas:

- **Métricas**. A Azure Front Door tem atualmente oito métricas para visualizar balcões de desempenho.
- **Troncos**. Os registos de atividade e de diagnóstico permitem que o desempenho, o acesso e outros dados sejam guardados ou consumidos a partir de um recurso para fins de monitorização.

### <a name="metrics"></a>Métricas

As métricas são uma característica de certos recursos Azure que permitem visualizar contadores de desempenho no portal. Estão disponíveis as métricas da Porta frontal:

| Métrica | Nome de exibição métrica | Unidade | Dimensões | Descrição |
| --- | --- | --- | --- | --- |
| PedidoCount | Número de Pedidos | de palavras | HttpStatus</br>Grupo HttpStatus</br>Região do Cliente</br>ClientCountry | O número de pedidos de clientes servidos pela Porta da Frente.  |
| Solicitação | Tamanho do pedido | Bytes | HttpStatus</br>Grupo HttpStatus</br>Região do Cliente</br>ClientCountry | O número de bytes enviados como pedidos de clientes para a Porta da Frente. |
| Tamanho das respostas | Tamanho da resposta | Bytes | HttpStatus</br>Grupo HttpStatus</br>Região do Cliente</br>ClientCountry | O número de bytes enviados como respostas da Porta da Frente aos clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>Grupo HttpStatus</br>Região do Cliente</br>ClientCountry | O tempo calculado a partir do pedido de cliente recebido pela Porta da Frente até que o cliente reconheceu a última resposta byte da Porta frontal. |
| BackendRequestCount | Contagem de pedidos de backend | de palavras | HttpStatus</br>Grupo HttpStatus</br>Back-end | O número de pedidos enviados da Porta da Frente para os backends. |
| BackendRequestatency | Pedido de backend Latência | Milissegundos | Back-end | O tempo calculado a partir do momento em que o pedido foi enviado pela Porta da Frente para o backend até que a Porta frontal recebeu a última resposta byte do backend. |
| BackendHealthPercentage | Percentagem de Saúde backend | Percentagem | Back-end</br>BackendPool | A percentagem de sondas de saúde bem sucedidas da Porta da Frente para os backends. |
| WebApplicationFirewallRequestCount | Contagem de pedidos de firewall de aplicação web | de palavras | PolicyName</br>Nome de Regras</br>Ação | O número de pedidos de cliente processados pela segurança da camada de aplicação da Porta frontal. |

## <a name="activity-logs"></a><a name="activity-log"></a>Troncos de atividade

Os registos de atividade fornecem informações sobre as operações feitas na Porta da Frente. Também determinam o quê, quem e quando para quaisquer operações de escrita (colocar, postar ou apagar) tomadas na Porta da Frente.

>[!NOTE]
>Os registos de atividade não incluem operações de leitura (obter). Também não incluem operações que executa utilizando o portal Azure ou a API de Gestão original.

Aceda aos registos de atividades na porta da frente ou em todos os registos dos seus recursos Azure no Azure Monitor. Para ver registos de atividades:

1. Selecione a sua instância da Porta da Frente.
2. Selecione **registo de atividades**.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Registo de atividades":::

3. Escolha um âmbito de filtragem e, em seguida, **selecione Aplicar**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Registos de diagnósticos
Os registos de diagnóstico fornecem informações ricas sobre operações e erros que são importantes para a auditoria e resolução de problemas. Os registos de diagnóstico diferem dos registos de atividade.

Os registos de atividade fornecem informações sobre as operações es feitas sobre os recursos da Azure. Os registos de diagnóstico fornecem informações sobre as operações que o seu recurso fez. Para obter mais informações, consulte [os registos de diagnóstico do Azure Monitor](../azure-monitor/platform/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Registo de atividades":::

Para configurar registos de diagnóstico para a sua porta frontal:

1. Selecione a sua Porta frontal Azure.

2. Escolha **as definições de Diagnóstico**.

3. **Selecione Ligue os diagnósticos**. Arquive os registos de diagnóstico juntamente com as métricas para uma conta de armazenamento, transmite-os para um centro de eventos ou envia-os para os registos do Azure Monitor.

A Porta frontal fornece atualmente registos de diagnóstico (lotados por hora). Os registos de diagnóstico fornecem pedidos individuais de API com cada entrada com o seguinte esquema:

| Propriedade  | Descrição |
| ------------- | ------------- |
| BackendHostname | Se o pedido foi encaminhado para um backend, este campo representa o nome anfitrião do backend. Este campo ficará em branco se o pedido for redirecionado ou encaminhado para uma cache regional (quando o cache estiver ativado para a regra de encaminhamento). |
| CacheStatus | Para cenários de cache, este campo define o cache hit/miss no POP |
| ClientIp | O endereço IP do cliente que fez o pedido. Se houve um cabeçalho X-Forwarded-For no pedido, então o IP do Cliente é escolhido do mesmo. |
| ClientPort | A porta IP do cliente que fez o pedido. |
| HttpMethod | Método HTTP utilizado pelo pedido. |
| HttpStatusCode | O código de estado HTTP devolvido do representante. |
| HttpStatusDetails | Estado resultante do pedido. O significado deste valor de cadeia pode ser encontrado numa tabela de referência de Estado. |
| HttpVersion | Tipo de pedido ou ligação. |
| POP | Nome curto da borda onde o pedido aterrou. |
| PedidoBytes | O tamanho da mensagem de pedido HTTP em bytes, incluindo os cabeçalhos de pedido e o corpo de pedido. |
| RequestUri | URI do pedido recebido. |
| RespostaBytes | Bytes enviados pelo servidor backend como resposta.  |
| RoutingRuleName | O nome da regra de encaminhamento que o pedido correspondia. |
| RegrasSEngineMatchNames | Os nomes das regras que o pedido correspondia. |
| SecurityProtocol | A versão do protocolo TLS/SSL utilizada pelo pedido ou nula se não houver encriptação. |
| SentToOriginShield | Campo booleano representando se houvesse uma falha de cache no primeiro ambiente e o pedido foi enviado para a cache regional. Ignore este campo se a regra de encaminhamento for um redirecionamento ou quando não tiver o caching ativado. |
| TimeTaken | O tempo de primeiro byte de pedido para a Porta frontal para durar a byte de resposta para fora, em segundos. |
| TrackingReference | A cadeia de referência única que identifica um pedido servido pela Porta da Frente, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |
| UserAgent | O tipo de navegador que o cliente usou. |

**Nota:** Para várias configurações de encaminhamento e comportamentos de tráfego, alguns dos campos como backendHostname, cacheStatus, sentToOriginShield e pop field podem responder com diferentes valores. A tabela abaixo explica os diferentes valores, estes campos terão para vários cenários:

| Cenários | Contagem de entradas de registo | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Regra de encaminhamento sem caching ativado | 1 | Código POP edge | Backend onde o pedido foi reencaminhado | Falso | CONFIG_NOCACHE |
| Regra de encaminhamento com caching ativado. Cache atingido na borda POP | 1 | Código POP edge | Vazio | Falso | ATROPELAMENTO |
| Regra de encaminhamento com caching ativado. Cache falha na borda POP mas cache atingido em cache pai POP | 2 | 1. Código POP edge</br>2. Código POP cache dos pais | 1. Nome de anfitrião POP da cache dos pais</br>2. Vazio | 1. Verdade</br>2. Falso | 1. MISS</br>2. PARTIAL_HIT |
| Regra de encaminhamento com caching ativado. Cache falha tanto na borda como na cache dos pais POP | 2 | 1. Código POP edge</br>2. Código POP cache dos pais | 1. Nome de anfitrião POP da cache dos pais</br>2. Backend que ajuda a preencher cache | 1. Verdade</br>2. Falso | 1. MISS</br>2. MISS |

## <a name="next-steps"></a>Próximos passos

- [Criar um perfil da porta da frente](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)
