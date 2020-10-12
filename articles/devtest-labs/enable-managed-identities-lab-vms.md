---
title: Ativar identidades geridas nos seus VMs de laboratório em Azure DevTest Labs
description: Este artigo mostra como um dono de laboratório pode ativar identidades geridas atribuídas pelo utilizador nas suas máquinas virtuais de laboratório.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88717642"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Ativar identidades geridas atribuídas pelo utilizador em máquinas virtuais de laboratório em Azure DevTest Labs
Como proprietário de laboratório, pode ativar identidades geridas atribuídas pelo utilizador nas suas máquinas virtuais de laboratório (VMs) em Azure DevTest Labs.

Uma identidade gerida pode ser usada para autenticar qualquer serviço que suporte a autenticação do Azure Ative Directory (AD), incluindo o Key Vault, sem passar quaisquer credenciais no código. Para obter mais informações sobre identidades geridas, veja [o que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Com esta funcionalidade, os utilizadores de laboratório podem partilhar recursos Azure, como a Base de Dados Azure SQL no contexto do laboratório. A autenticação do recurso é cuidada pela própria identidade. Uma vez configurados, todos os VM de laboratório existentes/recém-criados serão ativados com esta identidade. Os utilizadores de laboratório podem aceder aos recursos uma vez iniciados nas suas máquinas.

> [!NOTE]
> Pode adicionar várias identidades geridas por utilizadores atribuídas para serem ativadas nos seus VMs de laboratório.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Para adicionar um utilizador atribuído identidade gerida para VMs de laboratório, siga estes passos:

1. [Crie uma identidade gerida atribuída ao utilizador na sua subscrição](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navegue para a página **de Configuração e políticas** para o seu laboratório.
1. Selecione **Identidade (Pré-visualização)** no menu esquerdo.
1. Selecione o **separador máquina Virtual.**
1. **Selecione Adicionar** para selecionar uma identidade existente a partir de uma lista de dropdown pré-povoada. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar botão de identidade](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Selecione uma **identidade gerida pelo utilizador** existente a partir da lista de down-down e selecione **OK**. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar identidade](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Utilizar API

1.  Depois de criar uma identidade, note o ID de recurso da identidade. Deve parecer a seguinte amostra: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Executar um método PUT HTTPS para adicionar um novo recurso **ServiceRunner** ao laboratório, como mostrado no exemplo seguinte. 

    O recurso de corredor de serviço é um recurso proxy para gerir e controlar identidades geridas em DevTest Labs. O nome do corredor de serviço pode ser qualquer nome válido, mas recomendamos que utilize o nome do recurso de identidade gerido.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre identidades geridas, veja [o que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)







