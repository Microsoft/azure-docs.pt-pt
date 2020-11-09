---
title: Gerir a App QnA Maker - QnA Maker
description: O QnA Maker permite que várias pessoas colaborem numa base de conhecimento. O QnA Maker oferece uma capacidade para melhorar a qualidade da sua base de conhecimento com aprendizagem ativa. Pode-se rever, aceitar ou rejeitar, e adicionar sem remover ou alterar as questões existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 93d9cc871d1cb114f7f08b68eb8ae9d597e228b9
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376490"
---
# <a name="manage-qna-maker-app"></a>Gerir a aplicação QnA Maker

A QnA Maker permite-lhe colaborar com diferentes autores e editores de conteúdos, oferecendo uma capacidade de restringir o acesso dos colaboradores com base no papel do colaborador.
Saiba mais sobre [os conceitos de autenticação de colaboradores da QnA Maker.](../Concepts/role-based-access-control.md)

Também pode melhorar a qualidade da sua base de conhecimentos sugerindo questões alternativas através da [aprendizagem ativa.](../Concepts/active-learning-suggestions.md) As submissões de utilizador são tomadas em consideração e aparecem como sugestões na lista de perguntas alternativas. Tem a flexibilidade para adicionar essas sugestões como perguntas alternativas ou rejeitá-las.

A sua base de conhecimento não muda automaticamente. Para que qualquer alteração produza efeitos, tem de aceitar as sugestões. Estas sugestões adicionam perguntas mas não alteram ou removem as questões existentes.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Adicionar controlo de acesso baseado em funções Azure (Azure RBAC)

O QnA Maker permite que várias pessoas colaborem em todas as bases de conhecimento no mesmo recurso QnA Maker. Esta funcionalidade é fornecida com [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Acesso ao nível de recursos do QnA Maker

Não é possível partilhar uma base de conhecimento específica num serviço QnA Maker. Se quiser mais controlo de acesso granular, considere distribuir as suas bases de conhecimento por diferentes recursos do QnA Maker e, em seguida, adicione papéis a cada recurso.

## <a name="add-a-role-to-a-resource"></a>Adicione um papel a um recurso

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Adicione uma conta de utilizador ao recurso QnA Maker

Os passos seguintes usam o papel de colaborador, mas qualquer um dos [papéis](../reference-role-based-access-control.md) pode ser adicionado usando estes passos

1. Inscreva-se no portal [Azure](https://portal.azure.com/) e vá para o seu recurso QnA Maker.

    ![Lista de recursos do Fabricante QnA](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Aceda ao **separador Controlo de Acesso (IAM).**

    ![IAM do Fabricante QNA](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Selecione **Adicionar**.

    ![QnA Maker IAM adicionar](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Selecione uma função da seguinte lista:

    |Função|
    |--|
    |Proprietário|
    |Contribuinte|
    |Leitor de fabricante de serviços cognitivos QnA|
    |Editor de Serviços Cognitivos QnA Maker|
    |Utilizador de Serviços Cognitivos|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM adicionar papel.":::

1. Introduza o endereço de e-mail do utilizador e prima **Salvar.**

    ![QnA Maker IAM adicionar e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Ver bases de conhecimento do QnA Maker

Quando a pessoa que partilhou o seu serviço QnA Maker com registos no [portal QnA Maker,](https://qnamaker.ai)eles podem ver todas as bases de conhecimento nesse serviço com base no seu papel.

Quando selecionam uma base de conhecimento, o seu papel atual nesse recurso QnA Maker é visível ao lado do nome base do conhecimento.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Screenshot da base de conhecimento no modo editar com nome de função em parênteses ao lado do nome base do conhecimento no canto superior esquerdo da página web.":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Atualizar versão de tempo de execução para usar a aprendizagem ativa

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Ative Learning é suportado na versão 4.4.0 ou superior. Se a sua base de conhecimento foi criada numa versão anterior, [atualize o seu tempo de execução](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) para utilizar esta funcionalidade.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Na gestão do QnA Maker (Preview), uma vez que o tempo de execução é hospedado pelo próprio serviço QnA Maker, não há necessidade de atualizar o tempo de funcionaamento manualmente.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Ligue a aprendizagem ativa para questões alternativas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

A aprendizagem ativa está fora por defeito. Liga-o para ver as perguntas sugeridas. Depois de ligar a aprendizagem ativa, precisa enviar informações da aplicação do cliente para o QnA Maker. Para obter mais informações, consulte [o fluxo arquitetónico para utilizar as APIs generateAnswer e Train a partir de um bot.](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)

1. Selecione **Publicar** para publicar a base de conhecimentos. As consultas de aprendizagem ativa são recolhidas apenas a partir do ponto final de previsão da API GenerateAnswer. As consultas ao painel de teste no portal QnA Maker não têm impacto na aprendizagem ativa.

1. Para ativar a aprendizagem no portal QnA Maker, vá para o canto superior direito, selecione o seu **Nome,** vá para [**As Definições de Serviço**](https://www.qnamaker.ai/UserSettings).

    ![Ligue as alternativas de perguntas sugeridas pela aprendizagem ativa a partir da página de definições de Serviço. Selecione o nome do utilizador no menu superior direito e, em seguida, selecione Definições de Serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Encontre o serviço QnA Maker e, em seguida, altere a **Aprendizagem Ativa**.

    > [!div class="mx-imgBorder"]
    > [![Na página de definições de Serviço, alternar na funcionalidade De Aprendizagem Ativa. Se não conseguir alternar a funcionalidade, poderá ter de atualizar o seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. A sua versão pode ser diferente.
    Uma vez ativada a **Aprendizagem Ativa,** a base de conhecimento sugere novas questões a intervalos regulares com base em perguntas submetidas pelo utilizador. Pode desativar **a Aprendizagem Ativa** tocando novamente a definição.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Por padrão, a aprendizagem ativa está em **curso no** QnA Maker gerido (Preview). Para ver as perguntas alternativas sugeridas, [utilize as opções de Ver](../How-To/improve-knowledge-base.md#view-suggested-questions) na página Editar.

---

## <a name="review-suggested-alternate-questions"></a>Revisão sugeriu perguntas alternativas

[Reveja perguntas alternativas sugeridas](improve-knowledge-base.md) na página **de Edição** de cada base de conhecimento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma base de conhecimento](./manage-knowledge-bases.md)