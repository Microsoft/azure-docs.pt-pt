---
title: Origens de dados suportadas - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extrai automaticamente pares de pergunta-resposta de conteúdo semiestruturado, tais como FAQs, manuais de produto, diretrizes, documentos de suporte e armazenadas como páginas da web, ficheiros PDF ou arquivos de documento do Word de MS de políticas. Conteúdo também pode ser adicionado para a base de dados de conhecimento de ficheiros de conteúdo de QnA estruturados.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.openlocfilehash: 0e9e8456bb493ad4591c0f2a22d28bdf342f09e1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433115"
---
# <a name="data-sources-for-qna-maker-content"></a>Origens de dados para o conteúdo de QnA Maker

QnA Maker extrai automaticamente pares de pergunta-resposta de conteúdo semiestruturado, tais como FAQs, manuais de produto, diretrizes, documentos de suporte e armazenadas como páginas da web, ficheiros PDF ou arquivos de documento do Word de MS de políticas. Conteúdo também pode ser adicionado para a base de dados de conhecimento de ficheiros de conteúdo de QnA estruturados. 

A tabela abaixo resume os tipos de conteúdo e formatos de arquivo que são suportados pelo QnA Maker.

|Tipo de Fonte|Tipo de Conteúdo| Exemplos|
|--|--|--|
|do IdP|FAQs<br> (Fixa, com seções ou com uma home page de tópicos)<br>Páginas de suporte <br> (Página única artigos de procedimentos, resolução de problemas de artigos, etc.)|[FAQ simples](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Perguntas frequentes com ligações](https://www.microsoft.com/software-download/faq),<br> [Perguntas frequentes com home page de tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de suporte](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQs,<br> Manual de produto,<br> Brochuras,<br> Documento,<br> Política de documentos,<br> Guia de suporte,<br> QnA estruturado,<br> etc.|[Estruturado QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Manual.pdf de produto de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exemplo semiestruturados structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Branco paper.pdf de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Ficheiro de QnA estruturado<br> (incluindo RTF, HTML suportam)|[Exemplo QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Ficheiro de QnA estruturado|[Exemplo chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Localizações de origem de dados

Apenas os URLs públicas são válidos para todas as origens de dados. Não se submeta as origens de dados que requerem autenticação. Pode transferir o ficheiro a partir do site autenticado e depois utilizar a opção de carregamento de ficheiros para extrair perguntas e respostas.

## <a name="faq-urls"></a>URLs de FAQ

A ferramenta QnA Maker pode suportar FAQ páginas da web em 3 diferentes formatos: Páginas de FAQ sem formatação, páginas de FAQ com links, páginas de FAQ com uma home page de tópicos.

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


### <a name="support-urls"></a>Urls de suporte

A ferramenta QnA Maker pode processar suporte semiestruturados páginas da web, como artigos da web que descrevem como executar uma determinada tarefa, como diagnosticar e resolver um problema e quais são as melhores práticas para um determinado processo. Extração funciona melhor em conteúdo que tenha uma estrutura clara com cabeçalhos hierárquicas.

> [!NOTE]
> Extração de artigos de suporte é uma nova funcionalidade e nas fases iniciais. Ele funciona melhor para páginas simples, que são bem estruturadas e não contêm cabeçalhos/rodapés complexos.

![A ferramenta QnA Maker suporta extração semiestruturados páginas da web onde é apresentada uma estrutura clara cabeçalhos hierárquicos](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF / arquivos do documento

A ferramenta QnA Maker pode processar o conteúdo semiestruturado num ficheiro PDF ou documentos e convertê-los QnAs. Um ficheiro de boa que pode ser extraído também é um em que o conteúdo está organizado em alguma forma estruturada e é representado nas secções bem definidas. As secções mais podem ser divididas em subsecções ou subtópicos. Extração funciona melhor em documentos que tenham uma estrutura clara com cabeçalhos hierárquicas.

A ferramenta QnA Maker identifica as seções e subsecções e relações no arquivo com base em pistas visuais, como o tamanho da fonte, o estilo de fonte, numeração, cores, etc. Ficheiros PDF ou documentos semiestruturados poderiam ser manuais, FAQs, diretrizes, políticas, brochuras, panfletos e muitos outros tipos de ficheiros. Seguem-se alguns tipos de exemplo desses arquivos.

### <a name="product-manuals"></a>Manuais de produto

Um manual é, normalmente, material de orientação que acompanha um produto. Ele ajuda o utilizador para configurar, utilizar, manter e resolver problemas relacionados com o produto. Quando o QnA Maker processa um manual, extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Ver um exemplo [aqui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Segue-se um exemplo de um manual com uma página de índice e conteúdo hierárquico

 ![Exemplo de manuais de produto para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extração funciona melhor em manuais que tenham uma tabela de conteúdos e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicas.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros ficheiros

Muitos outros tipos de documentos também podem ser processados para gerar pares de controle de qualidade, desde que tenham uma estrutura clara e o layout. Estas incluem: Brochuras, diretrizes, relatórios, white papers, papers científicas, políticas, livros, etc. Ver um exemplo [aqui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Segue-se um exemplo de um documento semiestruturado, sem um índice:

 ![Armazenamento de Blobs do Azure Doc semiestruturado](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Documento de QnA estruturado

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

| Pergunta  | Resposta  | Metadados (1 chave: valor de 1) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Quaisquer colunas adicionais no arquivo de origem são ignoradas.

Segue-se um exemplo de um QnA estruturado *. xls* arquivo, com conteúdo HTML:

 ![Exemplo de uma base de dados de conhecimento do excel QnA estruturado](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados através de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. Importação requer um ficheiro. tsv estruturados que contém informações de origem de dados. Estas informações ajudam o QnA Maker agrupar os pares de resposta de pergunta e atributo-los para uma determinada origem de dados.

| Pergunta  | Resposta  | Origem| Metadados (1 chave: valor de 1) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Adicionar de forma editorial à base de dados de conhecimento

Se não tiver conteúdo já existente para preencher a base de dados de conhecimento, pode adicionar QnAs forma editorial nos conhecimentos do QnA Maker base. Saiba como atualizar a sua base de dados de conhecimento [aqui](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Considerações sobre a formatação

Depois de importar um ficheiro ou URL, é convertida em Markdown e armazenado nesse formato. Se o processo de conversão não é converter ligações em seus arquivos e os URLs corretamente, deve editar as perguntas e respostas sobre o **editar** página. 

|Formato|Objetivo|
|--|--|
|`\n\n`| Nova linha|
|`\n*`|Marcador para uma lista ordenada|

## <a name="editing-your-knowledge-base-locally"></a>Editar a sua base de dados de conhecimento localmente

Depois de criar uma base de dados de conhecimento, recomenda-se que efetue as edições no texto de base de dados de conhecimento dos [portal do QnA Maker](https://qnamaker.ai), em vez de exportar e voltar a importar por meio de arquivos locais. No entanto, pode haver ocasiões que terá de editar uma base de dados de conhecimento localmente. 

Exportar a base de dados de conhecimento a partir da **definições** página, em seguida, edite a base de dados de conhecimento com o Microsoft Excel. Se optar por usar outra aplicação para editar o ficheiro TSV exportado, o aplicativo pode introduzir erros de sintaxe, porque não é totalmente TSV em conformidade. Ficheiros do Microsoft Excel TSV geralmente não introduzem quaisquer erros de formatação. 

Quando tiver terminado as suas edições, importe novamente o ficheiro TSV a partir da **definições** página. Isso substituirá totalmente a base de dados de conhecimento atual com a base de dados de conhecimento importada. 

## <a name="testing-your-markdown"></a>Teste a sua Markdown

Utilize o **[CommonMark](https://commonmark.org/help/tutorial/index.html)** tutorial para validar a sua Markdown. O tutorial tem um **experimente** funcionalidade para a validação de copiar/colar rápida. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configurar um serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Consulte também 

[Descrição geral do Criador de FAQ](../Overview/overview.md)
