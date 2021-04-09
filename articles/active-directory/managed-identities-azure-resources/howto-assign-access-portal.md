---
title: Atribuir um acesso de identidade gerido a um recurso utilizando o portal Azure - Azure AD
description: Instruções passo a passo para atribuir uma identidade gerida num recurso de acesso a outro recurso, utilizando o portal Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6584754edf3ff7ae31c3b9ace72baf16459dbc44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93359999"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de identidade gerido a um recurso utilizando o portal Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso Azure com uma identidade gerida, pode dar acesso de identidade gerida a outro recurso, como qualquer diretor de segurança. Este artigo mostra-lhe como dar a uma máquina virtual Azure ou à balança de máquinas virtual acesso à identidade gerida de uma conta de armazenamento Azure, utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Use o Azure RBAC para atribuir um acesso de identidade gerido a outro recurso

Depois de ter ativado a identidade gerida num recurso Azure, como um conjunto de escala de máquina virtual [Azure VM](qs-configure-portal-windows-vm.md) ou [Azure:](qs-configure-portal-windows-vmss.md)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure sob a qual configura a identidade gerida.

2. Navegue para o recurso pretendido no qual pretende modificar o controlo de acesso. Neste exemplo, estamos a dar acesso a uma máquina virtual Azure a uma conta de armazenamento, por isso navegamos para a conta de armazenamento.

3. Selecione a página **de controlo de acesso (IAM)** do recurso e selecione + Adicionar a atribuição de **função**. Em seguida, especifique o **Função**, **Atribua o acesso a**, e especifique a **subscrição** correspondente . De acordo com a área de critérios de pesquisa, deverá consultar o recurso. Selecione o recurso e **selecione Guardar**. 

   ![Screenshot do controlo de acesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Passos seguintes

- [Identidade gerida para visão geral dos recursos da Azure](overview.md)
- Para permitir a identidade gerida numa máquina virtual Azure, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md).
- Para permitir a identidade gerida num conjunto de escala de máquina virtual Azure, consulte [identidades geridas configure para recursos Azure numa escala de máquina virtual definida utilizando o portal Azure](qs-configure-portal-windows-vmss.md).


