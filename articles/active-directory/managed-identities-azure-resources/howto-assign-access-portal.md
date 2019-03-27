---
title: Como atribuir acesso uma identidade gerida a um recurso do Azure no portal do Azure
description: Instruções passo a passo para atribuir uma identidade gerida no acesso a recursos um ao outro recurso, com o portal do Azure.
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
ms.openlocfilehash: 958b3d72a3a8df4a3b67f62e7db788d7142ca667
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445476"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de identidade gerida a um recurso com o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Depois de configurar um recurso do Azure com uma identidade gerida, pode dar o acesso de identidade gerida para outro recurso, tal como qualquer entidade de segurança. Este artigo mostra-lhe como conceder acesso de identidade gerida do conjunto de dimensionamento de máquina virtual ou máquina virtual do Azure para uma conta de armazenamento do Azure, com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utilizar o RBAC para atribuir um acesso de identidade gerida para outro recurso

Depois de ativar a identidade gerida num recurso do Azure, tal como um [VM do Azure](qs-configure-portal-windows-vm.md) ou [VMSS do Azure](qs-configure-portal-windows-vmss.md):

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure na qual configurou a identidade gerida.

2. Navegue para o recurso pretendido no qual pretende modificar o controlo de acesso. Neste exemplo, estamos oferecendo um acesso de máquina virtual do Azure para uma conta de armazenamento, para que, navegar para a conta de armazenamento.

3. Selecione o **controlo de acesso (IAM)** página do recurso e selecione **+ adicionar atribuição de função**. Em seguida, especifique a **função**, **atribuir acesso a**e especifique o correspondente **subscrição**. Na área de critérios de pesquisa, deverá ver o recurso. Selecione o recurso e selecione **guardar**. 

   ![Captura de ecrã de controlo (IAM) de acesso](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Passos Seguintes

- [Identidade gerida para descrição geral de recursos do Azure](overview.md)
- Para ativar a identidade gerida numa máquina virtual do Azure, consulte [configurar geridos identidades para recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md).
- Para ativar a identidade gerida num conjunto de dimensionamento de máquina virtual do Azure, consulte [configurar geridos identidades para recursos do Azure num conjunto de dimensionamento no portal do Azure](qs-configure-portal-windows-vmss.md).


