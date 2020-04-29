---
title: Melhorar a base de conhecimento - QnA Maker
description: Melhore a qualidade da sua base de conhecimento com aprendizagem ativa. Reveja, aceite ou rejeite, adicione sem remover ou alterar as questões existentes.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80071135"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Utilizar a aprendizagem ativa para melhorar a base de dados de conhecimento

[A aprendizagem ativa](../Concepts/active-learning-suggestions.md) permite-lhe melhorar a qualidade da sua base de conhecimentos sugerindo questões alternativas. As submissões dos utilizadores são tomadas em consideração e aparecem como sugestões na lista de perguntas alternativas. Tem a flexibilidade de adicionar essas sugestões como perguntas alternativas ou rejeitá-las.

A sua base de conhecimento não muda automaticamente. Para que qualquer alteração entre em vigor, deve aceitar as sugestões. Estas sugestões acrescentam perguntas, mas não alteram ou removem as questões existentes.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Atualizar versão de tempo de execução para usar aprendizagem ativa

Ative Learning é suportado na versão runtime 4.4.0 e acima. Se a sua base de conhecimento foi criada numa versão anterior, [atualize o seu tempo](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) de execução para utilizar esta funcionalidade.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Ativar a aprendizagem ativa para perguntas alternativas

A aprendizagem ativa está desligada por defeito. Ligue-o para ver as perguntas sugeridas. Depois de ativar a aprendizagem ativa, precisa enviar informações da app do cliente para o QnA Maker. Para obter mais informações, consulte o [fluxo arquitetónico para utilizar a GenerateAnswer e o Train APIs a partir de um bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selecione **Publicar** para publicar a base de conhecimentos. As consultas de aprendizagem ativa são recolhidas apenas a partir do ponto final de previsão Da API GenerateAnswer. As consultas ao painel de teste no portal QnA Maker não têm impacto na aprendizagem ativa.

1. Para ativar a aprendizagem ativa no portal QnA Maker, vá para o canto superior direito, selecione o seu **Nome,** vá para [**definições**](https://www.qnamaker.ai/UserSettings)de serviço .

    ![Ligue as alternativas de perguntas sugeridas pela aprendizagem ativa a partir da página de definições do Serviço. Selecione o nome do utilizador no menu superior direito e, em seguida, selecione Definições de Serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Encontre o serviço QnA Maker e, em seguida, alternar **a Aprendizagem Ativa**.

    > [!div class="mx-imgBorder"]
    > [![Na página de definições do Serviço, alternar na funcionalidade Aprendizagem Ativa. Se não conseguir alternar a funcionalidade, poderá necessitar de atualizar o seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. A sua versão pode ser diferente.

    Uma vez ativada a **Ative Learning,** a base de conhecimentos sugere novas questões em intervalos regulares com base em questões submetidas pelo utilizador. Pode desativar a **Aprendizagem Ativa** toggling a configuração novamente.

## <a name="review-suggested-alternate-questions"></a>Revisão sugeriu perguntas alternativas

[Reveja perguntas alternativas sugeridas](improve-knowledge-base.md) na página **de Edição** de cada base de conhecimento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./manage-knowledge-bases.md)