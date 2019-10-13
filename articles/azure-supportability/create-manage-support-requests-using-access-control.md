---
title: Controle de acesso para criar solicitações de suporte no Azure
description: RBAC (controle de acesso baseado em função) do Azure para controlar os direitos de acesso para criar e gerenciar solicitações de suporte
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure-supportability
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 328f4928aff6a892849ce3e08fe794578b5e03a4
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299252"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>RBAC (controle de acesso baseado em função) do Azure para controlar os direitos de acesso para criar e gerenciar solicitações de suporte

> [!IMPORTANT]
> Essa abordagem manual para controlar os direitos de acesso para dar suporte à solicitação no Azure foi substituída por uma função interna. Para obter mais informações, consulte [colaborador de solicitação de suporte](../role-based-access-control/built-in-roles.md#support-request-contributor). 

O [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview) permite o gerenciamento de acesso refinado para o Azure.
Solicitação de suporte criação no portal do Azure, [Portal.Azure.com](https://portal.azure.com), usa o modelo RBAC do Azure para definir quem pode criar e gerenciar solicitações de suporte.
O acesso é concedido atribuindo a função RBAC apropriada a usuários, grupos e aplicativos em um determinado escopo, que pode ser uma assinatura, um grupo de recursos ou um recurso.

Vamos pegar um exemplo: como um proprietário do grupo de recursos com permissões de leitura no escopo da assinatura, você pode gerenciar todos os recursos no grupo de recursos, como sites, máquinas virtuais e sub-redes.
No entanto, ao tentar criar uma solicitação de suporte no recurso de máquina virtual, você encontrará o seguinte erro

![Erro de assinatura](./media/create-manage-support-requests-using-access-control/subscription-error.png)

No gerenciamento de solicitações de suporte, você precisa de permissão de gravação ou de uma função que tenha a ação de suporte Microsoft. support/* no escopo da assinatura para poder criar e gerenciar solicitações de suporte.

O artigo a seguir explica como você pode usar o RBAC (controle de acesso baseado em função) personalizado do Azure para criar e gerenciar solicitações de suporte no portal do Azure.

## <a name="getting-started"></a>Introdução

Usando o exemplo acima, você poderá criar uma solicitação de suporte para o recurso se tiver atribuído uma função RBAC personalizada na assinatura pelo proprietário da assinatura.
As [funções RBAC personalizadas](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) podem ser criadas usando Azure PowerShell, a CLI (interface de linha de comando) do Azure e a API REST.

A propriedade Actions de uma função personalizada especifica as operações do Azure às quais a função concede acesso.
Para criar uma função personalizada para o gerenciamento de solicitações de suporte, a função deve ter a ação Microsoft. support/*

Aqui está um exemplo de uma função personalizada que você pode usar para criar e gerenciar solicitações de suporte.
Nós nomeamos essa função de "colaborador de solicitação de suporte" e é assim que nos referimos à função personalizada neste artigo.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Siga as etapas descritas neste [vídeo](https://www.youtube.com/watch?v=-PaBaDmfwKI) para saber como criar uma função personalizada para sua assinatura.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Criar e gerenciar solicitações de suporte no portal do Azure

Vamos pegar um exemplo – você é o proprietário da assinatura "Visual Studio MSDN Subscription".
Joe é o seu ponto que é proprietário de um recurso para alguns dos grupos de recursos nessa assinatura e tem permissão de leitura para a assinatura.
Você deseja dar acesso ao seu par, Joe, a capacidade de criar e gerenciar tíquetes de suporte para os recursos nessa assinatura.

1. A primeira etapa é ir para a assinatura. Em **configurações**, você verá uma lista de usuários. Selecione o usuário Joe, que tem acesso de leitor na assinatura. Vamos atribuir uma nova função personalizada para Joe.

    ![Adicionar função](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Clique em "Adicionar" na folha "usuários". Selecione a função personalizada "colaborador de solicitação de suporte" na lista de funções

    ![Adicionar função de colaborador de suporte](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Depois de selecionar o nome da função, clique em "adicionar usuários" e insira as credenciais de email de Joe. Clique em "Selecionar"

    ![Adicionar utilizadores](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Clique em "OK" para continuar

    ![Adicionar acesso](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Agora você vê o usuário com a função personalizada "colaborador de solicitação de suporte" recém-adicionada na assinatura para a qual você é o proprietário

    ![Usuário adicionado](./media/create-manage-support-requests-using-access-control/user-added.png)

    Quando Joe entra no portal, Joe vê a assinatura à qual Joe foi adicionado.

7. Joe clica em "New Solicitação de suporte" na folha "ajuda e suporte" e pode criar solicitações de suporte para "Visual Studio Ultimate com MSDN"

    ![Novo pedido de suporte](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Ao clicar em "todas as solicitações de suporte", Joe poderá ver a lista de solicitações de suporte criadas para esta assinatura ![Case exibição de detalhes @ no__t-1

## <a name="remove-support-request-access-in-the-azure-portal"></a>Remover o acesso à solicitação de suporte no portal do Azure

Assim como é possível conceder acesso a um usuário para criar e gerenciar solicitações de suporte, também é possível remover o acesso ao usuário.

Para remover a capacidade de criar e gerenciar solicitações de suporte, acesse a assinatura, clique em "configurações" e clique no usuário (neste caso, Joe). Clique com o botão direito do mouse no nome da função, "colaborador de solicitação de suporte" e clique em "remover"

![Remover acesso à solicitação de suporte](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Quando Joe fizer logon no portal e tentar criar uma solicitação de suporte, Joe encontrará o seguinte erro:

![Erro de assinatura-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe não pode ver nenhuma solicitação de suporte quando Joe seleciona "todas as solicitações de suporte"

![exibição de detalhes do caso-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
