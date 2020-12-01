---
title: Editar uma base de conhecimento - QnA Maker
description: O QnA Maker permite-lhe gerir o conteúdo da sua base de conhecimentos proporcionando uma experiência de edição fácil de usar.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 9541320f65060a0b1f2b5c84a131c08e92554e9e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351712"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Edite os pares QnA na sua base de conhecimentos

O QnA Maker permite-lhe gerir o conteúdo da sua base de conhecimentos proporcionando uma experiência de edição fácil de usar.

Os pares QNA são adicionados a partir de uma fonte de dados, como um ficheiro ou URL, ou adicionados como uma fonte editorial. Uma fonte editorial indica que o par QnA foi adicionado manualmente no portal QnA. Todos os pares QnA estão disponíveis para edição.

<a name="add-an-editorial-qna-set"></a>

## <a name="add-an-editorial-qna-pair"></a>Adicione um par editorial QnA

1. Inscreva-se no [portal QnA](https://www.qnamaker.ai/)e, em seguida, selecione a base de conhecimento para adicionar o par QnA.
1. Na página **EDIT** da base de conhecimento, selecione **Adicionar par QnA** para adicionar um novo par QnA.

    > [!div class="mx-imgBorder"]
    > ![Adicionar par QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. Na nova linha de pares QnA, adicione os campos de perguntas e respostas necessários. Os outros campos são opcionais. Todos os campos podem ser mudados a qualquer momento.

1. Opcionalmente, adicione **[frases alternativas](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)**. A formulação alternativa é qualquer forma da pergunta que é significativamente diferente da pergunta original, mas deve fornecer a mesma resposta.

    Quando a sua base de conhecimento é publicada e tem [a aprendizagem ativa](use-active-learning.md) ligada, a QnA Maker recolhe escolhas de frases alternativas para que aceite. Estas escolhas são selecionadas de forma a aumentar a precisão da previsão.

1. Opcionalmente, adicione **[metadados.](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)** Para visualizar metadados, **selecione Ver opções** no menu de contexto. Os metadados fornecem filtros às respostas que a aplicação do cliente, como um chat bot, fornece.

1. Opcionalmente, adicione **[pedidos de acompanhamento](multiturn-conversation.md)**. As instruções de acompanhamento fornecem caminhos de conversação adicionais para a aplicação do cliente para apresentar ao utilizador.

1. **Selecione Save e treine** para ver previsões, incluindo o novo par QnA.

## <a name="rich-text-editing-for-answer"></a>Edição de texto rico para resposta

A edição de texto rico do seu texto de resposta dá-lhe um estilo de marcação a partir de uma barra de ferramentas simples.

1. Selecione a área de texto para obter uma resposta, a barra de ferramentas do editor de texto rico aparece na linha de pares QnA.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do editor de texto rico com a pergunta e resposta de uma linha de pares QnA.](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    Qualquer texto já na resposta apresenta-se corretamente, uma vez que o seu utilizador o verá a partir de um bot.

1. Editar o texto. Selecione as funcionalidades de formatação a partir da barra de ferramentas de edição de texto rico ou use a função de toggle para mudar para a sintaxe de marcação.

    > [!div class="mx-imgBorder"]
    > ![Utilize o editor de texto rico para escrever e formatar texto e guardar como ponto de marcação.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Recursos de editor de texto rico|Atalho de teclado|
    |--|--|
    |Alternar entre editor de texto rico e markdown. `</>`|CTRL+M|
    |É arrojado. **B**|CTR+LB|
    |Itálico, indicado com um iálicizado **_I_**|CTRL+I|
    |Lista não ordenada||
    |Lista ordenada||
    |Estilo parágrafo||
    |Imagem - adicione uma imagem disponível a partir de um URL público.|CTRL+G|
    |Adicione link ao URL disponível ao público.|CTRL+K|
    |Emoticon - adicione de uma seleção de emoticons.|CTRL+E|
    |Menu avançado - desfazer|CTRL+Z|
    |Menu avançado - redo|CTRL+Y|

1. Adicione uma imagem à resposta utilizando o ícone Imagem na barra de ferramentas de texto rico. O editor no local precisa do URL de imagem acessível ao público e do texto alternativo para a imagem.


    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o editor no local com o URL de imagem acessível ao público e texto alternativo para a imagem inserida.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Adicione um link a um URL selecionando o texto na resposta, selecionando o ícone Link na barra de ferramentas ou selecionando o ícone Link na barra de ferramentas, introduzindo depois um novo texto e o URL.

    > [!div class="mx-imgBorder"]
    > ![Utilize o editor de texto rico adicionar uma imagem acessível ao público e o seu texto ALT.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>Editar um par QnA

Qualquer campo em qualquer par QnA pode ser editado, independentemente da fonte de dados original. Alguns campos podem não estar visíveis devido às definições **atuais das Opções de Visualização,** encontradas na barra de ferramentas de contexto.

## <a name="delete-a-qna-pair"></a>Apague um par QnA

Para eliminar um QnA, clique no ícone **de exclusão** na extrema direita da linha QnA. Esta é uma operação permanente. Não pode ser desfeito. Considere exportar o seu KB da página **Publicar** antes de eliminar os pares.

![Eliminar par QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Encontre o ID do par QnA

Se precisar encontrar o ID do par QnA, pode encontrá-lo em dois lugares:

* Hover no ícone de eliminação na linha de pares QnA em que está interessado. O texto hover inclui o ID do par QnA.
* Exportar a base de conhecimentos. Cada par QnA na base de conhecimento inclui o ID do par QnA.

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Adicione perguntas alternativas a um par QnA existente para melhorar a probabilidade de uma correspondência com uma consulta do utilizador.

![Adicionar Perguntas Alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Ligação dos pares QnA

A ligação dos pares QnA é fornecida com [indicações de seguimento](multiturn-conversation.md). Esta é uma ligação lógica entre pares QnA, geridos ao nível da base de conhecimento. Pode editar pedidos de seguimento no portal QnA Maker.

Não pode ligar os pares QnA nos metadados da resposta.

## <a name="add-metadata"></a>Adicionar metadados

Adicione os pares de metadados selecionando primeiro **as opções de visualização** e, em seguida, selecione **Mostrar metadados**. Isto mostra a coluna de metadados. Em seguida, selecione o **+** sinal para adicionar um par de metadados. Este par é composto por uma chave e um valor.

Saiba mais sobre metadados no portal QnA Maker quickstart para metadados:
* [Autoria - adicione metadados ao par QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Previsão de consulta - respostas de filtro por metadados](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>Guardar alterações nos pares QnA

Selecione periodicamente **Guardar e treinar** depois de fazer edições para evitar perder alterações.

![Adicionar metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Quando usar edição de texto rico versus markdown

[A edição de](#add-an-editorial-qna-set) respostas em texto rico permite-lhe, como autor, utilizar uma barra de ferramentas formatação para selecionar e formatar texto rapidamente.

[O Markdown](../reference-markdown-format.md) é uma ferramenta melhor quando é necessário autogerar conteúdo para criar bases de conhecimento para ser importado como parte de um pipeline CI/CD ou para [testes de lote](../index.yml).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Colaborar numa base de dados de conhecimento](../index.yml)

* [Gerir os recursos do Azure utilizados pela QnA Maker](set-up-qnamaker-service-azure.md)