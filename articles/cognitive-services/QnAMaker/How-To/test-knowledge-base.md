---
title: Como testar uma base de dados de conhecimento - QnA Maker
description: Teste a sua base de dados de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas a ser devolvido. Pode testar a base de dados de conhecimento através de uma interface de bate-papo avançada também permite que faça edições.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927264"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Teste a sua base de conhecimentos em QnA Maker

Teste a sua base de dados de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas a ser devolvido. Pode testar a base de dados de conhecimento através de uma interface de bate-papo avançada também permite que faça edições.

## <a name="interactively-test-in-qna-maker-portal"></a>Teste interativo no portal QnA Maker

1. Aceda à sua base de conhecimento selecionando o seu nome na página **base de conhecimento Do meu conhecimento.**
1. Para aceder ao painel de diapositivos do Teste, selecione **Test** no painel superior da sua aplicação.
1. Introduza uma consulta na caixa de texto e selecione Enter.
1. A resposta mais correspondente a partir da base de dados de conhecimento é retornada como a resposta.

### <a name="clear-test-panel"></a>Painel de teste clara

Para limpar todas as consultas de teste e os resultados da consola de teste, selecione **Iniciar de novo** no canto superior esquerdo do painel de teste.

### <a name="close-test-panel"></a>Painel de teste fechar

Para fechar o painel de teste, selecione novamente o botão **Teste.** Enquanto o painel de teste estiver aberto, não é possível editar o conteúdo de Base de dados de conhecimento.

### <a name="inspect-score"></a>Inspecionar a pontuação

Inspecione os detalhes do resultado do teste no painel de inspeção.

1.  Com o painel de diapositivos do teste aberto, **selecione Inspecione** mais detalhes sobre essa resposta.

    ![Inspecionar as respostas](../media/qnamaker-how-to-test-kb/inspect.png)

2.  O painel de inspeção aparece. O painel inclui a parte superior de classificação intenção, bem como qualquer entidades identificadas. O painel mostra o resultado da expressão selecionada.

### <a name="correct-the-top-scoring-answer"></a>Corrija o superior de resposta de classificação

Se a resposta de pontuação superior estiver incorreta, selecione a resposta correta da lista e selecione **Guardar e Treinar**.

![Corrija o superior de resposta de classificação](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Pode adicionar formas alternativas de uma pergunta para uma determinada resposta. Tipo a alternativa respostas na caixa de texto e clique em Inserir para adicioná-los. Selecione **Guardar e Treinar** para armazenar as atualizações.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Adicionar uma nova resposta

Pode adicionar uma nova resposta, se qualquer uma das respostas existentes que foram correspondidas estão incorretas ou a resposta não existe na base de dados de conhecimento (encontrada nenhuma correspondência boa no KB).

Na parte inferior da lista de respostas, utilize a caixa de texto para introduzir uma nova resposta e prima introduzir para adicioná-la.

Selecione **Guardar e Treinar** para persistir nesta resposta. Agora foi adicionado um novo par de pergunta-resposta à sua base de dados de conhecimento.

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

Os ensaios do lote são fornecidos com a ferramenta de teste do lote. Esta ferramenta está disponível como um [executável com fecho](https://aka.ms/qnamakerbatchtestingtool) de preços para download ou como [ C# código fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[A documentação de referência sobre a ferramenta](../reference-tsv-format-batch-testing.md) inclui:

* o exemplo da linha de comando da ferramenta
* o formato para ficheiros de entrada e ficheiros de entrada TSV

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
