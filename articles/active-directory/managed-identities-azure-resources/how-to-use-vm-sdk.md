---
title: Utilize identidades geridas num Azure VM com SDKs Azure - Azure AD
description: Amostras de código para a utilização de SDKs Azure com um VM Azure que tem gerido identidades para recursos Azure.
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
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184158"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Como utilizar identidades geridas para recursos Azure num Azure VM com SDKs Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece uma lista de amostras de SDK, que demonstram a utilização do apoio do respetivo Azure SDK a identidades geridas para recursos Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Todo o código/script da amostra neste artigo pressupõe que o cliente está a executar um VM com identidades geridas para recursos Azure habilitados. Utilize a função VM "Connect" no portal Azure, para ligar remotamente ao seu VM. Para mais detalhes sobre a possibilidade de permitir identidades geridas para os recursos Azure num VM, consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (utilizando PowerShell, CLI, um modelo ou um Azure SDK). 

## <a name="sdk-code-samples"></a>Amostras de código SDK

| SDK             | Exemplo de código |
| --------------- | ----------- |
| .NET            | [Implemente um modelo de Gestor de Recursos Azure a partir de um VM do Windows utilizando identidades geridas para recursos Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Ligue para os serviços Azure de um VM Linux usando identidades geridas para recursos Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Gerir recursos utilizando identidades geridas para recursos Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Utilize identidades geridas para os recursos do Azure autenticarsimplesmente a partir de dentro de um VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Gerir recursos de um VM com identidades geridas para recursos Azure habilitados](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Passos seguintes

- Consulte os [SDKs Azure](https://azure.microsoft.com/downloads/) para obter a lista completa de recursos Do SDK azure, incluindo transferências de bibliotecas, documentação e muito mais.
- Para permitir identidades geridas para os recursos Azure num Azure VM, consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure.](qs-configure-portal-windows-vm.md)








