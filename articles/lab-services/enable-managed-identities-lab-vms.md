---
title: Ativar identidades geridas nos seus VMs de laboratório em Laboratórios Azure DevTest
description: Este artigo mostra como um proprietário de laboratório pode ativar identidades geridas atribuídas pelo utilizador nas suas máquinas virtuais de laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692670"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Ativar identidades geridas atribuídas ao utilizador em máquinas virtuais de laboratório em Laboratórios Azure DevTest
Como proprietário de laboratório, pode ativar identidades geridas atribuídas ao utilizador nas suas máquinas virtuais de laboratório (VMs) em Azure DevTest Labs.

Uma identidade gerida pode ser usada para autenticar qualquer serviço que suporte a autenticação do Azure Ative Directory (AD), incluindo o Key Vault, sem passar quaisquer credenciais no código. Para obter mais informações sobre identidades geridas, veja [o que é gerida identidades para os recursos Do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Com esta funcionalidade, os utilizadores de laboratório podem partilhar recursos Azure como a Base de Dados Azure SQL no contexto do laboratório. A autenticação do recurso é cuidada pela própria identidade. Uma vez configurado, todos os VM de laboratório existentes/recém-criados serão ativados com esta identidade. Os utilizadores de laboratório podem aceder aos recursos uma vez que acedam às suas máquinas.

> [!NOTE]
> Pode adicionar várias identidades geridas ao utilizador para serem ativadas nos seus VMs de laboratório.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Para adicionar um utilizador designado identidade gerida para VMs de laboratório, siga estes passos:

1. [Crie uma identidade gerida atribuída ao utilizador na sua subscrição](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navegue na página de **Configuração e políticas** para o seu laboratório.
2. Selecione **Identidade (Pré-visualização)** no menu esquerdo.
3. Selecione o separador **máquina Virtual.**
4. Selecione **Adicionar** para selecionar uma identidade existente a partir de uma lista de dropdown pré-povoada. 

    ![Adicionar botão de identidade](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Selecione uma **identidade gerida** pelo utilizador existente a partir da lista abandonada e selecione **OK**. 

    ![Adicionar identidade](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Utilizar API

1.  Depois de criar uma identidade, note a identificação de recursos da identidade. Deve parecer a seguinte amostra: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Executar um método PUT HTTPS para adicionar um novo recurso **ServiceRunner** ao laboratório, como mostra o seguinte exemplo. 

    O recurso do corredor de serviço é um recurso proxy para gerir e controlar identidades geridas em DevTest Labs. O nome do corredor de serviço pode ser qualquer nome válido, mas recomendamos que utilize o nome do recurso de identidade gerido.

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
Para saber mais sobre identidades geridas, veja [o que é gerida identidades para os recursos Do Azure?](../active-directory/managed-identities-azure-resources/overview.md)







