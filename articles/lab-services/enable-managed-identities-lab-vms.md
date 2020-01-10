---
title: Habilitar identidades gerenciadas em suas VMs de laboratório no Azure DevTest Labs
description: Este artigo mostra como um proprietário de laboratório pode habilitar identidades gerenciadas atribuídas pelo usuário em suas máquinas virtuais de laboratório.
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
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692670"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Habilitar identidades gerenciadas atribuídas pelo usuário em máquinas virtuais de laboratório no Azure DevTest Labs
Como proprietário de um laboratório, você pode habilitar identidades gerenciadas atribuídas pelo usuário em suas VMs (máquinas virtuais) de laboratório em Azure DevTest Labs.

Uma identidade gerenciada pode ser usada para autenticar em qualquer serviço que ofereça suporte à autenticação Azure Active Directory (AD), incluindo Key Vault, sem passar credenciais no código. Para obter mais informações sobre identidades gerenciadas, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Com esse recurso, os usuários do laboratório podem compartilhar recursos do Azure, como o banco de dados SQL do Azure, no contexto do laboratório. A autenticação para o recurso é levada em conta com a própria identidade. Uma vez configurado, todas as VMs de laboratório existentes/recém-criadas serão habilitadas com essa identidade. Os usuários do laboratório podem acessar recursos depois de conectados a seus computadores.

> [!NOTE]
> Você pode adicionar várias identidades gerenciadas atribuídas ao usuário para serem habilitadas em suas VMs de laboratório.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Para adicionar uma identidade gerenciada atribuída ao usuário para VMs do laboratório, siga estas etapas:

1. [Criar uma identidade gerenciada atribuída pelo usuário em sua assinatura](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navegue até a página **configuração e políticas** do seu laboratório.
2. Selecione **identidade (versão prévia)** no menu à esquerda.
3. Selecione a guia **máquina virtual** .
4. Selecione **Adicionar** para selecionar uma identidade existente de uma lista suspensa preenchida previamente. 

    ![Botão Adicionar identidade](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Selecione uma **identidade gerenciada pelo usuário** existente na lista suspensa e selecione **OK**. 

    ![Adicionar identidade](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Usar API

1.  Depois de criar uma identidade, observe a ID de recurso da identidade. Ele deve ser semelhante ao exemplo a seguir: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Execute um método HTTPS PUT para adicionar um novo recurso do **perrunner** ao laboratório, conforme mostrado no exemplo a seguir. 

    O recurso do executor de serviço é um recurso de proxy para gerenciar e controlar identidades gerenciadas no DevTest Labs. O nome do executor de serviço pode ser qualquer nome válido, mas recomendamos que você use o nome do recurso de identidade gerenciada.

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
Para saber mais sobre identidades gerenciadas, confira [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).







