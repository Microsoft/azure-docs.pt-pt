---
title: Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure com SDKs do Azure
description: Exemplos de código para usar SDKs do Azure com uma VM do Azure que tem identidades gerenciadas para recursos do Azure.
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
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c66bb2b4e9af6f4b79b0ead98b8d18fc56cb467
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809183"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure com SDKs do Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece uma lista de exemplos de SDK, que demonstram o uso do suporte do respectivo SDK do Azure para identidades gerenciadas para recursos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Todo o código/script de exemplo neste artigo pressupõe que o cliente está sendo executado em uma VM com identidades gerenciadas para recursos do Azure habilitados. Use o recurso "conectar" da VM no portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar identidades gerenciadas para recursos do Azure em uma VM, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (usando o PowerShell, a CLI, um modelo ou um SDK do Azure). 

## <a name="sdk-code-samples"></a>Exemplos de código do SDK

| SDK             | Exemplo de código |
| --------------- | ----------- |
| .NET            | [Implantar um modelo de Azure Resource Manager de uma VM do Windows usando identidades gerenciadas para recursos do Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Chamar serviços do Azure de uma VM do Linux usando identidades gerenciadas para recursos do Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Gerenciar recursos usando identidades gerenciadas para recursos do Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Usar identidades gerenciadas para recursos do Azure para autenticar simplesmente de dentro de uma VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Gerenciar recursos de uma VM com identidades gerenciadas para recursos do Azure habilitados](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Passos seguintes

- Consulte [SDKs do Azure](https://azure.microsoft.com/downloads/) para obter a lista completa de recursos do SDK do Azure, incluindo downloads de biblioteca, documentação e muito mais.
- Para habilitar identidades gerenciadas para recursos do Azure em uma VM do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md).








