---
title: Conceitos linguísticos - QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker suporta o conteúdo da base de conhecimento em muitas línguas. No entanto, cada serviço QnA Maker deve ser reservado para uma única língua. A primeira base de conhecimento criada, visando um determinado serviço QnA Maker, define a linguagem desse serviço.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220635"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Suporte linguístico do conteúdo da base de conhecimento para o Fabricante qnA

A linguagem para o serviço é selecionada quando cria a primeira base de conhecimentos no recurso. Todas as bases de conhecimento adicionais do recurso devem estar na mesma língua.

O idioma determina a relevância dos resultados que o QnA Maker fornece em resposta às consultas dos utilizadores.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Uma linguagem para todas as bases de conhecimento em recursos

O QnA Maker permite-lhe selecionar o idioma para o seu serviço QnA, ao mesmo tempo que cria a primeira base de conhecimentos. Para todas as bases de conhecimento de um recurso qnA Maker, todos eles devem estar na mesma língua. Esta linguagem não pode ser mudada.

Criar bases de conhecimento em diferentes línguas num recurso afeta negativamente a relevância dos resultados que o QnA Maker fornece em resposta às consultas dos utilizadores.

Reveja uma lista de [línguas suportadas](../overview/language-support.md#languages-supported) e como as línguas impactam [a correspondência e a relevância.](#query-matching-and-relevance)

## <a name="select-language-when-creating-first-knowledge-base"></a>Selecione o idioma ao criar a primeira base de conhecimento

A seleção de idiomas faz parte dos passos para criar a primeira base de conhecimento num recurso.

![QnA Maker imagem do portal de seleção de linguagem para primeira base de conhecimento](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
O Fabricante QnA depende dos [analisadores](https://docs.microsoft.com/rest/api/searchservice/language-support) de linguagem de pesquisa cognitiva Azure para fornecer resultados.

Enquanto as capacidades de Pesquisa Cognitiva Azure estão a par para idiomas suportados, o QnA Maker tem um ranking adicional que se situa acima dos resultados de pesquisa do Azure. Neste modelo de classificação, usamos algumas características semânticas e baseadas em palavras nas seguintes línguas.

|Línguas com classificação adicional|
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

Este ranking adicional é um trabalho interno do ranker do Fabricante qna.

## <a name="verify-language"></a>Verificar a linguagem

Pode verificar a linguagem do seu recurso QnA Maker a partir da página de definições de serviço no Fabricante qnA.

![Imagem de portal do QnA Maker da página de definições do serviço](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar para uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
