---
title: Use identidades geridas pela Azure para criar ambientes em DevTest Labs | Microsoft Docs
description: Aprenda a usar identidades geridas em Azure para implantar ambientes num laboratório em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f3e4b4d7030eb26c25b291e03caaa430d1979c4
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185789"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Use identidades geridas pela Azure para implantar ambientes num laboratório 

Como dono de laboratório, pode usar uma identidade gerida para implantar ambientes num laboratório. Esta funcionalidade é útil em cenários onde o ambiente contém ou tem referências a recursos Azure, tais como cofres-chave, galerias de imagem partilhadas e redes externas ao grupo de recursos do ambiente. Permite a criação de ambientes de caixas de areia que não se limitam ao grupo de recursos desse ambiente.

> [!NOTE]
> Atualmente, uma única identidade atribuída ao utilizador é suportada por laboratório. 

## <a name="prerequisites"></a>Pré-requisitos

- [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador utilizando o portal Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Certifique-se de que a sua identidade gerida foi criada na mesma região e subscrição que o seu laboratório. A identidade gerida não precisa de estar no mesmo grupo de recursos.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure

Nesta secção você, como proprietário de laboratório, use o portal Azure para adicionar uma identidade gerida pelo utilizador ao laboratório. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure por **Laboratórios DevTest.**
1. Da lista de laboratórios, selecione o laboratório que quiser.
1. Selecione **Configuração e políticas**  ->  **Identidade (Pré-visualização)**. 
1. Para adicionar uma identidade atribuída ao utilizador, selecione o **separador Utilizador Atribuído.**
1. **Pressione Add** .
1. Selecione um utilizador existente que criou e/ou tenha acesso a partir do drop-down.
 
    ![Adicionar identidade gerida pelo utilizador](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Pressione **Guarde** em cima da página.

    Uma vez guardado, o laboratório usará esta identidade enquanto implanta todos os ambientes de laboratório. Também pode aceder ao recurso de identidade em Azure selecionando a identidade da lista. 

O dono do laboratório não precisa de fazer nada de especial enquanto implanta um ambiente, desde que a identidade adicionada ao laboratório tenha permissões para os recursos externos a que o ambiente precisa de aceder. 

Para alterar a identidade gerida pelo utilizador atribuída ao laboratório, remova primeiro a identidade anexada ao laboratório e adicione outra ao laboratório. Para remover uma identidade anexada ao laboratório, **selecione... (elipse)** e clique em **Remover**. 

## <a name="use-api"></a>Utilizar API

1. Depois de criar uma identidade, note o ID de recursos desta identidade. Deve parecer a seguinte amostra: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Execute um método PUT Https para adicionar um novo `ServiceRunner` recurso ao laboratório semelhante ao exemplo seguinte. O recurso de corredor de serviço é um recurso proxy para gerir e controlar identidades geridas em DevTest Labs. O nome do corredor de serviço pode ser qualquer nome válido, mas recomendamos que utilize o nome do recurso de identidade gerido. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Eis um exemplo: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Assim que a identidade atribuída ao utilizador for adicionada ao laboratório, o serviço Azure DevTest Labs irá usá-lo enquanto implementa ambientes do Azure Resource Manager. Por exemplo, se precisar do seu modelo de Gestor de Recursos para aceder a uma imagem de galeria de imagens partilhada externa, certifique-se de que a identidade que adicionou ao laboratório tem permissões mínimas necessárias para o recurso de galeria de imagens partilhada. 
