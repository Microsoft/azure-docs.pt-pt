---
title: Como testar uma base de conhecimento - QnA Maker
description: Testar a sua base de conhecimento qnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo devolvidas. Pode testar a base de conhecimento através de uma interface de chat melhorada que também lhe permite fazer edificações.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78927264"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Teste a sua base de conhecimentos em QnA Maker

Testar a sua base de conhecimento qnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo devolvidas. Pode testar a base de conhecimento através de uma interface de chat melhorada que também lhe permite fazer edificações.

## <a name="interactively-test-in-qna-maker-portal"></a>Teste interativo no portal QnA Maker

1. Aceda à sua base de conhecimento selecionando o seu nome na página **base de conhecimento Do meu conhecimento.**
1. Para aceder ao painel de diapositivos do Teste, selecione **Test** no painel superior da sua aplicação.
1. Introduza uma consulta na caixa de texto e selecione Enter.
1. A resposta mais bem igualada da base de conhecimento é devolvida como resposta.

### <a name="clear-test-panel"></a>Painel de teste claro

Para limpar todas as consultas de teste e os resultados da consola de teste, selecione **Iniciar de novo** no canto superior esquerdo do painel de teste.

### <a name="close-test-panel"></a>Painel de teste de encerramento

Para fechar o painel de teste, selecione novamente o botão **Teste.** Enquanto o painel de Teste estiver aberto, não é possível editar o conteúdo da Base de Conhecimento.

### <a name="inspect-score"></a>Inspecionar pontuação

Inspecione os detalhes do resultado do teste no painel de inspeção.

1.  Com o painel de diapositivos do teste aberto, **selecione Inspecione** mais detalhes sobre essa resposta.

    ![Inspecionar respostas](../media/qnamaker-how-to-test-kb/inspect.png)

2.  O painel de inspeção aparece. O painel inclui a intenção de pontuação superior, bem como quaisquer entidades identificadas. O painel mostra o resultado da expressão selecionada.

### <a name="correct-the-top-scoring-answer"></a>Corrija a resposta de pontuação superior

Se a resposta de pontuação superior estiver incorreta, selecione a resposta correta da lista e selecione **Guardar e Treinar**.

![Corrija a resposta de pontuação superior](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Pode adicionar formas alternativas de uma pergunta a uma determinada resposta. Digite as respostas alternativas na caixa de texto e clique em introduzir para as adicionar. Selecione **Guardar e Treinar** para armazenar as atualizações.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Adicione uma nova resposta

Pode adicionar uma nova resposta se alguma das respostas existentes que foram correspondidas estiver incorreta ou se a resposta não existir na base de conhecimentos (nenhuma boa correspondência encontrada no KB).

Na parte inferior da lista de respostas, utilize a caixa de texto para introduzir uma nova resposta e prima introduzir para adicioná-la.

Selecione **Guardar e Treinar** para persistir nesta resposta. Um novo par de respostas à pergunta foi agora adicionado à sua base de conhecimento.

> [!NOTE]
> Todas as edificações na sua base de conhecimento só são guardadas quando premir o botão **Save and Train.**

### <a name="test-the-published-knowledge-base"></a>Testar a base de conhecimentos publicada

Pode testar a versão publicada da base de conhecimento no painel de teste. Depois de publicar o KB, selecione a caixa **KB publicada** e envie uma consulta para obter resultados do KB publicado.

![Teste contra um KB publicado](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Teste de lote com ferramenta

Utilize a ferramenta de teste do lote quando quiser:

* determinar a resposta superior e pontuação para um conjunto de perguntas
* validar resposta esperada para conjunto de perguntas

Leia o [tutorial](../Quickstarts/batch-testing.md) de teste do lote para obter instruções passo a passo.

Os ensaios do lote são fornecidos com a ferramenta de teste do lote. Esta ferramenta está disponível como um [executável zipped](https://aka.ms/qnamakerbatchtestingtool) para download ou como [código fonte C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[A documentação de referência sobre a ferramenta](../reference-tsv-format-batch-testing.md) inclui:

* o exemplo da linha de comando da ferramenta
* o formato para ficheiros de entrada e ficheiros de entrada TSV

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
