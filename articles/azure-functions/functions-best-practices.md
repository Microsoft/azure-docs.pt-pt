---
title: Boas Práticas para Funções Azure
description: Aprenda as melhores práticas e padrões para funções Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f05afb3c23fc720bb0100a751a6943d7bb03453f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954788"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Otimizar o desempenho e fiabilidade das Funções do Azure

Este artigo fornece orientações para melhorar o desempenho e a fiabilidade das suas aplicações de função [sem servidor.](https://azure.microsoft.com/solutions/serverless/)  

## <a name="general-best-practices"></a>Melhores práticas gerais

Seguem-se as melhores práticas na forma como constrói e arquiteta as suas soluções sem servidor utilizando funções Azure.

### <a name="avoid-long-running-functions"></a>Evite funções de longa duração

Funções grandes e de longa duração podem causar problemas inesperados de tempo limite. Para saber mais sobre os intervalos para um determinado plano de hospedagem, consulte [a duração do tempo limite da aplicação da função](functions-scale.md#timeout). 

Uma função pode tornar-se grande devido a muitas dependências Node.js. As dependências importadoras também podem causar um aumento dos tempos de carga que resultam em intervalos inesperados. As dependências são carregadas explicitamente e implicitamente. Um único módulo carregado pelo seu código pode carregar os seus próprios módulos adicionais. 

Sempre que possível, refactor de grandes funções em conjuntos de funções mais pequenos que trabalham em conjunto e devolvem respostas rapidamente. Por exemplo, uma função webhook ou http trigger pode exigir uma resposta de reconhecimento dentro de um determinado prazo; é comum que os webhooks exijam uma resposta imediata. Pode passar a carga útil do gatilho HTTP para uma fila a ser processada por uma função de gatilho de fila. Esta abordagem permite adiar o trabalho real e devolver uma resposta imediata.


### <a name="cross-function-communication"></a>Comunicação de funções cruzadas

[As Funções Duradouras](durable/durable-functions-overview.md) e [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) são construídas para gerir as transições do estado e a comunicação entre múltiplas funções.

Se não utilizar funções duradouras ou aplicações lógicas para se integrar com múltiplas funções, o melhor é usar filas de armazenamento para comunicação de funções cruzadas. A razão principal é que as filas de armazenamento são mais baratas e muito mais fáceis de providenciar do que outras opções de armazenamento. 

As mensagens individuais numa fila de armazenamento são limitadas em tamanho a 64 KB. Se precisar de passar mensagens maiores entre funções, poderá ser utilizada uma fila de autocarros Azure Service para suportar tamanhos de mensagens até 256 KB no nível Standard e até 1 MB no nível Premium.

Os tópicos do Service Bus são úteis se necessitar de filtragem de mensagens antes de ser processado.

Os centros de eventos são úteis para suportar comunicações de alto volume.


### <a name="write-functions-to-be-stateless"></a>Escrever funções para ser apátrida 

As funções devem ser apátridas e idempotentes, se possível. Associe as informações de estado necessárias aos seus dados. Por exemplo, uma ordem que está a ser processada teria provavelmente um `state` membro associado. Uma função poderia processar uma ordem com base nesse estado enquanto a função em si permanece apátrida. 

As funções idempotentes são especialmente recomendadas com gatilhos temporizadores. Por exemplo, se tiver algo que deve ser executado uma vez por dia, escreva-o para que possa correr a qualquer momento durante o dia com os mesmos resultados. A função pode sair quando não há trabalho para um determinado dia. Além disso, se uma corrida anterior não tiver sido concluída, a próxima corrida deve retomar onde ficou.


### <a name="write-defensive-functions"></a>Escrever funções defensivas

Assuma que a sua função pode encontrar uma exceção a qualquer momento. Desenhe as suas funções com a capacidade de continuar a partir de um ponto de falha anterior durante a próxima execução. Considere um cenário que exija as seguintes ações:

1. Consulta por 10.000 filas numa base de dados.
2. Crie uma mensagem de fila para cada uma dessas linhas processar mais adiante.
 
Dependendo da complexidade do seu sistema, pode ter: serviços a jusante que se comportam mal, falhas de ligação em rede ou limites de quota atingidos, etc. Tudo isto pode afetar a sua função a qualquer momento. Tens de desenhar as tuas funções para estares preparado para isso.

Como reage o seu código se ocorrer uma falha após inserir 5.000 desses itens numa fila para processamento? Rastreia os itens num conjunto que completou. Caso contrário, poderá inseri-los de novo da próxima vez. Esta dupla inserção pode ter um impacto sério no seu fluxo de trabalho, por isso [torne as suas funções idempotentes.](functions-idempotent.md) 

Se um item de fila já foi processado, permita que a sua função seja um não-op.

Aproveite as medidas defensivas já previstas para os componentes que utiliza na plataforma Azure Functions. Por exemplo, consulte **o manuseamento de mensagens de fila venenosas** na documentação para [os gatilhos e encadernações da fila de armazenamento Azure](functions-bindings-storage-queue-trigger.md#poison-messages). 

## <a name="scalability-best-practices"></a>Melhores práticas de escalabilidade

Há uma série de fatores que impactam como os casos da sua escala de aplicações de função. Os detalhes são fornecidos na documentação para [dimensionamento de funções.](functions-scale.md)  Seguem-se algumas das melhores práticas para garantir a ótima escalabilidade de uma aplicação de função.

### <a name="share-and-manage-connections"></a>Partilhar e gerir ligações

Reutilizar as ligações a recursos externos sempre que possível. Ver [como gerir as ligações em Funções Azure](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Evite partilhar contas de armazenamento

Quando criar uma aplicação de função, deve associá-la a uma conta de armazenamento. A ligação à conta de armazenamento é mantida na definição da [aplicação AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misture o código de teste e de produção na mesma aplicação de função

As funções dentro de uma aplicação de função partilham recursos. Por exemplo, a memória é partilhada. Se estiver a utilizar uma aplicação de função em produção, não lhe adicione funções e recursos relacionados com o teste. Pode causar sobrecargas inesperadas durante a execução do código de produção.

Tenha cuidado com o que carrega nas suas aplicações de função de produção. A memória é média em cada função na aplicação.

Se tiver um conjunto partilhado referenciado em várias funções .NET, coloque-o numa pasta partilhada comum. Caso contrário, poderá acidentalmente implementar várias versões do mesmo binário que se comportam de forma diferente entre funções.

Não utilize a registo verboso no código de produção, o que tem um impacto negativo no desempenho.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Use código async, mas evite bloquear chamadas

A programação assíncronea é uma boa prática recomendada, especialmente quando se estão envolvidos operações de I/S.

Em C#, evite sempre fazer referência à `Result` propriedade ou ligar o método em uma `Wait` `Task` instância. Esta abordagem pode levar à exaustão do fio.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Use vários processos de trabalhador

Por predefinição, qualquer instância de anfitrião para Funções utiliza um único processo de trabalhador. Para melhorar o desempenho, especialmente com tempos de execução de linha única como python, use o [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) para aumentar o número de processos de trabalhador por hospedeiro (até 10). As Funções Azure tentam então distribuir uniformemente invocações de funções simultâneas por estes trabalhadores. 

O FUNCTIONS_WORKER_PROCESS_COUNT aplica-se a cada anfitrião que as Funções criam ao escalonar a sua aplicação para satisfazer a procura. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Receba mensagens em lote sempre que possível

Alguns gatilhos como o Event Hub permitem receber um lote de mensagens numa única invocação.  As mensagens de lote têm um desempenho muito melhor.  Pode configurar o tamanho máximo do lote no `host.json` ficheiro conforme detalhado nohost.jsna [ documentação de referência](functions-host-json.md)

Para as funções C#, pode alterar o tipo para uma matriz fortemente dotada.  Por exemplo, em vez `EventData sensorEvent` da assinatura do método poderia ser `EventData[] sensorEvent` .  Para outras línguas, você precisará explicitamente definir a propriedade cardinalitásia no seu `function.json` `many` para, de modo a permitir o lote [como mostrado aqui](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configure comportamentos hospedeiros para lidar melhor com a conúnva

O `host.json` ficheiro na aplicação de função permite a configuração do tempo de execução do anfitrião e comportamentos de desencadeamento.  Além dos comportamentos de loteamento, pode gerir a concordância para uma série de gatilhos. Muitas vezes, a adaptação dos valores nestas opções pode ajudar cada instância a escalar adequadamente para as exigências das funções invocadas.

As definições no host.jsno ficheiro aplicam-se em todas as funções dentro da aplicação, num *único caso* da função. Por exemplo, se tivesse uma aplicação de função com duas funções HTTP e [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) pedidos definidos para 25, um pedido para um ou outro detonador HTTP contaria para os 25 pedidos simultâneos partilhados.  Quando esta aplicação de função é dimensionada para 10 instâncias, as dez funções permitem efetivamente 250 pedidos simultâneos (10 instâncias * 25 pedidos simultâneos por exemplo). 

Outras opções de configuração do anfitrião encontram-se no [host.jsno artigo de configuração](functions-host-json.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os seguintes recursos:

* [Como gerir ligações em Funções Azure](manage-connections.md)
* [Melhores práticas do Azure App Service](../app-service/app-service-best-practices.md)
