---
title: Editar uma base de conhecimento - QnA Maker
description: O QnA Maker permite-lhe gerir o conteúdo da sua base de conhecimentos, proporcionando uma experiência de edição fácil de usar.
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 018e733dda06b7785b0a87ea3e08009967213134
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650862"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Editar pares QnA na sua base de conhecimentos

O QnA Maker permite-lhe gerir o conteúdo da sua base de conhecimentos, proporcionando uma experiência de edição fácil de usar.

Os pares QnA são adicionados a partir de uma fonte de dados, como um ficheiro ou URL, ou adicionados como uma fonte editorial. Uma fonte editorial indica que o par QnA foi adicionado manualmente no portal QnA. Todos os pares QnA estão disponíveis para edição.

<a name="add-an-editorial-qna-set"></a>

## <a name="add-an-editorial-qna-pair"></a>Adicione um par editorial qnA

1. Inscreva-se no [portal QnA](https://www.qnamaker.ai/)e, em seguida, selecione a base de conhecimento para adicionar o par QnA.
1. Na página **EDIT** da base de conhecimento, selecione **Adicionar par QnA** para adicionar um novo par QnA.

    > [!div class="mx-imgBorder"]
    > ![Adicione par QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. Na nova linha de pares QnA, adicione os campos de perguntas e respostas necessários. Os outros campos são opcionais. Todos os campos podem ser mudados a qualquer momento.

1. Opcionalmente, adicione **[frases alternativas.](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)** A formulação alternativa é qualquer forma da pergunta que é significativamente diferente da pergunta original, mas deve fornecer a mesma resposta.

    Quando a sua base de conhecimento é publicada, e você tem [aprendizagem ativa](use-active-learning.md) ligada, QnA Maker recolhe escolhas de frases alternativas para você aceitar. Estas escolhas são selecionadas de forma a aumentar a precisão da previsão.

1. Opcionalmente, adicione **[metadados.](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)** Para ver metadados, selecione **Ver opções** no menu de contexto. Os metadados fornecem filtros às respostas que a aplicação do cliente, como um chat bot, fornece.

1. Opcionalmente, adicione **[as instruções de acompanhamento](multiturn-conversation.md)**. As solicitações de seguimento fornecem caminhos de conversação adicionais para a aplicação do cliente para apresentar ao utilizador.

1. Selecione **Guardar e treinar** para ver previsões, incluindo o novo par QnA.

## <a name="rich-text-editing-for-answer"></a>Edição de texto rico para resposta

A edição de texto rico do seu texto de resposta dá-lhe um estilo de marcação a partir de uma barra de ferramentas simples.

1. Selecione a área de texto para obter uma resposta, a barra de ferramentas do editor de texto rico exibe na linha de pares QnA.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do editor de texto rico com a pergunta e resposta de uma linha de pares QnA.](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    Qualquer texto já na resposta exibe corretamente, uma vez que o utilizador o verá a partir de um bot.

1. Editar o texto. Selecione funcionalidades de formatação a partir da barra de ferramentas de edição de texto rico ou use a função de alternância para mudar para sintaxe de marcação.

    > [!div class="mx-imgBorder"]
    > ![Use o editor de texto rico para escrever e formar texto e economizar como marcação.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Características de editor de texto rico|Atalho de teclado|
    |--|--|
    |Alternaentre o editor de texto rico e o markdown. `</>`|CTRL+M|
    |Ousado. **B**|CTR+LB|
    |Itálico, indicado com um itálico ** _I_**|CTRL+I|
    |Lista não ordenada||
    |Lista ordenada||
    |Estilo de parágrafo||
    |Imagem - adicione uma imagem disponível a partir de um URL público.|CTRL+G|
    |Adicione link para URL disponível publicamente.|CTRL+K|
    |Emoticon - adicione de uma seleção de emoticons.|CTRL+E|
    |Menu avançado - desfazer|CTRL+Z|
    |Menu avançado - refazer|CTRL+Y|

1. Adicione uma imagem à resposta utilizando o ícone Imagem na barra de ferramentas de texto rico. O editor em local precisa do URL de imagem acessível ao público e do texto alternativo para a imagem.


    > [!div class="mx-imgBorder"]
    > ![Use o editor de texto rico adicione uma imagem acessível ao público e o seu texto ALT.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Adicione um link a um URL selecionando o texto na resposta, selecionando o ícone Link na barra de ferramentas ou selecionando o ícone Link na barra de ferramentas e, em seguida, introduzir o novo texto e o URL.

    > [!div class="mx-imgBorder"]
    > ![Use o editor de texto rico adicione uma imagem acessível ao público e o seu texto ALT.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>Editar um par QnA

Qualquer campo em qualquer par QnA pode ser editado, independentemente da fonte de dados original. Alguns campos podem não ser visíveis devido às definições atuais das Opções de **Visualização,** encontradas na barra de ferramentas de contexto.

## <a name="delete-a-qna-pair"></a>Eliminar um par QnA

Para eliminar um QnA, clique no ícone **de exclusão** na extrema direita da linha QnA. Esta é uma operação permanente. Não pode ser desfeito. Considere exportar o seu KB da página **Publicar** antes de apagar os conjuntos.

![Eliminar par QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Encontre o par qnA ID

Se precisar de encontrar o par de identificação do QnA, pode encontrá-lo em dois lugares:

* Paire sobre o ícone de exclusão na linha de pares QnA que lhe interessa. O texto hover inclui o ID de par QnA.
* Exportar a base de conhecimento. Cada par QnA na base de conhecimento inclui o ID de par QnA.

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Adicione perguntas alternativas a um par QnA existente para melhorar a probabilidade de uma correspondência com uma consulta do utilizador.

![Adicionar Perguntas Alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Combinando pares QnA

Os pares QnA de ligação são fornecidos com [instruções de seguimento](multiturn-conversation.md). Esta é uma ligação lógica entre pares QnA, gerido ao nível da base de conhecimento. Pode editar as solicitações de seguimento no portal QnA Maker.

Não se pode ligar pares qnA nos metadados da resposta.

## <a name="add-metadata"></a>Adicionar metadados

Adicione conjuntos de metadados selecionando primeiro **as opções de Visualização**e, em seguida, selecione **metadados do Show**. Isto exibe a coluna de metadados. Em seguida, selecione o sinal para adicionar um conjunto de **+** metadados. Este conjunto consiste numa chave e num valor.

## <a name="save-changes-to-the-qna-pairs"></a>Guardar alterações nos pares QnA

Selecione periodicamente **Guardar e treinar** depois de fazer edificações para evitar perder alterações.

![Adicionar Metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Quando utilizar a edição de texto rico versus marcação

[A edição de](#add-an-editorial-qna-set) respostas de texto rico permite-lhe, como autor, utilizar uma barra de ferramentas para selecionar e formatar rapidamente texto.

[O markdown](../reference-markdown-format.md) é uma ferramenta melhor quando é necessário regenerar automaticamente o conteúdo para criar bases de conhecimento para serem importadas como parte de um oleoduto CI/CD ou para testes de [lote](../Quickstarts/batch-testing.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Colaborar numa base de dados de conhecimento](./collaborate-knowledge-base.md)

* [Gerir os recursos azure utilizados pela QnA Maker](set-up-qnamaker-service-azure.md)
