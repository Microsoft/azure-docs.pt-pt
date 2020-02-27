---
title: Linhas de formato de documentode importação - QnA Maker
description: Compreenda como os tipos de URLs são usados para importar e criar conjuntos qnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651841"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Diretrizes de formato para documentos e URLs importados

Reveja estas diretrizes de formatação para obter os melhores resultados para o seu conteúdo.

## <a name="formatting-considerations"></a>Considerações sobre a formatação

Depois de importar um ficheiro ou URL, a QnA Maker converte e armazena o seu conteúdo no [formato de marcação](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas linhas no texto, como `\n\n`. Um conhecimento do formato de marcação ajuda-o a compreender o conteúdo convertido e a gerir o conteúdo da base de conhecimentos.

Se adicionar ou editar o seu conteúdo diretamente na sua base de conhecimentos, use formatação de **marcação** para criar conteúdo de texto rico ou altere o conteúdo do formato de marcação que já está na resposta. O QnA Maker suporta grande parte do formato de marcação para trazer ao seu conteúdo capacidades de texto ricas. No entanto, a aplicação do cliente, como um chat bot, não pode suportar o mesmo conjunto de formatos de marcação. É importante testar a exibição de respostas da aplicação do cliente.

## <a name="basic-document-formatting"></a>Formatação de documento básico

QnA Maker identifica secções e subsecções e relações no ficheiro com base em pistas visuais como:

* tamanho da fonte
* estilo de letra
* numeração
* cores

|Exemplos de documento|
|--|
||



## <a name="product-manuals"></a>Manuais de produto

Um manual é, normalmente, material de orientação que acompanha um produto. Ele ajuda o utilizador para configurar, utilizar, manter e resolver problemas relacionados com o produto. Quando o QnA Maker processa um manual, extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Veja um exemplo [aqui.](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)

Segue-se um exemplo de um manual com uma página de índice e conteúdo hierárquico

 ![Exemplo de manuais de produto para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extração funciona melhor em manuais que tenham uma tabela de conteúdos e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicas.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros ficheiros

Muitos outros tipos de documentos também podem ser processados para gerar pares de controle de qualidade, desde que tenham uma estrutura clara e o layout. Estes incluem: Brochuras, diretrizes, relatórios, artigos brancos, artigos científicos, políticas, livros, etc. Veja um exemplo [aqui.](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)

Segue-se um exemplo de um documento semiestruturado, sem um índice:

 ![Armazenamento de Blobs do Azure Doc semiestruturado](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Documento de QnA estruturado

O formato estruturados-respostas a perguntas nos arquivos de documento, é na forma de alternância de perguntas e respostas por linha, uma pergunta por linha, seguido sua resposta na linha seguinte, conforme mostrado abaixo:

```text
Question1

Answer1

Question2

Answer2
```

Segue-se um exemplo de um documento do word QnA estruturado:

 ![Exemplo de documento do QnA estruturado para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Ficheiros *TXT,* *TSV* e *XLS* estruturados

Os QnAs sob a forma de ficheiros estruturados *.txt,* *.tsv* ou *.xls* também podem ser enviados para o Fabricante qnA para criar ou aumentar uma base de conhecimento.  Estas podem ser texto simples, ou podem ter o conteúdo em RTF ou HTML.

| Pergunta  | Resposta  | Metadados (1 tecla: 1 valor) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Quaisquer colunas adicionais no arquivo de origem são ignoradas.

### <a name="example-of-structured-excel-file"></a>Exemplo de ficheiro Excel estruturado

Abaixo está um exemplo de um ficheiro QnA *.xls* estruturado, com conteúdo HTML:

 ![Exemplo de uma base de dados de conhecimento do excel QnA estruturado](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemplo de perguntas alternativas para resposta única no ficheiro Excel

Abaixo está um exemplo de um ficheiro QnA *.xls* estruturado, com várias perguntas alternativas para uma única resposta:

 ![Exemplo de perguntas alternativas para resposta única no ficheiro Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Após a importação do ficheiro, o par de perguntas e respostas encontra-se na base de conhecimentos, como mostrado abaixo:

 ![Screenshot de perguntas alternativas para resposta única importada para base de conhecimento](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados através de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. Importação requer um ficheiro. tsv estruturados que contém informações de origem de dados. Esta informação ajuda o QnA Maker a agrupar os pares de resposta seletiva e aatribuí-los a uma determinada fonte de dados.

| Pergunta  | Resposta  | Origem| Metadados (1 tecla: 1 valor) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Formatação de documento de várias voltas

* Utilize rubricas e sub-rubricas para denotar a hierarquia. Por exemplo, pode h1 para denotar o QnA e h2 dos pais para denotar o QnA que deve ser tomado como solicitado. Utilize o pequeno tamanho da cabeça para denotar a hierarquia subsequente. Não utilize o estilo, a cor ou algum outro mecanismo para implicar estrutura no seu documento, o Fabricante QnA não extrairá os avisos de várias voltas.
* O primeiro carácter da direção deve ser capitalizado.
* Não termine um título com um ponto de interrogação, `?`.


|Exemplos de documento|
|--|
||