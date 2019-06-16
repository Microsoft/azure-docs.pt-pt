---
title: Como testar uma base de dados de conhecimento - QnA Maker
titlesuffix: Azure Cognitive Services
description: Teste a sua base de dados de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas a ser devolvido. Pode testar a base de dados de conhecimento através de uma interface de bate-papo avançada também permite que faça edições.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2c596b49d5587b07fe75cefde72e897478dc3dc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472118"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testar a sua base de dados de conhecimento interativamente em QnA Maker

Teste a sua base de dados de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas a ser devolvido. Pode testar a base de dados de conhecimento através de uma interface de bate-papo avançada também permite que faça edições.

## <a name="test-answer-matching"></a>Correspondência de resposta de teste

1. Aceder à sua base de dados de conhecimento, selecionando o respetivo nome na **meu bases de dados de conhecimento** página.
1. Para acessar o painel de slide-out de teste, selecione **teste** no painel superior do seu aplicativo.
1. Introduza uma consulta na caixa de texto e selecione Enter.
1. A resposta mais correspondente a partir da base de dados de conhecimento é retornada como a resposta.

## <a name="clear-test-panel"></a>Painel de teste clara

Para limpar todas as consultas de teste inseridos e seus resultados a partir da consola de teste, selecione **recomeçar** no canto superior esquerdo do painel de teste.

## <a name="close-test-panel"></a>Painel de teste fechar

Para fechar o painel de teste, selecione o **teste** novamente no botão. Enquanto o painel de teste estiver aberto, não é possível editar o conteúdo de Base de dados de conhecimento.

## <a name="inspect-score"></a>Inspecionar a pontuação

Inspecione os detalhes do resultado do teste no painel de Inspect.

1.  Com o painel de deslizantes Test aberto, selecione **Inspect** para obter mais detalhes sobre que a resposta.

    ![Inspecionar as respostas](../media/qnamaker-how-to-test-kb/inspect.png)

2.  É apresentado o painel de inspeção. O painel inclui a parte superior de classificação intenção, bem como qualquer entidades identificadas. O painel mostra o resultado da expressão selecionada.

## <a name="correct-the-top-scoring-answer"></a>Corrija o superior de resposta de classificação

Se a parte superior de classificação resposta estiver incorreta, selecione a resposta correta na lista e selecione **guardar e treinar**.

![Corrija o superior de resposta de classificação](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Pode adicionar formas alternativas de uma pergunta para uma determinada resposta. Tipo a alternativa respostas na caixa de texto e clique em Inserir para adicioná-los. Selecione **guardar e treinar** para armazenar as atualizações.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Adicionar uma nova resposta

Pode adicionar uma nova resposta, se qualquer uma das respostas existentes que foram correspondidas estão incorretas ou a resposta não existe na base de dados de conhecimento (encontrada nenhuma correspondência boa no KB). 

Na parte inferior da lista de respostas, utilize a caixa de texto para introduzir uma nova resposta e prima enter para adicioná-lo. 

Selecione **guardar e treinar** para manter esta resposta. Agora foi adicionado um novo par de pergunta-resposta à sua base de dados de conhecimento. 

> [!NOTE]
> Todas as edições em sua base de dados de conhecimento apenas serão salvos de quando pressiona o **guardar e treinar** botão.

## <a name="test-the-published-knowledge-base"></a>Testar a base de dados de conhecimento publicada

Pode testar a versão publicada da base de dados de conhecimento no painel de teste. Assim que tiver publicado a KB, selecione o **publicados KB** caixa e enviará uma consulta para obter resultados do KB publicado.

![O teste com um KB publicado](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
