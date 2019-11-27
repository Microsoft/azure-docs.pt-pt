---
title: Atribuir um acesso de identidade gerenciada a um recurso usando o portal do Azure-Azure AD
description: Instruções passo a passo para atribuir uma identidade gerenciada em um acesso de recurso a outro recurso, usando o portal do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24c97909870c4d76b07ec837e5f624a509bd1f2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547291"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de identidade gerenciada a um recurso usando o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso do Azure com uma identidade gerenciada, você pode conceder o acesso de identidade gerenciada a outro recurso, assim como qualquer entidade de segurança. Este artigo mostra como conceder a uma máquina virtual do Azure ou o acesso de identidade gerenciada do conjunto de dimensionamento de máquinas virtuais a uma conta de armazenamento do Azure usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade gerenciada atribuída pelo sistema e](overview.md#how-does-the-managed-identities-for-azure-resources-work)** atribuída pelo usuário.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usar o RBAC para atribuir um acesso de identidade gerenciada a outro recurso

Depois de habilitar a identidade gerenciada em um recurso do Azure, como uma [VM do Azure](qs-configure-portal-windows-vm.md) ou [VMSS do Azure](qs-configure-portal-windows-vmss.md):

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure sob a qual você configurou a identidade gerenciada.

2. Navegue até o recurso desejado no qual você deseja modificar o controle de acesso. Neste exemplo, estamos fornecendo acesso a uma conta de armazenamento a uma máquina virtual do Azure e, portanto, navegamos até a conta de armazenamento.

3. Selecione a página **controle de acesso (iam)** do recurso e selecione **+ Adicionar atribuição de função**. Em seguida, especifique a **função**, **atribua acesso a**e especifique a **assinatura**correspondente. Na área critérios de pesquisa, você deve ver o recurso. Selecione o recurso e selecione **salvar**. 

   ![Captura de tela de controle de acesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Passos seguintes

- [Visão geral da identidade gerenciada para recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md).
- Para habilitar a identidade gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure](qs-configure-portal-windows-vmss.md).


