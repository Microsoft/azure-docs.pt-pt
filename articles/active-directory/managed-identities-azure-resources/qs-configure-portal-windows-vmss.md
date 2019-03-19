---
title: Como configurar o managed identidades para recursos do Azure num conjunto de dimensionamento de máquinas virtuais
description: Instruções passo a passo instruções para configurar geridos identidades para recursos do Azure num conjunto de dimensionamento no portal do Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c922e0ef005b0e0b22af32f52d8bf28aff0e9515
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57998692"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Configurar identidades geridas para recursos do Azure num conjunto de dimensionamento no portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, com o PowerShell, aprenderá a efetuar as seguintes identidades geridas para operações de recursos do Azure num conjunto de dimensionamento de máquina virtual:

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md).
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para efetuar as operações de gestão neste artigo, a conta tem das atribuições de controlo de acesso baseado em funções do Azure seguintes:

    > [!NOTE]
    > Nenhum adicionais do Azure AD directory as atribuições de funções necessárias.

    - [Contribuinte de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para ativar e remover atribuído de sistema de identidade gerida de um conjunto de dimensionamento de máquina virtual.

## <a name="system-assigned-managed-identity"></a>Atribuído ao sistema de identidade gerida

Nesta seção, aprenderá a ativar e desativar a identidade gerida atribuído ao sistema através do portal do Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuído de sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais

Atualmente, o portal do Azure não suporta a ativar identidade gerida atribuído ao sistema durante a criação de um conjunto de dimensionamento de máquina virtual. Em vez disso, consulte a criação de conjunto de dimensionamento de máquina virtual seguinte artigo de início rápido para primeiro criar um conjunto de dimensionamento de máquina virtual e, em seguida, avance para a secção seguinte para obter detalhes sobre como ativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquinas virtuais:

- [Criar um conjunto de dimensionamento de Máquina Virtual no portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Ativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual existente

Para ativar a identidade gerida atribuído de sistema num conjunto de dimensionamento de máquina virtual que foi originalmente aprovisionado sem ele:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual.

2. Navegue para o conjunto de dimensionamento de máquina virtual pretendida.

3. Sob **sistema atribuído**, **estado**, selecione **no** e, em seguida, clique em **guardar**:

   ![Captura de ecrã de página de configuração](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover sistema atribuído a identidade gerida de um conjunto de dimensionamento de máquinas virtuais

Se tiver um conjunto de dimensionamento de máquina virtual que já não necessita de uma identidade gerida atribuído de sistema:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual. Além disso, certifique-se de que a sua conta pertencer a uma função que dá-lhe permissões de escrita no conjunto de dimensionamento de máquina virtual.

2. Navegue para o conjunto de dimensionamento de máquina virtual pretendida.

3. Sob **sistema atribuído**, **estado**, selecione **desligado** e, em seguida, clique em **guardar**:

   ![Captura de ecrã de página de configuração](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Nesta secção, saiba como adicionar e remover uma identidade gerida atribuído ao utilizador a partir de um conjunto de dimensionamento no portal do Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador durante a criação de um conjunto de dimensionamento de máquinas virtuais

Atualmente, o portal do Azure não suporta a atribuição de uma identidade gerida atribuído ao utilizador durante a criação de um conjunto de dimensionamento de máquina virtual. Em vez disso, consulte a criação de conjunto de dimensionamento de máquina virtual seguinte artigo de início rápido para primeiro criar um conjunto de dimensionamento de máquina virtual e, em seguida, avance para a secção seguinte para obter detalhes sobre a atribuição de uma identidade gerida atribuído ao utilizador a ele:

- [Criar um conjunto de dimensionamento de Máquina Virtual no portal do Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade gerida atribuído ao utilizador a um conjunto de dimensionamento de máquina virtual existente

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure que contém o conjunto de dimensionamento de máquina virtual.
2. Navegue para o conjunto de dimensionamento de máquina virtual pretendida e clique em **identidade**, **atribuída ao utilizador** e, em seguida  **\+adicionar**.

   ![Adicionar a identidade de utilizador atribuído a VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Clique a identidade atribuído ao utilizador que pretende adicionar ao conjunto de dimensionamento de máquina virtual e, em seguida, clique em **adicionar**.
   
   ![Adicionar a identidade de utilizador atribuído a VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover uma identidade gerida atribuído ao utilizador a partir de um conjunto de dimensionamento de máquina virtual

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure que contém a VM.
2. Navegue para o conjunto de dimensionamento de máquina virtual pretendida e clique em **identidade**, **atribuída ao utilizador**, o nome do atribuído ao utilizador gerido identidade que pretende eliminar e, em seguida, clique em **remover** (clique em **Sim** no painel de confirmação).

   ![Remover a identidade de utilizador atribuída uma VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Passos Seguintes

- Com o portal do Azure, forneça um máquina virtual do Azure do conjunto de dimensionamento identidade gerida [acesso a outro recurso do Azure](howto-assign-access-portal.md).


