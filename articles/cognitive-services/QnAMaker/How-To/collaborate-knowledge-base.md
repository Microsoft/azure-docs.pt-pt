---
title: Colaboração na base de conhecimento - QnA Maker
description: O QnA Maker permite que várias pessoas colaborem numa base de conhecimento. Esta funcionalidade é fornecida com o controlo de acesso baseado em funções Azure (Azure RBAC).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 8adc02d4b397c2b0bdaf5d3998cfbe229c896d41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776855"
---
# <a name="collaboration-with-authors-and-editors"></a>Colaboração com autores e editores

A colaboração é fornecida ao nível dos recursos do QnA Maker para que possa restringir o acesso dos colaboradores com base no papel do colaborador. Saiba mais sobre [os conceitos](../Concepts/role-based-access-control.md)de autenticação de colaboradores da QnA Maker.

## <a name="add-azure-role-based-access-control-azure-rbac-to-your-qna-maker-resource"></a>Adicione o controlo de acesso baseado em funções Azure (Azure RBAC) ao seu recurso QnA Maker

O QnA Maker permite que várias pessoas colaborem em todas as bases de conhecimento no mesmo recurso QnA Maker. Esta funcionalidade é fornecida com o [controlo de acesso baseado em funções Azure (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Acesso ao nível de recursos do QnA Maker

Não é possível partilhar uma base de conhecimento específica num serviço QnA Maker. Se quiser mais controlo de acesso granular, considere distribuir as suas bases de conhecimento por diferentes recursos do QnA Maker e, em seguida, adicione papéis a cada recurso.

## <a name="add-role-to-resource"></a>Adicionar papel ao recurso

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

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="QnA Maker IAM adicionar papel.":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)

Saiba mais sobre a colaboração:
* Controlo de acesso baseado em funções [Azure](../../../active-directory/role-based-access-control-configure.md)
* Conceitos de controlo de acesso baseados em [funções](../Concepts/role-based-access-control.md) da QnA Maker
