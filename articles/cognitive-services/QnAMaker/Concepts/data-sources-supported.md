---
title: Origens de dados suportadas - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extrai automaticamente pares de pergunta-resposta de conteúdo semiestruturado, tais como FAQs, manuais de produto, diretrizes, documentos de suporte e armazenadas como páginas da web, ficheiros PDF ou arquivos de documento do Word de MS de políticas. Conteúdo também pode ser adicionado para a base de dados de conhecimento de ficheiros de conteúdo de QnA estruturados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: diberry
ms.openlocfilehash: 5175dee24542c716b3d087412864ae7e6f056d18
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615973"
---
# <a name="data-sources-for-qna-maker-content"></a>Origens de dados para o conteúdo de QnA Maker

QnA Maker extrai automaticamente pares de pergunta-resposta de conteúdo semiestruturado, tais como FAQs, manuais de produto, diretrizes, documentos de suporte e armazenadas como páginas da web, ficheiros PDF ou arquivos de documento do Word de MS de políticas. Conteúdo também pode ser adicionado para a base de dados de conhecimento de ficheiros de conteúdo de QnA estruturados. 

## <a name="data-types"></a>Tipos de dados

A tabela abaixo resume os tipos de conteúdo e formatos de arquivo que são suportados pelo QnA Maker.

|Tipo de Fonte|Tipo de Conteúdo| Exemplos|
|--|--|--|
|URL|FAQs<br> (Simples, com seções ou com uma home page de tópicos)<br>Páginas de suporte <br> (Artigos de instruções de página única, artigos de solução de problemas etc.)|[Perguntas frequentes simples](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Perguntas frequentes com links](https://www.microsoft.com/software-download/faq),<br> [Página de perguntas frequentes com tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de suporte](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQs<br> Manual do produto,<br> Folhetos<br> Documentos<br> Política de panfleto,<br> Guia de suporte,<br> QnA estruturado,<br> diante.|[Estruturado QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Manual de produto de exemplo. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exemplo de semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exemplo white paper. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Ficheiro de QnA estruturado<br> (incluindo RTF, suporte a HTML)|[Exemplo QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Ficheiro de QnA estruturado|[Exemplo chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Localizações de origem de dados

Os locais de fonte de dados são **URLs ou arquivos públicos**, que não exigem autenticação. 

Se você precisar de autenticação para sua fonte de dados, considere os seguintes métodos para obter esses dados em QnA Maker:

* [Baixar o arquivo manualmente](#download-file-from-authenticated-data-source-location) e importar para o QnA Maker
* Importar arquivo para o [local do SharePoint](#import-file-from-authenticated-sharepoint) autenticado 

### <a name="download-file-from-authenticated-data-source-location"></a>Baixar arquivo do local da fonte de dados autenticada

Se você tiver um arquivo autenticado (não em um local do SharePoint autenticado) ou URL, uma opção alternativa é baixar o arquivo do site autenticado para o computador local e, em seguida, adicionar o arquivo do seu computador local à base de dados de conhecimento.

### <a name="import-file-from-authenticated-sharepoint"></a>Importar arquivo do SharePoint autenticado 

Os [locais de fonte de dados do SharePoint](../How-to/add-sharepoint-datasources.md) têm permissão para fornecer **arquivos**autenticados. Os recursos do SharePoint devem ser arquivos, não páginas da Web. Se a URL terminar com uma extensão da Web, como **. ASPX**, ele não será importado para o QnA Maker do SharePoint.


## <a name="faq-urls"></a>URLs de FAQ

QnA Maker pode dar suporte a páginas da Web de perguntas frequentes em três formas diferentes: Páginas de perguntas frequentes simples, páginas de perguntas frequentes com links, páginas de perguntas frequentes com uma página de tópicos.

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

A ferramenta QnA Maker identifica as seções e subsecções e relações no arquivo com base em pistas visuais, como o tamanho da fonte, o estilo de fonte, numeração, cores, etc. Ficheiros PDF ou documentos semiestruturados poderiam ser manuais, FAQs, diretrizes, políticas, brochuras, panfletos e muitos outros tipos de ficheiros. Seguem-se alguns tipos de exemplo desses arquivos.

### <a name="product-manuals"></a>Manuais de produto

Um manual é, normalmente, material de orientação que acompanha um produto. Ele ajuda o utilizador para configurar, utilizar, manter e resolver problemas relacionados com o produto. Quando o QnA Maker processa um manual, extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Ver um exemplo [aqui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Segue-se um exemplo de um manual com uma página de índice e conteúdo hierárquico

 ![Exemplo de manuais de produto para uma base de dados de conhecimento](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extração funciona melhor em manuais que tenham uma tabela de conteúdos e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicas.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros ficheiros

Muitos outros tipos de documentos também podem ser processados para gerar pares de controle de qualidade, desde que tenham uma estrutura clara e o layout. Estas incluem: Folhetos, diretrizes, relatórios, White papers, documentos científicos, políticas, livros etc. Ver um exemplo [aqui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados através de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. Importação requer um ficheiro. tsv estruturados que contém informações de origem de dados. Essas informações ajudam a QnA Maker agrupar os pares de respostas de perguntas e os atributos para uma determinada fonte de dados.

| Pergunta  | Resposta  | Source| Metadados (1 chave: 1 valor) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Adicionar de forma editorial à base de dados de conhecimento

Se não tiver conteúdo já existente para preencher a base de dados de conhecimento, pode adicionar QnAs forma editorial nos conhecimentos do QnA Maker base. Saiba como atualizar a sua base de dados de conhecimento [aqui](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Considerações sobre a formatação

Depois de importar um arquivo ou URL, QnA Maker converte e armazena seu conteúdo no [formato de redução](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas linhas no texto, como `\n\n`. Um conhecimento do formato de redução ajuda você a entender o conteúdo convertido e gerenciar seu conteúdo da base de dados de conhecimento. 

Se você adicionar ou editar seu conteúdo diretamente na sua base de dados de conhecimento, use a **formatação de redução** para criar conteúdo de Rich Text ou alterar o conteúdo do formato de redução que já está na resposta. QnA Maker dá suporte a grande parte do formato de redução para trazer recursos de Rich Text para seu conteúdo. No entanto, o aplicativo cliente, como um bot de chat, pode não dar suporte ao mesmo conjunto de formatos de redução. É importante testar a exibição de respostas do aplicativo cliente. 

A seguir está a lista de formatos de redução que você pode usar em QnA Maker: 

|Objetivo|Formato|Redução de exemplo|Composição<br>conforme exibido no bot de chat|
|--|--|--|--|
Uma nova linha entre duas frases.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![Formatar nova linha entre duas frases](../media/qnamaker-concepts-datasources/format-newline.png)|
|Cabeçalhos de H1 a H6, o número de `#` denota o cabeçalho. 1 `#` é o H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![Formatar com cabeçalhos de redução](../media/qnamaker-concepts-datasources/format-headers.png)<br>![Formatar com cabeçalhos de redução H1 a H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Itálico |`*text*`|`How do I create a bot with *QnA Maker*?`|![Formatar com itálico](../media/qnamaker-concepts-datasources/format-italics.png)|
|Cadeia de caracteres (negrito)|`**text**`|`How do I create a bot with **QnA Maker**?`|![Formatar com marcação forte para negrito](../media/qnamaker-concepts-datasources/format-strong.png)|
|URL para o link|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formato da URL (hiperlink)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* URL para imagem pública|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formato da URL da imagem pública ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Rasurado|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formato de tachado](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Negrito e itálico|`***text***`|`How can I create a ***QnA Maker*** bot?`|![formato para negrito e itálico](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|URL em negrito para o link|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formato para URL em negrito](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL de itálico para o link|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formato para URL de itálico](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Símbolos de redução de escape|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![formato para URL de itálico](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Lista ordenada|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>O exemplo anterior usa a numeração automática incorporada à redução.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>O exemplo anterior usa a numeração explícita.|![formato da lista ordenada](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Lista não ordenada|`\n * item1 \n * item2`<br>ou<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![formato da lista não ordenada](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Listas aninhadas|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Você pode aninhar listas ordenadas e não ordenadas juntas. A guia, `\t`, indica o nível de recuo do elemento filho.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formato da lista não ordenada aninhada](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formato da lista ordenada aninhada](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker não processa a imagem de forma alguma. É a função do aplicativo cliente para renderizar a imagem. 

## <a name="editing-your-knowledge-base-locally"></a>Editando sua base de dados de conhecimento localmente

Quando uma base de dados de conhecimento é criada, é recomendável que você faça edições no texto da base de dados de conhecimento no [portal de QnA Maker](https://qnamaker.ai), em vez de exportar e reimportar por meio de arquivos locais. No entanto, pode haver ocasiões em que você precisa editar uma base de dados de conhecimento localmente. 

Exporte a base de dados de conhecimento na página **configurações** e edite a base de dados de conhecimento com o Microsoft Excel. Se você optar por usar outro aplicativo para editar o arquivo TSV exportado, o aplicativo poderá introduzir erros de sintaxe porque ele não é compatível totalmente com TSV. Os arquivos TSV do Microsoft Excel geralmente não introduzem erros de formatação. 

Depois de concluir suas edições, reimporte o arquivo TSV da página **configurações** . Isso substituirá completamente a base de dados de conhecimento atual pela base de dados de conhecimento importada. 

## <a name="testing-your-markdown"></a>Testando sua redução

Use o tutorial do **[CommonMark](https://commonmark.org/help/tutorial/index.html)** para validar sua redução. O tutorial tem um recurso **experimentar** para a validação rápida de copiar/colar. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configurar um serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Consulte também 

[Descrição geral do Criador de FAQ](../Overview/overview.md)