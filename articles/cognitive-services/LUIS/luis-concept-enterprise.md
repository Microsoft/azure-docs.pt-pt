---
title: Conceitos de Enterprise para uma aplicação do LUIS - Azure | Documentos da Microsoft
description: Compreenda os conceitos de design para aplicações de LUIS grandes.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/05/2018
ms.author: diberry
ms.openlocfilehash: fda4a089866950688d88f9f47988c1540abe1cc0
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224865"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Estratégias da empresa para uma aplicação LUIS
Reveja essas estratégias de design para a sua aplicação empresarial.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando esperar que os pedidos de LUIS além a quota
Se a taxa de pedidos de aplicação do LUIS exceder permitidos [taxa de quota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), distribuir a carga para obter mais aplicações do LUIS com o [mesma definição de aplicação](#use-multiple-apps-with-same-app-definition) ou crie e [atribuir várias chaves](#assign-multiple-luis-keys-to-same-app) para o aplicação. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Utilizar várias aplicações com a mesma definição de aplicação
Exportar a aplicação LUIS original e, em seguida, importar a aplicação novamente para aplicações separadas. Cada aplicação tem seu próprio ID de aplicação. Quando publica, em vez de utilizar a mesma chave em todas as aplicações, crie uma chave separada para cada aplicação. Balancear a carga em todas as aplicações para que nenhuma aplicação única está sobrecarregada. Adicione [Application Insights](luis-tutorial-bot-csharp-appinsights.md) para monitorizar a utilização. 

Para obter a mesma intenção principal entre todas as aplicações, certifique-se a predição intencional entre a intenção de primeira e a segunda é suficientemente amplo para que LUIS não é confuso, dando-resultados diferentes entre aplicações pequenas variações nos expressões com. 

Designe uma única aplicação como o mestre. Qualquer expressões que são sugeridas para revisão devem ser adicionados para a aplicação principal, em seguida, movidas de volta para todas as outras aplicações. Isso é uma exportação completa da aplicação, ou ao carregar as expressões etiquetadas de mestre de para os filhos. A carregar pode ser feito a partir de qualquer uma a [LUIS](luis-reference-regions.md) Web site ou a API de criação para um [única expressão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) ou para um [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Agendar um periódica [revisão de expressões de ponto final](luis-how-to-review-endoint-utt.md) para aprendizagem ativa, como a cada duas semanas, em seguida, voltar a preparar e voltar a publicar. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Atribuir o LUIS várias chaves para a mesma aplicação
Se a sua aplicação LUIS receber o ponto de extremidade mais acertos que permite que a quota da sua chave única, criarem e atribuir mais chaves para a aplicação do LUIS. Criar um tráfego manager ou Balanceador de carga para gerir as consultas de ponto final entre as chaves de ponto final. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando a sua aplicação monolítica devolve intenção errada
Se a sua aplicação destina-se para prever uma grande variedade de expressões de utilizador, considere implementar o [modelo de expedição](#dispatch-tool-and-model). Dividir uma aplicação monolítica permite que o LUIS para detecção de foco entre objetivos com êxito em vez de introdução confusos entre os objetivos em toda a aplicação principal e subordinado. 

Agendar um periódica [revisão de expressões de ponto final](luis-how-to-review-endoint-utt.md) para aprendizagem ativa, como a cada duas semanas, em seguida, voltar a preparar e voltar a publicar. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando tem de ter mais de 500 intenções
Por exemplo, digamos que está desenvolvendo um assistente do office que tenha mais de 500 intenções. Se 200 objetivos relacionam ao agendamento de reuniões, 200 são sobre lembretes, 200 são sobre como obter informações sobre os colegas e 200 servem para enviar o e-mail, os objetivos de grupo para que cada grupo seja numa única aplicação, em seguida, crie uma aplicação de nível superior que contém cada intenção. Utilize o [expedir a ferramenta e arquitetura](#dispatch-tool-and-model) para criar a aplicação de nível superior. Em seguida, altere o seu bot para utilizar a chamada em cascata, como mostra a [tutorial de expedição][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando tiver de combinar vários LUIS aplicações e não compatíveis QnA maker
Se tiver as aplicações do vários LUIS e QnA maker que precisam para responder a um bot, utilize o [ferramenta de expedição](#dispatch-tool-and-model) para criar a aplicação de nível superior. Em seguida, altere o seu bot para utilizar a chamada em cascata, como mostra a [tutorial de expedição][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Ferramenta de distribuição e o modelo
Utilize o [expedição] [ dispatch-tool] ferramenta da linha de comandos, encontrada no [ferramentas BotBuilder](https://github.com/Microsoft/botbuilder-tools) para combinar vários LUIS e/ou QnA Maker aplicações num elemento principal aplicação LUIS. Esta abordagem permite que tenha um domínio principal, incluindo todos os assuntos e domínios de sujeito do filho diferentes nas aplicações separadas. 

![Imagem conceptual da arquitetura de distribuição](./media/luis-concept-enterprise/dispatch-architecture.png)

O domínio principal é mencionado em LUIS como um **V expedição** aplicação. 

![Lista de aplicações de captura de ecrã do LUIS com a aplicação do LUIS criada pela ferramenta de distribuição](./media/luis-concept-enterprise/dispatch.png)

O chatbot recebe a expressão, em seguida, envia para o elemento principal aplicação LUIS para predição. A intenção de prevista superior a partir da aplicação principal determina qual filho aplicação LUIS é chamada em seguida. O chatbot envia a expressão para a aplicação de subordinados para uma previsão mais específica.

Compreender a forma como esta hierarquia de chamadas é feita a partir de construtor de Bot v4 [dispatcher-aplicação-tutorial][dispatcher-application-tutorial].  

### <a name="intent-limits-in-dispatch-model"></a>Limites de intenção no modelo de expedição
Uma aplicação de distribuição tem 500 origens de expedição, equivalentes a 500 intenções, como o máximo. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [um lote de teste](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch