---
title: Do Azure baseada em funções controlo de acesso (RBAC) para controlar direitos de acesso para criar e gerir pedidos de suporte | Documentos da Microsoft
description: Do Azure baseada em funções controlo de acesso (RBAC) para controlar direitos de acesso para criar e gerir pedidos de suporte
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: d98d0637c6d520193b11f4267c59016772ef063a
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792485"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Do Azure baseada em funções controlo de acesso (RBAC) para controlar direitos de acesso para criar e gerir pedidos de suporte

[Controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) permite a gestão de acessos detalhada para o Azure.
Suporta a criação de pedido no portal do Azure, [portal.azure.com](https://portal.azure.com), usa o modelo RBAC do Azure para definir quem pode criar e gerir pedidos de suporte.
O acesso é concedido ao atribuir a função RBAC adequada para os utilizadores, grupos e aplicações num determinado âmbito, que pode ser uma subscrição, grupo de recursos ou um recurso.

Vejamos um exemplo: Como um proprietário do grupo de recursos com permissões de leitura no âmbito da subscrição, pode gerir todos os recursos no grupo de recursos, como Web sites, máquinas virtuais e sub-redes.
No entanto, quando tentar criar um pedido de suporte com o recurso de máquina virtual, encontrar o erro seguinte

![Erro de subscrição](./media/create-manage-support-requests-using-access-control/subscription-error.png)

Na gestão de pedido de suporte, precisa escrever permissão ou uma função que tenha a ação de suporte Microsoft.Support/* no âmbito da subscrição para conseguir criar e gerir pedidos de suporte.

O seguinte artigo explica como pode utilizar personalizado baseado em funções controlo de acesso do Azure (RBAC) para criar e gerir pedidos de suporte no portal do Azure.

## <a name="getting-started"></a>Introdução

Com o exemplo acima, seria capaz de criar um pedido de suporte para o seu recurso se foi atribuído uma função RBAC personalizada na subscrição, o proprietário da subscrição.
[Funções RBAC personalizadas](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) podem ser criadas com o Azure PowerShell, a Interface de linha de comandos (CLI do Azure) e a API REST.

A propriedade de ações de uma função personalizada especifica as operações do Azure para o qual a função concede acesso.
Para criar uma função personalizada para a gestão de pedido de suporte, a função tem de ter a ação Microsoft.Support/*

Eis um exemplo de uma função personalizada, que pode usar para criar e gerir pedidos de suporte.
Podemos ter com o nome desta função "Contribuidor de pedido de suporte" e que é a forma como fazemos referência a função personalizada neste artigo.

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

Siga os passos descritos em [este vídeo](https://www.youtube.com/watch?v=-PaBaDmfwKI) para saber como criar uma função personalizada para a sua subscrição.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Criar e gerir pedidos de suporte no portal do Azure

Vejamos um exemplo – é o proprietário da subscrição "Visual Studio assinatura do MSDN."
O João é o elemento de rede que é um proprietário do recurso a alguns dos grupos de recursos nesta subscrição e tem permissão de leitura para a subscrição.
Pretende conceder acesso ao seu ponto a ponto, Joe, a capacidade de criar e gerir pedidos de suporte para os recursos nesta subscrição.

1. A primeira etapa é ir para a subscrição e em "Definições" ver uma lista de utilizadores. Clique com o utilizador João quem tem acesso de leitor na subscrição e vamos atribuir uma nova função de personalizada a ele.

    ![Adicionar função](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Clique em "Adicionar" no painel de "Utilizadores". Selecione a função personalizada "Contribuidor de pedido de suporte" na lista de funções

    ![Adicionar função de contribuinte de suporte](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Depois de selecionar o nome da função, clique em "Adicionar utilizadores" e introduza as credenciais de e-mail do João. Clique em "Selecionar"

    ![Adicionar utilizadores](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Clique em "Ok" para continuar

    ![Adicionar acesso](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Agora, pode ver o utilizador com a função personalizada recém-adicionada "Suporte Contribuidor de pedido" sob a subscrição para o qual é o proprietário

    ![Utilizador adicionado](./media/create-manage-support-requests-using-access-control/user-added.png)

    Quando o João registos no portal, ele vê a subscrição à qual ele foi adicionado.

7. O João clica em "Novo pedido de suporte" no painel "Ajuda e suporte" e pode criar pedidos de suporte para "Visual Studio Ultimate com MSDN"

    ![Novo pedido de suporte](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Clicar em "Todos os pedidos de suporte" João pode ver a lista de pedidos de suporte criados para esta subscrição ![vista de detalhes do caso](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Remover o acesso de pedido de suporte no portal do Azure

Tal como é possível conceder acesso a um utilizador para criar e gerir pedidos de suporte, é possível remover o acesso para o utilizador também.
Para remover a capacidade de criar e gerir pedidos de suporte, vá para a subscrição, clique em "Definições" e clique em do utilizador (no caso, o João).
Faça duplo clique o nome da função "Contribuidor de pedido de suporte" e clique em "Remover"

![Remover o acesso de pedido de suporte](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Quando o João inicia sessão no portal e tenta criar um pedido de suporte, ele encontra o erro seguinte

![Erro de subscrição-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe não pode ver quaisquer pedidos de suporte quando clicar em "Todos os pedidos de suporte"

![Vista de detalhes do caso-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
