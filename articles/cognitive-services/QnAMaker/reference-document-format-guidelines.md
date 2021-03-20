---
title: Diretrizes do formato de documento de importação - QnA Maker
description: Utilize estas diretrizes para importar documentos para obter os melhores resultados para o seu conteúdo.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99549546"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Diretrizes de formato para documentos e URLs importados

Reveja estas diretrizes de formatação para obter os melhores resultados para o seu conteúdo.

## <a name="formatting-considerations"></a>Considerações de formatação

Depois de importar um ficheiro ou URL, o QnA Maker converte e armazena o seu conteúdo no [formato de marcação](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas linhas no texto, tais como `\n\n` . Um conhecimento do formato de marcação ajuda-o a compreender o conteúdo convertido e a gerir o conteúdo da base de conhecimento.

Se adicionar ou editar o seu conteúdo diretamente na sua base de conhecimento, utilize **a formatação de marcação** para criar conteúdo de texto rico ou alterar o conteúdo do formato de marcação que já está na resposta. O QnA Maker suporta grande parte do formato de marcação para trazer capacidades de texto ricas para o seu conteúdo. No entanto, a aplicação do cliente, como um chat bot, pode não suportar o mesmo conjunto de formatos de marcação. É importante testar a exibição de respostas da aplicação do cliente.

Consulte uma lista completa de [tipos e exemplos](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)de conteúdo.

## <a name="basic-document-formatting"></a>Formatação de documentos básicos

O Fabricante QnA identifica secções e subsecções e relações no ficheiro com base em pistas visuais como:

* tamanho do tipo de letra:
* estilo de fonte
* numeragem
* cores

> [!NOTE]
> Não apoiamos a extração de imagens de documentos enviados atualmente.

### <a name="product-manuals"></a>Manuais de produtos

Um manual é normalmente material de orientação que acompanha um produto. Ajuda o utilizador a configurar, usar, manter e resolver problemas do produto. Quando a QnA Maker processa um manual, extrai as rubricas e subposições como perguntas e o conteúdo subsequente como resposta. Veja um exemplo [aqui.](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)

Abaixo está um exemplo de um manual com uma página de índice, e conteúdo hierárquico

 ![Exemplo manual do produto para uma base de conhecimento](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A extração funciona melhor em manuais que tenham uma tabela de conteúdos e/ou uma página de índice, e uma estrutura clara com títulos hierárquicos.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros ficheiros

Muitos outros tipos de documentos também podem ser processados para gerar pares de QA, desde que tenham uma estrutura e layout claros. Estes incluem: Brochuras, orientações, relatórios, documentos brancos, artigos científicos, políticas, livros, etc. Veja um exemplo [aqui.](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)

Abaixo está um exemplo de um doc semi-estruturado, sem um índice:

 ![Azure Blob armazenamento semi-estruturado Doc](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Documento QnA estruturado

O formato para Question-Answers estruturados em ficheiros DOC, é na forma de perguntas e respostas alternadas por linha, uma pergunta por linha seguida da sua resposta na seguinte linha, como mostra abaixo:

```text
Question1

Answer1

Question2

Answer2
```

Abaixo está um exemplo de um documento de palavra QnA estruturado:

 ![Exemplo de documento da QnA estruturado para uma base de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>*Arquivos TXT* estruturados, *TSV* e *XLS*

QnAs sob a forma de *.txt* estruturados , *.tsv* ou *.xls* ficheiros também podem ser enviados para qnA Maker para criar ou aumentar uma base de conhecimento.  Estes podem ser texto simples, ou podem ter conteúdo em RTF ou HTML.

| Pergunta  | Resposta  | Metadados (1 chave: 1 valor) |
|-----------|---------|-------------------------|
| Pergunta1 | Resposta1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 |      `Key:Value`           |

Quaisquer colunas adicionais no ficheiro de origem são ignoradas.

#### <a name="example-of-structured-excel-file"></a>Exemplo de arquivo Excel estruturado

Abaixo está um exemplo de um ficheiro de *.xls QnA* estruturado, com conteúdo HTML:

 ![QnA estruturado exemplo de excel para uma base de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemplo de perguntas alternativas para resposta única no ficheiro Excel

Abaixo está um exemplo de um ficheiro de *.xls* QnA estruturado, com várias perguntas alternativas para uma única resposta:

 ![Exemplo de perguntas alternativas para resposta única no ficheiro Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Após a importação do ficheiro, o par de perguntas e respostas encontra-se na base de conhecimentos, tal como mostrado abaixo:

 ![Screenshot de perguntas alternativas para resposta única importada para base de conhecimento](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>Formato de dados estruturado através da importação

Importar uma base de conhecimento substitui o conteúdo da base de conhecimento existente. A importação requer um ficheiro .tsv estruturado que contenha informações sobre fontes de dados. Esta informação ajuda a QnA Maker a agrupar os pares de respostas de perguntas e atribuí-los a uma determinada fonte de dados.

| Pergunta  | Resposta  | Origem| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Formatação de documentos multi-volta

* Utilize títulos e sub-títulos para denotar hierarquia. Por exemplo, pode h1 denotar o QnA e h2 do progenitor para denotar o QnA que deve ser tomado como rápido. Utilize o tamanho do título pequeno para denotar a hierarquia subsequente. Não utilize estilo, cor ou algum outro mecanismo que insinue estrutura no seu documento, o QnA Maker não extrairá as indicações de várias voltas.
* O primeiro carácter de posição deve ser capitalizado.
* Não termine um título com um ponto de interrogação, `?` .

**Documentos da amostra:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Benefícios Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Benefícios Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>FAQ URLs

O QnA Maker pode suportar páginas web de FAQ em 3 formas diferentes:

* Páginas simples de FAQ
* Páginas de FAQ com links
* Páginas de FAQ com uma página inicial de tópicos

### <a name="plain-faq-pages"></a>Páginas simples de FAQ

Este é o tipo mais comum de página de PERGUNTAS Frequentes, em que as respostas seguem imediatamente as perguntas na mesma página.

Abaixo está um exemplo de uma página simples de FAQ:

![Exemplo simples da página de FAQ para uma base de conhecimento](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de FAQ com links

Neste tipo de página de PERGUNTAS Frequentes, as perguntas são agregadas em conjunto e estão ligadas a respostas que estão em diferentes secções da mesma página, ou em páginas diferentes.

Abaixo está um exemplo de uma página de FAQ com links em secções que estão na mesma página:

 ![Secção Link FAQ exemplo página para uma base de conhecimento](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Página de tópicos dos pais ligações para páginas de respostas de crianças

Este tipo de FAQ tem uma página de Tópicos onde cada tópico está ligado a um conjunto correspondente de perguntas e respostas numa página diferente. O QnA Maker rasteja todas as páginas ligadas para extrair as perguntas correspondentes & respostas.

Abaixo está um exemplo de uma página de Tópicos com links para secções faq em diferentes páginas.

 ![Exemplo de página de FAQ de ligação profunda para uma base de conhecimento](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>UrLs de apoio

O QnA Maker pode processar páginas web de suporte semi-estruturados, tais como artigos web que descrevem como executar uma determinada tarefa, como diagnosticar e resolver um determinado problema, e quais são as melhores práticas para um determinado processo. A extração funciona melhor em conteúdos que têm uma estrutura clara com títulos hierárquicos.

> [!NOTE]
> A extração para artigos de suporte é uma nova característica e está em fase inicial. Funciona melhor para páginas simples, que estão bem estruturadas, e não contêm cabeçalhos/rodapés complexos.

![O QnA Maker suporta a extração de páginas web semi-estruturadas onde uma estrutura clara é apresentada com títulos hierárquicos](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>Base de conhecimentos de importação e exportação

**Os ficheiros TSV e XLS**, a partir de bases de conhecimento exportadas, só podem ser utilizados importando os ficheiros da página **Definições** no portal Do Criador QnA. Não podem ser utilizados como fontes de dados durante a criação da base de conhecimento ou a partir do **ficheiro + Adicionar** ou + Adicionar a função **URL** na página **Definições.** 

Quando importa a base de conhecimento através **destes ficheiros TSV e XLS,** os pares QnA são adicionados à fonte editorial e não às fontes a partir das quais os QnAs foram extraídos na Base de Conhecimento exportada. 


## <a name="next-steps"></a>Passos seguintes

Consulte uma lista completa de [tipos e exemplos](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base) de conteúdo
