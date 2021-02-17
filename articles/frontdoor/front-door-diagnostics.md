---
title: Métricas de monitorização e troncos na Porta frontal de Azure| Microsoft Docs
description: Este artigo descreve as diferentes métricas e registos de acesso que a Porta Frontal Azure suporta
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: d1f3e59cc88ea9cb30e7eacbd26591e08d71be61
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575249"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Métricas de monitorização e troncos na Porta frontal de Azure

Ao utilizar a Porta Frontal Azure, pode monitorizar os recursos das seguintes formas:

- **Métricas**. A Azure Front Door tem atualmente oito métricas para visualizar balcões de desempenho.
- **Troncos**. Os registos de atividade e de diagnóstico permitem que o desempenho, o acesso e outros dados sejam guardados ou consumidos a partir de um recurso para fins de monitorização.

### <a name="metrics"></a>Métricas

As métricas são uma característica de certos recursos Azure que permitem visualizar contadores de desempenho no portal. Estão disponíveis as métricas da Porta frontal:

| Metric | Nome de exibição métrica | Unidade | Dimensões | Description |
| --- | --- | --- | --- | --- |
| PedidoCount | Número de Pedidos | de palavras | HttpStatus</br>Grupo HttpStatus</br>Região do Cliente</br>ClientCountry | O número de pedidos de clientes servidos pela Porta da Frente.  |
| Solicitação | Tamanho do pedido | Bytes | HttpStatus</br>Grupo HttpStatus</br>Região do Cliente</br>ClientCountry | O número de bytes enviados como pedidos de clientes para a Porta da Frente. |
| Tamanho das respostas | Tamanho da resposta | Bytes | HttpStatus</br>Grupo HttpStatus</br>Região do Cliente</br>ClientCountry | O número de bytes enviados como respostas da Porta da Frente aos clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>Grupo HttpStatus</br>Região do Cliente</br>ClientCountry | O tempo total do pedido de cliente recebido pela Porta da Frente até à última resposta byte enviado da AFD para o cliente. |
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

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Registos de diagnóstico
Os registos de diagnóstico fornecem informações ricas sobre operações e erros que são importantes para a auditoria e resolução de problemas. Os registos de diagnóstico diferem dos registos de atividade.

Os registos de atividade fornecem informações sobre as operações es feitas sobre os recursos da Azure. Os registos de diagnóstico fornecem informações sobre as operações que o seu recurso fez. Para obter mais informações, consulte [os registos de diagnóstico do Azure Monitor](../azure-monitor/essentials/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Registos de diagnóstico":::

Para configurar registos de diagnóstico para a sua porta frontal:

1. Selecione a sua Porta frontal Azure.

2. Escolha **as definições de Diagnóstico**.

3. **Selecione Ligue os diagnósticos**. Arquive os registos de diagnóstico juntamente com as métricas para uma conta de armazenamento, transmite-os para um centro de eventos ou envia-os para os registos do Azure Monitor.

A Porta frontal fornece atualmente registos de diagnóstico (lotados por hora). Os registos de diagnóstico fornecem pedidos individuais de API com cada entrada com o seguinte esquema:

| Propriedade  | Descrição |
| ------------- | ------------- |
| BackendHostname | Se o pedido foi encaminhado para um backend, este campo representa o nome anfitrião do backend. Este campo ficará em branco se o pedido for redirecionado ou reencaminhado para uma cache regional (quando o cache for ativado para a regra de encaminhamento). |
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
| SentToOriginShield </br> (depreciado) * **Ver notas sobre depreciação na secção seguinte.**| Se for verdade, significa que o pedido foi respondido a partir da cache do escudo de origem em vez do pop de borda. O escudo de origem é uma cache dos pais usada para melhorar a relação de cache. |
| isReceivedFromClient | Se for verdade, significa que o pedido veio do cliente. Se for falso, o pedido é uma falha na borda (criança POP) e é respondido a partir do escudo de origem (pai POP). |
| TimeTaken | O tempo de primeiro byte de pedido para a Porta frontal para durar a byte de resposta para fora, em segundos. |
| TrackingReference | A cadeia de referência única que identifica um pedido servido pela Porta da Frente, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |
| UserAgent | O tipo de navegador que o cliente usou. |
| ErrorInfo | Este campo contém o tipo específico de erro para uma maior resolução de problemas. </br> Valores possíveis incluem: </br> **NoError**: Indica que não foi encontrado nenhum erro. </br> **CertificadoError**: Erro genérico do certificado SSL.</br> **CertificadoNameCheckFailed**: O nome de anfitrião no certificado SSL é inválido ou não corresponde. </br> **ClientDisconnected**: Solicitar falha por causa da ligação à rede do cliente. </br> **Não especificadoClientError**: Erro genérico do cliente. </br> **InvalidRequest**: Pedido inválido. Pode ocorrer por causa de cabeçalho, corpo e URL mal formados. </br> **DNSFailure**: Falha do DNS. </br> **DNSNameNotResolved**: O nome ou endereço do servidor não pôde ser resolvido. </br> **OriginConnectionAborted**: A ligação com a origem foi interrompida abruptamente. </br> **OriginConnectionError**: Erro de ligação de origem genérica. </br> **OriginConnectionRefused**: A ligação com a origem não foi capaz de estabelecer. </br> **OriginError**: Erro de origem genérica. </br> **OriginInvalidResponse**: A origem devolveu uma resposta inválida ou não reconhecida. </br> **OriginTimeout**: O prazo de validade do pedido de origem expirou. </br> **ResponseHeaderTooBig**: A origem voltou demasiado grande de um cabeçalho de resposta. </br> **Restrição:** O pedido foi bloqueado devido a um período de inquérito restrito. </br> **SSLHandshakeError**: Incapaz de estabelecer ligação com a origem devido à falha do aperto de mão SSL. </br> **Não especificadoError**: Ocorreu um erro que não se enquadrava em nenhum dos erros da tabela. |

### <a name="sent-to-origin-shield-deprecation"></a>Enviado para a deprecação do escudo de origem
A propriedade de log cru **isSentToOriginShield** foi depreciada e substituída por um novo campo **éReceivedFromClient**. Use o novo campo se já estiver a utilizar o campo deprecado. 

Os registos brutos incluem troncos gerados tanto a partir da borda cdN (child POP) como do escudo de origem. O escudo de origem refere-se aos nós dos pais que estão estrategicamente localizados em todo o mundo. Estes nós comunicam com servidores de origem e reduzem a carga de tráfego na origem. 

Para cada pedido que vai para o escudo de origem, existem entradas de 2 registos:

* Um para os nosdes de borda
* Um para o escudo de origem. 

Para diferenciar a saída ou respostas dos nós de borda vs. escudo de origem, pode utilizar o campo **éReceivedFromClient** para obter os dados corretos. 

Se o valor for falso, significa que o pedido é respondido do escudo de origem aos nós de borda. Esta abordagem é eficaz para comparar registos brutos com dados de faturação. As cargas não são incorridos por saídas do escudo de origem para os nós de borda. As taxas são cobradas por saídas dos nós de borda para os clientes. 

**Amostra de consulta de Kusto para excluir registos gerados no escudo de origem em Log Analytics.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> Para várias configurações de encaminhamento e comportamentos de tráfego, alguns dos campos como backendHostname, cacheStatus, isReceivedFromClient e pop field podem responder com diferentes valores. A tabela abaixo explica os diferentes valores que estes campos terão para vários cenários:

| Cenários | Contagem de entradas de registo | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Regra de encaminhamento sem caching ativado | 1 | Código POP edge | Backend onde o pedido foi reencaminhado | Verdadeiro | CONFIG_NOCACHE |
| Regra de encaminhamento com caching ativado. Cache atingido na borda POP | 1 | Código POP edge | Vazio | Verdadeiro | ATROPELAMENTO |
| Regra de encaminhamento com caching ativado. Cache falha no pop de borda mas cache atingido no pai cache POP | 2 | 1. Código POP edge</br>2. Código POP cache dos pais | 1. Nome de anfitrião POP da cache dos pais</br>2. Vazio | 1. Verdade</br>2. Falso | 1. MISS</br>2. BATER |
| Regra de encaminhamento com caching ativado. Caches falham na borda POP mas cache parcial atingido em cache dos pais POP | 2 | 1. Código POP edge</br>2. Código POP cache dos pais | 1. Nome de anfitrião POP da cache dos pais</br>2. Backend que ajuda a preencher cache | 1. Verdade</br>2. Falso | 1. MISS</br>2. PARTIAL_HIT |
| Regra de encaminhamento com caching ativado. Cache PARTIAL_HIT no pop de borda mas cache bateu na cache dos pais POP | 2 | 1. Código POP edge</br>2. Código POP cache dos pais | 1. Código POP edge</br>2. Código POP cache dos pais | 1. Verdade</br>2. Falso | 1. PARTIAL_HIT</br>2. BATER |
| Regra de encaminhamento com caching ativado. Cache falha tanto na borda como na cache dos pais POPP | 2 | 1. Código POP edge</br>2. Código POP cache dos pais | 1. Código POP edge</br>2. Código POP cache dos pais | 1. Verdade</br>2. Falso | 1. MISS</br>2. MISS |

> [!NOTE]
> Para cenários de cache, o valor para o Estado de Cache será partial_hit quando alguns dos bytes para um pedido são servidos a partir da borda da porta frontal ou cache do escudo de origem enquanto alguns dos bytes são servidos da origem para objetos grandes.

A Porta da Frente usa uma técnica chamada "pancada de objetos". Quando um ficheiro grande é solicitado, a Porta frontal recupera pedaços menores do ficheiro a partir da origem. Depois de o servidor POP da porta frontal receber uma gama completa ou byte do ficheiro solicitado, o servidor front door edge solicita o ficheiro a partir da origem em pedaços de 8 MB.

Depois de o pedaço chegar à borda da porta da frente, é em cache e imediatamente servido ao utilizador. A Porta da Frente, em seguida, prefetches o próximo pedaço em paralelo. Esta prefetch garante que o conteúdo permanece um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado), todos os intervalos byte estão disponíveis (se solicitado), ou o cliente fecha a ligação. Para obter mais informações sobre o pedido de byte-range, consulte RFC 7233. A Porta da Frente cache qualquer pedaços como eles são recebidos. Todo o ficheiro não precisa de ser colocado na cache da porta da frente. Os pedidos subsequentes para o ficheiro ou intervalos de byte são servidos a partir da cache da Porta frontal. Se nem todos os pedaços estiverem em cache na porta da frente, a prefetch é utilizada para solicitar pedaços da origem. Esta otimização baseia-se na capacidade do servidor de origem de suportar pedidos de alcance byte. Se o servidor de origem não suportar pedidos de alcance byte, esta otimização não é eficaz.

## <a name="next-steps"></a>Passos seguintes

- [Criar um perfil da porta da frente](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)
