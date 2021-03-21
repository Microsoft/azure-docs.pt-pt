---
title: Conceitos empresariais - LUIS
titleSuffix: Azure Cognitive Services
description: Compreenda conceitos de design para grandes aplicações LUIS ou várias aplicações, incluindo LUIS e QnA Maker juntos.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 3f52dc502233cbab42ae5decff70b77c41c38e0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609690"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Estratégias empresariais para uma aplicação LUIS
Reveja estas estratégias de design para a sua aplicação empresarial.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando se espera pedidos de LUIS para além da quota

A LUIS tem uma quota mensal, bem como uma quota por segundo, com base no nível de preços do recurso Azure. 

Se a sua taxa de pedido de aplicação LUIS exceder a taxa de [quota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)permitida, pode:

* Espalhe a carga para mais aplicativos LUIS com a [mesma definição de aplicação.](#use-multiple-apps-with-same-app-definition) Isto inclui, opcionalmente, o funcionamento luis de um [recipiente.](luis-container-howto.md) 
* Crie e [atribua várias chaves](#assign-multiple-luis-keys-to-same-app) à aplicação. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Use várias aplicações com a mesma definição de aplicativo
Exporte a app ORIGINAL LUIS e, em seguida, importe a app de volta em aplicações separadas. Cada aplicação tem o seu próprio ID de aplicações. Quando publicar, em vez de usar a mesma chave em todas as aplicações, crie uma chave separada para cada aplicação. Equilibre a carga em todas as aplicações para que nenhuma aplicação seja sobrecarregada. Adicione [Insights de Aplicação](./luis-csharp-tutorial-bf-v4.md) para monitorizar a utilização. 

Para obter a mesma intenção de topo entre todas as aplicações, certifique-se de que a previsão de intenção entre a primeira e a segunda intenção é suficientemente ampla para que o LUIS não esteja confuso, dando resultados diferentes entre apps para pequenas variações nas expressões. 

Ao treinar estas aplicações de irmãos, certifique-se de [treinar com todos os dados.](luis-how-to-train.md#train-with-all-data)

Designe uma única aplicação como mestre. Quaisquer declarações que sejam sugeridas para revisão devem ser adicionadas à app principal e depois reemerídas para todas as outras aplicações. Isto ou é uma exportação completa da app, ou carregar as expressões rotuladas do mestre para as crianças. O carregamento pode ser feito a partir do website [da LUIS](luis-reference-regions.md) ou da API de autoria para uma [única expressão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) ou para um [lote](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Agende uma revisão periódica, como de duas em duas [semanas,](luis-how-to-review-endpoint-utterances.md) de proclamações de ponto final para aprendizagem ativa, em seguida, retreine e reedita. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Atribua várias chaves LUIS à mesma app
Se a sua aplicação LUIS receber mais sucessos de ponto final do que a quota da sua única tecla permite, crie e atribua mais chaves à app LUIS. Crie um gestor de tráfego ou um equilibrador de carga para gerir as consultas de ponto final através das teclas do ponto final. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando a sua aplicação monolítica retorna a intenção errada
Se a sua aplicação pretende prever uma grande variedade de declarações de utilizador, considere implementar o [modelo de despacho.](#dispatch-tool-and-model) A rutura de uma aplicação monolítica permite ao LUIS focar a deteção entre as intenções com sucesso em vez de se confundir entre as intenções através da aplicação dos pais e das aplicações infantis. 

Agende uma revisão periódica [das expressões de ponto final](luis-how-to-review-endpoint-utterances.md) para a aprendizagem ativa, como de duas em duas semanas, em seguida, retreine e reedita. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando é preciso ter mais de 500 intenções
Suponha que está a desenvolver um assistente de escritório que tem mais de 500 intenções. Se 200 intenções dizem respeito a reuniões de agendamento, 200 são sobre lembretes, 200 são sobre obter informações sobre colegas, e 200 são para enviar e-mails, intenções de grupo para que cada grupo esteja em uma única app, em seguida, criar uma app de alto nível contendo cada intenção. Utilize o [modelo de despacho](#dispatch-tool-and-model) para construir a aplicação de nível superior. Em seguida, mude o seu bot para utilizar a chamada em cascata, como mostra o [tutorial do modelo de despacho](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando você precisa combinar várias aplicações de fabricantes LUIS e QnA
Se tiver várias aplicações de fabricantes LUIS e QnA que precisam responder a um bot, use o [modelo de despacho](#dispatch-tool-and-model) para construir a app de alto nível.  Em seguida, mude o seu bot para utilizar a chamada em cascata, como mostra o [tutorial do modelo de despacho](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Ferramenta de despacho e modelo
Utilize a ferramenta de linha de comando [Despacho,][dispatch-tool] encontrada em [ferramentas BotBuilder](https://github.com/Microsoft/botbuilder-tools) para combinar várias aplicações LUIS e/ou QnA Maker numa aplicação DE LUIS principal. Esta abordagem permite-lhe ter um domínio parental, incluindo todos os assuntos e diferentes domínios de menores em aplicações separadas. 

![Imagem conceptual da arquitetura de despacho](./media/luis-concept-enterprise/dispatch-architecture.png)

O domínio dos pais é notado em LUIS com uma versão nomeada `Dispatch` na lista de aplicações. 

O bot de chat recebe a expressão e, em seguida, envia para a app de entrada LUIS para previsão. A intenção prevista pela aplicação dos pais determina qual a aplicação para crianças LUIS que é chamada a seguir. O chat bot envia a expressão para a aplicação infantil para uma previsão mais específica.

Entenda como esta hierarquia de chamadas é feita a partir do Bot Builder v4 [dispatcher-application-tutorial](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limites de intenção no modelo de despacho
Um pedido de despacho tem 500 fontes de expedição, equivalentes a 500 intenções, como o máximo. 

## <a name="more-information"></a>Mais informações

* [Estrutura bot SDK](https://github.com/Microsoft/botframework)
* [Tutorial de modelo de despacho](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs)
* [Despacho CLI](https://github.com/Microsoft/botbuilder-tools)
* Amostra de bot modelo de despacho [-.NET,](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [testar um lote](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: /azure/bot-service/bot-builder-tutorial-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool