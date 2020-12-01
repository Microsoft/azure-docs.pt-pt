---
title: Fontes de dados e tipos de conteúdo - QnA Maker
description: Saiba como importar pares de perguntas e respostas de fontes de dados e tipos de conteúdo suportados, que incluem muitos documentos estruturados padrão como PDF, DOCX e TXT - QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a6488d2c7611513528ff02d4e620124763912730
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353260"
---
# <a name="importing-from-data-sources"></a>Importar a partir de origens de dados

Uma base de conhecimento consiste em pares de perguntas e respostas trazidos por URLs públicos e ficheiros.

## <a name="data-source-locations"></a>Locais de origem de dados

O conteúdo é trazido para uma base de conhecimento a partir de uma fonte de dados. As localizações de origem de dados são **URLs ou ficheiros públicos,** que não requerem autenticação.

[Os ficheiros SharePoint](../how-to/add-sharepoint-datasources.md), protegidos com autenticação, são a exceção. Os recursos do SharePoint devem ser ficheiros, não páginas web. Se o URL terminar com uma extensão Web, como . ASPX, não importará do SharePoint para o Criador de FAQ.

## <a name="chit-chat-content"></a>Conteúdo de chit-chat

O conjunto de conteúdos de chit-chat é oferecido como uma fonte completa de dados de conteúdo em vários idiomas e estilos de conversação. Este pode ser um ponto de partida para a personalidade do seu bot, e irá poupar-lhe o tempo e o custo de escrevê-los do zero. Aprenda [a adicionar conteúdo de chit-chat](../how-to/chit-chat-knowledge-base.md) à sua base de conhecimentos.

## <a name="structured-data-format-through-import"></a>Formato de dados estruturado através da importação

Importar uma base de conhecimento substitui o conteúdo da base de conhecimento existente. A importação requer um ficheiro estruturado `.tsv` que contenha perguntas e respostas. Esta informação ajuda a QnA Maker a agrupar os pares de respostas de perguntas e atribuí-los a uma determinada fonte de dados.

| Pergunta  | Resposta  | Origem| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato multi-turn estruturado através da importação

Pode criar as conversas multi-turn num `.tsv` formato de ficheiro. O formato proporciona-lhe a capacidade de criar as conversas multi-turn analisando registos de chat anteriores (com outros processos, não usando o QnA Maker), e depois criar o `.tsv` ficheiro através da automatização. Importe o ficheiro para substituir a base de conhecimento existente.

> [!div class="mx-imgBorder"]
> ![Modelo conceptual de 3 níveis de pergunta multi-volta](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A coluna para uma curva multi-turn `.tsv` , específica para multi-voltas é **Prompts**. Um exemplo `.tsv` , mostrado no Excel, mostra as informações que incluem para definir as crianças multi-volta:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

O **displayOrder** é numérico e o **displayText** é texto que não deve incluir o marcador.

> [!div class="mx-imgBorder"]
> ![Exemplo de pergunta multi-volta como mostrado no Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportação como exemplo

Se não tiver a certeza de como representar o seu par QnA no `.tsv` ficheiro:
* Use este [exemplo transferível do GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Ou criar o par no portal QnA Maker, salvar e, em seguida, exportar a base de conhecimento para um exemplo de como representar o par.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipos de conteúdo de documentos que pode adicionar a uma base de conhecimento
Os tipos de conteúdo incluem muitos documentos estruturados padrão, tais como PDF, DOC e TXT.

### <a name="file-and-url-data-types"></a>Tipos de dados de ficheiros e URL

A tabela abaixo resume os tipos de conteúdos e formatos de ficheiros suportados pela QnA Maker.

|Tipo de Fonte|Tipo de Conteúdo| Exemplos|
|--|--|--|
|URL|FAQs<br> (Plano, com secções ou com página inicial de tópicos)<br>Páginas de suporte <br> (Artigos de como fazer, artigos de resolução de problemas, etc.)|[FaQ simples,](../troubleshooting.md) <br>[FAQ com links,](https://www.microsoft.com/en-us/software-download/faq)<br> [FAQ com página inicial de tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de apoio](./best-practices.md)|
|PDF / DOC|Perguntas frequentes,<br> Manual do Produto,<br> Brochuras,<br> Papel,<br> Política flyer,<br> Guia de apoio,<br> QnA estruturado,<br> etc.|**Sem multi-volta**<br>[QnA.docxestruturados, ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)<br> [Manual.pdfde produto de amostra, ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)<br> [semi-structured.docxde amostras, ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)<br> [Amostra paper.pdfbrancas, ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)<br><br>**Multi-volta:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Benefícios Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Benefícios Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Arquivo QnA estruturado<br> (incluindo o apoio ao RTF, HTML)|**Sem multi-volta:**<br>[Amostra QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Multi-volta:**<br>[FAQ.xlssimples estruturados ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsdo portátil de superfície ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|*TXT/TSV|Arquivo QnA estruturado|[Amostra chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se necessitar de autenticação para a sua fonte de dados, considere os seguintes métodos para colocar esse conteúdo no QnA Maker:

* Descarregue o ficheiro manualmente e importe para o QnA Maker
* Adicione o ficheiro a partir da localização autenticada do [Sharepoint](../how-to/add-sharepoint-datasources.md)

### <a name="url-content"></a>Conteúdo de URL

Dois tipos de documentos podem ser importados via **URL** no QnA Maker:

* FAQ URLs
* UrLs de apoio

Cada tipo indica um formato esperado.

### <a name="file-based-content"></a>Conteúdo baseado em ficheiros

Pode adicionar ficheiros a uma base de conhecimento de uma fonte pública, ou ao seu sistema de ficheiros local, no [portal QnA Maker](https://www.qnamaker.ai).

### <a name="content-format-guidelines"></a>Diretrizes de formatos de conteúdo

Saiba mais sobre as [diretrizes](../reference-document-format-guidelines.md) de formato para os diferentes ficheiros.

## <a name="next-steps"></a>Passos seguintes

Compreenda que informação é armazenada num [par de perguntas e respostas (QnA).](question-answer-set.md)