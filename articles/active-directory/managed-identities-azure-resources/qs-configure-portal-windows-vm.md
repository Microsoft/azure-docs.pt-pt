---
title: Configurar identidades geridas usando o portal Azure - Azure AD
description: Instruções passo a passo para configurar identidades geridas para recursos Azure num VM Azure utilizando o portal Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590992"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Configure identidades geridas para recursos Azure num VM usando o portal Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, aprende-se a ativar e desativar o sistema e as identidades geridas atribuídas pelo utilizador para uma Máquina Virtual Azure (VM), utilizando o portal Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, aprende-se a ativar e desativar a identidade gerida atribuída pelo sistema para VM utilizando o portal Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de uma VM

Para ativar a identidade gerida atribuída pelo sistema num VM durante a sua criação, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

- No **separador Gestão** na secção **Identidade,** troque a **identidade de serviço gerido** para **On**.  

![Permitir a identidade atribuída ao sistema durante a criação de VM](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Consulte os seguintes Quickstarts para criar um VM: 

- [Criar uma máquina virtual do Windows com o portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Criar uma máquina virtual do Linux com o portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Ativar a identidade gerida atribuída pelo sistema numa VM existente

Para permitir a identidade gerida atribuída pelo sistema num VM que foi originalmente a provisionado sem ele, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM.

2. Navegue para a máquina virtual desejada e selecione **Identidade**.

3. Em **Sistema atribuído**, **Estado**, selecione **On** e, em seguida, clique em **Guardar**:

   ![Screenshot que mostra a página "Identidade (pré-visualização)" com o estado "Sistema atribuído" definido para "On".](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Remover identidade gerida atribuída pelo sistema a partir de um VM

Para remover a identidade gerida atribuída pelo sistema a partir de um VM, a sua conta precisa da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Não são necessárias atribuições adicionais de diretórios Azure AD.

Se tiver uma Máquina Virtual que já não necessita de identidade gerida atribuída ao sistema:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM. 

2. Navegue para a máquina virtual desejada e selecione **Identidade**.

3. Em **Sistema atribuído**, **Estado**, selecione **Off** e clique em **Guardar**:

   ![Screenshot da página de configuração](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

 Nesta secção, aprende-se a adicionar e remover uma identidade gerida atribuída ao utilizador a partir de um VM utilizando o portal Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Atribuir uma identidade atribuída ao utilizador durante a criação de um VM

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

Atualmente, o portal Azure não suporta a atribuição de uma identidade gerida atribuída pelo utilizador durante a criação de um VM. Em vez disso, consulte um dos seguintes artigos de criação de VM Quickstart para primeiro criar um VM e, em seguida, dirija-se à secção seguinte para obter detalhes sobre a atribuição de uma identidade gerida atribuída pelo utilizador ao VM:

- [Criar uma máquina virtual do Windows com o portal do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual do Linux com o portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Atribuir uma identidade gerida atribuída ao utilizador a um VM existente

Para atribuir uma identidade atribuída ao utilizador a um VM, a sua conta necessita das atribuições de funções [de Colaborador de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e Operador de Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM.
2. Navegue para o VM desejado e clique em **Identidade,** **Utilizador designado** e, em seguida, **\+ Adicione**.

   ![Screenshot que mostra a página "Identidade" com "User assigned" selecionado e o botão "Adicionar" realçado.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Clique na identidade atribuída ao utilizador que pretende adicionar ao VM e, em seguida, clique em **Adicionar**.

    ![Adicionar identidade gerida atribuída ao utilizador à VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Remover uma identidade gerida atribuída pelo utilizador de um VM

Para remover uma identidade atribuída ao utilizador de um VM, a sua conta necessita da atribuição de função [de Contribuinte de Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) Não são necessárias atribuições adicionais de diretórios Azure AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM.
2. Navegue para o VM pretendido e clique em **Identidade**, **Utilizador atribuído**, o nome da identidade gerida atribuída pelo utilizador que pretende eliminar e, em seguida, clique em **Remover** (clique em **Sim** no painel de confirmação).

   ![Remover a identidade gerida atribuída pelo utilizador de um VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Passos seguintes

- Utilizando o portal Azure, dê acesso de identidade gerido [a outro recurso Azure](howto-assign-access-portal.md)VM gerido.