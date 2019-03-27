---
title: Como ver o principal de serviço de uma identidade gerida com o PowerShell
description: Obter instruções passo a passo para visualizar o principal de serviço de uma identidade gerida com o PowerShell.
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
ms.openlocfilehash: 5aa5ab96ae85833e59ed40161cafcc1eb88379be
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485803"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Ver o principal de serviço de uma identidade gerida com o PowerShell

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, sem ter credenciais em seu código. 

Neste artigo, irá aprender a ver o principal de serviço de uma identidade gerida com o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md).
- Se ainda não tiver uma conta do Azure, [Inscreva-se numa conta gratuita](https://azure.microsoft.com/free/).
- Ativar [identidade do sistema atribuído numa máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicação](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Ver o principal de serviço

Este comando seguinte demonstra como visualizar o principal de serviço de uma VM ou a aplicação com a identidade de sistema atribuído ativada. Substitua `<VM or application name>` pelos seus próprios valores.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a visualização de principais de serviço do Azure AD com o PowerShell, consulte [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


