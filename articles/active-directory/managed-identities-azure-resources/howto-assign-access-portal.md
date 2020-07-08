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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2af718c3555176167eb154b0a718218c42e93dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608301"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de identidade gerido a um recurso utilizando o portal Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso Azure com uma identidade gerida, pode dar acesso de identidade gerida a outro recurso, como qualquer diretor de segurança. Este artigo mostra-lhe como dar a uma máquina virtual Azure ou à balança de máquinas virtual acesso à identidade gerida de uma conta de armazenamento Azure, utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utilize o RBAC para atribuir um acesso de identidade gerido a outro recurso

Depois de ter ativado a identidade gerida num recurso Azure, como um conjunto de escala de máquina virtual [Azure VM](qs-configure-portal-windows-vm.md) ou [Azure:](qs-configure-portal-windows-vmss.md)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure sob a qual configura a identidade gerida.

2. Navegue para o recurso pretendido no qual pretende modificar o controlo de acesso. Neste exemplo, estamos a dar acesso a uma máquina virtual Azure a uma conta de armazenamento, por isso navegamos para a conta de armazenamento.

3. Selecione a página **de controlo de acesso (IAM)** do recurso e selecione + Adicionar a atribuição de **função**. Em seguida, especifique o **Função**, **Atribua o acesso a**, e especifique a **subscrição**correspondente . De acordo com a área de critérios de pesquisa, deverá consultar o recurso. Selecione o recurso e **selecione Guardar**. 

   ![Screenshot do controlo de acesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Próximos passos

- [Identidade gerida para visão geral dos recursos da Azure](overview.md)
- Para permitir a identidade gerida numa máquina virtual Azure, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md).
- Para permitir a identidade gerida num conjunto de escala de máquina virtual Azure, consulte [identidades geridas configure para recursos Azure numa escala de máquina virtual definida utilizando o portal Azure](qs-configure-portal-windows-vmss.md).


