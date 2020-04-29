---
title: Conceitos empresariais - LUIS
titleSuffix: Azure Cognitive Services
description: Compreenda os conceitos de design para grandes apps LUIS ou múltiplas aplicações, incluindo LUIS e QnA Maker juntos.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221064"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Estratégias empresariais para uma app LUIS
Reveja estas estratégias de design para a sua aplicação empresarial.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando espera pedidos luis para além da quota

A LUIS tem uma quota mensal, bem como uma quota por segundo, com base no nível de preços do recurso Azure. 

Se a sua taxa de pedido de aplicação LUIS exceder a taxa de [quota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)permitida, pode:

* Espalhe a carga para mais aplicações LUIS com a [mesma definição](#use-multiple-apps-with-same-app-definition)de app. Isto inclui, opcionalmente, executar O LUIS a partir de um [recipiente.](luis-container-howto.md) 
* Crie e [atribua várias chaves](#assign-multiple-luis-keys-to-same-app) à aplicação. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Use várias aplicações com a mesma definição de app
Exportar a aplicação ORIGINAL LUIS e, em seguida, importar a app de volta para aplicações separadas. Cada aplicação tem o seu próprio ID de aplicação. Quando publicar, em vez de usar a mesma chave em todas as aplicações, crie uma chave separada para cada aplicação. Equilibre a carga em todas as aplicações para que nenhuma aplicação fique sobrecarregada. Adicione [insights de aplicação](luis-tutorial-bot-csharp-appinsights.md) para monitorizar a utilização. 

De forma a obter a mesma intenção de topo entre todas as aplicações, certifique-se de que a previsão de intenção entre a primeira e a segunda intenções é suficientemente ampla para que o LUIS não esteja confuso, dando resultados diferentes entre apps para pequenas variações nas expressões. 

Ao treinar estas aplicações para irmãos, certifique-se de [treinar com todos os dados.](luis-how-to-train.md#train-with-all-data)

Designe uma única aplicação como mestre. Quaisquer declarações sugeridas para revisão devem ser adicionadas à app principal e depois transferidas para todas as outras aplicações. Ou se trata de uma exportação completa da app, ou de carregar as palavras rotuladas do mestre para as crianças. O carregamento pode ser feito a partir do site da [LUIS](luis-reference-regions.md) ou da autoria da API para uma [única expressão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) ou para um [lote](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Agendar uma revisão periódica, como de duas em duas semanas, de [declarações de pontofinal](luis-how-to-review-endpoint-utterances.md) para aprendizagem ativa, em seguida, retreinar e republicar. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Atribuir várias teclas LUIS à mesma aplicação
Se a sua aplicação LUIS receber mais acessos finais do que a quota da sua chave permite, crie e atribua mais chaves à aplicação LUIS. Crie um gestor de tráfego ou um equilibrador de carga para gerir as consultas de ponto final através das teclas de ponto final. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando a sua aplicação monolítica devolve a intenção errada
Se a sua aplicação pretende prever uma grande variedade de declarações de utilizadores, considere implementar o modelo de [despacho](#dispatch-tool-and-model). A rutura de uma aplicação monolítica permite ao LUIS focar a deteção entre intenções com sucesso, em vez de se confundir entre as intenções através da app dos pais e das aplicações infantis. 

Agendar uma revisão periódica [das declarações de pontos finais](luis-how-to-review-endpoint-utterances.md) para a aprendizagem ativa, como de duas em duas semanas, em seguida, retreinar e republicar. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando precisa ter mais de 500 intenções
Assuma que está a desenvolver um assistente de escritório que tem mais de 500 intenções. Se 200 intenções dizem respeito a reuniões de agendamento, 200 são sobre lembretes, 200 são sobre obter informações sobre colegas, e 200 são para enviar e-mail, intenções de grupo para que cada grupo esteja numa única app, em seguida, criar uma app de alto nível contendo cada intenção. Utilize o modelo de [despacho](#dispatch-tool-and-model) para construir a aplicação de alto nível. Em seguida, mude o seu bot para utilizar a chamada em cascata, como mostrado no [tutorial do modelo de despacho](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando precisa combinar várias aplicações de fabricante luis e QnA
Se tiver várias aplicações de fabricante luis e QnA que precisam responder a um bot, use o modelo de [despacho](#dispatch-tool-and-model) para construir a aplicação de alto nível.  Em seguida, mude o seu bot para utilizar a chamada em cascata, como mostrado no [tutorial do modelo de despacho](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Ferramenta e modelo de despacho
Utilize a ferramenta de linha de comando [Dispatch,][dispatch-tool] encontrada em [ferramentas BotBuilder](https://github.com/Microsoft/botbuilder-tools) para combinar várias aplicações LUIS e/ou QnA Maker numa aplicação DE MA-mãe. Esta abordagem permite-lhe ter um domínio de pais, incluindo todos os assuntos e diferentes domínios de assuntos infantis em aplicações separadas. 

![Imagem conceptual da arquitetura de despacho](./media/luis-concept-enterprise/dispatch-architecture.png)

O domínio dos pais é notado `Dispatch` em LUIS com uma versão nomeada na lista de aplicações. 

O chat bot recebe a expressão e depois envia para a app LUIS para previsão. A principal intenção prevista da app dos pais determina qual a app infantil LUIS que é chamada a seguir. O chat bot envia a expressão para a aplicação infantil para uma previsão mais específica.

Compreenda como esta hierarquia de chamadas é feita a partir do Bot Builder v4 [despachante-application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limites de intenção no modelo de despacho
Um pedido de despacho tem 500 fontes de expedição, o equivalente a 500 intenções, como o máximo. 

## <a name="more-information"></a>Mais informações

* [Quadro bot SDK](https://github.com/Microsoft/botframework)
* [Tutorial de modelo de despacho](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Despacho CLI](https://github.com/Microsoft/botbuilder-tools)
* Despacho modelo bot amostra - [.NET,](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [testar um lote](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
