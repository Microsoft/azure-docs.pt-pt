---
title: Conceitos empresariais - LUIS
titleSuffix: Azure Cognitive Services
description: Compreenda os conceitos de design para aplicações de LUIS grandes ou várias aplicações, incluindo o LUIS e o QnA Maker em conjunto.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361151"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Estratégias da empresa para uma aplicação LUIS
Reveja essas estratégias de design para a sua aplicação empresarial.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando esperar que os pedidos de LUIS além a quota

A LUIS tem uma quota mensal, bem como uma quota por segundo, com base no nível de preços do recurso Azure. 

Se a sua taxa de pedido de aplicação LUIS exceder a taxa de [quota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)permitida, pode:

* Espalhe a carga para mais aplicações LUIS com a [mesma definição](#use-multiple-apps-with-same-app-definition)de app. Isto inclui, opcionalmente, executar O LUIS a partir de um [recipiente.](luis-container-howto.md) 
* Crie e [atribua várias chaves](#assign-multiple-luis-keys-to-same-app) à aplicação. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Utilizar várias aplicações com a mesma definição de aplicação
Exportar a aplicação LUIS original e, em seguida, importar a aplicação novamente para aplicações separadas. Cada aplicação tem seu próprio ID de aplicação. Quando publica, em vez de utilizar a mesma chave em todas as aplicações, crie uma chave separada para cada aplicação. Balancear a carga em todas as aplicações para que nenhuma aplicação única está sobrecarregada. Adicione [insights de aplicação](luis-tutorial-bot-csharp-appinsights.md) para monitorizar a utilização. 

Para obter a mesma intenção principal entre todas as aplicações, certifique-se a predição intencional entre a intenção de primeira e a segunda é suficientemente amplo para que LUIS não é confuso, dando-resultados diferentes entre aplicações pequenas variações nos expressões com. 

Ao treinar estas aplicações para irmãos, certifique-se de [treinar com todos os dados.](luis-how-to-train.md#train-with-all-data)

Designe uma única aplicação como o mestre. Qualquer expressões que são sugeridas para revisão devem ser adicionados para a aplicação principal, em seguida, movidas de volta para todas as outras aplicações. Isso é uma exportação completa da aplicação, ou ao carregar as expressões etiquetadas de mestre de para os filhos. O carregamento pode ser feito a partir do site da [LUIS](luis-reference-regions.md) ou da autoria da API para uma [única expressão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) ou para um [lote](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Agendar uma revisão periódica, como de duas em duas semanas, de [declarações de pontofinal](luis-how-to-review-endpoint-utterances.md) para aprendizagem ativa, em seguida, retreinar e republicar. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Atribuir o LUIS várias chaves para a mesma aplicação
Se a sua aplicação LUIS receber o ponto de extremidade mais acertos que permite que a quota da sua chave única, criarem e atribuir mais chaves para a aplicação do LUIS. Criar um tráfego manager ou Balanceador de carga para gerir as consultas de ponto final entre as chaves de ponto final. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando a sua aplicação monolítica devolve intenção errada
Se a sua aplicação pretende prever uma grande variedade de declarações de utilizadores, considere implementar o modelo de [despacho](#dispatch-tool-and-model). Dividir uma aplicação monolítica permite que o LUIS para detecção de foco entre objetivos com êxito em vez de introdução confusos entre os objetivos em toda a aplicação principal e subordinado. 

Agendar uma revisão periódica [das declarações de pontos finais](luis-how-to-review-endpoint-utterances.md) para a aprendizagem ativa, como de duas em duas semanas, em seguida, retreinar e republicar. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando tem de ter mais de 500 intenções
Assuma que está a desenvolver um assistente de escritório que tem mais de 500 intenções. Se 200 objetivos relacionam ao agendamento de reuniões, 200 são sobre lembretes, 200 são sobre como obter informações sobre os colegas e 200 servem para enviar o e-mail, os objetivos de grupo para que cada grupo seja numa única aplicação, em seguida, crie uma aplicação de nível superior que contém cada intenção. Utilize o modelo de [despacho](#dispatch-tool-and-model) para construir a aplicação de alto nível. Em seguida, mude o seu bot para utilizar a chamada em cascata, como mostrado no [tutorial do modelo de despacho](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando tiver de combinar vários LUIS aplicações e não compatíveis QnA maker
Se tiver várias aplicações de fabricante luis e QnA que precisam responder a um bot, use o modelo de [despacho](#dispatch-tool-and-model) para construir a aplicação de alto nível.  Em seguida, mude o seu bot para utilizar a chamada em cascata, como mostrado no [tutorial do modelo de despacho](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Ferramenta de distribuição e o modelo
Utilize a ferramenta de linha de comando [Dispatch,][dispatch-tool] encontrada em [ferramentas BotBuilder](https://github.com/Microsoft/botbuilder-tools) para combinar várias aplicações LUIS e/ou QnA Maker numa aplicação DE MA-mãe. Esta abordagem permite que tenha um domínio principal, incluindo todos os assuntos e domínios de sujeito do filho diferentes nas aplicações separadas. 

![Imagem conceptual da arquitetura de distribuição](./media/luis-concept-enterprise/dispatch-architecture.png)

O domínio dos pais é notado em LUIS com uma versão chamada `Dispatch` na lista de aplicações. 

O chat bot recebe a expressão e depois envia para a app LUIS para previsão. A principal intenção prevista da app dos pais determina qual a app infantil LUIS que é chamada a seguir. O chat bot envia a expressão para a aplicação infantil para uma previsão mais específica.

Compreenda como esta hierarquia de chamadas é feita a partir do Bot Builder v4 [despachante-application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limites de intenção no modelo de expedição
Uma aplicação de distribuição tem 500 origens de expedição, equivalentes a 500 intenções, como o máximo. 

## <a name="more-information"></a>Mais informações

* [Quadro bot SDK](https://github.com/Microsoft/botframework)
* [Tutorial de modelo de despacho](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Despacho CLI](https://github.com/Microsoft/botbuilder-tools)
* Despacho modelo bot amostra - [.NET,](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Passos Seguintes

* Aprenda a [testar um lote](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
