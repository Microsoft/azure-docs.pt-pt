---
title: Como utilizar identidades geridas para recursos do Azure numa VM do Azure com os SDKs do Azure
description: Exemplos de código para utilizar os SDKs do Azure com uma VM do Azure que geriu identidades para recursos do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 6827b01e72cd72d3f017df36205ccb985b4f242e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157655"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Como utilizar identidades geridas para recursos do Azure numa VM do Azure com os SDKs do Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece uma lista de exemplos do SDK, que demonstram a utilização de seus respectivos suporte do SDK Azure para identidades geridas para recursos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Todos os código/script de exemplo neste artigo supõe que o cliente está em execução numa VM com identidades geridas para recursos do Azure ativados. Utilize a funcionalidade de "Ligar" de VM no portal do Azure, para ligar remotamente à VM. Para obter detalhes sobre como ativar identidades geridas para recursos do Azure numa VM, consulte [configurar geridos identidades para recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (com o PowerShell, CLI, um modelo ou um Azure SDK). 

## <a name="sdk-code-samples"></a>Exemplos de código do SDK

| SDK             | Exemplo de código |
| --------------- | ----------- |
| .NET            | [Implementar um modelo Azure Resource Manager a partir de uma VM do Windows com identidades geridas para recursos do Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Chamar os serviços do Azure a partir de uma VM com Linux através de identidades geridas para recursos do Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Gerir recursos através de identidades geridas para recursos do Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Utilizar identidades geridas para recursos do Azure para autenticar a partir de dentro de uma VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Gerir recursos de uma VM com identidades geridas para recursos do Azure ativados](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Passos Seguintes

- Ver [SDKs do Azure](https://azure.microsoft.com/downloads/) para a lista completa de recursos do SDK do Azure, incluindo downloads de biblioteca, documentação e muito mais.
- Para ativar identidades geridas para recursos do Azure numa VM do Azure, consulte [configurar geridos identidades para recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md).








