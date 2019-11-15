---
title: Como testar uma base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: Teste a sua base de dados de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas a ser devolvido. Pode testar a base de dados de conhecimento através de uma interface de bate-papo avançada também permite que faça edições.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c139d3a740067e3cecaff90d3171d7b0cb3d52c7
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091759"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Teste sua base de dados de conhecimento no QnA Maker

Teste a sua base de dados de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas a ser devolvido. Pode testar a base de dados de conhecimento através de uma interface de bate-papo avançada também permite que faça edições.

## <a name="interactively-test-in-qna-maker-portal"></a>Teste interativamente no portal QnA Maker

1. Acesse sua base de dados de conhecimento selecionando seu nome na página **minhas bases de dados de conhecimento** .
1. Para acessar o painel de deslizamento de teste, selecione **teste** no painel superior do seu aplicativo.
1. Introduza uma consulta na caixa de texto e selecione Enter.
1. A resposta mais correspondente a partir da base de dados de conhecimento é retornada como a resposta.

### <a name="clear-test-panel"></a>Painel de teste clara

Para limpar todas as consultas de teste inseridas e seus resultados do console de teste, selecione **recomeçar** no canto superior esquerdo do painel de teste.

### <a name="close-test-panel"></a>Painel de teste fechar

Para fechar o painel de teste, selecione o botão **testar** novamente. Enquanto o painel de teste estiver aberto, não é possível editar o conteúdo de Base de dados de conhecimento.

### <a name="inspect-score"></a>Inspecionar a pontuação

Você inspeciona detalhes do resultado do teste no painel inspecionar.

1.  Com o painel deslizante de teste aberto, selecione **inspecionar** para obter mais detalhes sobre essa resposta.

    ![Inspecionar as respostas](../media/qnamaker-how-to-test-kb/inspect.png)

2.  O painel de inspeção é exibido. O painel inclui a parte superior de classificação intenção, bem como qualquer entidades identificadas. O painel mostra o resultado da expressão selecionada.

### <a name="correct-the-top-scoring-answer"></a>Corrija o superior de resposta de classificação

Se a parte superior de classificação resposta estiver incorreta, selecione a resposta correta na lista e selecione **guardar e treinar**.

![Corrija o superior de resposta de classificação](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Pode adicionar formas alternativas de uma pergunta para uma determinada resposta. Tipo a alternativa respostas na caixa de texto e clique em Inserir para adicioná-los. Selecione **guardar e treinar** para armazenar as atualizações.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Adicionar uma nova resposta

Pode adicionar uma nova resposta, se qualquer uma das respostas existentes que foram correspondidas estão incorretas ou a resposta não existe na base de dados de conhecimento (encontrada nenhuma correspondência boa no KB). 

Na parte inferior da lista de respostas, use a caixa de texto para inserir uma nova resposta e pressione ENTER para adicioná-la. 

Selecione **guardar e treinar** para manter esta resposta. Agora foi adicionado um novo par de pergunta-resposta à sua base de dados de conhecimento. 

> [!NOTE]
> Todas as edições em sua base de dados de conhecimento apenas serão salvos de quando pressiona o **guardar e treinar** botão.

### <a name="test-the-published-knowledge-base"></a>Testar a base de dados de conhecimento publicada

Você pode testar a versão publicada da base de dados de conhecimento no painel de teste. Depois de publicar a KB, selecione a caixa **KB publicados** e envie uma consulta para obter os resultados da KB publicada.

![Teste em relação a uma base de conhecimento publicada](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Teste em lote com a ferramenta

Use a ferramenta de teste do lote quando desejar:

* determinar a resposta principal e a pontuação de um conjunto de perguntas
* validar a resposta esperada para o conjunto de perguntas

Os testes em lote são fornecidos com a ferramenta de teste em lote. Essa ferramenta está disponível como um [executável compactado](https://aka.ms/qnamakerbatchtestingtool) para download ou como [ C# código-fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting). 

[A documentação de referência sobre a ferramenta](../reference-tsv-format-batch-testing.md) inclui:

* o exemplo de linha de comando da ferramenta
* o formato para arquivos de entrada TSV e de outfile 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
