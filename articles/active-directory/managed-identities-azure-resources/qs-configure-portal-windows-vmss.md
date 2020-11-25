---
title: Configurar identidades geridas em VMSS - Azure AD
description: Instruções passo a passo para configurar identidades geridas para recursos Azure numa escala de máquina virtual definida usando o portal Azure.
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
ms.date: 02/20/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43b986b6bb25dff1227247b1d249ce565553877a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997414"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Configure identidades geridas para recursos Azure em uma escala de máquina virtual definida usando o portal Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, utilizando o PowerShell, aprende-se a executar as seguintes identidades geridas para operações de recursos Azure num conjunto de escala de máquina virtual:

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gestão neste artigo, a sua conta necessita das seguintes atribuições de funções Azure:

    > [!NOTE]
    > Não são necessárias atribuições adicionais de diretório ad AD.

    - [Contribuinte de máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para ativar e remover a identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual.

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Nesta secção, irá aprender a ativar e desativar a identidade gerida atribuída pelo sistema através do portal Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema durante a criação de um conjunto de escala de máquina virtual

Atualmente, o portal Azure não suporta permitir a criação de uma escala de máquina virtual. Em vez disso, consulte o seguinte conjunto de escala de máquina virtual criação Quickstart artigo para primeiro criar um conjunto de escala de máquina virtual e, em seguida, proceder para a secção seguinte para obter detalhes sobre como permitir a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual:

- [Criar um conjunto de escala de máquina virtual no portal Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual existente

Para permitir a identidade gerida atribuída pelo sistema num conjunto de escala de máquina virtual que foi originalmente previsto sem ele:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o conjunto de escalas de máquina virtual.

2. Navegue para o conjunto de escala de máquina virtual desejado.

3. Em **Sistema atribuído**, **Estado**, selecione **On** e, em seguida, clique em **Guardar**:

   ![A screenshot mostra a página "Identidade (pré-visualização)" com "Sistema atribuído" selecionado, o Estado "Ligado" e o botão "Guardar" realçado.](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover identidade gerida atribuída pelo sistema a partir de um conjunto de escala de máquina virtual

Se tiver um conjunto de escala de máquina virtual que já não necessita de uma identidade gerida atribuída pelo sistema:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o conjunto de escalas de máquina virtual. Certifique-se também de que a sua conta pertence a uma função que lhe dá permissões de escrita no conjunto de escala de máquina virtual.

2. Navegue para o conjunto de escala de máquina virtual desejado.

3. Em **Sistema atribuído**, **Estado**, selecione **Off** e clique em **Guardar**:

   ![Screenshot da página de configuração](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, aprende-se a adicionar e remover uma identidade gerida atribuída pelo utilizador a partir de um conjunto de escala de máquina virtual utilizando o portal Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de escala de máquina virtual

Atualmente, o portal Azure não suporta a atribuição de uma identidade gerida atribuída ao utilizador durante a criação de um conjunto de escala de máquina virtual. Em vez disso, consulte o seguinte conjunto de escala de máquina virtual criação Quickstart artigo para primeiro criar um conjunto de escala de máquina virtual e, em seguida, proceder para a secção seguinte para obter detalhes sobre a atribuição de uma identidade gerida atribuída pelo utilizador:

- [Criar um conjunto de escala de máquina virtual no portal Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribua uma identidade gerida atribuída ao utilizador a um conjunto de escala de máquina virtual existente

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o conjunto de escalas de máquina virtual.
2. Navegue para o conjunto de escala de máquina virtual desejado e clique em **Identidade,** **Utilizador designado** e, em seguida, **\+ Adicionar**.

   ![Screenshot que mostra a página "Identidade" com "User assigned" selecionado e o botão "Adicionar" realçado.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Clique na identidade atribuída ao utilizador que pretende adicionar ao conjunto de escala de máquina virtual e, em seguida, clique em **Adicionar**.
   
   ![Adicionar identidade atribuída ao utilizador ao VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remova uma identidade gerida atribuída pelo utilizador de um conjunto de escala de máquina virtual

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure que contém o VM.
2. Navegue para o conjunto de escala de máquina virtual pretendida e clique em **Identidade**, **Utilizador atribuído**, o nome da identidade gerida atribuída pelo utilizador que pretende eliminar e, em seguida, clique em **Remover** (clique em **Sim** no painel de confirmação).

   ![Remover a identidade atribuída ao utilizador de um VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Passos seguintes

- Utilizando o portal Azure, dê a um conjunto de máquina virtual Azure acesso de identidade [gerido a outro recurso Azure](howto-assign-access-portal.md).