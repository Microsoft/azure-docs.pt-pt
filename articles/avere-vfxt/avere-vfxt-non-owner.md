---
title: Solução de não proprietário de vFXT Avere - Azure
description: Solução para permitir que os utilizadores sem permissões de proprietário de subscrição para implementação Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: e72e6d969649de09389ee38b94e874fad98ee08f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409214"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autorizar não proprietários a implementar o Avere vFXT

Estas instruções são uma solução que permite que um usuário sem assinatura privilégios de proprietário para criar um vFXT Avere para o sistema do Azure.

(O modo recomendado para implementar o sistema vFXT é fazer com que um utilizador com privilégios de proprietário, fazer a criação de Avere os passos, conforme explicado [preparar para criar o vFXT Avere](avere-vfxt-prereqs.md).)  

A solução envolve a criação de uma função de acesso adicional que dá permissões suficientes para instalar o cluster de seus usuários. A função tem de ser criada por um proprietário da subscrição e um proprietário deve atribuí-la para os utilizadores apropriados. 

Também tem um proprietário da subscrição [aceitar os termos de utilização](avere-vfxt-prereqs.md) para a imagem do marketplace vFXT Avere. 

> [!IMPORTANT] 
> Todos estes passos devem ser utilizados por um utilizador com privilégios de proprietário da assinatura que será utilizado para o cluster.

1. Copie estas linhas e salvá-los num arquivo (por exemplo, `averecreatecluster.json`). Utilizar o seu ID de subscrição no `AssignableScopes` instrução.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",
   
           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Execute este comando para criar a função:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Exemplo:
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Atribua esta função ao utilizador que irá criar o cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Após este procedimento, qualquer utilizador atribuído esta função tem as seguintes permissões para a subscrição: 

* Criar e configurar a infraestrutura de rede
* Criar o controlador de cluster
* Executar scripts de criação de cluster a partir do controlador de cluster para criar o cluster
