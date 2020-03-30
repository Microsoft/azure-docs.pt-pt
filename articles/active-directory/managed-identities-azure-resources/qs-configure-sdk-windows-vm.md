---
title: Use um SDK para configurar identidades geridas num VM - Azure AD
description: Instruções passo a passo para configurar e utilizar identidades geridas para os recursos Azure num Azure VM, utilizando um SDK Azure.
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
ms.openlocfilehash: d11cd51984f82bc20c02669e796d9ba21b9ed5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74183482"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Configure um VM com identidades geridas para recursos Azure usando um SDK Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Azure Ative Directory (AD). Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, aprende-se a ativar e remover identidades geridas para os recursos Azure para um Azure VM, utilizando um Azure SDK.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>SDKs Azure com identidades geridas para apoio aos recursos do Azure 

O Azure suporta várias plataformas de programação através de uma série de [SDKs Azure.](https://azure.microsoft.com/downloads) Vários deles foram atualizados para apoiar identidades geridas para os recursos do Azure, e fornecer amostras correspondentes para demonstrar o uso. Esta lista é atualizada à medida que o suporte adicional é adicionado:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Gerir o recurso a partir de um VM habilitado com identidades geridas para recursos Azure habilitados](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gerir o armazenamento a partir de um VM habilitado com identidades geridas para recursos Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Criar um VM com identidade gerida atribuída pelo sistema ativada](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Criar um VM com identidade gerida atribuída pelo sistema ativada](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Criar VM Azure com uma identidade atribuída ao sistema ativada](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Passos seguintes

- Consulte artigos relacionados sob **identidade configure para um VM Azure,** para saber como também pode usar o portal Azure, PowerShell, CLI e modelos de recursos.
