---
title: O que é o serviço QnA Maker?
description: QnA Maker é um serviço NLP baseado em nuvem que facilmente cria uma camada de conversação natural sobre os seus dados. Pode ser usado para encontrar a resposta mais adequada para qualquer entrada de linguagem natural, a partir da sua base de conhecimento personalizada (KB) de informação.
ms.topic: overview
ms.date: 05/26/2020
ms.openlocfilehash: 5962ab2294a19c600beb00816394d96059bd4108
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873715"
---
# <a name="what-is-the-qna-maker-service"></a>O que é o serviço QnA Maker?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker é um serviço de processamento de linguagem natural baseado na nuvem (NLP) que cria facilmente uma camada de conversação natural sobre os seus dados. Pode ser usado para encontrar a resposta mais adequada para qualquer entrada de linguagem natural, a partir da sua base de conhecimento personalizada (KB) de informação.

Uma aplicação de cliente para o QnA Maker é qualquer aplicação de conversação que comunica com um utilizador em linguagem natural para responder a uma pergunta. Alguns exemplos de aplicações cliente incluem aplicações de redes sociais, bots de chat e aplicações para ambiente de trabalho com fala ativada.

## <a name="when-to-use-qna-maker"></a>Quando usar o Fabricante QnA

* **Quando tiver informações estáticas** - Use O Fabricante de QnA quando tiver informações estáticas na sua base de respostas de conhecimento. Esta base de conhecimento é personalizada às suas necessidades, que construiu com documentos como [PDFs e URLs.](../concepts/content-types.md)
* **Quando pretende dar a mesma resposta a um pedido, pergunta ou comando** - quando diferentes utilizadores submetem a mesma pergunta, a mesma resposta é devolvida.
* **Quando pretender filtrar informações estáticas baseadas em meta-informações** - adicione etiquetas de [metadados](../how-to/metadata-generateanswer-usage.md) para fornecer opções de filtragem adicionais relevantes para os utilizadores da sua aplicação cliente e as informações. Informações comuns de metadados incluem [chit-chat,](../how-to/chit-chat-knowledge-base.md)tipo de conteúdo ou formato, finalidade de conteúdo e frescura de conteúdo.
* **Quando pretende gerir uma conversa de bot que inclua informações estáticas** - a sua base de conhecimentos pega no texto ou comando de um utilizador e responde-a. Se a resposta faz parte de um fluxo de conversação pré-determinado, representado na sua base de conhecimentos com [contexto multi-turn,](../how-to/multiturn-conversation.md)o bot pode facilmente fornecer este fluxo.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Use a base de conhecimento do QnA Maker num chat bot

Uma vez publicada uma base de conhecimento da QnA Maker, uma aplicação de cliente envia uma pergunta para o seu ponto final de base de conhecimento e recebe os resultados como resposta JSON. Um pedido comum de cliente para a QnA Maker é um chat bot.

![Faça uma pergunta a um bot e obtenha resposta do conteúdo da base de conhecimento](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Passo|Ação|
|:--|:--|
|1|A aplicação do cliente envia a _pergunta_ do utilizador (texto nas suas próprias palavras), "Como atualizo programáticamente a minha Base de Conhecimento?" para o seu ponto final de base de conhecimento.|
|2|O QnA Maker utiliza a base de conhecimentos treinada para fornecer a resposta correta e quaisquer instruções de seguimento que possam ser usadas para refinar a procura da melhor resposta. A QnA Maker devolve uma resposta formatada da JSON.|
|3|A aplicação do cliente utiliza a resposta da JSON para tomar decisões sobre como continuar a conversa. Estas decisões podem incluir mostrar a resposta máxima e apresentar mais escolhas para refinar a procura da melhor resposta. |
|||

## <a name="what-is-a-knowledge-base"></a>O que é uma base de conhecimento?

A QnA Maker [importa o seu conteúdo](../concepts/knowledge-base.md) numa base de conhecimentos de conjuntos de perguntas e respostas. O processo de importação extrai informações sobre a relação entre as partes do seu conteúdo estruturado e semi-estruturado para implicar relações entre os conjuntos de perguntas e respostas. Pode editar estes conjuntos de perguntas e respostas ou adicionar novos conjuntos.

O conteúdo do conjunto de perguntas e respostas inclui:
* Todas as formas alternativas da pergunta
* Etiquetas de metadados usadas para filtrar escolhas de resposta durante a pesquisa
* Instruções de acompanhamento para continuar o requinte de pesquisa

![Exemplo de pergunta e resposta com metadados](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Depois de publicar a sua base de conhecimento, uma aplicação de cliente envia a pergunta de um utilizador para o seu ponto final. O seu serviço QnA Maker processa a pergunta e responde com a melhor resposta.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Criar, gerir e publicar a um bot sem código

O portal QnA Maker fornece a experiência completa de autoria da base de conhecimento. Pode importar documentos, na sua forma atual, para a sua base de conhecimentos. Estes documentos (tais como uma FAQ, manual de produto, folha de cálculo ou página web) são convertidos em conjuntos de perguntas e respostas. Cada conjunto é analisado para instruções de seguimento e ligado a outros conjuntos. O formato de _marcação_ final suporta uma apresentação rica, incluindo imagens e links.

Assim que a sua base de conhecimentos for editada, publique a base de conhecimentos para um [bot azure Web App](https://azure.microsoft.com/services/bot-service/) em funcionamento sem escrever qualquer código. Teste o seu bot no [portal Azure](https://portal.azure.com) ou descarregue e continue o desenvolvimento.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>A qualidade e o ranking de pesquisa fornecem a melhor resposta possível

O sistema do Fabricante qna é uma abordagem de ranking em camadas. Os dados são armazenados na pesquisa Azure, que também serve como a primeira camada de ranking. Os principais resultados da pesquisa azure são então passados através do modelo de reclassificação de NLP da QnA Maker para produzir os resultados finais e pontuação de confiança.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker melhora o processo de conversação

O QnA Maker fornece solicitações multi-turn e aprendizagem ativa para ajudá-lo a melhorar os seus conjuntos básicos de perguntas e respostas.

**Solicitações de várias voltas dão-lhe** a oportunidade de ligar pares de perguntas e respostas. Esta ligação permite que a aplicação do cliente forneça uma resposta superior e fornece mais perguntas para refinar a procura de uma resposta final.

Depois de a base de conhecimento receber perguntas dos utilizadores no ponto final publicado, a QnA Maker aplica **aprendizagem ativa** a estas questões do mundo real para sugerir alterações na sua base de conhecimentos para melhorar a qualidade.

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

A QnA Maker fornece autoria, formação e publicação, juntamente com permissões de colaboração para integrar em todo o ciclo de vida de desenvolvimento.

> [!div class="mx-imgBorder"]
> ![Imagem conceptual do ciclo de desenvolvimento](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Como posso começar?

**Passo 1**: Criar um recurso QnA Maker no [portal Azure](https://portal.azure.com).

**Passo 2**: Criar uma base de conhecimento no portal [QnA Maker.](https://www.qnamaker.ai) Adicione [ficheiros e URLs](../concepts/content-types.md) para criar a base de conhecimento.

**Passo 3**: Publique a sua base de conhecimentos e teste a partir do seu ponto final personalizado utilizando [cURL ou Carteiro](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Passo 4**: A partir da sua aplicação cliente, ligue programáticamente para o ponto final da sua base de conhecimentos. A aplicação do cliente processa a resposta da JSON para mostrar a melhor resposta ao utilizador.

## <a name="next-steps"></a>Passos seguintes
A QnA Maker fornece tudo o que precisa para construir, gerir e implementar a sua base de conhecimento personalizada.

> [!div class="nextstepaction"]
> [Reveja as últimas alterações](../whats-new.md)
