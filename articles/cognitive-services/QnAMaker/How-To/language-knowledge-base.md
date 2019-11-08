---
title: Conceitos de linguagem-QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker dá suporte ao conteúdo da base de dados de conhecimento em muitos idiomas. No entanto, cada serviço de QnA Maker deve ser reservado para uma única linguagem. A primeira base de dados de conhecimento criada, direcionando um determinado serviço de QnA Maker, define o idioma desse serviço.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: a818d84bf3df712190ccc6c1721c26933a9fbcdb
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793927"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Suporte de idioma do conteúdo da base de dados de conhecimento para QnA Maker

Idioma do serviço é selecionado quando você cria a primeira base de dados de conhecimento no recurso. Todas as bases de dados de conhecimento adicionais no recurso devem estar no mesmo idioma. 

O idioma determina a relevância dos resultados QnA Maker fornece em resposta a consultas de usuário.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Um idioma para todas as bases de dados de conhecimento no recurso

QnA Maker permite que você selecione o idioma do serviço QnA ao criar a primeira base de dados de conhecimento. Para todas as bases de dados de conhecimento em um recurso QnA Maker, todas elas devem estar no mesmo idioma. Este idioma não pode ser alterado.

A criação de bases de dados de conhecimento em diferentes idiomas em um recurso afeta negativamente a relevância dos resultados QnA Maker fornece em resposta a consultas de usuário.

Examine uma lista de [idiomas com suporte](../overview/language-support.md#languages-supported) e como as linguagens afetam a [correspondência e a relevância](#query-matching-and-relevance). 

## <a name="select-language-when-creating-first-knowledge-base"></a>Selecione o idioma ao criar a primeira base de dados de conhecimento

A seleção de idioma faz parte das etapas para criar a primeira base de dados de conhecimento em um recurso. 

![Captura de tela do portal do QnA Maker de seleção da linguagem para a primeira base de conhecimento](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
QnA Maker depende dos [analisadores de linguagem do Azure pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/language-support) para fornecer resultados. 

Embora os recursos de Pesquisa Cognitiva do Azure estejam em par para os idiomas com suporte, QnA Maker tem um classificador adicional que fica acima dos resultados do Azure Search. Neste modelo de classificação, usamos alguns recursos semânticos e baseados em palavras especiais nos idiomas a seguir. 

|Idiomas com classificação adicional|
|--|
|Chinês|
|Tcheco|
|Holandês|
|Português|
|Francês|
|Alemão|
|Húngaro|
|Italiano|
|Japonês|
|Coreano|
|Polaco|
|Português|
|Espanhol|
|Sueco|

Essa classificação adicional é um trabalho interno do classificador de QnA Maker.

## <a name="verify-language"></a>Verificar idioma

Você pode verificar o idioma do QnA Maker recurso na página Configurações de serviço no QnA Maker.

![Captura de tela do portal do QnA Maker da página de configurações de serviço](../media/language-support/language-knowledge-base.png) 


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um bot QnA com o serviço de bot do Azure](../Tutorials/create-qna-bot.md)
