---
title: Melhores práticas para as funções do Azure | Documentos da Microsoft
description: Aprenda as práticas recomendadas e padrões para as funções do Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: as funções do Azure, padrões, melhor prática, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30b187676f0c1fb03b7124d93b3991b0e32d61ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848954"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Otimizar o desempenho e fiabilidade das funções do Azure

Este artigo fornece orientações para melhorar o desempenho e confiabilidade de seus [sem servidor](https://azure.microsoft.com/solutions/serverless/) aplicações de funções. 

## <a name="general-best-practices"></a>Melhores práticas gerais

Seguem-se as melhores práticas para a criação e a arquitetura de suas soluções sem servidor com funções do Azure.

### <a name="avoid-long-running-functions"></a>Evite as funções de execução longa

Funções grandes e de longa execução podem causar problemas de tempo limite inesperado. Uma função pode se tornar grande devido à quantidade de dependências de node. js. Importar dependências também pode causar tempos de aumento de carga que resultam em tempos limite inesperado. As dependências são ambos carregadas explicitamente e implicitamente. Um mesmo módulo carregado pelo seu código pode carregar os seus próprios módulos adicionais.  

Sempre que possível, funções grande do refactor em função menor define que funcionam em conjunto e devolver respostas rapidamente. Por exemplo, um webhook ou uma função de Acionador HTTP pode exigir uma resposta de confirmação dentro de um determinado limite de tempo; é comum para webhooks exigir uma resposta imediata. Pode transmitir o payload de Acionador HTTP numa fila para ser processado por uma função de Acionador de fila. Esta abordagem permite-lhe diferir o trabalho real e devolver uma resposta imediata.


### <a name="cross-function-communication"></a>Cruzar a comunicação de função

[Funções duráveis](durable/durable-functions-concepts.md) e [do Azure Logic Apps](../logic-apps/logic-apps-overview.md) baseiam-se para gerir as transições de estado e a comunicação entre várias funções.

Se não utilizar funções duráveis ou o Logic Apps para integrar com várias funções, geralmente é uma prática recomendada usar filas de armazenamento para cruzada comunicação de função.  O principal motivo é que as filas de armazenamento são mais baratos e muito mais fácil de aprovisionar. 

Mensagens individuais numa fila de armazenamento são limitadas em tamanho a 64 KB. Se precisar de passar as mensagens maiores entre as funções, uma fila pode ser utilizada para a mensagem de suporte do Azure Service Bus tamanhos até 256 KB no escalão Standard e até 1 MB no escalão Premium.

Tópicos do Service Bus são úteis se precisar de filtragem antes do processamento de mensagens.

Os hubs de eventos são úteis para suportarem comunicações de elevado volume.


### <a name="write-functions-to-be-stateless"></a>Escrever funções tenham monitoração de estado 

As funções devem ser sem estado e idempotentes se possível. Associe quaisquer informações de estado necessárias com os seus dados. Por exemplo, uma ordem a ser processado provavelmente teria associado ao `state` membro. Uma função pode processar um pedido com base nesse Estado, enquanto a própria função permanece sem monitoração de estado. 

Funções de Idempotentes especialmente recomenda-se com os acionadores de temporizadores. Por exemplo, se tiver algo que com certeza deve ser executado uma vez por dia, escrevê-lo a para que possa executar a qualquer momento durante o dia com os mesmos resultados. A função pode sair quando não existe nenhum trabalho para um determinado dia. Também se anterior execução falhou para concluir, a próxima execução deve começar onde terminou.


### <a name="write-defensive-functions"></a>Escrever funções de defesa

Assumir que a função foi possível encontrar uma exceção em qualquer altura. Crie as suas funções com a capacidade de continuar a partir de um ponto de falha anterior durante a execução seguinte. Considere um cenário que requer as seguintes ações:

1. Consulta para 10 000 linhas numa db.
2. Criar uma mensagem de fila de cada um da linha para baixo de linhas para processar mais.
 
Dependendo da complexidade é de seu sistema, talvez tenha: serviços downstream envolvidos se comportando mal, indisponibilidade de rede ou quota limita atingiu, etc. Tudo isso pode afetar a sua função em qualquer altura. Terá de criar as suas funções estar preparado para ele.

Como seu código reagir se ocorrer uma falha depois de inserir 5.000 um desses itens numa fila para processamento? Controle de itens de um conjunto que tiver concluído. Caso contrário, pode inseri-las novamente. Isto pode ter um impacto grave no seu fluxo de trabalho. 

Se um item da fila já foi processado, permitem que sua função para ser não operacional.

Tire partido das medidas defensiva já fornecida para os componentes que utilizar na plataforma do funções do Azure. Por exemplo, veja **manipulação de mensagens não processáveis da fila** na documentação de [fila de armazenamento do Azure acionadores e enlaces](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Melhores práticas de escalabilidade

Existem vários fatores que afetam como dimensionar instâncias da sua aplicação de função. Os detalhes são fornecidos na documentação de [função dimensionamento](functions-scale.md).  Seguem-se algumas melhores práticas para garantir escalabilidade ideal de uma aplicação de funções.

### <a name="share-and-manage-connections"></a>Partilhar e gerir ligações

Voltar a utilizar ligações para recursos externos, sempre que possível.  Ver [como gerir ligações nas funções do Azure](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misturar código de teste e produção na mesma function app

As funções dentro de uma aplicação de funções partilham recursos. Por exemplo, a memória é compartilhada. Se estiver a utilizar uma aplicação de funções na produção, não adicione funções relacionadas ao teste e recursos ao mesmo. Isso pode causar sobrecarga inesperada durante a execução de código de produção.

Cuidado com a carga nas suas aplicações de função de produção. Memória é calculada através de cada função na aplicação.

Se tiver um assembly compartilhado referenciado em várias funções de .NET, coloque-o numa pasta compartilhada comum. Referência ao assembly com uma declaração semelhante ao seguinte exemplo, se utilizar o c# Scripts (. csx): 

    #r "..\Shared\MyAssembly.dll". 

Caso contrário, é fácil acidentalmente implementar várias versões de teste do mesmo binário que se comportam de forma diferente entre as funções.

Não utilize o registo verboso no código de produção. Ele tem um impacto negativo no desempenho.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Utilize o código assíncrono mas evitar o bloqueio de chamadas

Programação assíncrona é uma prática recomendada. No entanto, sempre evitar fazer referência a `Result` propriedade ou chamar `Wait` método num `Task` instância. Essa abordagem pode levar ao esgotamento de thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Receber mensagens no batch sempre que possível

Alguns acionadores, como o Hub de eventos permitem a receção de um lote de mensagens numa invocação de única.  Mensagens de criação de batches tem um desempenho muito melhor.  Pode configurar o tamanho de lote máximo na `host.json` de ficheiros conforme detalhado no [documentação de referência do Host. JSON](functions-host-json.md)

Para funções c# pode alterar o tipo a uma matriz de rigidez de tipos.  Por exemplo, em vez de `EventData sensorEvent` poderia ser a assinatura do método `EventData[] sensorEvent`.  Para outros idiomas terá de definir explicitamente a propriedade de cardinalidade no seu `function.json` para `many` para ativar a criação de batches [conforme mostrado a seguir](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurar comportamentos de anfitrião para lidar melhor com simultaneidade

O `host.json` ficheiro na function app permite a configuração dos comportamentos de tempo de execução e o acionador do anfitrião.  Para além de comportamentos de criação de batches, pode gerir a simultaneidade para um número de acionadores.  Muitas vezes, ajustar os valores nestas opções pode ajudar a cada escala de instância adequadamente para as necessidades das funções invocadas.

Definições no ficheiro de anfitriões são aplicadas em todas as funções na aplicação, dentro uma *única instância* da função. Por exemplo, se tivesse uma aplicação de funções com 2 funções HTTP e definida de pedidos simultâneos para 25, um pedido para o acionador HTTP seria contam para 25 pedidos simultâneos partilhados.  Se essa aplicação de função aumentado para 10 instâncias, as 2 funções efetivamente permitiria a 250 pedidos simultâneos (10 instâncias * 25 pedidos simultâneos por instância).

**Opções de anfitrião de simultaneidade HTTP**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Outras opções de configuração de anfitrião podem ser encontradas [no documento de configuração de host](functions-host-json.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Como gerir ligações nas funções do Azure](manage-connections.md)
* [Práticas recomendadas do serviço de aplicações do Azure](../app-service/app-service-best-practices.md)
