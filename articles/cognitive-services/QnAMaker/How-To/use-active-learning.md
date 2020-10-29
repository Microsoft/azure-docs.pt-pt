---
title: Use aprendizagem ativa com base de conhecimento - QnA Maker
description: Aprenda a melhorar a qualidade da sua base de conhecimentos com aprendizagem ativa. Reveja, aceite ou rejeite, adicione sem remover ou alterar as questões existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 8492e2722e456b689e23041726f6eaf94e284c3b
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93028130"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Utilizar a aprendizagem ativa para melhorar a base de dados de conhecimento

[A aprendizagem ativa](../Concepts/active-learning-suggestions.md) permite-lhe melhorar a qualidade da sua base de conhecimentos, sugerindo questões alternativas. As submissões de utilizador são tomadas em consideração e aparecem como sugestões na lista de perguntas alternativas. Tem a flexibilidade para adicionar essas sugestões como perguntas alternativas ou rejeitá-las.

A sua base de conhecimento não muda automaticamente. Para que qualquer alteração produza efeitos, tem de aceitar as sugestões. Estas sugestões adicionam perguntas mas não alteram ou removem as questões existentes.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Atualizar versão de tempo de execução para usar a aprendizagem ativa

Ative Learning é suportado na versão 4.4.0 ou superior. Se a sua base de conhecimento foi criada numa versão anterior, [atualize o seu tempo de execução](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) para utilizar esta funcionalidade.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Ligue a aprendizagem ativa para questões alternativas

A aprendizagem ativa está fora por defeito. Liga-o para ver as perguntas sugeridas. Depois de ligar a aprendizagem ativa, precisa enviar informações da aplicação do cliente para o QnA Maker. Para obter mais informações, consulte [o fluxo arquitetónico para utilizar as APIs generateAnswer e Train a partir de um bot.](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)

1. Selecione **Publicar** para publicar a base de conhecimentos. As consultas de aprendizagem ativa são recolhidas apenas a partir do ponto final de previsão da API GenerateAnswer. As consultas ao painel de teste no portal QnA Maker não têm impacto na aprendizagem ativa.

1. Para ativar a aprendizagem no portal QnA Maker, vá para o canto superior direito, selecione o seu **Nome,** vá para [**As Definições de Serviço**](https://www.qnamaker.ai/UserSettings).

    ![Ligue as alternativas de perguntas sugeridas pela aprendizagem ativa a partir da página de definições de Serviço. Selecione o nome do utilizador no menu superior direito e, em seguida, selecione Definições de Serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Encontre o serviço QnA Maker e, em seguida, altere a **Aprendizagem Ativa** .

    > [!div class="mx-imgBorder"]
    > [![Na página de definições de Serviço, alternar na funcionalidade De Aprendizagem Ativa. Se não conseguir alternar a funcionalidade, poderá ter de atualizar o seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. A sua versão pode ser diferente.

    Uma vez ativada a **Aprendizagem Ativa,** a base de conhecimento sugere novas questões a intervalos regulares com base em perguntas submetidas pelo utilizador. Pode desativar **a Aprendizagem Ativa** tocando novamente a definição.

## <a name="review-suggested-alternate-questions"></a>Revisão sugeriu perguntas alternativas

[Reveja perguntas alternativas sugeridas](improve-knowledge-base.md) na página **de Edição** de cada base de conhecimento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma base de conhecimento](./manage-knowledge-bases.md)
