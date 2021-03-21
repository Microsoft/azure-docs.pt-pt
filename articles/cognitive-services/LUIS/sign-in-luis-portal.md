---
title: Iniciar sessão no portal LUIS
description: Se for um novo utilizador a iniciar sessão no portal LUIS, a experiência de inscrição será ligeiramente diferente com base na sua conta de utilizador corrente.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706780"
---
# <a name="sign-in-to-luis-portal"></a>Iniciar sessão no portal LUIS

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Use este artigo para começar com o portal LUIS e crie um recurso de autoria. Depois de completar os passos neste artigo, poderá criar e publicar aplicações LUIS.

## <a name="access-the-portal"></a>Aceda ao portal


1. Para começar com o LUIS, vá ao [Portal LUIS.](https://www.luis.ai) Caso ainda não tenha uma subscrição, será solicitado que vá criar uma [conta gratuita](https://azure.microsoft.com//free/cognitive-services/) e regressar ao portal.
2. Atualize a página para atualizá-la com a sua subscrição recém-criada
3. Selecione a sua subscrição a partir da lista de dropdown

    > [!div class="mx-imgBorder"]
    > ![selecionar subscrições](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Se a sua subscrição viver sob outro inquilino, não poderá trocar os inquilinos da janela existente. Você pode trocar de inquilino fechando esta janela e selecionando o avatar mais à direita contendo as suas iniciais na barra superior. Clique em **Escolher um recurso de autoria diferente** a partir da parte superior para reabrir a janela.

    > [!div class="mx-imgBorder"]
    > ![diretórios de comutação](./media/migrate-authoring-key/switch-directories.png)

5. Se tiver um recurso de autoria LUIS existente associado à sua subscrição, escolha-o na lista de abandono. Pode ver todas as aplicações criadas sob este recurso de autoria.
6. Caso contrário, clique em **Criar um novo recurso de autoria** na parte inferior deste modal.
7.  Ao criar um novo recurso de autoria, forneça as seguintes informações:

    > [!div class="mx-imgBorder"]
    > ![Criar novos recursos](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Nome do Inquilino** - o inquilino a que a sua assinatura Azure está associada. Não poderá trocar os inquilinos da janela existente. Pode trocar os inquilinos fechando esta janela e selecionando o avatar no canto superior direito do ecrã, contendo as suas iniciais. Selecione Escolha um recurso de **autoria diferente** a partir da parte superior para reabrir a janela.
    * **Nome do grupo Azure Resource** - um nome de grupo de recursos personalizado que escolher na sua subscrição. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão. Se atualmente não tiver um grupo de recursos na sua subscrição, não poderá criar um no portal LUIS. Vá ao [portal Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) para criar um e depois vá ao LUIS para continuar o processo de inscrição.
    * **Nome do Recurso Azure** - um nome personalizado que escolhe, usado como parte do URL para as suas transações de autoria. O seu nome de recurso só pode incluir caracteres alfanuméricos, `-` e não pode começar ou terminar com `-` . Se outros símbolos estiverem incluídos no nome, a criação de um recurso falhará.
    * **Localização** - Escolha autor de suas aplicações em um dos [três locais de autoria](./luis-reference-regions.md) que são atualmente apoiados pela LUIS, incluindo: West Us, West Europe e East Australia
    * **Nível de preços** - Por padrão, o nível de preços de autoria de F0 é selecionado como recomendado. Crie uma [chave gerida pelo cliente](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding) a partir do portal Azure se estiver à procura de uma camada extra de segurança.
8. Agora assinou com sucesso com o LUIS. Pode agora começar a criar aplicações.

## <a name="troubleshooting"></a>Resolução de problemas

* Ao criar um novo recurso, certifique-se de que o nome do recurso apenas inclui caracteres alfanuméricos, '-', e não pode começar ou terminar com '-'. Caso contrário, falhará.
* Certifique-se de que tem as [permissões adequadas na sua subscrição para criar um recurso Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Se não tiver as permissões adequadas, contacte a administração da sua subscrição para lhe dar permissões suficientes.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [iniciar uma nova aplicação](luis-how-to-start-new-app.md)