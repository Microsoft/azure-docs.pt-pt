---
title: Tipos de conteúdo - QnA Maker
description: Os tipos de conteúdo incluem muitos documentos estruturados padrão, tais como PDF, DOCX e TXT.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 50924ce035d9a7dc7778cf45668dc993ee5486e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776991"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipos de conteúdo de documentos que pode adicionar a uma base de conhecimento
Os tipos de conteúdo incluem muitos documentos estruturados padrão, tais como PDF, DOC e TXT.

## <a name="file-and-url-data-types"></a>Tipos de dados de ficheiros e URL

A tabela abaixo resume os tipos de conteúdos e formatos de ficheiros suportados pela QnA Maker.

|Tipo de Fonte|Tipo de Conteúdo| Exemplos|
|--|--|--|
|URL|FAQs<br> (Plano, com secções ou com página inicial de tópicos)<br>Páginas de suporte <br> (Artigos de como fazer, artigos de resolução de problemas, etc.)|[FaQ simples,](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[FAQ com links,](https://www.microsoft.com/en-us/software-download/faq)<br> [FAQ com página inicial de tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de apoio](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|Perguntas frequentes,<br> Manual do Produto,<br> Brochuras,<br> Papel,<br> Política flyer,<br> Guia de apoio,<br> QnA estruturado,<br> etc.|**Sem multi-volta**<br>[QnA.docxestruturados, ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)<br> [Manual.pdfde produto de amostra, ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)<br> [semi-structured.docxde amostras, ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)<br> [Amostra paper.pdfbrancas, ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)<br><br>**Multi-volta:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Benefícios Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Benefícios Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Arquivo QnA estruturado<br> (incluindo o apoio ao RTF, HTML)|**Sem multi-volta:**<br>[Amostra QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Multi-volta:**<br>[FAQ.xlssimples estruturados ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsdo portátil de superfície ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|*TXT/TSV|Arquivo QnA estruturado|[Amostra chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se necessitar de autenticação para a sua fonte de dados, considere os seguintes métodos para colocar esse conteúdo no QnA Maker:

* Descarregue o ficheiro manualmente e importe-o para o QnA Maker
* Adicionar ficheiro a partir da localização autenticada do [Sharepoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Conteúdo de URL

Dois tipos de documentos podem ser importados via **URL** no QnA Maker:

* FAQ URLs
* UrLs de apoio

Cada tipo indica um formato esperado.

## <a name="file-based-content"></a>Conteúdo baseado em ficheiros

Pode adicionar ficheiros a uma base de conhecimento de uma fonte pública, ou ao seu sistema de ficheiros local, no [portal QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Diretrizes de formatos de conteúdo

Saiba mais sobre as [diretrizes](../reference-document-format-guidelines.md) de formato para os diferentes ficheiros.

## <a name="next-steps"></a>Passos seguintes

Compreenda que informação é armazenada num [par de perguntas e respostas (QnA).](question-answer-set.md)
