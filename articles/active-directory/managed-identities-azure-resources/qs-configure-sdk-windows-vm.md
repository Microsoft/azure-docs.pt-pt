---
title: Utilize um SDK para configurar identidades geridas num VM - Azure AD
description: Instruções passo a passo para configurar e utilizar identidades geridas para recursos Azure num Azure VM, utilizando um Azure SDK.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d1be35d2add24cbbc171c3da3bd4d06bce4337
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359668"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Configure um VM com identidades geridas para recursos Azure usando um Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (AD). Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, você aprende como ativar e remover identidades geridas para recursos Azure para um Azure VM, usando um Azure SDK.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDKs com identidades geridas para apoio aos recursos da Azure 

O Azure suporta várias plataformas de programação através de uma série de [Azure SDKs](https://azure.microsoft.com/downloads). Vários deles foram atualizados para apoiar identidades geridas para os recursos Azure, e fornecer amostras correspondentes para demonstrar o uso. Esta lista é atualizada à medida que o suporte adicional é adicionado:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Gerir recurso a partir de um VM habilitado com identidades geridas para recursos Azure habilitados](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gerir o armazenamento a partir de um VM habilitado com identidades geridas para recursos Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Criar um VM com identidade gerida atribuída pelo sistema](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Criar um VM com identidade gerida atribuída pelo sistema](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Criar VM Azure com uma identidade atribuída ao sistema](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Passos seguintes

- Consulte artigos relacionados em **Configuração Identidade para um VM Azure,** para saber como também pode usar o portal Azure, PowerShell, CLI e modelos de recursos.
