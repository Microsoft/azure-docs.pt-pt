---
title: Utilize identidades geridas num Azure VM com Azure SDKs - Azure AD
description: Amostras de código para a utilização de SDKs Azure com um VM Azure que geriu identidades para recursos Azure.
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
ms.openlocfilehash: 9eb5f1d81874c8303d095aec320f60cc9ae6a7dc
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360569"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Como utilizar identidades geridas para recursos Azure num Azure VM com Azure SDKs 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece uma lista de amostras de SDK, que demonstram a utilização do apoio respetivo da Azure SDK para identidades geridas para os recursos Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Todo o código/script de amostra neste artigo pressupõe que o cliente está a executar um VM com identidades geridas para recursos Azure ativados. Utilize a função VM "Connect" no portal Azure, para ligar remotamente ao seu VM. Para obter detalhes sobre como permitir identidades geridas para recursos Azure num VM, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (utilizando PowerShell, CLI, um modelo ou um Azure SDK). 

## <a name="sdk-code-samples"></a>Amostras de código SDK

| SDK             | Exemplo de código |
| --------------- | ----------- |
| .NET            | [Implementar um modelo de Gestor de Recursos Azure a partir de um VM do Windows utilizando identidades geridas para recursos Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Ligue para os serviços da Azure a partir de um VM Linux utilizando identidades geridas para recursos Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Gerir recursos utilizando identidades geridas para recursos Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Utilize identidades geridas para que os recursos da Azure autentem simplesmente de dentro de um VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Gerir recursos a partir de um VM com identidades geridas para recursos Azure habilitados](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Próximos passos

- Consulte [Azure SDKs](https://azure.microsoft.com/downloads/) para obter a lista completa dos recursos do Azure SDK, incluindo transferências de bibliotecas, documentação e muito mais.
- Para permitir identidades geridas para recursos Azure num Azure VM, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md).








