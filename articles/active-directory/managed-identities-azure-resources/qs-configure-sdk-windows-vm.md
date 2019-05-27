---
title: Utilizar um Azure SDK para configurar uma VM com identidades geridas para recursos do Azure
description: Instruções passo a passo instruções para configurar e utilizar managed identidades para recursos do Azure na VM do Azure, com um SDK do Azure.
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
ms.openlocfilehash: 29446f6f0a86a00cc92fe46211caddd22aaa4859
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66112568"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Configurar uma VM com identidades geridas para recursos do Azure com um SDK do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory (AD). Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, saiba como ativar e remover identidades geridas para recursos do Azure para a VM do Azure, com um SDK do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>SDKs do Azure com identidades geridas para recursos do Azure suportam 

O Azure suporta várias plataformas de programação através de uma série de [SDKs do Azure](https://azure.microsoft.com/downloads). Várias delas foram atualizadas para suportar identidades geridas para recursos do Azure e fornecer exemplos de correspondentes para demonstrar a utilização. Esta lista é atualizada à medida que é adicionado suporte adicional:

| SDK | Exemplo |
| --- | ------ | 
| .NET   | [Gerir o recurso a partir de uma VM ativada com identidades geridas para recursos do Azure ativados](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Gerir o armazenamento a partir de uma VM ativada com identidades geridas para recursos do Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Criar uma VM com a identidade gerida sistema atribuído ativada](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Criar uma VM com a identidade gerida sistema atribuído ativada](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Criar VM do Azure com uma identidade de sistema atribuído ativada](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Passos Seguintes

- Consulte artigos relacionados sob **configurar a identidade para uma VM do Azure**, para saber como também pode utilizar os modelos de portal, PowerShell, CLI e recursos do Azure.
