---
title: Visão geral da compreensão da linguagem (LUIS)
description: Language Understanding (LUIS) - um serviço de API baseado na nuvem que utiliza machine-learning para linguagem conversacional e natural para prever significado e extrair informações.
keywords: Azure, inteligência artificial, ai, processamento de linguagem natural, nlp, compreensão da linguagem natural, nlu, LUIS, CONVERSAÇÃO AI, ai chatbot, nlp ai, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/13/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f46586b3f120cf191d88b7de9cf8686ca9b16cca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503777"
---
# <a name="what-is-language-understanding-luis"></a>O que é o Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) é um serviço de IA de conversação baseado na nuvem que aplica inteligência personalizada de aprendizagem automática ao texto de linguagem natural e conversacional de um utilizador para prever o significado geral, e retirar informações relevantes e detalhadas. A LUIS fornece acesso através do seu [portal personalizado,](https://www.luis.ai) [APIs][endpoint-apis] e [bibliotecas de clientes SDK.](client-libraries-rest-api.md)

Pela primeira vez os utilizadores, siga estes passos para [iniciar sôm no portal LUIS](sign-in-luis-portal.md "assinar no portal LUIS") Para começar, pode experimentar aplicações de domínio [pré-construídas](luis-get-started-create-app.md) LUIS, ou pode [construir a sua app.](get-started-portal-build-app.md)

Esta documentação contém os seguintes tipos de artigos:  

* [**Os quickstarts**](luis-get-started-create-app.md) estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.  
* [**Os guias de como fazer**](luis-how-to-start-new-app.md) contêm instruções para a utilização do serviço de formas mais específicas ou personalizadas.  
* [**Os conceitos**](artificial-intelligence.md) fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.  
* [**Os tutoriais**](tutorial-intents-only.md) são guias mais longos que mostram como usar o serviço como componente em soluções de negócio mais amplas.  

## <a name="what-does-luis-offer"></a>O que luis oferece 

* **Simplicidade**: O LUIS descarrega-o da necessidade de conhecimentos de IA internos ou de qualquer conhecimento prévio de aprendizagem automática. Com apenas alguns cliques pode construir a sua própria aplicação de IA conversacional. Você pode construir a sua aplicação personalizada seguindo um dos [nossos quickstarts](get-started-portal-build-app.md), ou você pode usar uma das nossas aplicações de [domínio pré-construídas.](luis-get-started-create-app.md)
* **Segurança, Privacidade e Conformidade**: Apoiado pela infraestrutura Azure, a LUIS oferece segurança, privacidade e conformidade de nível empresarial. Os seus dados continuam a ser seus; pode apagar os seus dados a qualquer momento. Os seus dados são encriptados enquanto estão armazenados. Saiba mais sobre o assunto [aqui](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy).
* **Integração:** integrar facilmente a sua aplicação LUIS com outros serviços da Microsoft, como [a estrutura do Microsoft Bot,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)e serviço [speech](../Speech-Service/quickstarts/intent-recognition.md).


## <a name="luis-scenarios"></a>Cenários LUIS
* [Construa um bot de conversação de nível empresarial](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot): Esta arquitetura de referência descreve como construir um bot de conversação de nível empresarial (chatbot) usando o Azure Bot Framework.
* [Commerce Chatbot](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot): Juntos, o serviço Azure Bot Service e o serviço de compreensão de linguagem permitem aos desenvolvedores criar interfaces de conversação para vários cenários como banca, viagens e entretenimento.
* [Controlo de dispositivos IoT utilizando um Voice Assistant](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant): Crie interfaces de conversação sem emenda com todos os seus dispositivos acessíveis à Internet, desde a televisão ou frigorífico ligados a dispositivos numa central elétrica ligada.


## <a name="application-development-life-cycle"></a>Ciclo de vida de Desenvolvimento de Aplicações

![Ciclo de vida de desenvolvimento de aplicativos LUIS](./media/luis-overview/luis-dev-lifecycle.png "Ciclo de vida develooment da aplicação LUIS")

-   **Plano**: Identifique os cenários para os quais os utilizadores possam utilizar a sua aplicação. Defina as ações e informações relevantes que precisam de ser reconhecidas.
-   **Construa:** Use o seu recurso de autoria para desenvolver a sua aplicação. Comece por definir [intenções](luis-concept-intent.md) e [entidades.](luis-concept-entity-types.md) Em seguida, adicione [proclamações de](luis-concept-utterance.md) treino para cada intenção. 
-   **Teste e Melhora:** Comece a testar o seu modelo com outras expressões para ter uma noção de como a aplicação se comporta, e pode decidir se é necessária alguma melhoria. Pode melhorar a sua aplicação seguindo estas [boas práticas.](luis-concept-best-practices.md) 
-   **Publicar:** Implemente a sua aplicação para previsão e consulta o ponto final utilizando o seu recurso de previsão. Saiba mais sobre a autoria e previsão de recursos [aqui.](luis-how-to-azure-subscription.md#luis-resources) 
-   **Conecte-se**: Conecte-se a outros serviços como [a estrutura do Microsoft Bot,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)e serviço de [fala](../Speech-Service/quickstarts/intent-recognition.md). 
-   **Refine**: [Rever as expressões de ponto final](luis-concept-review-endpoint-utterances.md) para melhorar a sua aplicação com exemplos da vida real

Saiba mais sobre planear e construir a sua candidatura [aqui.](luis-how-plan-your-app.md)

## <a name="next-steps"></a>Passos seguintes

* [Novidades](whats-new.md "Novidades") com o serviço e documentação
* [Criar uma aplicação LUIS](tutorial-intents-only.md)
* [Referência da API][endpoint-apis]
* [Melhores práticas](luis-concept-best-practices.md)
* [Recursos de desenvolvimento](developer-reference-resource.md "Recursos para programadores") para o LUIS.
* [Planeie a sua app](luis-how-plan-your-app.md "Planear a sua aplicação") com [intenções](luis-concept-intent.md "intenções") e [entidades.](luis-concept-entity-types.md "entidades")

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
