---
title: Tipos de conteúdo - QnA Maker
description: Os tipos de conteúdo incluem muitos documentos estruturados padrão, tais como PDF, DOC e TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650202"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipos de conteúdo de documentos que pode adicionar a uma base de conhecimento
Os tipos de conteúdo incluem muitos documentos estruturados padrão, tais como PDF, DOC e TXT.

## <a name="file-and-url-data-types"></a>Tipos de dados de ficheiros e URL

A tabela abaixo resume os tipos de conteúdo e formatos de ficheiros que são suportados pelo QnA Maker.

|Tipo de Fonte|Tipo de Conteúdo| Exemplos|
|--|--|--|
|do IdP|FAQs<br> (Plano, com secções ou com uma página inicial de tópicos)<br>Páginas de suporte <br> (Página única como obter artigos, artigos de resolução de problemas, etc.)|[FaQ simples,](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[FAQ com links,](https://www.microsoft.com/en-us/software-download/faq)<br> [FAQ com página inicial de tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de apoio](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|Perguntas frequentes,<br> Manual do Produto,<br> Brochuras,<br> Papel,<br> Política de Panfleto,<br> Guia de apoio,<br> QnA estruturado,<br> etc.|**Sem multi-volta**<br>[QnA.doc estruturado,](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)<br> [Manual do Produto da Amostra.pdf,](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)<br> [Amostra semi-estruturada.doc,](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)<br> [Amostra de papel branco.pdf,](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)<br><br>**Volta supor:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Benefícios Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Benefícios Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Ficheiro QnA estruturado<br> (incluindo RTF, suporte HTML)|[Amostra QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Ficheiro QnA estruturado|[Amostra chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se necessitar de autenticação para a sua fonte de dados, considere os seguintes métodos para obter esse conteúdo no QnA Maker:

* Descarregue o ficheiro manualmente e importe para o QnA Maker
* Adicionar ficheiro a partir da localização autenticada do [Sharepoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Conteúdo de URL

Dois tipos de documentos podem ser importados através de **URL** no Fabricante qnA:

* FAQ URLs
* URLs de suporte

Cada tipo indica um formato esperado.

## <a name="file-based-content"></a>Conteúdo baseado em ficheiros

Pode adicionar ficheiros a uma base de conhecimento a partir de uma fonte pública, ou ao seu sistema de ficheiros local, no [portal QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Diretrizes de formato de conteúdo

Saiba mais sobre as diretrizes de [formato](../reference-document-format-guidelines.md) para os diferentes ficheiros.

## <a name="next-steps"></a>Passos seguintes

Compreenda que informações são armazenadas num conjunto de [perguntas e respostas (QnA).](question-answer-set.md)