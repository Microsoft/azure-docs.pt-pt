---
title: Sem dono da Avere vFXT - Azure
description: Seleção para permitir aos utilizadores sem autorização do proprietário de subscrição para implementar Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76153280"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autorizar não proprietários a implementar o Avere vFXT

Estas instruções são uma suposição que permite a um utilizador sem privilégios do proprietário de subscrição criar um Sistema Avere vFXT para o sistema Azure.

(A forma recomendada de implementar o sistema Avere vFXT é ter um utilizador com privilégios do proprietário a fazer os passos de criação, como explicado na [Prepare para criar o Avere vFXT](avere-vfxt-prereqs.md).)  

A solução de solução envolve a criação de uma função de acesso adicional que dê aos seus utilizadores permissões suficientes para instalar o cluster. A função deve ser criada por um proprietário de subscrição, e um proprietário deve atribuí-la aos utilizadores apropriados.

Um proprietário de subscrição também deve [aceitar os termos de uso](avere-vfxt-prereqs.md) para a imagem de mercado Avere vFXT.

> [!IMPORTANT]
> Todas estas medidas devem ser tomadas por um utilizador com privilégios do proprietário na subscrição que será utilizada para o cluster.

1. Copie estas linhas e guarde-as `averecreatecluster.json`num ficheiro (por exemplo, ). Use o seu `AssignableScopes` ID de subscrição no comunicado.

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

1. Executar este comando para criar o papel:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Exemplo:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Atribuir esta função ao utilizador que irá criar o cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Após a conclusão deste processo, a função confere a qualquer utilizador que lhe tenha atribuído as seguintes permissões para a subscrição:

* Criar e configurar a infraestrutura de rede
* Criar o controlador de cluster
* Executar scripts de criação de cluster do controlador cluster para criar o cluster
