---
title: Suporte de idiomas - QnA Maker
titleSuffix: Azure Cognitive Services
description: A linguagem de uma base de dados de conhecimento afeta a capacidade do QnA Maker de extrair perguntas e respostas de fontes automaticamente, bem como a relevância dos resultados QnA Maker fornece em resposta a consultas do usuário. Uma lista de cultura, idiomas suportados pelo QnA Maker para sua base de dados de conhecimento. Não misture idiomas na mesma base de dados de conhecimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066670"
---
# <a name="language-support-for-qna-maker"></a>Suporte a idiomas para QnA Maker

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

## <a name="primary-language-detection"></a>Detecção de idioma primário

O idioma principal usado para detecção é definido para o recurso QnA Maker e todas as bases de dados de conhecimento criadas nesse recurso, quando o primeiro documento ou URL é adicionado à primeira base de dados de conhecimento. O idioma não pode ser alterado. 

Se o usuário planeja oferecer suporte a vários idiomas, ele precisa ter um novo recurso QnA Maker para cada idioma. Saiba como [criar uma base de dados de conhecimento QnA Maker baseada em linguagem](../how-to/language-knowledge-base.md).  

Verifique o idioma principal com as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).  
1. Procure e selecione o Azure Search recurso criado como parte do recurso de QnA Maker. O nome do recurso de Azure Search começará com o mesmo nome que o recurso de QnA Maker e terá o **serviço de pesquisa**de tipo. Tenha em mente, apenas um recurso de QnA Maker pode ser vinculado a um recurso de Azure Search.
1. Na página **visão geral** do recurso de pesquisa, selecione **índices**. 
1. Selecione o índice **testkb** .
1. Selecione a guia **campos** . 
1. Exiba a coluna **analisador** para os campos **perguntas** e **resposta** . 


## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
Depende do QnA Maker [analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support) na pesquisa do Azure para fornecer os resultados. A classificação de recursos especiais estão disponíveis para En-* idiomas que permitem a relevância melhor.

Enquanto os recursos de pesquisa do Azure estão no mesmo nível para idiomas com suporte, a ferramenta QnA Maker tem um adicional classificador que fica acima os resultados da pesquisa do Azure. Neste modelo de classificação, usamos alguns recursos semânticos e baseados em palavras especiais em en-*, que ainda não estão disponíveis para outros idiomas. Nós não disponibilizamos esses recursos, pois fazem parte do trabalho interno do classificador de QnA Maker. 

QnA Maker [detecta automaticamente o idioma da base de dados de conhecimento](#primary-language-detection) durante a criação e define o analisador adequadamente. Pode criar bases de dados de conhecimento nos seguintes idiomas. 

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
