---
title: Configure identidades geridas em Azure VMSS - Azure AD
description: Instruções passo a passo para configurar identidades geridas para os recursos Do Azure num conjunto de escala de máquina virtual utilizando o portal Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184042"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Configure identidades geridas para recursos Azure em um conjunto de máquinas virtuais usando o portal Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, utilizando o PowerShell, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num conjunto de escala de máquina virtual:

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de controlo de acesso baseadas no papel Azure:

    > [!NOTE]
    > Não são necessárias atribuições adicionais de diretório da AD.

    - [Colaborador de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para ativar e remover a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, você aprenderá a ativar e desativar a identidade gerida atribuída pelo sistema usando o portal Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de um conjunto de escala de máquina virtual

Atualmente, o portal Azure não suporta a identidade gerida atribuída pelo sistema durante a criação de um conjunto de máquinas virtuais. Em vez disso, consulte a seguinte criação de conjunto de máquinas virtuais, o artigo Quickstart para primeiro criar um conjunto de escala de máquina virtual e, em seguida, seguir para a secção seguinte para obter detalhes sobre a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual:

- [Crie um conjunto de escala de máquina virtual no portal Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual existente

Para permitir a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual que foi originalmente aprovisionado sem ele:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o conjunto de escala de máquina virtual.

2. Navegue para o conjunto de escala de máquina virtual desejado.

3. No **sistema atribuído**, **Status**, selecione **On** e, em seguida, clique em **Guardar**:

   ![Screenshot da página de configuração](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover a identidade gerida atribuída pelo sistema de um conjunto de escala de máquina virtual

Se tiver um conjunto de escala de máquina virtual que já não precisa de uma identidade gerida atribuída pelo sistema:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o conjunto de escala de máquina virtual. Certifique-se também de que a sua conta pertence a um papel que lhe dá permissões de escrita no conjunto de escala de máquina virtual.

2. Navegue para o conjunto de escala de máquina virtual desejado.

3. No **sistema atribuído**, **Status,** selecione **Off** e, em seguida, clique em **Guardar**:

   ![Screenshot da página de configuração](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

Nesta secção, aprende-se a adicionar e remover uma identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual utilizando o portal Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de escala de máquina virtual

Atualmente, o portal Azure não suporta atribuir uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de máquinas virtuais. Em vez disso, consulte a seguinte criação de conjunto de máquinas virtuais, a criação do artigo Quickstart para primeiro criar um conjunto de escala de máquina virtual e, em seguida, passar para a secção seguinte para obter detalhes sobre a atribuição de uma identidade gerida atribuída pelo utilizador:

- [Crie um conjunto de escala de máquina virtual no portal Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual existente

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o conjunto de escala de máquina virtual.
2. Navegue para o conjunto de escala de máquina virtual pretendido e clique em **Identidade,** **Utilizador atribuído** ** \+e,** em seguida, Adicionar .

   ![Adicionar identidade atribuída ao VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Clique na identidade atribuída ao utilizador que pretende adicionar ao conjunto de escala de máquina virtual e, em seguida, clique em **Adicionar**.
   
   ![Adicionar identidade atribuída ao VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remova uma identidade gerida atribuída ao utilizador de um conjunto de escala de máquina virtual

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM.
2. Navegue para o conjunto de escala de máquina virtual pretendido e clique em **Identidade**, **Utilizador atribuído,** o nome da identidade gerida atribuída pelo utilizador que pretende eliminar e, em seguida, clique em **Remover** (clique **Sim** no painel de confirmação).

   ![Remova a identidade atribuída ao utilizador de um VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Passos seguintes

- Utilizando o portal Azure, dê a um conjunto de máquinas virtuais Azure acesso de identidade [a outro recurso Azure.](howto-assign-access-portal.md)


