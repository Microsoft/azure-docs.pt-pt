---
title: Conceitos empresariais-LUIS
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
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619932"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Estratégias da empresa para uma aplicação LUIS
Reveja essas estratégias de design para a sua aplicação empresarial.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando esperar que os pedidos de LUIS além a quota

LUIS tem uma cota mensal, bem como uma cota por segundo, com base no tipo de preço do recurso do Azure. 

Se a taxa de solicitação do aplicativo LUIS exceder a [taxa de cota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)permitida, você poderá:

* Espalhe a carga para aplicativos mais LUIS com a [mesma definição de aplicativo](#use-multiple-apps-with-same-app-definition). Isso inclui, opcionalmente, a execução de LUIS de um [contêiner](luis-container-howto.md). 
* Crie e [atribua várias chaves](#assign-multiple-luis-keys-to-same-app) ao aplicativo. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Utilizar várias aplicações com a mesma definição de aplicação
Exportar a aplicação LUIS original e, em seguida, importar a aplicação novamente para aplicações separadas. Cada aplicação tem seu próprio ID de aplicação. Quando publica, em vez de utilizar a mesma chave em todas as aplicações, crie uma chave separada para cada aplicação. Balancear a carga em todas as aplicações para que nenhuma aplicação única está sobrecarregada. Adicione [Application Insights](luis-tutorial-bot-csharp-appinsights.md) para monitorizar a utilização. 

Para obter a mesma intenção principal entre todas as aplicações, certifique-se a predição intencional entre a intenção de primeira e a segunda é suficientemente amplo para que LUIS não é confuso, dando-resultados diferentes entre aplicações pequenas variações nos expressões com. 

Ao treinar esses aplicativos irmãos, certifique-se de [treinar com todos os dados](luis-how-to-train.md#train-with-all-data).

Designe uma única aplicação como o mestre. Qualquer expressões que são sugeridas para revisão devem ser adicionados para a aplicação principal, em seguida, movidas de volta para todas as outras aplicações. Isso é uma exportação completa da aplicação, ou ao carregar as expressões etiquetadas de mestre de para os filhos. A carregar pode ser feito a partir de qualquer uma a [LUIS](luis-reference-regions.md) Web site ou a API de criação para um [única expressão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) ou para um [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Agende uma revisão periódica, como a cada duas semanas, do [ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md) para o aprendizado ativo e, em seguida, retreine e Republique. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Atribuir o LUIS várias chaves para a mesma aplicação
Se a sua aplicação LUIS receber o ponto de extremidade mais acertos que permite que a quota da sua chave única, criarem e atribuir mais chaves para a aplicação do LUIS. Criar um tráfego manager ou Balanceador de carga para gerir as consultas de ponto final entre as chaves de ponto final. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando a sua aplicação monolítica devolve intenção errada
Se a sua aplicação destina-se para prever uma grande variedade de expressões de utilizador, considere implementar o [modelo de expedição](#dispatch-tool-and-model). Dividir uma aplicação monolítica permite que o LUIS para detecção de foco entre objetivos com êxito em vez de introdução confusos entre os objetivos em toda a aplicação principal e subordinado. 

Agendar um periódica [revisão de expressões de ponto final](luis-how-to-review-endpoint-utterances.md) para aprendizagem ativa, como a cada duas semanas, em seguida, voltar a preparar e voltar a publicar. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando tem de ter mais de 500 intenções
Suponha que você esteja desenvolvendo um assistente do Office com mais de 500 tentativas. Se 200 objetivos relacionam ao agendamento de reuniões, 200 são sobre lembretes, 200 são sobre como obter informações sobre os colegas e 200 servem para enviar o e-mail, os objetivos de grupo para que cada grupo seja numa única aplicação, em seguida, crie uma aplicação de nível superior que contém cada intenção. Use o [modelo de expedição](#dispatch-tool-and-model) para criar o aplicativo de nível superior. Em seguida, altere o bot para usar a chamada em cascata, conforme mostrado no [tutorial do modelo de expedição](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando tiver de combinar vários LUIS aplicações e não compatíveis QnA maker
Se você tiver vários aplicativos LUIS e QnA Maker que precisam responder a um bot, use o [modelo de expedição](#dispatch-tool-and-model) para criar o aplicativo de nível superior.  Em seguida, altere o bot para usar a chamada em cascata, conforme mostrado no [tutorial do modelo de expedição](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Ferramenta de distribuição e o modelo
Use a ferramenta de linha de comando [Dispatch][dispatch-tool] , encontrada em [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools) para combinar vários LUIS e/ou QnA Maker aplicativos em um aplicativo Luis pai. Esta abordagem permite que tenha um domínio principal, incluindo todos os assuntos e domínios de sujeito do filho diferentes nas aplicações separadas. 

![Imagem conceptual da arquitetura de distribuição](./media/luis-concept-enterprise/dispatch-architecture.png)

O domínio principal é mencionado em LUIS com uma versão com o nome `Dispatch` na lista de aplicações. 

O bot de chat recebe o expressão e, em seguida, envia para o aplicativo LUIS pai para previsão. A principal intenção prevista do aplicativo pai determina qual aplicativo filho LUIS é chamado em seguida. O bot de chat envia o expressão para o aplicativo filho para uma previsão mais específica.

Entenda como essa hierarquia de chamadas é feita do Bot Builder v4 [Dispatcher-Application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limites de intenção no modelo de expedição
Uma aplicação de distribuição tem 500 origens de expedição, equivalentes a 500 intenções, como o máximo. 

## <a name="more-information"></a>Mais informações

* [SDK do bot Framework](https://github.com/Microsoft/botframework)
* [Tutorial do modelo de expedição](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [CLI de expedição](https://github.com/Microsoft/botbuilder-tools)
* Exemplo de bot de modelo de expedição – [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [um lote de teste](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
