---
title: Gerir a App QnA Maker - QnA Maker
description: O QnA Maker permite que várias pessoas colaborem numa base de conhecimento. O QnA Maker oferece uma capacidade para melhorar a qualidade da sua base de conhecimento com aprendizagem ativa. Pode-se rever, aceitar ou rejeitar, e adicionar sem remover ou alterar as questões existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e4f0e229488093067b231a5c92334238ca216234
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550560"
---
# <a name="manage-qna-maker-app"></a>Gerir a aplicação QnA Maker

A QnA Maker permite-lhe colaborar com diferentes autores e editores de conteúdos, oferecendo uma capacidade de restringir o acesso dos colaboradores com base no papel do colaborador.
Saiba mais sobre [os conceitos de autenticação de colaboradores da QnA Maker.](../Concepts/role-based-access-control.md)

Também pode melhorar a qualidade da sua base de conhecimentos sugerindo questões alternativas através da [aprendizagem ativa.](../Concepts/active-learning-suggestions.md) As submissões de utilizador são tomadas em consideração e aparecem como sugestões na lista de perguntas alternativas. Tem a flexibilidade para adicionar essas sugestões como perguntas alternativas ou rejeitá-las.

A sua base de conhecimento não muda automaticamente. Para que qualquer alteração produza efeitos, tem de aceitar as sugestões. Estas sugestões adicionam perguntas mas não alteram ou removem as questões existentes.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Adicionar controlo de acesso baseado em funções Azure (Azure RBAC)

O QnA Maker permite que várias pessoas colaborem em todas as bases de conhecimento no mesmo recurso QnA Maker. Esta funcionalidade é fornecida com [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md).

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma base de conhecimento](./manage-knowledge-bases.md)
