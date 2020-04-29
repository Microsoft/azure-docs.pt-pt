---
title: Linhas de formato de documentode importação - QnA Maker
description: Compreenda como os tipos de URLs são usados para importar e criar pares QnA.
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 799e85e2200d3caa29c9b76bc57a62fc883d246d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804338"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Diretrizes de formato para documentos e URLs importados

Reveja estas diretrizes de formatação para obter os melhores resultados para o seu conteúdo.

## <a name="formatting-considerations"></a>Considerações de formatação

Depois de importar um ficheiro ou URL, a QnA Maker converte e armazena o seu conteúdo no [formato de marcação](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas `\n\n`linhas no texto, tais como . Um conhecimento do formato de marcação ajuda-o a compreender o conteúdo convertido e a gerir o conteúdo da base de conhecimentos.

Se adicionar ou editar o seu conteúdo diretamente na sua base de conhecimentos, use formatação de **marcação** para criar conteúdo de texto rico ou altere o conteúdo do formato de marcação que já está na resposta. O QnA Maker suporta grande parte do formato de marcação para trazer ao seu conteúdo capacidades de texto ricas. No entanto, a aplicação do cliente, como um chat bot, não pode suportar o mesmo conjunto de formatos de marcação. É importante testar a exibição de respostas da aplicação do cliente.

Consulte uma lista completa de [tipos e exemplos](./Concepts/content-types.md#file-and-url-data-types)de conteúdo .

## <a name="basic-document-formatting"></a>Formatação de documento básico

QnA Maker identifica secções e subsecções e relações no ficheiro com base em pistas visuais como:

* tamanho do tipo de letra:
* estilo de letra
* numeração
* cores

## <a name="product-manuals"></a>Manuais de produtos

Um manual é tipicamente material de orientação que acompanha um produto. Ajuda o utilizador a configurar, utilizar, manter e resolver problemas com o produto. Quando o Fabricante qnA processa um manual, extrai as rubricas e subposições como perguntas e o conteúdo subsequente como respostas. Veja um exemplo [aqui.](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)

Abaixo está um exemplo de um manual com uma página de índice, e conteúdo hierárquico

 ![Exemplo manual do produto para uma base de conhecimento](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A extração funciona melhor em manuais que têm uma tabela de conteúdos e/ou uma página de índice, e uma estrutura clara com rubricas hierárquicas.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros ficheiros

Muitos outros tipos de documentos também podem ser processados para gerar pares de QA, desde que tenham uma estrutura e layout claros. Estes incluem: Brochuras, diretrizes, relatórios, artigos brancos, artigos científicos, políticas, livros, etc. Veja um exemplo [aqui.](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)

Abaixo está um exemplo de um médico semi-estruturado, sem um índice:

 ![Doc semi-estruturado de armazenamento Azure Blob](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Documento Estruturado qna

O formato de respostas estruturadas em ficheiros DOC, é sob a forma de perguntas e respostas alternadas por linha, uma pergunta por linha seguida pela sua resposta na seguinte linha, como mostrado abaixo:

```text
Question1

Answer1

Question2

Answer2
```

Abaixo está um exemplo de um documento de palavra SNA estruturado:

 ![Exemplo de documento estruturado da QnA para uma base de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Ficheiros *TXT,* *TSV* e *XLS* estruturados

Os QnAs sob a forma de ficheiros estruturados *.txt,* *.tsv* ou *.xls* também podem ser enviados para o Fabricante qnA para criar ou aumentar uma base de conhecimento.  Estes podem ser texto simples, ou podem ter conteúdo em RTF ou HTML.

| Pergunta  | Resposta  | Metadados (1 tecla: 1 valor) |
|-----------|---------|-------------------------|
| Pergunta1 | Resposta1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta 2 | Resposta2 |      `Key:Value`           |

Quaisquer colunas adicionais no ficheiro fonte são ignoradas.

### <a name="example-of-structured-excel-file"></a>Exemplo de ficheiro Excel estruturado

Abaixo está um exemplo de um ficheiro QnA *.xls* estruturado, com conteúdo HTML:

 ![QnA estruturado excel exemplo para uma base de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemplo de perguntas alternativas para resposta única no ficheiro Excel

Abaixo está um exemplo de um ficheiro QnA *.xls* estruturado, com várias perguntas alternativas para uma única resposta:

 ![Exemplo de perguntas alternativas para resposta única no ficheiro Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Após a importação do ficheiro, o par de perguntas e respostas encontra-se na base de conhecimentos, como mostrado abaixo:

 ![Screenshot de perguntas alternativas para resposta única importada para base de conhecimento](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formato de dados estruturado através da importação

Importar uma base de conhecimentos substitui o conteúdo da base de conhecimentos existente. A importação requer um ficheiro .tsv estruturado que contenha informações sobre fonte de dados. Esta informação ajuda o QnA Maker a agrupar os pares de resposta seletiva e aatribuí-los a uma determinada fonte de dados.

| Pergunta  | Resposta  | Origem| Metadados (1 tecla: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta 2 | Resposta2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Formatação de documento de várias voltas

* Utilize rubricas e sub-rubricas para denotar a hierarquia. Por exemplo, pode h1 para denotar o QnA e h2 dos pais para denotar o QnA que deve ser tomado como solicitado. Utilize o pequeno tamanho da cabeça para denotar a hierarquia subsequente. Não utilize o estilo, a cor ou algum outro mecanismo para implicar estrutura no seu documento, o Fabricante QnA não extrairá os avisos de várias voltas.
* O primeiro carácter da direção deve ser capitalizado.
* Não termine um título com `?`um ponto de interrogação, .

**Documentos da amostra:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Benefícios Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Benefícios Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Passos seguintes

Ver uma lista completa de [tipos e exemplos](./Concepts/content-types.md#file-and-url-data-types) de conteúdo