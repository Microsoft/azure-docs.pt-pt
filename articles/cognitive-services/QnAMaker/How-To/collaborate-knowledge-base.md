---
title: Colaborando na base de conhecimento - QnA Maker
description: A QnA Maker permite que várias pessoas colaborem numa base de conhecimento. Esta funcionalidade é fornecida com o Controlo de Acesso baseado em Funções Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650778"
---
# <a name="collaboration-with-authors-and-editors"></a>Colaboração com autores e editores

A colaboração é fornecida ao nível de recursos do QnA Maker para permitir restringir o acesso dos colaboradores com base no papel do colaborador. Saiba mais sobre [os conceitos](../Concepts/role-based-access-control.md)de autenticação do colaborador QnA Maker.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Adicione o acesso baseado em papéis (RBAC) ao seu recurso QnA Maker

O QnA Maker permite que várias pessoas colaborem em todas as bases de conhecimento no mesmo recurso QnA Maker. Esta funcionalidade é fornecida com o Controlo de [Acesso baseado em funções](../../../active-directory/role-based-access-control-configure.md)Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Acesso ao nível de recursos do Fabricante qnA

Não pode partilhar uma base de conhecimento específica num serviço QnA Maker. Se quiser um controlo de acesso mais granular, considere distribuir as suas bases de conhecimento através de diferentes recursos do QnA Maker, em seguida, adicione funções a cada recurso.

## <a name="add-role-to-resource"></a>Adicionar papel ao recurso

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Adicione uma conta de utilizador ao recurso QnA Maker

Os seguintes passos usam o papel do colaborador, mas qualquer uma das [funções](../reference-role-based-access-control.md) pode ser adicionada usando estes passos

1. Inscreva-se no portal [Azure](https://portal.azure.com/) e vá ao seu recurso QnA Maker.

    ![Lista de recursos do Fabricante qna](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Aceda ao separador Controlo de **Acesso (IAM).**

    ![Fabricante de QnA IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selecione **Adicionar**.

    ![QnA Maker IAM adicionar](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selecione uma função na seguinte lista:

    |Função|
    |--|
    |Proprietário|
    |Contribuinte|
    |Leitor de Criador qna|
    |Editor de Criador da QnA|
    |Utilizador de Serviços Cognitivos|

    ![QnA Maker IAM adicionar papel](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Introduza o endereço de e-mail do utilizador e prima **Guardar**.

    ![QnA Maker IAM adicionar e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando a pessoa que partilhou o seu serviço QnA Maker com logins no [portal QnA Maker,](https://qnamaker.ai)eles podem ver todas as bases de conhecimento desse serviço com base no seu papel.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)

Saiba mais sobre colaboração:
* Controlo de acesso baseado em funções [azure](../../../active-directory/role-based-access-control-configure.md)
* Conceitos de controlo de acesso [baseados](../Concepts/role-based-access-control.md) em papéis qnA Maker
