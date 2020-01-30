---
title: Tipos de conteúdo - QnA Maker
description: Os tipos de conteúdo incluem muitos documentos estruturados padrão, tais como PDF, DOC e TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ac94f51fbe20c2efc277c084f9c704b5bcfa3a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843440"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipos de conteúdo de documentos que pode adicionar a uma base de conhecimento
Os tipos de conteúdo incluem muitos documentos estruturados padrão, tais como PDF, DOC e TXT.

## <a name="file-and-url-data-types"></a>Tipos de dados de arquivo e URL

A tabela abaixo resume os tipos de conteúdo e formatos de arquivo que são suportados pelo QnA Maker.

|Tipo de Fonte|Tipo de Conteúdo| Exemplos|
|--|--|--|
|URL|FAQ<br> (Simples, com seções ou com uma home page de tópicos)<br>Páginas de suporte <br> (Artigos de instruções de página única, artigos de solução de problemas etc.)|[Perguntas frequentes simples](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Perguntas frequentes com links](https://www.microsoft.com/en-us/software-download/faq),<br> [Página de perguntas frequentes com tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de suporte](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQs<br> Manual do produto,<br> Folhetos<br> Documentos<br> Política de panfleto,<br> Guia de suporte,<br> QnA estruturado,<br> etc.|[Estruturado QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Manual de produto de exemplo. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exemplo de semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exemplo White Paper. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Exemplo de multi-Turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Ficheiro de QnA estruturado<br> (incluindo RTF, suporte a HTML)|[Exemplo QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Ficheiro de QnA estruturado|[Exemplo chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se necessitar de autenticação para a sua fonte de dados, considere os seguintes métodos para obter esse conteúdo no QnA Maker:

* Descarregue o ficheiro manualmente e importe para o QnA Maker
* Adicionar ficheiro a partir da localização autenticada do [Sharepoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Conteúdo de URL

Dois tipos de documentos podem ser importados através de **URL** no Fabricante qnA:

* URLs de FAQ
* URLs de suporte

Cada tipo indica um formato esperado.

## <a name="file-based-content"></a>Conteúdo baseado em ficheiros

Pode adicionar ficheiros a uma base de conhecimento a partir de uma fonte pública, ou ao seu sistema de ficheiros local, no [portal QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Diretrizes de formato de conteúdo

O QnA Maker adiciona conteúdo de ficheiro e URL, convertendo o conteúdo em conjuntos QnA, armazenados como marcação (`.md`). O conteúdo é adequado para uma base de conhecimento onde o conteúdo é organizado de alguma forma estruturada, e está representado em secções bem definidas. As secções podem ser ainda divididas em subsecções ou subtópicos. A extração funciona melhor em conteúdo que tem uma estrutura clara com títulos hierárquicos.

O QnA Maker identifica secções e subsecções e relações no conteúdo com base em pistas visuais como tamanho de fonte, estilo de fonte, numeração, cores, etc. O conteúdo semi-estruturado inclui manuais, PERGUNTAS, diretrizes, políticas, brochuras, panfletos e muitos outros tipos de ficheiros.

## <a name="faq-urls"></a>URLs de FAQ

A ferramenta QnA Maker pode suportar FAQ páginas da web em 3 diferentes formatos: páginas de FAQ sem formatação, páginas de FAQ com links, páginas de FAQ com uma home page de tópicos.


### <a name="plain-faq-pages"></a>Páginas de FAQ simples

Este é o tipo mais comum da página de FAQ, em que as respostas siga imediatamente as perguntas na mesma página.

Segue-se um exemplo de uma página de FAQ simples:

![Exemplo de página de FAQ simples para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de FAQ com links

Este tipo de página de perguntas frequentes, as perguntas são agregadas em conjunto e são associadas a respostas que estão em diferentes seções da mesma página ou em diferentes páginas.

Segue-se um exemplo de uma página de FAQ com ligações nas secções que se encontram na mesma página:

 ![Exemplo de página de FAQ de ligação de seção para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>Páginas de FAQ com uma home page de tópicos

Esse tipo de FAQ tem uma home page com os tópicos em que cada tópico é um link para sua QnAs relevantes numa página diferente. Aqui, o QnA Maker rastreia todas as páginas ligadas para extrair o correspondentes de perguntas e respostas.

Segue-se um exemplo de uma página de perguntas frequentes sobre onde uma home page de tópicos com links para páginas diferentes secções FAQ.

 ![Exemplo de página de perguntas frequentes sobre a ligação avançada para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>URLs de suporte

QnA Maker pode processar páginas da Web de suporte semiestruturadas, como artigos da Web que descrevem como executar uma determinada tarefa, como diagnosticar e resolver um determinado problema e quais são as práticas recomendadas para um determinado processo. A extração funciona melhor em conteúdo que tem uma estrutura clara com títulos hierárquicos.

> [!NOTE]
> A extração para artigos de suporte é um novo recurso e está em estágios iniciais. Ele funciona melhor para páginas simples, que são bem estruturadas e não contêm cabeçalhos/rodapés complexos.

![QnA Maker dá suporte à extração de páginas da Web semiestruturadas onde uma estrutura clara é apresentada com títulos hierárquicos](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF / arquivos do documento

A ferramenta QnA Maker pode processar o conteúdo semiestruturado num ficheiro PDF ou documentos e convertê-los QnAs. Um ficheiro de boa que pode ser extraído também é um em que o conteúdo está organizado em alguma forma estruturada e é representado nas secções bem definidas. As secções mais podem ser divididas em subsecções ou subtópicos. Extração funciona melhor em documentos que tenham uma estrutura clara com cabeçalhos hierárquicas.

QnA Maker identifica seções e subseções e relações no arquivo com base em pistas visuais, como tamanho da fonte, estilo da fonte, numeração, cores, etc. Arquivos de documento ou PDF semiestruturados podem ser manuais, perguntas frequentes, diretrizes, políticas, folhetos, panfletos e muitos outros tipos de arquivos. Seguem-se alguns tipos de exemplo desses arquivos.

### <a name="product-manuals"></a>Manuais de produto

Um manual é, normalmente, material de orientação que acompanha um produto. Ele ajuda o utilizador para configurar, utilizar, manter e resolver problemas relacionados com o produto. Quando o QnA Maker processa um manual, extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Ver um exemplo [aqui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Segue-se um exemplo de um manual com uma página de índice e conteúdo hierárquico

 ![Exemplo de manuais de produto para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extração funciona melhor em manuais que tenham uma tabela de conteúdos e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicas.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros ficheiros

Muitos outros tipos de documentos também podem ser processados para gerar pares de controle de qualidade, desde que tenham uma estrutura clara e o layout. Eles incluem: folhetos, diretrizes, relatórios, White papers, documentos científicos, políticas, livros, etc. Veja um exemplo [aqui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Segue-se um exemplo de um documento semiestruturado, sem um índice:

 ![Armazenamento de Blobs do Azure Doc semiestruturado](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Documento Estruturado qnA

O formato estruturados-respostas a perguntas nos arquivos de documento, é na forma de alternância de perguntas e respostas por linha, uma pergunta por linha, seguido sua resposta na linha seguinte, conforme mostrado abaixo:

```text
Question1

Answer1

Question2

Answer2
```

Segue-se um exemplo de um documento do word QnA estruturado:

 ![Exemplo de documento do QnA estruturado para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Estruturados *TXT*, *TSV* e *XLS* ficheiros

QnAs na forma de estruturado *. txt*, *. tsv* ou *. xls* ficheiros também podem ser carregados para o QnA Maker para criar ou aumente uma base de dados de conhecimento.  Estas podem ser texto simples, ou podem ter o conteúdo em RTF ou HTML.

| Pergunta  | Resposta  | Metadados (1 chave: 1 valor) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Quaisquer colunas adicionais no arquivo de origem são ignoradas.

### <a name="example-of-structured-excel-file"></a>Exemplo de arquivo do Excel estruturado

Segue-se um exemplo de um QnA estruturado *. xls* arquivo, com conteúdo HTML:

 ![Exemplo de uma base de dados de conhecimento do excel QnA estruturado](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemplo de perguntas alternativas para uma única resposta no arquivo do Excel

Veja abaixo um exemplo de um arquivo QnA *. xls* estruturado, com várias perguntas alternativas para uma única resposta:

 ![Exemplo de perguntas alternativas para uma única resposta no arquivo do Excel](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Depois que o arquivo for importado, o par de perguntas e respostas estará na base de dados de conhecimento, conforme mostrado abaixo:

 ![Captura de tela de perguntas alternativas para uma única resposta importada na base de dados de conhecimento](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>Considerações sobre a formatação

Depois de importar um arquivo ou URL, QnA Maker converte e armazena seu conteúdo no [formato de redução](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas linhas no texto, como `\n\n`. Um conhecimento do formato de redução ajuda você a entender o conteúdo convertido e gerenciar seu conteúdo da base de dados de conhecimento.

Se você adicionar ou editar seu conteúdo diretamente na sua base de dados de conhecimento, use a **formatação de redução** para criar conteúdo de Rich Text ou alterar o conteúdo do formato de redução que já está na resposta. QnA Maker dá suporte a grande parte do formato de redução para trazer recursos de Rich Text para seu conteúdo. No entanto, o aplicativo cliente, como um bot de chat, pode não dar suporte ao mesmo conjunto de formatos de redução. É importante testar a exibição de respostas do aplicativo cliente.

Saiba mais com a documentação de referência de referência do [Fabricante qnA.](../reference-markdown-format.md)

## <a name="testing-your-markdown"></a>Testando sua redução

Use o tutorial do **[CommonMark](https://commonmark.org/help/tutorial/index.html)** para validar sua redução. O tutorial tem um recurso **experimentar** para a validação rápida de copiar/colar.

## <a name="next-steps"></a>Passos seguintes

* Compreender como conceber e gerir conjuntos de [perguntas e respostas (QnA)](question-answer-set.md)