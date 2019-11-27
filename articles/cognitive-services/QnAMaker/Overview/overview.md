---
title: O que é o serviço QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker é um serviço de NLP baseado em nuvem que cria facilmente uma camada de conversação natural sobre seus dados. Ele pode ser usado para encontrar a resposta mais apropriada para qualquer entrada de idioma natural, de sua base de dados de conhecimento personalizada (KB) de informações.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 944ddb7f83a4d10861e5a16dbc69b8f9e4dabfe0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422682"
---
# <a name="what-is-the-qna-maker-service"></a>O que é o serviço de QnA Maker?

QnA Maker é um serviço de NLP (processamento de linguagem natural) baseado em nuvem que cria facilmente uma camada de conversação natural sobre seus dados. Ele pode ser usado para encontrar a resposta mais apropriada para qualquer entrada de idioma natural, de sua base de dados de conhecimento personalizada (KB) de informações.

Um aplicativo cliente para QnA Maker é qualquer aplicativo de conversa que se comunique com um usuário em linguagem natural para responder a uma pergunta. Alguns exemplos de aplicações cliente incluem aplicações de redes sociais, bots de chat e aplicações para ambiente de trabalho com fala ativada.

## <a name="when-to-use-qna-maker"></a>Quando usar QnA Maker

* **Quando você tiver informações estáticas** , use QnA Maker quando tiver informações estáticas em sua base de dados de conhecimento de respostas. Essa base de dados de conhecimento é personalizada para suas necessidades, que você criou com documentos como [PDFs e URLs](../concepts/data-sources-supported.md).
* **Quando você deseja fornecer a mesma resposta a uma solicitação, pergunta ou comando** – quando usuários diferentes enviam a mesma pergunta, a mesma resposta é retornada. 
* **Quando você deseja filtrar informações estáticas com base nas metainformações** – Adicione marcas de [metadados](../how-to/metadata-generateanswer-usage.md) para fornecer opções de filtragem adicionais relevantes para os usuários e as informações do aplicativo cliente. Informações de metadados comuns incluem [Chit](../how-to/chit-chat-knowledge-base.md), tipo de conteúdo ou formato, finalidade de conteúdo e atualização de conteúdo.
* **Quando você deseja gerenciar uma conversa de bot que inclui informações estáticas** , sua base de dados de conhecimento usa o texto ou o comando de conversa de um usuário e responde a ele. Se a resposta fizer parte de um fluxo de conversa predeterminado, representado na sua base de dados de conhecimento com o [contexto de várias](../how-to/multiturn-conversation.md)transformações, o bot poderá facilmente fornecer esse fluxo.  

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Usar QnA Maker base de dados de conhecimento em um bot de chat

Quando uma base de dados de conhecimento QnA Maker é publicada, um aplicativo cliente envia uma pergunta para o ponto de extremidade da base de dados de conhecimento e recebe os resultados como uma resposta JSON. Um aplicativo cliente comum para QnA Maker é um bot de chat.

![Faça uma pergunta a um bot e obtenha respostas do conteúdo da base de dados de conhecimento](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Passo|Ação|
|:--|:--|
|1|O aplicativo cliente envia a _pergunta_ do usuário (texto em suas próprias palavras), "como fazer atualizar por meio de programação minha base de dados de conhecimento?" para o ponto de extremidade da base de dados de conhecimento.|
|2|QnA Maker usa a base de dados de conhecimento treinada para fornecer a resposta correta e quaisquer avisos de acompanhamento que possam ser usados para refinar a pesquisa para obter a melhor resposta. QnA Maker retorna uma resposta formatada em JSON.|
|3|O aplicativo cliente usa a resposta JSON para tomar decisões sobre como continuar a conversa. Essas decisões podem incluir mostrar a resposta principal e apresentar mais opções para refinar a pesquisa para obter a melhor resposta. |
|||

## <a name="what-is-a-knowledge-base"></a>O que é uma base de dados de conhecimento? 

QnA Maker [importa seu conteúdo](../concepts/data-sources-supported.md) para uma base de dados de conhecimento de conjuntos de perguntas e respostas. O processo de importação extrai informações sobre a relação entre as partes de seu conteúdo estruturado e semiestruturado para implicar relações entre os conjuntos de perguntas e respostas. Você pode editar esses conjuntos de perguntas e respostas ou adicionar novos conjuntos.  

O conteúdo do conjunto de perguntas e respostas inclui:
* Todas as formas alternativas da pergunta
* Marcas de metadados usadas para filtrar as opções de resposta durante a pesquisa
* Avisos de acompanhamento para continuar o refinamento da pesquisa

![Exemplo de pergunta e resposta com metadados](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Depois de publicar sua base de dados de conhecimento, um aplicativo cliente envia uma pergunta do usuário para seu ponto de extremidade. O serviço de QnA Maker processa a pergunta e responde com a melhor resposta. 

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Criar, gerenciar e publicar em um bot sem código

O portal de QnA Maker fornece a experiência de criação da base de dados de conhecimento completa. Você pode importar documentos, em sua forma atual, para sua base de dados de conhecimento. Esses documentos (como perguntas frequentes, manual de produto, planilha ou página da Web) são convertidos em conjuntos de perguntas e respostas. Cada conjunto é analisado para solicitações de acompanhamento e conectado a outros conjuntos. O formato de _redução_ final dá suporte à apresentação avançada, incluindo imagens e links. 

Depois que sua base de dados de conhecimento for editada, publique a base de dados de conhecimento em um [bot de aplicativo Web do Azure](https://azure.microsoft.com/services/bot-service/) em funcionamento sem escrever nenhum código. Teste o bot no [portal do Azure](https://portal.azure.com) ou baixe e continue o desenvolvimento. 

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>A qualidade de pesquisa e a classificação fornecem a melhor resposta possível

O sistema de QnA Maker é uma abordagem de classificação em camadas. Os dados são armazenados no Azure Search, que também serve como a primeira camada de classificação. Os principais resultados da pesquisa do Azure são passados pelo modelo de reclassificação de NLP de QnA Maker para produzir os resultados finais e a pontuação de confiança.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker melhora o processo de conversa

QnA Maker fornece prompts de múltipla ativação e aprendizado ativo para ajudá-lo a melhorar seus conjuntos básicos de perguntas e respostas. 

As **solicitações de múltipla ativação** oferecem a oportunidade de conectar pares de perguntas e respostas. Essa conexão permite que o aplicativo cliente forneça uma resposta principal e forneça mais perguntas para refinar a pesquisa para uma resposta final. 

Depois que a base de dados de conhecimento receber perguntas de usuários no ponto de extremidade publicado, QnA Maker aplicará o **aprendizado ativo** a essas perguntas do mundo real para sugerir alterações na sua base de dados de conhecimento para melhorar a qualidade. 

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

O QnA Maker fornece criação, treinamento e publicação junto com as permissões de colaboração para integrar o ciclo de vida de desenvolvimento completo. 

## <a name="how-do-i-start"></a>O que devo fazer para começar?

**Etapa 1**: criar um recurso de QnA Maker no [portal do Azure](https://portal.azure.com). 

**Etapa 2**: criar uma base de dados de conhecimento no portal de [QnA Maker](https://www.qnamaker.ai) . Adicione [arquivos e URLs](../concepts/data-sources-supported.md) para criar a base de dados de conhecimento.  

**Etapa 3**: publique sua base de dados de conhecimento e teste de seu ponto de extremidade personalizado usando [ondulação ou postmaster](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md). 

**Etapa 4**: do seu aplicativo cliente, chame programaticamente o ponto de extremidade da base de dados de conhecimento. O aplicativo cliente processa a resposta JSON para mostrar a melhor resposta ao usuário.  

## <a name="next-steps"></a>Passos seguintes
QnA Maker fornece tudo o que você precisa para criar, gerenciar e implantar sua base de dados de conhecimento personalizada. 

> [!div class="nextstepaction"]
> [Examine as alterações mais recentes](../whats-new.md)
