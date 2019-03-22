---
title: Suporte de idiomas - QnA Maker
titleSuffix: Azure Cognitive Services
description: O idioma de uma base de dados de conhecimento afeta a capacidade de QnA Maker de extração automática perguntas e respostas de fontes, bem como a relevância dos resultados que do QnA Maker oferece em resposta a consultas do utilizador. Uma lista de cultura, idiomas suportados pelo QnA Maker para sua base de dados de conhecimento. Não misture idiomas na mesma base de dados de conhecimento.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 820a9ae0df91fba8cf00764428867bec6196841a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335866"
---
# <a name="language-support-for-qna-maker"></a>Suporte de idiomas para o QnA Maker

O idioma de uma base de dados de conhecimento afeta a capacidade de QnA Maker de extração automática perguntas e respostas partir [origens](../Concepts/data-sources-supported.md), bem como a relevância dos resultados do QnA Maker oferece em resposta a consultas do utilizador.

## <a name="auto-extraction"></a>Extração automática
A ferramenta QnA Maker suporta a extração de pergunta/resposta em qualquer página de idioma, mas a eficácia da extração é muito superior para os seguintes idiomas, como o QnA Maker utiliza as palavras-chave para identificar a perguntas.

|Idiomas suportados| Região|
|-----|----|
|Português|EN-*|
|Francês|FR-*|
|Italiano|isso-*|
|Alemão|Alemanha-*|
|Espanhol|es-*|

## <a name="primary-language-detection"></a>Deteção de idioma primária

O idioma principal usado para a deteção está definido para o recurso do QnA Maker e todas as bases de dados de conhecimento criados esse recurso, quando o primeiro documento ou o URL é adicionado para a primeira base de dados de conhecimento. Não é possível alterar o idioma. 

Se o utilizador planos de suporte a vários idiomas, terá de ter um novo recurso do QnA Maker para cada idioma. Saiba como [criar uma base de dados de conhecimento do QnA Maker baseadas em linguagem](/how-to/language-knowledge-base.md).  

Verifique se o idioma principal com os seguintes passos:

1. Inicie sessão no [portal do Azure](http://portal.azure.com).  
1. Procure e selecione o recurso de pesquisa do Azure criado como parte do seu recurso do QnA Maker. O nome do recurso de pesquisa do Azure irá começar com o mesmo nome que o recurso do QnA Maker e terá o tipo **serviço de pesquisa**. 
1. Partir do **descrição geral** página do recurso de pesquisa, selecione **índices**. 
1. Selecione o **testkb** índice.
1. Selecione o **campos** separador. 
1. Ver os **analisador** coluna para o **perguntas** e **resposta** campos. 


## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
Depende do QnA Maker [analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support) na pesquisa do Azure para fornecer os resultados. A classificação de recursos especiais estão disponíveis para En-* idiomas que permitem a relevância melhor.

Enquanto os recursos de pesquisa do Azure estão no mesmo nível para idiomas com suporte, a ferramenta QnA Maker tem um adicional classificador que fica acima os resultados da pesquisa do Azure. Nesse modelo classificador, podemos usar alguns recursos especiais de semânticos e baseada em palavras em en-*, que ainda não estão disponíveis para outros idiomas. Nós não disponibilizar estas funcionalidades, como fazem parte do funcionamento interno classificador do QnA Maker de. 

A ferramenta QnA Maker [Deteta automaticamente o idioma da base de dados de conhecimento](#primary-language-detection) durante a criação e define o analisador em conformidade. Pode criar bases de dados de conhecimento nos seguintes idiomas. 

|Idiomas suportados|
|-----|
|Árabe|
|Arménio|
Bengali|
|Basco (Basco)|
|Búlgaro|
|Catalão|
|Chinese_Simplified|
|Chinese_Traditional|
|Croata|
|Checo|
|Dinamarquês|
|Neerlandês|
|Português|
|Estónio|
|Finlandês|
|Francês|
|Galego|
|Alemão|
|Grego|
|Guzarate|
|Hebraico|
|Hindi|
|Húngaro|
|Islandês|
|Indonésio|
|Irlandês|
|Italiano|
|Japonês|
|Canarim|
|Coreano|
|Letão|
|Lituano|
|Malayalam|
|Malaio|
|Norueguês|
|Polaco|
|Português|
|Punjabi|
|Romeno|
|Russo|
|Serbian_Cyrillic|
|Serbian_Latin|
|Eslovaco|
|Esloveno|
|Espanhol|
|Sueco|
|Tamil|
|Télego|
|Tailandês|
|Turco|
|Ucraniano|
|Urdu|
|Vietnamita|
