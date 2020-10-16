---
title: Diretrizes do formato de documento de importação - QnA Maker
description: Utilize estas diretrizes para importar documentos para obter os melhores resultados para o seu conteúdo.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: d35ea57a68e500deffa99033b83114ec518dd5e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776736"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Diretrizes de formato para documentos e URLs importados

Reveja estas diretrizes de formatação para obter os melhores resultados para o seu conteúdo.

## <a name="formatting-considerations"></a>Considerações de formatação

Depois de importar um ficheiro ou URL, o QnA Maker converte e armazena o seu conteúdo no [formato de marcação](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas linhas no texto, tais como `\n\n` . Um conhecimento do formato de marcação ajuda-o a compreender o conteúdo convertido e a gerir o conteúdo da base de conhecimento.

Se adicionar ou editar o seu conteúdo diretamente na sua base de conhecimento, utilize **a formatação de marcação** para criar conteúdo de texto rico ou alterar o conteúdo do formato de marcação que já está na resposta. O QnA Maker suporta grande parte do formato de marcação para trazer capacidades de texto ricas para o seu conteúdo. No entanto, a aplicação do cliente, como um chat bot, pode não suportar o mesmo conjunto de formatos de marcação. É importante testar a exibição de respostas da aplicação do cliente.

Consulte uma lista completa de [tipos e exemplos](./Concepts/content-types.md#file-and-url-data-types)de conteúdo.

## <a name="basic-document-formatting"></a>Formatação de documentos básicos

O Fabricante QnA identifica secções e subsecções e relações no ficheiro com base em pistas visuais como:

* tamanho do tipo de letra:
* estilo de fonte
* numeragem
* cores

## <a name="product-manuals"></a>Manuais de produtos

Um manual é normalmente material de orientação que acompanha um produto. Ajuda o utilizador a configurar, usar, manter e resolver problemas do produto. Quando a QnA Maker processa um manual, extrai as rubricas e subposições como perguntas e o conteúdo subsequente como resposta. Veja um exemplo [aqui.](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)

Abaixo está um exemplo de um manual com uma página de índice, e conteúdo hierárquico

 ![Exemplo manual do produto para uma base de conhecimento](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A extração funciona melhor em manuais que tenham uma tabela de conteúdos e/ou uma página de índice, e uma estrutura clara com títulos hierárquicos.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros ficheiros

Muitos outros tipos de documentos também podem ser processados para gerar pares de QA, desde que tenham uma estrutura e layout claros. Estes incluem: Brochuras, orientações, relatórios, documentos brancos, artigos científicos, políticas, livros, etc. Veja um exemplo [aqui.](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)

Abaixo está um exemplo de um doc semi-estruturado, sem um índice:

 ![Azure Blob armazenamento semi-estruturado Doc](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Documento QnA estruturado

O formato para Question-Answers estruturados em ficheiros DOC, é na forma de perguntas e respostas alternadas por linha, uma pergunta por linha seguida da sua resposta na seguinte linha, como mostra abaixo:

```text
Question1

Answer1

Question2

Answer2
```

Abaixo está um exemplo de um documento de palavra QnA estruturado:

 ![Exemplo de documento da QnA estruturado para uma base de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>*Arquivos TXT*estruturados, *TSV* e *XLS*

Os QnAs sob a forma de ficheiros *estruturados .txt*, *.tsv* ou *.xls* também podem ser enviados para o QnA Maker para criar ou aumentar uma base de conhecimento.  Estes podem ser texto simples, ou podem ter conteúdo em RTF ou HTML.

| Pergunta  | Resposta  | Metadados (1 chave: 1 valor) |
|-----------|---------|-------------------------|
| Pergunta1 | Resposta1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 |      `Key:Value`           |

Quaisquer colunas adicionais no ficheiro de origem são ignoradas.

### <a name="example-of-structured-excel-file"></a>Exemplo de arquivo Excel estruturado

Abaixo está um exemplo de um ficheiro QnA *.xls* estruturado, com conteúdo HTML:

 ![QnA estruturado exemplo de excel para uma base de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemplo de perguntas alternativas para resposta única no ficheiro Excel

Abaixo está um exemplo de um ficheiro QnA *.xls* estruturado, com várias perguntas alternativas para uma única resposta:

 ![Exemplo de perguntas alternativas para resposta única no ficheiro Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Após a importação do ficheiro, o par de perguntas e respostas encontra-se na base de conhecimentos, tal como mostrado abaixo:

 ![Screenshot de perguntas alternativas para resposta única importada para base de conhecimento](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formato de dados estruturado através da importação

Importar uma base de conhecimento substitui o conteúdo da base de conhecimento existente. A importação requer um ficheiro .tsv estruturado que contenha informações sobre fontes de dados. Esta informação ajuda a QnA Maker a agrupar os pares de respostas de perguntas e atribuí-los a uma determinada fonte de dados.

| Pergunta  | Resposta  | Origem| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Formatação de documentos multi-volta

* Utilize títulos e sub-títulos para denotar hierarquia. Por exemplo, pode h1 denotar o QnA e h2 do progenitor para denotar o QnA que deve ser tomado como rápido. Utilize o tamanho do título pequeno para denotar a hierarquia subsequente. Não utilize estilo, cor ou algum outro mecanismo que insinue estrutura no seu documento, o QnA Maker não extrairá as indicações de várias voltas.
* O primeiro carácter de posição deve ser capitalizado.
* Não termine um título com um ponto de interrogação, `?` .

**Documentos da amostra:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Benefícios Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Benefícios Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Passos seguintes

Consulte uma lista completa de [tipos e exemplos](./Concepts/content-types.md#file-and-url-data-types) de conteúdo