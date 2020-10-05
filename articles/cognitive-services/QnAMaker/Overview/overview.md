---
title: O que é o serviço QnA Maker?
description: O QnA Maker é um serviço NLP baseado na nuvem que cria facilmente uma camada de conversação natural sobre os seus dados. Pode ser usado para encontrar a resposta mais adequada para qualquer dado contributo de linguagem natural, a partir da sua base de conhecimento personalizado (KB) de informação.
ms.topic: overview
ms.date: 05/26/2020
ms.openlocfilehash: d2ff2d789f2ea1ae6018d95ef1d880da87b4ff74
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "83994872"
---
# <a name="what-is-the-qna-maker-service"></a>O que é o serviço QnA Maker?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker é um serviço de processamento de linguagem natural (NLP) baseado na nuvem que cria facilmente uma camada de conversação natural sobre os seus dados. Pode ser usado para encontrar a resposta mais adequada para qualquer dado contributo de linguagem natural, a partir da sua base de conhecimento personalizado (KB) de informação.

Uma aplicação de cliente para o QnA Maker é qualquer aplicação de conversação que comunique com um utilizador em linguagem natural para responder a uma pergunta. Alguns exemplos de aplicações cliente incluem aplicações de redes sociais, bots de chat e aplicações para ambiente de trabalho com fala ativada.

## <a name="when-to-use-qna-maker"></a>Quando usar o Fabricante QnA

* **Quando tiver informações estáticas** - Use o Fabricante QnA quando tiver informações estáticas na base de respostas do seu conhecimento. Esta base de conhecimento é personalizada às suas necessidades, que construiu com documentos como [PDFs e URLs.](../concepts/content-types.md)
* **Quando pretende dar a mesma resposta a um pedido, pergunta ou comando** - quando diferentes utilizadores submetem a mesma pergunta, a mesma resposta é devolvida.
* **Quando pretende filtrar informações estáticas com base em metodagens** - adicione tags [de metadados](../how-to/metadata-generateanswer-usage.md) para fornecer opções de filtragem adicionais relevantes para os utilizadores da sua aplicação do cliente e as informações. As informações comuns de metadados incluem [chit-chat,](../how-to/chit-chat-knowledge-base.md)tipo de conteúdo ou formato, finalidade do conteúdo e frescura de conteúdo.
* **Quando pretende gerir uma conversa bot que inclua informações estáticas** - a sua base de conhecimentos pega no texto ou comando de um utilizador e responde-o. Se a resposta for parte de um fluxo de conversação pré-determinado, representado na sua base de conhecimento com [contexto multi-volta,](../how-to/multiturn-conversation.md)o bot pode facilmente fornecer este fluxo.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Use a base de conhecimento do QnA Maker num bot de chat

Uma vez publicada uma base de conhecimento da QnA Maker, uma aplicação do cliente envia uma pergunta para o seu ponto final da base de conhecimento e recebe os resultados como resposta JSON. Uma aplicação comum de cliente para o QnA Maker é um chat bot.

![Faça uma pergunta a um bot e obtenha resposta a partir de conteúdo de base de conhecimento](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Passo|Ação|
|:--|:--|
|1|A aplicação do cliente envia a _pergunta_ do utilizador (texto nas suas próprias palavras), "Como é que eu actualo programáticamente a minha Base de Conhecimento?" para o seu ponto final base de conhecimento.|
|2|O QnA Maker utiliza a base de conhecimento treinado para fornecer a resposta correta e quaisquer indicações de seguimento que possam ser usadas para refinar a procura da melhor resposta. O QnA Maker devolve uma resposta formatada por JSON.|
|3|A aplicação do cliente usa a resposta JSON para tomar decisões sobre como continuar a conversa. Estas decisões podem incluir mostrar a melhor resposta e apresentar mais escolhas para aperfeiçoar a procura da melhor resposta. |
|||

## <a name="what-is-a-knowledge-base"></a>O que é uma base de conhecimento?

A QnA Maker [importa o seu conteúdo](../concepts/knowledge-base.md) numa base de conhecimento de pares de perguntas e respostas. O processo de importação extrai informações sobre a relação entre as partes do seu conteúdo estruturado e semi-estruturado para implicar relações entre os pares de perguntas e respostas. Pode editar estes pares de perguntas e respostas ou adicionar novos pares.

O conteúdo do par de perguntas e respostas inclui:
* Todas as formas alternativas da pergunta
* Tags de metadados usadas para filtrar escolhas de resposta durante a pesquisa
* Pedidos de acompanhamento para continuar o refinamento de pesquisa

![Pergunta e resposta de exemplo com metadados](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Depois de publicar a sua base de conhecimento, uma aplicação do cliente envia a pergunta de um utilizador para o seu ponto final. O seu serviço QnA Maker processa a pergunta e responde com a melhor resposta.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Criar, gerir e publicar para um bot sem código

O portal QnA Maker proporciona a experiência completa de autoria da base de conhecimento. Pode importar documentos, na sua forma atual, para a sua base de conhecimentos. Estes documentos (como um FAQ, manual do produto, folha de cálculo ou página web) são convertidos em pares de perguntas e respostas. Cada par é analisado para pedidos de seguimento e ligado a outros pares. O formato _de marcação_ final suporta uma apresentação rica, incluindo imagens e links.

Assim que a sua base de conhecimentos for editada, publique a base de conhecimentos num [bot Azure Web App](https://azure.microsoft.com/services/bot-service/) funcional sem escrever nenhum código. Teste o seu bot no [portal Azure](https://portal.azure.com) ou descarregue e continue o desenvolvimento.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>A qualidade e o ranking da pesquisa fornecem a melhor resposta possível

O sistema do QnA Maker é uma abordagem de classificação em camadas. Os dados são armazenados na pesquisa do Azure, que também serve como a primeira camada de classificação. Os melhores resultados da pesquisa da Azure são então passados através do modelo de reclasse NLP da QnA Maker para produzir os resultados finais e a pontuação de confiança.

## <a name="qna-maker-improves-the-conversation-process"></a>O QnA Maker melhora o processo de conversação

O QnA Maker fornece solicitações de várias voltas e aprendizagem ativa para ajudá-lo a melhorar os seus pares básicos de perguntas e respostas.

**As instruções multi-volta dão-lhe** a oportunidade de ligar pares de perguntas e respostas. Esta ligação permite que a aplicação do cliente forneça uma resposta de topo e fornece mais perguntas para aperfeiçoar a procura de uma resposta final.

Depois de a base de conhecimento receber perguntas dos utilizadores no ponto final publicado, a QnA Maker aplica **aprendizagem ativa** a estas questões do mundo real para sugerir alterações na sua base de conhecimento para melhorar a qualidade.

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

A QnA Maker fornece autoria, formação e publicação, juntamente com permissões de colaboração para integrar no ciclo de vida de desenvolvimento completo.

> [!div class="mx-imgBorder"]
> ![Imagem conceptual do ciclo de desenvolvimento](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Como posso começar?

**Passo 1**: Criar um recurso QnA Maker no [portal Azure](https://portal.azure.com).

**Passo 2**: Criar uma base de conhecimento no portal [QnA Maker.](https://www.qnamaker.ai) Adicione [ficheiros e URLs](../concepts/content-types.md) para criar a base de conhecimento.

**Passo 3**: Publique a sua base de conhecimento e teste a partir do seu ponto final personalizado utilizando [cURL ou Carteiro](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Passo 4**: A partir da sua aplicação ao cliente, ligue programáticamente para o ponto final da sua base de conhecimento. A aplicação do cliente processa a resposta JSON para mostrar a melhor resposta ao utilizador.

## <a name="next-steps"></a>Passos seguintes
O QnA Maker fornece tudo o que precisa para construir, gerir e implementar a sua base de conhecimentos personalizados.

> [!div class="nextstepaction"]
> [Rever as últimas alterações](../whats-new.md)
