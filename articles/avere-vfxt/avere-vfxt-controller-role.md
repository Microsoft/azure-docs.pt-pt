---
title: Função de acesso de controlador personalizado - Avere vFXT para o Azure
description: Como criar uma função de acesso personalizado para o controlador de cluster de vFXT Avere
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 2a0f4a628764aaa561a5567d3435a42da804a994
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417845"
---
# <a name="customized-controller-access-role"></a>Função de acesso de controlo personalizado

O vFXT Avere para o controlador de cluster do Azure utiliza uma identidade gerida e o controlo de acesso baseado em funções (RBAC) para permiti-lo criar e gerir o cluster. 

Por predefinição, o controlador de cluster é atribuído a [função de proprietário incorporada](../role-based-access-control/built-in-roles.md#owner). Além disso, o acesso do controlador tem um âmbito para o seu grupo de recursos - -não é possível modificar a elementos fora do grupo de recursos do cluster.

Este artigo explica como criar sua própria função de acesso para o controlador de cluster em vez de utilizar a predefinição. 

## <a name="edit-the-role-prototype"></a>Editar o protótipo de função

Iniciar a partir da função de protótipo disponível em <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/read",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

Adicione o ID de subscrição para o vFXT Avere para implementação do Azure na instrução AssignableScopes. Personalizar o nome e adicionar ou alterar as definições conforme necessário. 

Tenha cuidado se restringir privilégios. Criação do cluster pode falhar se o controlador não tem acesso suficiente. 

Para ajuda para compreender o que privilégios o controlador de cluster necessita para criar um cluster, [abra um pedido de suporte](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Guarde a definição de função personalizada como um ficheiro. JSON. 

## <a name="define-the-role"></a>Definir a função 

Siga estes passos para adicionar a definição de função personalizada à sua subscrição. 

1. Abra o Azure Cloud Shell no portal do Azure ou navegue até [ https://shell.azure.com ](https://shell.azure.com).

1. Utilize o comando da CLI do Azure para mudar para a sua subscrição vFXT:

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. Crie a função:

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   Utilizar o seu nome de ficheiro e caminho em vez de ```/avere-contributor-custom.json``` neste exemplo. 

Guarde a saída do comando de definição de função - contém o identificador de função que precisa fornecer para o modelo de criação do cluster. 

## <a name="find-the-role-id"></a>Localizar o ID de função

O modelo de implementação de vFXT Avere precisa identificador global exclusivo da função (GUID) para atribuir o controlador de uma função personalizada. 

A função GUID é uma cadeia de caracteres de 32 carateres neste formulário: 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

Para procurar o GUID de sua função, utilize este comando com o nome da sua função no ```--name``` parâmetro.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Introduza essa cadeia de caracteres no **ID da função de criação do Avere cluster** campo ao implementar o vFXT Avere para o Azure.

## <a name="next-steps"></a>Passos Seguintes

Leia como implementar o vFXT Avere para o Azure em [implementar o cluster vFXT](avere-vfxt-deploy.md)
