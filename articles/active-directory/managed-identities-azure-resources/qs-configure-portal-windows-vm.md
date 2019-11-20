---
title: Configurar identidades gerenciadas usando o portal do Azure-Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o portal do Azure.
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
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75971dbd35a6b29306bc87988423ab662a343705
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183858"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, você aprenderá a habilitar e desabilitar identidades gerenciadas atribuídas pelo usuário e pelo sistema para uma VM (máquina virtual) do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md).
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá a habilitar e desabilitar a identidade gerenciada atribuída pelo sistema para a VM usando o portal do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de uma VM

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM durante sua criação, sua conta precisa da atribuição de função de [colaborador de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

- Na guia **Gerenciamento** na seção **identidade** , altere identidade de **serviço gerenciado** para **ativado**.  

![Habilitar identidade atribuída pelo sistema durante a criação da VM](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Consulte os seguintes guias de início rápido para criar uma VM: 

- [Criar uma máquina virtual do Windows com o portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Criar uma máquina virtual Linux com o portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Habilitar identidade gerenciada atribuída pelo sistema em uma VM existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM que foi originalmente provisionada sem ela, sua conta precisa da atribuição de função de [colaborador de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém a VM.

2. Navegue até a máquina virtual desejada e selecione **identidade**.

3. Em **sistema atribuído**, **status**, selecione **ativado** e, em seguida, clique em **salvar**:

   ![Captura de tela da página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Remover a identidade gerenciada atribuída pelo sistema de uma VM

Para remover a identidade gerenciada atribuída pelo sistema de uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Não são necessárias atribuições de função de diretório do Azure AD adicionais.

Se você tiver uma máquina virtual que não precisa mais de uma identidade gerenciada atribuída pelo sistema:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém a VM. 

2. Navegue até a máquina virtual desejada e selecione **identidade**.

3. Em **sistema atribuído**, **status**, selecionar **desativado** e, em seguida, clique em **salvar**:

   ![Captura de tela da página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

 Nesta seção, você aprenderá a adicionar e remover uma identidade gerenciada atribuída pelo usuário de uma VM usando o portal do Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Atribuir uma identidade atribuída pelo usuário durante a criação de uma VM

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

Atualmente, o portal do Azure não dá suporte à atribuição de uma identidade gerenciada atribuída pelo usuário durante a criação de uma VM. Em vez disso, consulte um dos seguintes artigos de início rápido de criação de VM para primeiro criar uma VM e, em seguida, vá para a próxima seção para obter detalhes sobre como atribuir uma identidade gerenciada atribuída pelo usuário à VM:

- [Criar uma máquina virtual do Windows com o portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual Linux com o portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Atribuir uma identidade gerenciada atribuída pelo usuário a uma VM existente

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém a VM.
2. Navegue até a VM desejada e clique em **identidade**, **usuário atribuído** e **\+adicionar**.

   ![Adicionar identidade gerenciada atribuída pelo usuário à VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Clique na identidade atribuída pelo usuário que você deseja adicionar à VM e, em seguida, clique em **Adicionar**.

    ![Adicionar identidade gerenciada atribuída pelo usuário à VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM

Para remover uma identidade atribuída pelo usuário de uma VM, sua conta precisa da atribuição de função de [colaborador da máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Não são necessárias atribuições de função de diretório do Azure AD adicionais.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure que contém a VM.
2. Navegue até a VM desejada e clique em **identidade**, **usuário atribuído**, o nome da identidade gerenciada atribuída pelo usuário que você deseja excluir e, em seguida, clique em **remover** (clique em **Sim** no painel de confirmação).

   ![Remover a identidade gerenciada atribuída pelo usuário de uma VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Passos seguintes

- Usando o portal do Azure, conceda acesso de identidade gerenciada da VM do Azure [a outro recurso do Azure](howto-assign-access-portal.md).

