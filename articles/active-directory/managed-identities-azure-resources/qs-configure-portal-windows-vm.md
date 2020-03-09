---
title: Configure identidades geridas usando o portal Azure - Azure AD
description: Instruções passo a passo para configurar identidades geridas para os recursos Azure num Azure VM utilizando o portal Azure.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372626"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Configure identidades geridas para recursos Azure em um VM usando o portal Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, aprende-se a ativar e desativar o sistema e as identidades geridas atribuídas pelo utilizador para uma Máquina Virtual Azure (VM), utilizando o portal Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema para vM utilizando o portal Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de um VM

Para permitir a identidade gerida atribuída pelo sistema num VM durante a sua criação, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

- Sob o separador **Gestão** na secção **Identidade,** altere a **identidade de serviço gerida** para **On**.  

![Ativar a identidade atribuída ao sistema durante a criação vm](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Consulte os seguintes Quickstarts para criar um VM: 

- [Criar uma máquina virtual Windows com o portal Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Crie uma máquina virtual Linux com o portal Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Ativar a identidade gerida atribuída pelo sistema num VM existente

Para permitir a identidade gerida atribuída pelo sistema num VM originalmente provisionado sem ele, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM.

2. Navegue para a máquina virtual desejada e selecione **Identidade**.

3. No **sistema atribuído**, **Status**, selecione **On** e, em seguida, clique em **Guardar**:

   ![Screenshot da página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Remover a identidade gerida atribuída pelo sistema a partir de um VM

Para remover a identidade gerida atribuída pelo sistema a partir de um VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretório da AD.

Se tiver uma Máquina Virtual que já não precisa de identidade gerida atribuída pelo sistema:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM. 

2. Navegue para a máquina virtual desejada e selecione **Identidade**.

3. No **sistema atribuído**, **Status,** selecione **Off** e, em seguida, clique em **Guardar**:

   ![Screenshot da página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída ao utilizador

 Nesta secção, aprende-se a adicionar e remover uma identidade gerida atribuída pelo utilizador a partir de um VM utilizando o portal Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Atribuir uma identidade atribuída ao utilizador durante a criação de um VM

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

Atualmente, o portal Azure não suporta a atribuição de uma identidade gerida atribuída ao utilizador durante a criação de um VM. Em vez disso, consulte um dos seguintes artigos de criação de VM Quickstart para primeiro criar um VM, e depois seguir para a secção seguinte para obter detalhes sobre a atribuição de uma identidade gerida atribuída pelo utilizador ao VM:

- [Criar uma máquina virtual Windows com o portal Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Crie uma máquina virtual Linux com o portal Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM existente

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções de Colaborador de [Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e Operador de [Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Não são necessárias atribuições adicionais de diretório da AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM.
2. Navegue para o VM desejado e clique em **Identidade,** **Utilizador atribuído** **e,** em seguida,\+Adicionar .

   ![Adicione a identidade gerida atribuída pelo utilizador à VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Clique na identidade atribuída ao utilizador que pretende adicionar ao VM e, em seguida, clique em **Adicionar**.

    ![Adicione a identidade gerida atribuída pelo utilizador à VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Remova uma identidade gerida atribuída ao utilizador de um VM

Para remover uma identidade atribuída ao utilizador de um VM, a sua conta necessita da atribuição de função de Colaborador de [Máquina Virtual.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Não são necessárias atribuições adicionais de diretório da AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM.
2. Navegue para o VM pretendido e clique em **Identidade**, **Utilizador atribuído,** o nome da identidade gerida atribuída pelo utilizador que pretende eliminar e, em seguida, clique em **Remover** (clique **Sim** no painel de confirmação).

   ![Remover a identidade gerida atribuída pelo utilizador de um VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Passos seguintes

- Utilizando o portal Azure, dê a um Azure O acesso de identidade gerido por um Azure [a outro recurso Azure.](howto-assign-access-portal.md)

