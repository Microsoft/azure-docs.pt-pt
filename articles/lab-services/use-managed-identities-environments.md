---
title: Use identidades geridas pelo Azure para criar ambientes em DevTest Labs [ Laboratórios DevTest] Microsoft Docs
description: Aprenda a usar identidades geridas em Azure para implementar ambientes num laboratório em Azure DevTest Labs.
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
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931161"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Use identidades geridas pelo Azure para implantar ambientes num laboratório 
Como dono de laboratório, pode usar uma identidade gerida para implementar ambientes num laboratório. Esta funcionalidade é útil em cenários onde o ambiente contém ou tem referências a recursos do Azure, como cofres-chave, galerias de imagem partilhadas e redes externas ao grupo de recursos do ambiente. Permite a criação de ambientes de caixa de areia que não se limitam ao grupo de recursos desse ambiente.

> [!NOTE]
> Atualmente, uma única identidade atribuída ao utilizador é suportada por laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
- [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)pelo utilizador através do portal Azure . 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Nesta secção, você, como proprietário de laboratório, usa o portal Azure para adicionar uma identidade gerida pelo utilizador ao laboratório. 

1. Na página do laboratório, selecione **Configuração e políticas.** 
1. Selecione **Identidade** na secção **Definições.**
1. Para adicionar uma identidade atribuída ao utilizador, **selecione Adicionar** na barra de ferramentas. 
1. Selecione uma **identidade** de uma lista de abandono pré-povoado.
1. Selecione **OK**.

    ![Adicionar identidade gerida pelo utilizador](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. Vê a identidade gerida pelo utilizador adicionada na lista. 

    ![Identidade gerida pelo utilizador na lista](./media/use-managed-identities-environments/identity-in-list.png)

Uma vez salvo, o laboratório usará esta identidade enquanto implanta todos os ambientes de laboratório. Também pode aceder ao recurso de identidade em Azure, selecionando a identidade da lista. 

O dono do laboratório não precisa de fazer nada de especial enquanto implanta um ambiente, desde que a identidade adicionada ao laboratório tenha permissões aos recursos externos a que o ambiente precisa de aceder. 

Para alterar a identidade gerida pelo utilizador atribuída ao laboratório, remova a identidade anexa ao laboratório primeiro e, em seguida, adicione outra ao laboratório. Para remover uma identidade anexa da casa de laboratório, **selecione... (elipse)**, e clique em **Remover**. 

![Identidade gerida pelo utilizador na lista](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Utilizar API

1. Depois de criar uma identidade, note a identificação de recursos desta identidade. Deve parecer a seguinte amostra: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Execute um método PUT Https `ServiceRunner` para adicionar um novo recurso ao laboratório semelhante ao seguinte exemplo. O recurso do corredor de serviço é um recurso proxy para gerir e controlar identidades geridas em DevTest Labs. O nome do corredor de serviço pode ser qualquer nome válido, mas recomendamos que utilize o nome do recurso de identidade gerido. 
 
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
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Assim que o utilizador aditada a identidade for adicionada ao laboratório, o serviço Azure DevTest Labs irá usá-lo enquanto implanta ambientes do Gestor de Recursos Azure. Por exemplo, se precisar do seu modelo de Gestor de Recursos para aceder a uma imagem de galeria de imagens partilhada externa, certifique-se de que a identidade que adicionou ao laboratório tem permissões mínimas necessárias para o recurso de galeria de imagem partilhada. 
