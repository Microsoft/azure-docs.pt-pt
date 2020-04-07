---
title: Editar uma base de conhecimento - QnA Maker
description: O QnA Maker permite-lhe gerir o conteúdo da sua base de conhecimentos, proporcionando uma experiência de edição fácil de usar.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756739"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Editar pares QnA na sua base de conhecimentos

O QnA Maker permite-lhe gerir o conteúdo da sua base de conhecimentos, proporcionando uma experiência de edição fácil de usar.

Os pares QnA são adicionados a partir de uma fonte de dados, como um ficheiro ou URL, ou adicionados como uma fonte editorial. Uma fonte editorial indica que o par QnA foi adicionado manualmente no portal QnA. Todos os pares QnA estão disponíveis para edição.

## <a name="add-an-editorial-qna-pair"></a>Adicione um par editorial qnA
1. Inscreva-se no [portal QnA](https://www.qnamaker.ai/)e, em seguida, selecione a base de conhecimento para adicionar o par QnA.
1. Na página **EDIT** da base de conhecimento, selecione **Adicionar par QnA** para adicionar um novo par QnA.

1. Na nova linha de pares QnA, adicione os campos de **Perguntas** e **Respostas** necessários. Os outros campos são opcionais. Todos os campos podem ser mudados a qualquer momento.

1. Opcionalmente, adicione **frases alternativas.** A formulação alternativa é qualquer forma da pergunta que é significativamente diferente da pergunta original, mas deve fornecer a mesma resposta.

    Quando a sua base de conhecimento é publicada, e você tem [aprendizagem ativa](use-active-learning.md) ligada, QnA Maker recolhe escolhas de frases alternativas para você aceitar. Estas escolhas são selecionadas de forma a aumentar a precisão da previsão.

1. Opcionalmente, adicione **metadados.** Para ver metadados, selecione **Ver opções** no menu de contexto. Os metadados fornecem filtros às respostas que a aplicação do cliente, como um chat bot, fornece.

1. Opcionalmente, adicione **as instruções de acompanhamento**. As solicitações de seguimento fornecem caminhos de conversação adicionais para a aplicação do cliente para apresentar ao utilizador.

1. Selecione **Guardar e treinar** para ver previsões, incluindo o novo par QnA.

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

Adicione conjuntos de metadados selecionando primeiro **as opções de Visualização**e, em seguida, selecione **metadados do Show**. Isto exibe a coluna de metadados. Em seguida, **+** selecione o sinal para adicionar um conjunto de metadados. Este conjunto consiste numa chave e num valor.

## <a name="save-changes-to-the-qna-pairs"></a>Guardar alterações nos pares QnA

Selecione periodicamente **Guardar e treinar** depois de fazer edificações para evitar perder alterações.

![Adicionar Metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Colaborar numa base de dados de conhecimento](./collaborate-knowledge-base.md)

* [Gerir os recursos azure utilizados pela QnA Maker](set-up-qnamaker-service-azure.md)