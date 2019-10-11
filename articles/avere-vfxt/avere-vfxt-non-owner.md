---
title: Solução alternativa de não-proprietário do avere vFXT – Azure
description: Solução alternativa para permitir que os usuários sem permissão de proprietário da assinatura implantem o avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 77fc5a53c8bdc389c24cd1e6406415eefc3f167b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256182"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autorizar não proprietários a implementar o Avere vFXT

Estas instruções são uma solução alternativa que permite que um usuário sem privilégios de proprietário da assinatura crie um avere vFXT para o sistema do Azure.

(A maneira recomendada de implantar o sistema avere vFXT é fazer com que um usuário com privilégios de proprietário faça as etapas de criação, conforme explicado em [preparar para criar o avere vFXT](avere-vfxt-prereqs.md).)  

A solução alternativa envolve a criação de uma função de acesso adicional que concede a seus usuários permissões suficientes para instalar o cluster. A função deve ser criada por um proprietário de assinatura e um proprietário deve atribuí-la aos usuários apropriados. 

Um proprietário de assinatura também deve [aceitar os termos de uso](avere-vfxt-prereqs.md) da imagem do avere vFXT Marketplace. 

> [!IMPORTANT] 
> Todas essas etapas devem ser tomadas por um usuário com privilégios de proprietário na assinatura que será usada para o cluster.

1. Copie essas linhas e salve-as em um arquivo (por exemplo, `averecreatecluster.json`). Use sua ID de assinatura na instrução `AssignableScopes`.

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

1. Atribua essa função ao usuário que criará o cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Após esse procedimento, qualquer usuário atribuído a essa função tem as seguintes permissões para a assinatura: 

* Criar e configurar a infraestrutura de rede
* Criar o controlador de cluster
* Executar scripts de criação de cluster do controlador de cluster para criar o cluster
