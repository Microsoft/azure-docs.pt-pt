---
title: Boas Práticas para Funções Azure
description: Aprenda as melhores práticas e padrões para funções azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd52c4cc5cd9d4c01ddb355bdfb28d0952e65759
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210264"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Otimizar o desempenho e a fiabilidade das Funções Azure

Este artigo fornece orientações para melhorar o desempenho e a fiabilidade das suas aplicações de função [sem servidores.](https://azure.microsoft.com/solutions/serverless/)  

## <a name="general-best-practices"></a>Melhores práticas gerais

Seguem-se as melhores práticas na forma como constrói e arquiteta as suas soluções sem servidores utilizando funções Azure.

### <a name="avoid-long-running-functions"></a>Evite funções de longo prazo

Funções grandes e de longa duração podem causar problemas inesperados de tempo. Para saber mais sobre os intervalos para um determinado plano de hospedagem, consulte a duração do [tempo de tempo da aplicação de funções](functions-scale.md#timeout). 

Uma função pode tornar-se grande devido a muitas dependências de Node.js. As dependências de importação também podem causar um aumento dos tempos de carga que resultam em tempos inesperados. As dependências são carregadas explicitamente e implicitamente. Um único módulo carregado pelo seu código pode carregar os seus próprios módulos adicionais. 

Sempre que possível, refactor arrefactor grandes funções em conjuntos de funções menores que funcionam em conjunto e devolvem respostas rapidamente. Por exemplo, uma função de gatilho webhook ou HTTP pode exigir uma resposta de reconhecimento dentro de um determinado prazo; é comum que os webhooks exijam uma resposta imediata. Pode passar a carga de gatilho HTTP para uma fila a ser processada por uma função de gatilho de fila. Esta abordagem permite adiar o trabalho real e devolver uma resposta imediata.


### <a name="cross-function-communication"></a>Comunicação de funções cruzadas

[Funções Duráveis](durable/durable-functions-overview.md) e [Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) são construídas para gerir transições estatais e comunicação entre múltiplas funções.

Se não utilizar funções duráveis ou aplicações lógicas para se integrar com múltiplas funções, o melhor é utilizar filas de armazenamento para comunicação transfunção. A razão principal é que as filas de armazenamento são mais baratas e muito mais fáceis de fornecer do que outras opções de armazenamento. 

As mensagens individuais numa fila de armazenamento são limitadas em tamanho a 64 KB. Se precisar de passar mensagens maiores entre funções, uma fila de ônibus de serviço Azure poderia ser usada para suportar tamanhos de mensagens até 256 KB no nível Standard e até 1 MB no nível Premium.

Os tópicos do ônibus de serviço são úteis se necessitar de filtragem de mensagens antes de ser processado.

Os centros de eventos são úteis para apoiar comunicações de alto volume.


### <a name="write-functions-to-be-stateless"></a>Escrever funções para ser apátrida 

As funções devem ser apátridas e idempotentes, se possível. Associe todas as informações de estado necessárias com os seus dados. Por exemplo, uma ordem que está a ser processada teria provavelmente um membro `state` associado. Uma função poderia processar uma ordem baseada nesse estado enquanto a função em si permanece apátrida. 

Funções idempotentes são especialmente recomendadas com gatilhos temporizadores. Por exemplo, se tiver algo que deve funcionar uma vez por dia, escreva-o para que possa funcionar a qualquer momento durante o dia com os mesmos resultados. A função pode sair quando não há trabalho para um dia em particular. Além disso, se uma corrida anterior não tiver terminado, a próxima corrida deve retomar onde ficou parada.


### <a name="write-defensive-functions"></a>Escrever funções defensivas

Assuma que a sua função pode encontrar uma exceção a qualquer momento. Desenhe as suas funções com a capacidade de continuar a partir de um ponto de falha anterior durante a próxima execução. Considere um cenário que exija as seguintes ações:

1. Consulta por 10.000 filas numa base de dados.
2. Crie uma mensagem de fila para cada uma dessas filas para processar mais abaixo a linha.
 
Dependendo do quão complexo é o seu sistema, pode ter: serviços envolvidos a jusante comportam-se mal, interrupções em rede ou limites de quota atingidos, etc. Tudo isto pode afetar a sua função a qualquer momento. Precisa desenhar as suas funções para estar preparado para isso.

Como reage o seu código se ocorre uma falha depois de inserir 5.000 desses itens numa fila para processamento? Rastreie os itens num conjunto que completou. Caso contrário, poderá inseri-los novamente da próxima vez. Esta dupla inserção pode ter um impacto sério no fluxo de trabalho, por isso, torne as [suas funções idempotentes](functions-idempotent.md). 

Se um item de fila já foi processado, deixe a sua função não op.

Aproveite as medidas defensivas já previstas para os componentes utilizados na plataforma Funções Azure. Por exemplo, consulte **o manuseamento** de mensagens de fila de veneno na documentação para [os gatilhos e encadernações](functions-bindings-storage-queue.md#trigger---poison-messages)da fila de armazenamento do Azure . 

## <a name="scalability-best-practices"></a>As melhores práticas de escalabilidade

Há uma série de fatores que impactam a forma como os casos da sua função app escala. Os detalhes são fornecidos na documentação para [a escalade de funções.](functions-scale.md)  Seguem-se algumas boas práticas para garantir a escalabilidade ideal de uma aplicação de função.

### <a name="share-and-manage-connections"></a>Partilhar e gerir ligações

Reutilizar sempre que possível as ligações a recursos externos. Veja [como gerir as ligações em Funções Azure](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Evite partilhar contas de armazenamento

Quando cria uma aplicação de função, deve associá-la a uma conta de armazenamento. A ligação da conta de armazenamento é mantida na definição da [aplicação AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misture o teste e o código de produção na mesma aplicação de funções

As funções dentro de uma aplicação de função partilham recursos. Por exemplo, a memória é partilhada. Se estiver a usar uma aplicação de função em produção, não adicione funções e recursos relacionados com o teste. Pode causar sobrecargas inesperadas durante a execução do código de produção.

Tenha cuidado com o que carrega nas suas aplicações de função de produção. A memória é média em cada função da aplicação.

Se tiver um conjunto partilhado referenciado em múltiplas funções .NET, coloque-o numa pasta partilhada comum. Caso contrário, poderia acidentalmente implementar várias versões do mesmo binário que se comportam de forma diferente entre funções.

Não utilize a verbosa login no código de produção, o que tem um impacto negativo no desempenho.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Use código assync mas evite bloquear chamadas

A programação assíncrona é uma melhor prática recomendada, especialmente quando se estão envolvidas operações de bloqueio de E/S.

Em C#, evite sempre fazer referência ao `Result` imóvel ou chamar `Wait` método numa `Task` instância. Esta abordagem pode levar à exaustão dos fios.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Utilize vários processos de trabalhador

Por predefinição, qualquer exemplo de hospedeiro para Funções utiliza um único processo de trabalhador. Para melhorar o desempenho, especialmente com tempos de execução de roscar como python, use o [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) para aumentar o número de processos de trabalhador por hospedeiro (até 10). As Funções Azure tentam então distribuir uniformemente invocações de funções simultâneas por estes trabalhadores. 

O FUNCTIONS_WORKER_PROCESS_COUNT aplica-se a cada anfitrião que as Funções criam ao escalonar a sua aplicação para satisfazer a procura. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Receba mensagens em lote sempre que possível

Alguns gatilhos como o Event Hub permitem receber um lote de mensagens numa única invocação.  As mensagens de lotação têm um desempenho muito melhor.  Pode configurar o tamanho máximo do lote no ficheiro `host.json` conforme detalhado na documentação de [referência host.json](functions-host-json.md)

Para C# funções, pode alterar o tipo para uma matriz fortemente dactilografada.  Por exemplo, em vez de `EventData sensorEvent` a assinatura do método poderia ser `EventData[] sensorEvent`.  Para outras línguas, você precisará definir explicitamente a propriedade cardinalidade na sua `function.json` para `many` de modo a permitir o lote [como mostrado aqui](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configure comportamentos de hospedeiro para lidar melhor com a conmoeda

O ficheiro `host.json` na aplicação de função permite configurar o tempo de execução do hospedeiro e desencadear comportamentos.  Além de comportamentos de lotação, você pode gerir a concurrency para uma série de gatilhos. Muitas vezes, ajustar os valores nestas opções pode ajudar cada instância a escalar adequadamente para as exigências das funções invocadas.

As definições no ficheiro host.json aplicam-se em todas as funções dentro da app, numa *única instância* da função. Por exemplo, se tivesse uma aplicação de função com duas funções HTTP e [pedidos de`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) definidos para 25, um pedido para qualquer um dos gatilhos HTTP contaria para os 25 pedidos simultâneos partilhados.  Quando essa aplicação de funções é dimensionada para 10 instâncias, as duas funções permitem efetivamente 250 pedidos simultâneos (10 instâncias * 25 pedidos simultâneos por exemplo). 

Outras opções de configuração do anfitrião encontram-se no artigo de [configuração host.json](functions-host-json.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Como gerir ligações em Funções Azure](manage-connections.md)
* [Boas práticas do Serviço de Aplicações Azure](../app-service/app-service-best-practices.md)
