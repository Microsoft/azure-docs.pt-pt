---
title: Práticas recomendadas para Azure Functions | Microsoft Docs
description: Aprenda as práticas recomendadas e padrões para Azure Functions.
author: ggailey777
manager: gwallace
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad2f56388b49692d799202d06ed3dc0123f272e5
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294356"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Otimizar o desempenho e a confiabilidade do Azure Functions

Este artigo fornece orientação para melhorar o desempenho e a confiabilidade de seus aplicativos de funções sem [servidor](https://azure.microsoft.com/solutions/serverless/) . 

## <a name="general-best-practices"></a>Melhores práticas gerais

Veja a seguir as práticas recomendadas de como criar e arquitetar suas soluções sem servidor usando Azure Functions.

### <a name="avoid-long-running-functions"></a>Evite funções de longa execução

Funções grandes e de execução longa podem causar problemas de tempo limite inesperados. Para saber mais sobre os tempos limite de um determinado plano de hospedagem, confira [duração do tempo limite do aplicativo de funções](functions-scale.md#timeout). 

Uma função pode se tornar grande devido a muitas dependências de Node. js. A importação de dependências também pode causar tempos de carregamento maiores que resultam em tempos limite inesperados. As dependências são carregadas de forma explícita e implícita. Um único módulo carregado pelo seu código pode carregar seus próprios módulos adicionais. 

Sempre que possível, refatore funções grandes em conjuntos de funções menores que funcionam em conjunto e retornam respostas rapidamente. Por exemplo, um webhook ou uma função de gatilho HTTP pode exigir uma resposta de confirmação dentro de um determinado limite de tempo; é comum que WebHooks exijam uma resposta imediata. Você pode passar o conteúdo do gatilho HTTP para uma fila a ser processada por uma função de gatilho de fila. Essa abordagem permite que você adie o trabalho real e retorne uma resposta imediata.


### <a name="cross-function-communication"></a>Comunicação entre funções

[Durable Functions](durable/durable-functions-overview.md) e [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) são criados para gerenciar transições de estado e comunicação entre várias funções.

Se não estiver usando Durable Functions ou aplicativos lógicos para integrar com várias funções, geralmente é uma prática recomendada usar filas de armazenamento para comunicação entre funções.  O principal motivo é que as filas de armazenamento são mais baratas e muito mais fáceis de provisionar. 

As mensagens individuais em uma fila de armazenamento têm tamanho limitado a 64 KB. Se você precisar passar mensagens maiores entre as funções, uma fila do barramento de serviço do Azure poderá ser usada para dar suporte a tamanhos de mensagem de até 256 KB na camada Standard e até 1 MB na camada Premium.

Os tópicos do barramento de serviço serão úteis se você precisar de filtragem de mensagens antes do processamento.

Os hubs de eventos são úteis para dar suporte a comunicações de alto volume.


### <a name="write-functions-to-be-stateless"></a>Funções de gravação para sem monitoração de estado 

As funções devem ser sem monitoração de estado e idempotentes, se possível. Associe todas as informações de estado necessárias aos seus dados. Por exemplo, um pedido que está sendo processado provavelmente teria um membro `state` associado. Uma função pode processar uma ordem com base nesse estado, enquanto a própria função permanece sem estado. 

Funções idempotentes são especialmente recomendadas com gatilhos de temporizador. Por exemplo, se você tiver algo que absolutamente deve ser executado uma vez por dia, grave-o para que possa ser executado a qualquer momento durante o dia com os mesmos resultados. A função pode sair quando não há nenhum trabalho para um dia específico. Além disso, se uma execução anterior não tiver sido concluída, a próxima execução deverá continuar de onde parou.


### <a name="write-defensive-functions"></a>Gravar funções defensivas

Suponha que sua função possa encontrar uma exceção a qualquer momento. Projete suas funções com a capacidade de continuar de um ponto de falha anterior durante a próxima execução. Considere um cenário que requer as seguintes ações:

1. Consulta de 10.000 linhas em um BD.
2. Crie uma mensagem de fila para cada uma dessas linhas para processar mais adiante na linha.
 
Dependendo de quão complexo for o seu sistema, você pode ter: serviços de downstream envolvidos com problemas incorretos, interrupções de rede ou limites de cota atingidos, etc. Todos eles podem afetar sua função a qualquer momento. Você precisa criar suas funções para estar preparado para ela.

Como o seu código reage se ocorrer uma falha depois de inserir 5.000 desses itens em uma fila para processamento? Rastreie itens em um conjunto que você concluiu. Caso contrário, você pode inseri-los novamente na próxima vez. Isso pode afetar seriamente o fluxo de trabalho. 

Se um item de fila já tiver sido processado, permita que sua função seja uma operação não operacional.

Aproveite as medidas defensivas já fornecidas para os componentes que você usa na plataforma Azure Functions. Por exemplo, consulte **tratamento de mensagens de fila suspeita** na documentação para [gatilhos e associações de fila de armazenamento do Azure](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Práticas recomendadas de escalabilidade

Há vários fatores que afetam a escala de instâncias do seu aplicativo de funções. Os detalhes são fornecidos na documentação para [dimensionamento de função](functions-scale.md).  Veja a seguir algumas práticas recomendadas para garantir a escalabilidade ideal de um aplicativo de funções.

### <a name="share-and-manage-connections"></a>Compartilhar e gerenciar conexões

Use novamente as conexões para recursos externos sempre que possível.  Consulte [como gerenciar conexões no Azure Functions](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misture o código de teste e de produção no mesmo aplicativo de funções

Funções em um aplicativo de funções compartilham recursos. Por exemplo, a memória é compartilhada. Se você estiver usando um aplicativo de funções em produção, não adicione funções e recursos relacionados a teste a ele. Isso pode causar sobrecarga inesperada durante a execução do código de produção.

Tenha cuidado com o que você carrega em seus aplicativos de função de produção. A média da memória é calculada em cada função no aplicativo.

Se você tiver um assembly compartilhado referenciado em várias funções .NET, coloque-o em uma pasta compartilhada comum. Referencie o assembly com uma instrução semelhante ao exemplo a seguir se estiver C# usando scripts (. CSX): 

    #r "..\Shared\MyAssembly.dll". 

Caso contrário, é fácil implantar acidentalmente várias versões de teste do mesmo binário que se comportam de forma diferente entre as funções.

Não use o log detalhado no código de produção. Ele tem um impacto negativo no desempenho.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Usar código assíncrono, mas evitar chamadas de bloqueio

A programação assíncrona é uma prática recomendada. No entanto, sempre Evite referenciar a propriedade `Result` ou chamar o método `Wait` em uma instância `Task`. Essa abordagem pode levar ao esgotamento de threads.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Receber mensagens no lote sempre que possível

Alguns gatilhos como o Hub de eventos permitem o recebimento de um lote de mensagens em uma única invocação.  O envio de mensagens em lote tem um desempenho muito melhor.  Você pode configurar o tamanho máximo do lote no arquivo `host.json`, conforme detalhado na [documentação de referência do host. JSON](functions-host-json.md)

Para C# funções, você pode alterar o tipo para uma matriz fortemente tipada.  Por exemplo, em vez de `EventData sensorEvent`, a assinatura do método poderia ser `EventData[] sensorEvent`.  Para outras linguagens, você precisará definir explicitamente a propriedade cardinalidade em seu `function.json` como `many` para habilitar o envio em lote [, conforme mostrado aqui](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurar comportamentos de host para lidar melhor com a simultaneidade

O arquivo `host.json` no aplicativo de funções permite a configuração do tempo de execução do host e os comportamentos do gatilho.  Além dos comportamentos de envio em lote, você pode gerenciar a simultaneidade para vários gatilhos.  Geralmente, ajustar os valores nessas opções pode ajudar cada escala de instância adequadamente para as demandas das funções invocadas.

As configurações no arquivo de hosts se aplicam a todas as funções dentro do aplicativo, dentro de uma *única instância* da função. Por exemplo, se você tivesse um aplicativo de funções com 2 funções HTTP e solicitações simultâneas definidas como 25, uma solicitação para o gatilho HTTP contaria para as 25 solicitações simultâneas compartilhadas.  Se esse aplicativo de funções for dimensionado para 10 instâncias, as duas funções permitirão efetivamente 250 solicitações simultâneas (10 instâncias * 25 solicitações simultâneas por instância).

**Opções de host de simultaneidade HTTP**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Outras opções de configuração de host podem ser encontradas [no documento de configuração do host](functions-host-json.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Como gerenciar conexões no Azure Functions](manage-connections.md)
* [Práticas recomendadas de serviço Azure App](../app-service/app-service-best-practices.md)
