---
title: O que é o serviço QnA Maker?
description: O QnA Maker é um serviço NLP baseado na nuvem que cria facilmente uma camada de conversação natural sobre os seus dados. Pode ser usado para encontrar a resposta mais adequada para qualquer dado contributo de linguagem natural, a partir da sua base de conhecimento personalizado (KB) de informação.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 01/22/2021
ms.custom: cog-serv-seo-aug-2020
keywords: fabricante de qna, bots de chat de código baixo, conversas multi-turn
ms.openlocfilehash: 8df9daa213156fc38ed08bced44ea919da95e6a4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869213"
---
# <a name="what-is-qna-maker"></a>O que é o Criador de FAQ?

O QnA Maker é um serviço de processamento de linguagem natural (NLP) baseado na nuvem que lhe permite criar uma camada de conversação natural sobre os seus dados. É usado para encontrar a resposta mais adequada para qualquer entrada da sua base de conhecimento personalizado (KB) de informação.

O QnA Maker é comumente usado para construir aplicações de clientes conversais, que incluem aplicações de redes sociais, chat bots e aplicações de desktop ativadas por discursos.

A QnA Maker não armazena os dados dos clientes. Todos os dados do cliente (respostas de perguntas e chatlogs) são armazenados na região em que o cliente implementa as instâncias de serviço dependentes. Para mais detalhes sobre serviços dependentes consulte [aqui.](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/plan?tabs=v1)

## <a name="when-to-use-qna-maker"></a>Quando usar o Fabricante QnA

* **Quando tiver informações estáticas** - Use o Fabricante QnA quando tiver informações estáticas na base de respostas do seu conhecimento. Esta base de conhecimento é personalizada às suas necessidades, que construiu com documentos como [PDFs e URLs.](../Concepts/data-sources-and-content.md)
* **Quando pretende dar a mesma resposta a um pedido, pergunta ou comando** - quando diferentes utilizadores submetem a mesma pergunta, a mesma resposta é devolvida.
* **Quando pretende filtrar informações estáticas com base em metodagens** - adicione tags [de metadados](../how-to/metadata-generateanswer-usage.md) para fornecer opções de filtragem adicionais relevantes para os utilizadores da sua aplicação do cliente e as informações. As informações comuns de metadados incluem [chit-chat,](../how-to/chit-chat-knowledge-base.md)tipo de conteúdo ou formato, finalidade do conteúdo e frescura de conteúdo.
* **Quando pretende gerir uma conversa bot que inclua informações estáticas** - a sua base de conhecimentos pega no texto ou comando de um utilizador e responde-o. Se a resposta for parte de um fluxo de conversação pré-determinado, representado na sua base de conhecimento com [contexto multi-volta,](../how-to/multiturn-conversation.md)o bot pode facilmente fornecer este fluxo.

## <a name="what-is-a-knowledge-base"></a>O que é uma base de conhecimento?

A QnA Maker [importa o seu conteúdo](../Concepts/plan.md) numa base de conhecimento de pares de perguntas e respostas. O processo de importação extrai informações sobre a relação entre as partes do seu conteúdo estruturado e semi-estruturado para implicar relações entre os pares de perguntas e respostas. Pode editar estes pares de perguntas e respostas ou adicionar novos pares.

O conteúdo do par de perguntas e respostas inclui:
* Todas as formas alternativas da pergunta
* Tags de metadados usadas para filtrar escolhas de resposta durante a pesquisa
* Pedidos de acompanhamento para continuar o refinamento de pesquisa

![Pergunta e resposta de exemplo com metadados](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Depois de publicar a sua base de conhecimento, uma aplicação do cliente envia a pergunta de um utilizador para o seu ponto final. O seu serviço QnA Maker processa a pergunta e responde com a melhor resposta.

## <a name="create-a-chat-bot-programmatically"></a>Crie um chat bot programáticamente

Uma vez publicada uma base de conhecimento da QnA Maker, uma aplicação do cliente envia uma pergunta para o seu ponto final da base de conhecimento e recebe os resultados como resposta JSON. Uma aplicação comum de cliente para o QnA Maker é um chat bot.

![Faça uma pergunta a um bot e obtenha resposta a partir de conteúdo de base de conhecimento](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Passo|Ação|
|:--|:--|
|1|A aplicação do cliente envia a _pergunta_ do utilizador (texto nas suas próprias palavras), "Como é que eu actualo programáticamente a minha Base de Conhecimento?" para o seu ponto final base de conhecimento.|
|2|O QnA Maker utiliza a base de conhecimento treinado para fornecer a resposta correta e quaisquer indicações de seguimento que possam ser usadas para refinar a procura da melhor resposta. O QnA Maker devolve uma resposta formatada por JSON.|
|3|A aplicação do cliente usa a resposta JSON para tomar decisões sobre como continuar a conversa. Estas decisões podem incluir mostrar a melhor resposta e apresentar mais escolhas para aperfeiçoar a procura da melhor resposta. |
|||

## <a name="build-low-code-chat-bots"></a>Construir bots de chat de código baixo

O portal QnA Maker proporciona a experiência completa de autoria da base de conhecimento. Pode importar documentos, na sua forma atual, para a sua base de conhecimentos. Estes documentos (como um FAQ, manual do produto, folha de cálculo ou página web) são convertidos em pares de perguntas e respostas. Cada par é analisado para pedidos de seguimento e ligado a outros pares. O formato _de marcação_ final suporta uma apresentação rica, incluindo imagens e links.

Assim que a sua base de conhecimentos for editada, publique a base de conhecimentos num [bot Azure Web App](https://azure.microsoft.com/services/bot-service/) funcional sem escrever nenhum código. Teste o seu bot no [portal Azure](https://portal.azure.com) ou descarregue-o e continue o desenvolvimento.

## <a name="high-quality-responses-with-layered-ranking"></a>Respostas de alta qualidade com ranking em camadas

O sistema do QnA Maker é uma abordagem de classificação em camadas. Os dados são armazenados na pesquisa do Azure, que também serve como a primeira camada de classificação. Os melhores resultados da pesquisa da Azure são então passados através do modelo de reclasse NLP da QnA Maker para produzir os resultados finais e a pontuação de confiança.

## <a name="multi-turn-conversations"></a>Conversações multiturno

O QnA Maker fornece solicitações de várias voltas e aprendizagem ativa para ajudá-lo a melhorar os seus pares básicos de perguntas e respostas.

**As instruções multi-volta dão-lhe** a oportunidade de ligar pares de perguntas e respostas. Esta ligação permite que a aplicação do cliente forneça uma resposta de topo e fornece mais perguntas para aperfeiçoar a procura de uma resposta final.

Depois de a base de conhecimento receber perguntas dos utilizadores no ponto final publicado, a QnA Maker aplica **aprendizagem ativa** a estas questões do mundo real para sugerir alterações na sua base de conhecimento para melhorar a qualidade.

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

A QnA Maker fornece autoria, formação e publicação, juntamente com permissões de colaboração para integrar no ciclo de vida de desenvolvimento completo.

> [!div class="mx-imgBorder"]
> ![Imagem conceptual do ciclo de desenvolvimento](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>Complete um arranque rápido

Oferecemos quickstarts nas linguagens de programação mais populares, cada uma projetada para lhe ensinar padrões básicos de design, e tê-lo a executar código em menos de 10 minutos. Consulte a seguinte lista para o arranque rápido de cada recurso.

* [Começa com a biblioteca de clientes da QnA Maker](../quickstarts/quickstart-sdk.md)
* [Começa com o portal QnA Maker](../quickstarts/create-publish-knowledge-base.md)

## <a name="next-steps"></a>Passos seguintes
O QnA Maker fornece tudo o que precisa para construir, gerir e implementar a sua base de conhecimentos personalizados.

> [!div class="nextstepaction"]
> [Rever as últimas alterações](../whats-new.md)
