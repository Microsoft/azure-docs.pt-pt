---
title: Avere vFXT não proprietário solução - Azure
description: Solução alternativa para permitir aos utilizadores sem autorização do proprietário da subscrição para implementar Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 0d9b1060ee35af6cbc2e1b95b0f7813072c52d2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85505380"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autorizar não proprietários a implementar o Avere vFXT

Estas instruções são uma solução alternativa que permite a um utilizador sem privilégios de proprietário de subscrição criar um sistema Avere vFXT para Azure.

(A forma recomendada de implantar o sistema Avere vFXT é ter um utilizador com privilégios de proprietário a fazer os passos de criação, como explicado na [Preparação para criar o Avere vFXT](avere-vfxt-prereqs.md).)  

A solução alternativa passa pela criação de uma função de acesso adicional que dê aos seus utilizadores permissões suficientes para instalar o cluster. A função deve ser criada por um proprietário de subscrição, e o proprietário deve atribuí-la aos utilizadores apropriados.

Um proprietário de subscrição também deve [aceitar os termos de uso](avere-vfxt-prereqs.md) para a imagem de mercado Avere vFXT.

> [!IMPORTANT]
> Todas estas medidas devem ser tomadas por um utilizador com privilégios de proprietário na subscrição que será utilizada para o cluster.

1. Copie estas linhas e guarde-as num ficheiro (por exemplo, `averecreatecluster.json` ). Use o seu ID de assinatura na `AssignableScopes` declaração.

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

1. Atribua esta função ao utilizador que irá criar o cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Após completar este processo, a função confere a qualquer utilizador que lhe tenha atribuído as seguintes permissões para a subscrição:

* Criar e configurar a infraestrutura de rede
* Criar o controlador de cluster
* Executar scripts de criação de cluster a partir do controlador de cluster para criar o cluster
