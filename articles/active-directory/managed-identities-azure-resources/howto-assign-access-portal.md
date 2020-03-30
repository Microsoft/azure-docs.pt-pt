---
title: Atribuir um acesso de identidade gerido a um recurso utilizando o portal Azure - Azure AD
description: Instruções passo a passo para atribuir uma identidade gerida num recurso de acesso a outro recurso, utilizando o portal Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547291"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de identidade gerido a um recurso utilizando o portal Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso Azure com uma identidade gerida, pode dar acesso à identidade gerida a outro recurso, tal como qualquer diretor de segurança. Este artigo mostra-lhe como dar a uma máquina virtual Azure ou ao conjunto de máquinas virtuais acesso de identidade a uma conta de armazenamento Azure, utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utilize o RBAC para atribuir um acesso de identidade gerido a outro recurso

Depois de ter ativado a identidade gerida num recurso Azure, como um [Azure VM](qs-configure-portal-windows-vm.md) ou [Azure VMSS:](qs-configure-portal-windows-vmss.md)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição Azure sob a qual configura a identidade gerida.

2. Navegue para o recurso pretendido no qual pretende modificar o controlo de acesso. Neste exemplo, estamos a dar acesso a uma máquina virtual Azure a uma conta de armazenamento, por isso navegamos para a conta de armazenamento.

3. Selecione a página de controlo de **acesso (IAM)** do recurso e selecione + Adicionar a atribuição de **funções**. Em seguida, especifique a **Função**, **Atribuir acesso a**, e especificar a **subscrição**correspondente . Na área dos critérios de pesquisa, deve ver o recurso. Selecione o recurso e selecione **Guardar**. 

   ![Screenshot de controlo de acesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Passos seguintes

- [Identidade gerida para visão geral dos recursos do Azure](overview.md)
- Para permitir a identidade gerida numa máquina virtual Azure, consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md).
- Para permitir a identidade gerida num conjunto de máquinas virtuais Azure, consulte [as identidades geridas pela Configure para os recursos Do Azure num conjunto de máquinas virtuais utilizando o portal Azure](qs-configure-portal-windows-vmss.md).


