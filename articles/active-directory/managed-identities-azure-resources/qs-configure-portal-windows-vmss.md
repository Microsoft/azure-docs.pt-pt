---
title: Configurar identidades gerenciadas no Azure VMSS – Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32d8944b55794412eb322697469933030e2fbb56
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184042"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, usando o PowerShell, você aprende a executar as seguintes identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais:

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md).
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar as operações de gerenciamento neste artigo, sua conta precisa das seguintes atribuições de controle de acesso com base em função do Azure:

    > [!NOTE]
    > Nenhuma atribuição adicional de função de diretório do Azure AD é necessária.

    - [Colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para habilitar e remover a identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída pelo sistema usando o portal do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais

Atualmente, o portal do Azure não dá suporte à habilitação da identidade gerenciada atribuída pelo sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais. Em vez disso, consulte o seguinte artigo de início rápido da criação do conjunto de dimensionamento de máquinas virtuais para primeiro criar um conjunto de dimensionamento de máquinas virtuais e, em seguida, vá para a próxima seção para obter detalhes sobre como habilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas

- [Criar um conjunto de dimensionamento de máquinas virtuais no portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Habilitar identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais existente

Para habilitar a identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais que foi originalmente provisionado sem ele:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Em **sistema atribuído**, **status**, selecione **ativado** e, em seguida, clique em **salvar**:

   ![Captura de tela da página de configuração](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover a identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade gerenciada atribuída pelo sistema:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Verifique também se sua conta pertence a uma função que lhe dá permissões de gravação no conjunto de dimensionamento de máquinas virtuais.

2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado.

3. Em **sistema atribuído**, **status**, selecionar **desativado** e, em seguida, clique em **salvar**:

   ![Captura de tela da página de configuração](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

Atualmente, o portal do Azure não dá suporte à atribuição de uma identidade gerenciada atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais. Em vez disso, consulte o seguinte artigo de início rápido da criação do conjunto de dimensionamento de máquinas virtuais para primeiro criar um conjunto de dimensionamento de máquinas virtuais e, em seguida, vá para a próxima seção para obter detalhes sobre como atribuir uma identidade gerenciada atribuída pelo usuário a ela:

- [Criar um conjunto de dimensionamento de máquinas virtuais no portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais existente

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.
2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado e clique em **identidade**, **usuário atribuído** e **\+adicionar**.

   ![Adicionar identidade atribuída pelo usuário ao VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Clique na identidade atribuída pelo usuário que você deseja adicionar ao conjunto de dimensionamento de máquinas virtuais e clique em **Adicionar**.
   
   ![Adicionar identidade atribuída pelo usuário ao VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquinas virtuais

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém a VM.
2. Navegue até o conjunto de dimensionamento de máquinas virtuais desejado e clique em **identidade**, **usuário atribuído**, o nome da identidade gerenciada atribuída pelo usuário que você deseja excluir e, em seguida, clique em **remover** (clique em **Sim** no painel de confirmação).

   ![Remover a identidade atribuída pelo usuário de um VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Passos seguintes

- Usando o portal do Azure, dê um conjunto de dimensionamento de máquinas virtuais do Azure [acesso de identidade gerenciado a outro recurso do Azure](howto-assign-access-portal.md).


