---
title: Como configurar gerido identidades para recursos do Azure na VM do Azure com o portal do Azure
description: Instruções passo a passo instruções para configurar geridos identidades para recursos do Azure na VM do Azure com o portal do Azure.
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
ms.openlocfilehash: ab0a5b021048f0f684473d3f54bbeadf870cd007
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66112866"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Configurar identidades geridas para recursos do Azure numa VM com o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, saiba como ativar e desativar o sistema e de identidades geridas atribuído ao utilizador para uma Azure Virtual Machine (VM), com o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md).
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta secção, saiba como ativar e desativar a identidade gerida atribuído de sistema para a VM no portal do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Ativar a identidade gerida atribuído ao sistema durante a criação de uma VM

Para ativar a identidade gerida atribuído de sistema numa VM durante sua criação, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

- Sob o **gerenciamento** separador o **identidade** secção, mude **identidade de serviço gerida** para **no**.  

![Ativar a identidade do sistema atribuídos durante a criação da VM](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Veja os inícios Rápidos seguintes para criar uma VM: 

- [Criar uma máquina virtual do Windows com o portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Criar uma máquina virtual Linux com o portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Ativar a identidade gerida atribuído de sistema numa VM existente

Para ativar a identidade gerida atribuído de sistema numa VM que foi originalmente aprovisionada sem ele, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure que contém a VM.

2. Navegue para a Máquina Virtual pretendida e selecione **identidade**.

3. Sob **sistema atribuído**, **estado**, selecione **no** e, em seguida, clique em **guardar**:

   ![Captura de ecrã de página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Remover sistema atribuído a identidade gerida de uma VM

Para remover o sistema atribuído a identidade gerida de uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função.  Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

Se tiver uma Máquina Virtual que já não tem atribuído o sistema de identidade gerida:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure que contém a VM. 

2. Navegue para a Máquina Virtual pretendida e selecione **identidade**.

3. Sob **sistema atribuído**, **estado**, selecione **desligado** e, em seguida, clique em **guardar**:

   ![Captura de ecrã de página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

 Nesta secção, saiba como adicionar e remover uma identidade gerida atribuído ao utilizador a partir de uma VM com o portal do Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Atribuir uma identidade de utilizador atribuída durante a criação de uma VM

Para atribuir uma identidade de utilizador atribuído a uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) atribuições de funções. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

Atualmente, o portal do Azure não suporta a atribuição de uma identidade gerida atribuído ao utilizador durante a criação de uma VM. Em vez disso, consulte um dos seguintes artigos de início rápido de criação de VM para primeiro criar uma VM e, em seguida, avance para a secção seguinte para obter detalhes sobre uma identidade gerida atribuído ao utilizador a atribuir à VM:

- [Criar uma máquina virtual do Windows com o portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual Linux com o portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Atribuir uma identidade gerida atribuído ao utilizador a uma VM existente

Para atribuir uma identidade de utilizador atribuído a uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) atribuições de funções. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure que contém a VM.
2. Navegue para a VM e clique em pretendido **identidade**, **atribuída ao utilizador** e, em seguida  **\+adicionar**.

   ![Adicionar identidade gerida atribuído ao utilizador a VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Clique a identidade atribuído ao utilizador que pretende adicionar à VM e, em seguida, clique em **adicionar**.

    ![Adicionar identidade gerida atribuído ao utilizador a VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Remover uma identidade gerida atribuído ao utilizador a partir de uma VM

Para remover uma identidade atribuído ao utilizador a partir de uma VM, a conta tem do [contribuinte de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) atribuição de função. Não existem adicionais do Azure atribuições de funções de diretório do AD são necessárias.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure que contém a VM.
2. Navegue para a VM e clique em pretendido **identidade**, **atribuída ao utilizador**, o nome do atribuído ao utilizador gerido identidade que pretende eliminar e, em seguida, clique em **remover** (clique em  **Sim** no painel de confirmação).

   ![Remover utilizador atribuído a identidade gerida de uma VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Passos Seguintes

- Utilizar o portal do Azure, atribua a identidade gerida de uma VM do Azure [acesso a outro recurso do Azure](howto-assign-access-portal.md).

