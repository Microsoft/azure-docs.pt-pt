---
title: Exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell-Azure AD
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell.
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
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ac2d1d4fc5e669f63bff49a7948db74ec1baa62
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184113"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, você aprenderá a exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [Inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- Habilite [a identidade atribuída pelo sistema em uma máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicativo](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Instalar a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

Este comando a seguir demonstra como exibir a entidade de serviço de uma VM ou aplicativo com a identidade atribuída pelo sistema habilitada. Substitua `<VM or application name>` pelos seus próprios valores.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como exibir entidades de serviço do Azure AD usando o PowerShell, consulte [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


