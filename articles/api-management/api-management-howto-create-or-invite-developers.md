---
title: Como gerenciar contas de usuário no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como criar ou convidar usuários no gerenciamento de API do Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 9de75b5537b3e267116882e7f151d893de575071
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072470"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Como gerenciar contas de usuário no gerenciamento de API do Azure

No gerenciamento de API, os desenvolvedores são os usuários das APIs que você expõe usando o gerenciamento de API. Este guia mostra como criar e convidar os desenvolvedores para usarem as APIs e os produtos que você disponibiliza para eles com sua instância de gerenciamento de API. Para obter informações sobre como gerenciar contas de usuário programaticamente, consulte a documentação da [entidade de usuário](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user) na referência [REST do gerenciamento de API](/rest/api/apimanagement/) .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as tarefas neste artigo: [Crie uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Criar um novo desenvolvedor

Para adicionar um novo usuário, siga as etapas nesta seção:

1. Selecione a guia **usuários** à esquerda da tela.
2. Pressione **+ Adicionar**.
3. Insira as informações apropriadas para o usuário.
4. Prima **Adicionar**.

    ![Adicionar um novo utilizador](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Por padrão, as contas de desenvolvedor recém-criadas estão **ativas**e associadas ao grupo de **desenvolvedores** . As contas de desenvolvedor que estão em um estado **ativo** podem ser usadas para acessar todas as APIs para as quais elas têm assinaturas. Para associar o desenvolvedor recém-criado a grupos adicionais, consulte [como associar grupos a desenvolvedores][How to associate groups with developers].

## <a name="invite-developer"> </a>Convidar um desenvolvedor
Para convidar um desenvolvedor, siga as etapas nesta seção:

1. Selecione a guia **usuários** à esquerda da tela.
2. Pressione **+ convidar**.

Uma mensagem de confirmação é exibida, mas o desenvolvedor recentemente convidado não aparece na lista até depois de aceitar o convite. 

Quando um desenvolvedor é convidado, um email é enviado para o desenvolvedor. Esse email é gerado usando um modelo e é personalizável. Para obter mais informações, consulte [configurar modelos de email][Configure email templates].

Depois que o convite for aceito, a conta ficará ativa.

## <a name="block-developer"></a> Desativar ou reativar uma conta de desenvolvedor

Por padrão, contas de desenvolvedor criadas recentemente ou convidadas estão **ativas**. Para desativar uma conta de desenvolvedor, clique em **Bloquear**. Para reativar uma conta de desenvolvedor bloqueada, clique em **Ativar**. Uma conta de desenvolvedor bloqueada não pode acessar o portal do desenvolvedor nem chamar APIs. Para excluir uma conta de usuário, clique em **excluir**.

Para bloquear um usuário, siga as etapas a seguir.

1. Selecione a guia **usuários** à esquerda da tela.
2. Clique no usuário que você deseja bloquear.
3. Pressione **Bloquear**.

## <a name="reset-a-user-password"></a>Redefinir uma senha de usuário

Para trabalhar de forma programática com contas de usuário, consulte a documentação da entidade de usuário na referência da [API REST do gerenciamento de API](/rest/api/apimanagement/) . Para redefinir uma senha de conta de usuário para um valor específico, você pode usar a operação [atualizar um usuário](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) e especificar a senha desejada.

## <a name="next-steps"> </a>Passos seguintes
Depois que uma conta de desenvolvedor for criada, você poderá associá-la a funções e assiná-la a produtos e APIs. Para obter mais informações, consulte [como criar e usar grupos][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
