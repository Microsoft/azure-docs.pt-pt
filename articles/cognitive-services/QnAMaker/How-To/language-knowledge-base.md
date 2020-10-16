---
title: Conceitos linguísticos - QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker suporta conteúdos de base de conhecimento em muitas línguas. No entanto, cada serviço QnA Maker deve ser reservado para uma única língua. A primeira base de conhecimento criada, visando um serviço específico do QnA Maker, define a linguagem desse serviço.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: b0d4250a6659996187923905955a9825a44cea42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132624"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Suporte linguístico do conteúdo base de conhecimento para o QnA Maker

O idioma para o serviço é selecionado quando cria a primeira base de conhecimento no recurso. Todas as bases de conhecimento adicionais no recurso devem estar na mesma língua.

O idioma determina a relevância dos resultados que o QnA Maker fornece em resposta às consultas do utilizador.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Uma linguagem para todas as bases de conhecimento em recursos

O Criador de FAQ permite-lhe selecionar o idioma do serviço de FAQ ao criar a primeira base de dados de conhecimento. Todas as bases de conhecimento num recurso do Criador de FAQ devem estar no mesmo idioma. Este idioma não pode ser alterado.

A criação de bases de conhecimento em diferentes idiomas num único recurso afeta negativamente a relevância dos resultados que o QnA Maker fornece em resposta às consultas do utilizador.

Reveja uma lista de [línguas apoiadas](../overview/language-support.md#languages-supported) e como as línguas [impactam a correspondência e a relevância.](#query-matching-and-relevance)

## <a name="select-language-when-creating-first-knowledge-base"></a>Selecione a linguagem ao criar a primeira base de conhecimento

A seleção linguística faz parte dos passos para criar a primeira base de conhecimento num recurso.

![Imagem de imagem do portal QnA Maker de selecionar linguagem para primeira base de conhecimento](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Combinação de consulta e relevância
O QnA Maker depende dos [analisadores de linguagem Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/language-support) para fornecer resultados.

Enquanto as capacidades de Pesquisa Cognitiva Azure estão a par para línguas suportadas, o QnA Maker tem um ranker adicional que se situa acima dos resultados de pesquisa do Azure. Neste modelo ranker, usamos algumas características semânticas e baseadas em palavras nas seguintes línguas.

|Línguas com ranker adicional|
|--|
|Chinês|
|Checo|
|Neerlandês|
|Inglês|
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

Este ranking adicional é um trabalho interno do ranker do QnA Maker.

## <a name="verify-language"></a>Verificar a língua

Pode verificar o idioma do seu recurso QnA Maker a partir da página de definições de serviço no QnA Maker.

![Imagem de imagem do portal do QnA Maker da página de definições de serviço](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar para uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
