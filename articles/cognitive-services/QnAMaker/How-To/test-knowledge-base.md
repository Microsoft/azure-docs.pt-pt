---
title: Como testar uma base de dados de conhecimento-QnA Maker
titleSuffix: Azure Cognitive Services
description: Testar seu QnA Maker base de dados de conhecimento é uma parte importante de um processo iterativo para melhorar a precisão das respostas retornadas. Você pode testar a base de dados de conhecimento por meio de uma interface de chat aprimorada que também permite fazer edições.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 64b4b9a4ad5ceb0b3c33ae022b34daeafda93a62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491202"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Teste sua base de dados de conhecimento no QnA Maker

Testar seu QnA Maker base de dados de conhecimento é uma parte importante de um processo iterativo para melhorar a precisão das respostas retornadas. Você pode testar a base de dados de conhecimento por meio de uma interface de chat aprimorada que também permite fazer edições.

## <a name="interactively-test-in-qna-maker-portal"></a>Teste interativamente no portal QnA Maker

1. Acesse sua base de dados de conhecimento selecionando seu nome na página **minhas bases de dados de conhecimento** .
1. Para acessar o painel de deslizamento de teste, selecione **teste** no painel superior do seu aplicativo.
1. Insira uma consulta na caixa de texto e selecione Enter.
1. A resposta de melhor correspondência da base de dados de conhecimento é retornada como a resposta.

### <a name="clear-test-panel"></a>Limpar o painel de teste

Para limpar todas as consultas de teste inseridas e seus resultados do console de teste, selecione **recomeçar** no canto superior esquerdo do painel de teste.

### <a name="close-test-panel"></a>Fechar o painel de teste

Para fechar o painel de teste, selecione o botão **testar** novamente. Enquanto o painel de teste estiver aberto, você não poderá editar o conteúdo da base de dados de conhecimento.

### <a name="inspect-score"></a>Inspecionar Pontuação

Você inspeciona detalhes do resultado do teste no painel inspecionar.

1.  Com o painel deslizante de teste aberto, selecione **inspecionar** para obter mais detalhes sobre essa resposta.

    ![Inspecionar respostas](../media/qnamaker-how-to-test-kb/inspect.png)

2.  O painel de inspeção é exibido. O painel inclui a principal intenção de pontuação, bem como quaisquer entidades identificadas. O painel mostra o resultado do expressão selecionado.

### <a name="correct-the-top-scoring-answer"></a>Corrigir a resposta de pontuação superior

Se a resposta de pontuação superior estiver incorreta, selecione a resposta correta na lista e selecione **salvar e treinar**.

![Corrigir a resposta de pontuação superior](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Você pode adicionar formas alternativas de uma pergunta a uma determinada resposta. Digite as respostas alternativas na caixa de texto e clique em Enter para adicioná-las. Selecione **salvar e treinar** para armazenar as atualizações.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Adicionar uma nova resposta

Você pode adicionar uma nova resposta se alguma das respostas existentes que foram correspondidas estiver incorreta ou a resposta não existir na base de dados de conhecimento (nenhuma correspondência adequada encontrada no KB). 

Na parte inferior da lista de respostas, use a caixa de texto para inserir uma nova resposta e pressione ENTER para adicioná-la. 

Selecione **salvar e treinar** para persistir essa resposta. Um novo par pergunta-resposta agora foi adicionado à sua base de dados de conhecimento. 

> [!NOTE]
> Todas as edições na base de dados de conhecimento serão salvas apenas quando você pressionar o botão **salvar e treinar** .

### <a name="test-the-published-knowledge-base"></a>Testar a base de dados de conhecimento publicada

Você pode testar a versão publicada da base de dados de conhecimento no painel de teste. Depois de publicar a KB, selecione a caixa **KB publicados** e envie uma consulta para obter os resultados da KB publicada.

![Teste em relação a uma base de conhecimento publicada](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Teste em lote com a ferramenta

Use a ferramenta de teste do lote quando desejar:

* determinar a resposta principal e a pontuação de um conjunto de perguntas
* validar a resposta esperada para o conjunto de perguntas

Os testes em lote são fornecidos com a ferramenta de teste em lote. Essa ferramenta está disponível como um [executável compactado](https://qnamakerstore.blob.core.windows.net/qnamakerdata/batchtesting/bt.zip) para download ou como [ C# código-fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting). 

[A documentação de referência sobre a ferramenta](../reference-tsv-format-batch-testing.md) inclui:

* o exemplo de linha de comando da ferramenta
* o formato para arquivos de entrada TSV e de outfile 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
