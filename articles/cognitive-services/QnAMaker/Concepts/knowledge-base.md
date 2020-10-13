---
title: Importação de fontes de dados - QnA Maker
description: Saiba como importar os pares de perguntas e respostas a partir de fontes de dados - QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5f609dc27aa1251cfad0249d26ef5140936bfe41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776957"
---
# <a name="importing-from-data-sources"></a>Importar a partir de origens de dados

Uma base de conhecimento consiste em pares de perguntas e respostas trazidos por URLs públicos e ficheiros.

## <a name="data-source-locations"></a>Locais de origem de dados

O conteúdo é trazido para uma base de conhecimento a partir de uma fonte de dados. As localizações de origem de dados são **URLs ou ficheiros públicos,** que não requerem autenticação.

[Os ficheiros SharePoint](../how-to/add-sharepoint-datasources.md), protegidos com autenticação, são a exceção. Os recursos do SharePoint devem ser ficheiros, não páginas web. Se o URL terminar com uma extensão Web, como . ASPX, não importará do SharePoint para o Criador de FAQ.

## <a name="chit-chat-content"></a>Conteúdo de conversa chit

O conjunto de conteúdos Chit chat QnA é oferecido como uma fonte completa de dados de conteúdo em vários idiomas e estilos de conversação. Este pode ser um ponto de partida para a personalidade do seu bot, e irá poupar-lhe o tempo e o custo de escrevê-los do zero. Saiba [como adicionar](../how-to/chit-chat-knowledge-base.md) este conteúdo definido à sua base de conhecimentos.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

Utilize a referência de [Markdown](../reference-markdown-format.md) do Criador QnA para ajudá-lo a formatar as suas respostas.

[Descrição geral do Criador de FAQ](../Overview/overview.md)

Criar e editar uma base de conhecimento com:
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Gerar uma resposta com:
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
