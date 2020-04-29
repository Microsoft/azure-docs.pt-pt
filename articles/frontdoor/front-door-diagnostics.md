---
title: Monitorização de métricas e registos na Porta da Frente Azure. Microsoft Docs
description: Este artigo descreve as diferentes métricas e registos de acesso que a Porta Frontal azure suporta
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
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471732"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Monitorizar métricas e registos na Porta da Frente Azure

Ao utilizar a Porta Frontal Azure, pode monitorizar os recursos das seguintes formas:

- **Métricas.** A Porta Frontal Azure tem atualmente sete métricas para visualizar contadores de desempenho.
- **Registos**. Os registos de atividade e diagnóstico permitem que o desempenho, o acesso e outros dados sejam guardados ou consumidos a partir de um recurso para fins de monitorização.

### <a name="metrics"></a>Métricas

As métricas são uma característica para certos recursos Azure que lhe permitem ver contadores de desempenho no portal. As métricas da Porta da Frente disponíveis:

| Métrica | Nome de exibição métrica | Unidade | Dimensões | Descrição |
| --- | --- | --- | --- | --- |
| Contagem de Pedidos | Número de Pedidos | Contagem | HttpStatus</br>HttpStatusGroup</br>Região de Clientes</br>ClientCountry | O número de pedidos de clientes servidos pela Porta da Frente.  |
| Tamanho de pedido | Tamanho do pedido | Bytes | HttpStatus</br>HttpStatusGroup</br>Região de Clientes</br>ClientCountry | O número de bytes enviados como pedidos de clientes para a Porta da Frente. |
| Tamanho de resposta | Tamanho da resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>Região de Clientes</br>ClientCountry | O número de bytes enviados como respostas da Porta da Frente aos clientes. |
| TotalLatency | Latência Total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>Região de Clientes</br>ClientCountry | O tempo calculado a partir do pedido do cliente recebido pela Porta da Frente até que o cliente reconheceu o último byte de resposta da Porta da Frente. |
| Contagem de pedidos de backend | Contagem de pedidos de backend | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de pedidos enviados da Porta da Frente para backends. |
| AtrasodePedido | Latência de Pedido de Backend | Milissegundos | Back-end | O tempo calculado a partir de quando o pedido foi enviado pela Porta da Frente para o backend até que a Porta da Frente recebeu o último byte de resposta do backend. |
| BackendHealthPercentage | Percentagem de Saúde Endend | Percentagem | Back-end</br>BackendPool | A percentagem de sondas de saúde bem sucedidas da Porta da Frente para os backends. |
| Contagem de pedidos de firewall da web | Contagem de pedidos de firewall de aplicação web | Contagem | PolicyName</br>Nome de regras</br>Ação | O número de pedidos de clientes processados pela segurança da camada de aplicação da Porta da Frente. |

## <a name="activity-logs"></a><a name="activity-log"></a>Registos de atividade

Os registos de atividade fornecem informações sobre as operações feitas na Porta da Frente. Também determinam o quê, quem, e quando para quaisquer operações de escrita (colocar, postar ou apagar) tomadas na Porta da Frente.

>[!NOTE]
>Os registos de atividade não incluem operações de leitura (get). Também não incluem operações que executa utilizando o portal Azure ou a API de Gestão original.

Aceda aos registos de atividade na porta da frente ou em todos os registos dos seus recursos Azure no Monitor Azure. Para ver registos de atividades:

1. Selecione a sua instância porta da frente.
2. Selecione **registo de atividade**.

    ![Registo de atividades](./media/front-door-diagnostics/activity-log.png)

3. Escolha um âmbito de filtragem e, em seguida, selecione **Aplicar**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Registos de diagnósticos
Os registos de diagnóstico fornecem informações ricas sobre operações e erros que são importantes para a auditoria e resolução de problemas. Os registos de diagnóstico diferem dos registos de atividade.

Os registos de atividade fornecem informações sobre as operações feitas sobre os recursos do Azure. Os registos de diagnóstico fornecem informações sobre as operações que o seu recurso realizou. Para mais informações, consulte os [registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md)do Monitor Azure .

![Registos de diagnósticos](./media/front-door-diagnostics/diagnostic-log.png)

Para configurar os registos de diagnóstico para a porta da frente:

1. Selecione a porta da frente Azure.

2. Escolha **as definições de diagnóstico**.

3. Selecione **Ligar os diagnósticos**. Arquivar registos de diagnóstico juntamente com métricas para uma conta de armazenamento, transmiti-los para um centro de eventos ou enviá-los para registos do Monitor Azure.

A Porta da Frente fornece atualmente registos de diagnóstico (loteado de hora a hora). Os registos de diagnóstico fornecem pedidos de API individuais com cada entrada com o seguinte esquema:

| Propriedade  | Descrição |
| ------------- | ------------- |
| BackendHostname | Se o pedido estava a ser encaminhado para um backend, este campo representa o nome de anfitrião do backend. Este campo ficará em branco se o pedido for redirecionado ou encaminhado para uma cache regional (quando o cache estiver habilitado para a regra de encaminhamento). |
| CacheStatus | Para cenários de cache, este campo define o cache hit/miss no POP |
| ClientIp | O endereço IP do cliente que fez o pedido. Se houve um cabeçalho X-Forward-For no pedido, então o IP do cliente é escolhido a partir do mesmo. |
| ClientPort | O porto IP do cliente que fez o pedido. |
| HttpMethod | Método HTTP utilizado pelo pedido. |
| Código httpstatus | O código de estado HTTP devolvido do representante. |
| HttpStatusDetails | Consequente situação no pedido. O significado deste valor de cadeia pode ser encontrado numa tabela de referência do Estado. |
| HttpVersion | Tipo do pedido ou ligação. |
| POP | Nome curto da borda onde o pedido aterrou. |
| RequestBytes | O tamanho da mensagem de pedido http em bytes, incluindo os cabeçalhos de pedido e o corpo de pedido. |
| Requesturi | URI do pedido recebido. |
| RespostaBytes | Bytes enviados pelo servidor backend como resposta.  |
| Nome de RoutingRule | O nome da regra de encaminhamento que o pedido correspondia. |
| Protocolo de Segurança | A versão protocolar TLS/SSL utilizada pelo pedido ou nula se não houver encriptação. |
| Escudo EnviadoOrigem | Campo booleano representando se houvesse uma falha de cache no primeiro ambiente e o pedido foi enviado para a cache regional. Ignore este campo se a regra de encaminhamento for um redirecionamento ou quando não tiver o cache ativado. |
| Tempo tomado | O tempo que a ação demorou, em milissegundos. |
| TrackingReference | A cadeia de referência única que identifica um pedido servido pela Porta da Frente, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |
| UserAgent | O tipo de navegador que o cliente usou. |

**Nota:** Para várias configurações de encaminhamento e comportamentos de tráfego, alguns dos campos como backendHostname, cacheStatus, sentToOriginShield e campo POP podem responder com diferentes valores. A tabela abaixo explica os diferentes valores, estes campos terão para vários cenários:

| Cenários | Contagem de entradas de registo | POP | BackendHostname | Escudo EnviadoOrigem | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Regra de encaminhamento sem cache habilitado | 1 | Código EDGE POP | Backend onde o pedido foi reencaminhado | Falso | CONFIG_NOCACHE |
| Regra de encaminhamento com cache habilitado. Cache atingido na borda POP | 1 | Código EDGE POP | Vazio | Falso | BATER |
| Regra de encaminhamento com cache habilitado. Cache falha no EDGE POP mas cache atingido no pai cache POP | 2 | 1. Código POP de borda</br>2. Código POP cache dos pais | 1. Nome de anfitrião pop cache dos pais</br>2. Vazio | 1. Verdadeiro</br>2. Falso | 1. MISS</br>2. PARTIAL_HIT |
| Regra de encaminhamento com cache habilitado. Cache falha na borda e na cache dos pais POP | 2 | 1. Código POP de borda</br>2. Código POP cache dos pais | 1. Nome de anfitrião pop cache dos pais</br>2. Backend que ajuda a povoar cache | 1. Verdadeiro</br>2. Falso | 1. MISS</br>2. MISS |


## <a name="next-steps"></a>Passos seguintes

- [Criar um perfil porta da frente](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)
