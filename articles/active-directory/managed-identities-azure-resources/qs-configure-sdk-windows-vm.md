---
title: Usar um SDK do Azure para configurar uma VM com identidades gerenciadas para recursos do Azure
description: Instruções passo a passo para configurar e usar identidades gerenciadas para recursos do Azure em uma VM do Azure, usando um SDK do Azure.
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
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b4f5a26f240656911fb9627d273de8b69edff6a
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809164"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Configurar uma VM com identidades gerenciadas para recursos do Azure usando um SDK do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory (AD). Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, você aprende a habilitar e a remover identidades gerenciadas de recursos do Azure para uma VM do Azure, usando um SDK do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>SDKs do Azure com identidades gerenciadas para suporte de recursos do Azure 

O Azure dá suporte a várias plataformas de programação por meio de uma série de [SDKs do Azure](https://azure.microsoft.com/downloads). Várias delas foram atualizadas para dar suporte a identidades gerenciadas para recursos do Azure e fornecer exemplos correspondentes para demonstrar o uso. Essa lista é atualizada conforme o suporte adicional é adicionado:

| SDK | Exemplo |
| --- | ------ | 
| .NET   | [Gerenciar recursos de uma VM habilitada com identidades gerenciadas para recursos do Azure habilitados](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gerenciar o armazenamento de uma VM habilitada com identidades gerenciadas para recursos do Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Criar uma VM com identidade gerenciada atribuída pelo sistema habilitada](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Criar uma VM com identidade gerenciada atribuída pelo sistema habilitada](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Criar VM do Azure com uma identidade atribuída pelo sistema habilitada](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Passos seguintes

- Consulte os artigos relacionados em **Configurar identidade para uma VM do Azure**, para saber como você também pode usar o portal do Azure, o PowerShell, a CLI e os modelos de recursos.
