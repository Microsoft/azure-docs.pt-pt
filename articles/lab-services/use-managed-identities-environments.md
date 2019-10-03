---
title: Usar identidades gerenciadas do Azure para criar ambientes no DevTest Labs | Microsoft Docs
description: Saiba como usar identidades gerenciadas no Azure para implantar ambientes em um laboratório no Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: d1dd059f1a6f9ce96b27d4fe1f214978dfc06a8f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815991"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Usar identidades gerenciadas do Azure para implantar ambientes em um laboratório 
Como proprietário de um laboratório, você pode usar uma identidade gerenciada para implantar ambientes em um laboratório. Esse recurso é útil em cenários em que o ambiente contém ou tem referências a recursos do Azure, como cofres de chaves, galerias de imagens compartilhadas e redes que são externas ao grupo de recursos do ambiente. Ele permite a criação de ambientes de área restrita que não são limitados ao grupo de recursos desse ambiente.

> [!NOTE]
> Atualmente, há suporte para uma única identidade atribuída pelo usuário por laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
- [Crie, liste, exclua ou atribua uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Nesta seção, você, como proprietário do laboratório, usa o portal do Azure para adicionar uma identidade gerenciada pelo usuário ao laboratório. 

1. Na página do laboratório, selecione **configuração e políticas**. 
1. Selecione **identidade** na seção **configurações** .
1. Para adicionar uma identidade atribuída ao usuário, selecione **Adicionar** na barra de ferramentas. 
1. Selecione uma **identidade** em uma lista suspensa preenchida previamente.
1. Selecione **OK**.

    ![Adicionar identidade gerenciada pelo usuário](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. Você vê a identidade adicionada gerenciada pelo usuário na lista. 

    ![Identidade gerenciada pelo usuário na lista](./media/use-managed-identities-environments/identity-in-list.png)

Depois de salvo, o laboratório usará essa identidade durante a implantação de todos os ambientes de laboratório. Você também pode acessar o recurso de identidade no Azure selecionando a identidade na lista. 

O proprietário do laboratório não precisa fazer nada especial durante a implantação de um ambiente, contanto que a identidade adicionada ao laboratório tenha permissões para os recursos externos que o ambiente precisa acessar. 

Para alterar a identidade gerenciada pelo usuário atribuída ao laboratório, remova a identidade anexada ao laboratório primeiro e, em seguida, adicione outra ao laboratório. Para remover uma identidade anexada ao laboratório, selecione **... (reticências)** e clique em **remover**. 

![Identidade gerenciada pelo usuário na lista](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Usar API

1. Depois de criar uma identidade, observe a ID de recurso dessa identidade. Ele deve ser semelhante ao exemplo a seguir: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Execute um método https PUT para adicionar um novo recurso `ServiceRunner` ao laboratório semelhante ao exemplo a seguir. O recurso do executor de serviço é um recurso de proxy para gerenciar e controlar identidades gerenciadas no DevTest Labs. O nome do executor de serviço pode ser qualquer nome válido, mas recomendamos que você use o nome do recurso de identidade gerenciada. 
 
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
    }
    ```
 
    Segue-se um exemplo: 

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
    }
    ```
 
Depois que a identidade atribuída pelo usuário for adicionada ao laboratório, o serviço de Azure DevTest Labs o usará durante a implantação de ambientes de Azure Resource Manager. Por exemplo, se você precisar que seu modelo do Resource Manager acesse uma imagem da Galeria de imagens compartilhada externa, certifique-se de que a identidade adicionada ao laboratório tenha as permissões mínimas necessárias para o recurso da Galeria de imagens compartilhadas. 
