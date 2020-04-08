---
title: Importação de fontes de dados - QnA Maker
description: Uma base de conhecimento do QnA Maker consiste num conjunto de conjuntos de perguntas e respostas (QnA) e metadados opcionais associados a cada par qnA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804287"
---
# <a name="importing-from-data-sources"></a>Importar a partir de origens de dados

Uma base de conhecimento consiste em conjuntos de perguntas e respostas trazidos por URLs e ficheiros públicos.

## <a name="data-source-locations"></a>Localizações de fonte de dados

O conteúdo é trazido para uma base de conhecimento a partir de uma fonte de dados. As localizações de fonte de dados são **URLs ou ficheiros públicos,** que não requerem autenticação.

[Os ficheiros SharePoint,](../how-to/add-sharepoint-datasources.md)protegidos com autenticação, são a exceção. Os recursos do SharePoint devem ser ficheiros, não páginas web. Se o URL terminar com uma extensão web, como . ASPX, não importará para a QnA Maker a partir do SharePoint.

## <a name="chit-chat-content"></a>Conteúdo de chat chit

O conjunto de conteúdos Chit chat QnA é oferecido como uma fonte completa de dados de conteúdo em vários idiomas e estilos de conversação. Este pode ser um ponto de partida para a personalidade do seu bot, e poupar-lhe-á tempo e custo de escrevê-los do zero. Aprenda [a adicionar](../how-to/chit-chat-knowledge-base.md) este conteúdo à sua base de conhecimentos.

## <a name="structured-data-format-through-import"></a>Formato de dados estruturado através da importação

Importar uma base de conhecimentos substitui o conteúdo da base de conhecimentos existente. A importação requer `.tsv` um ficheiro estruturado que contenha perguntas e respostas. Esta informação ajuda o QnA Maker a agrupar os conjuntos de resposta a perguntas e a atribuí-los a uma determinada fonte de dados.

| Pergunta  | Resposta  | Origem| Metadados (1 tecla: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta 2 | Resposta2 | Editorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato multi-turn estruturado através da importação

Pode criar as conversas multi-turn num `.tsv` formato de ficheiro. O formato proporciona-lhe a capacidade de criar as conversas multi-turn através da análise de registos de `.tsv` chat anteriores (com outros processos, não utilizando o QnA Maker), e depois criar o ficheiro através da automatização. Importar o ficheiro para substituir a base de conhecimentos existente.

> [!div class="mx-imgBorder"]
> ![Modelo conceptual de 3 níveis de questão multi-turn](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A coluna para uma `.tsv`volta multi-volta, específica para multi-turn é **Prompts**. Um `.tsv`exemplo, mostrado no Excel, mostra a informação a incluir para definir as crianças multi-turn:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

O **visorOrder** é numérico e o **ecrãTexto** é texto que não deve incluir marcação.

> [!div class="mx-imgBorder"]
> ![Exemplo de pergunta multi-turn como mostrado no Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportação como exemplo

Se não tem a certeza de como `.tsv` representar o seu par QnA no ficheiro:
* Use este [exemplo descarregado do GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Ou criar o conjunto no portal QnA Maker, economizar, em seguida, exportar a base de conhecimento para um exemplo de como representar o conjunto.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

Use a referência QnA Maker [Markdown](../reference-markdown-format.md) para ajudá-lo a formatar as suas respostas.

[Descrição geral do Criador de FAQ](../Overview/overview.md)

Criar e editar uma base de conhecimento com:
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Gerar uma resposta com:
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
