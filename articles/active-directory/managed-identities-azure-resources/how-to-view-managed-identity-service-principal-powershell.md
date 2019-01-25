---
title: Como ver o principal de serviço de uma identidade gerida com o PowerShell
description: Obter instruções passo a passo para visualizar o principal de serviço de uma identidade gerida com o PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: priyamo
ms.openlocfilehash: bb0462820cc94c06054ada12f0c764d4cfb190f7
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900158"
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

```PowerShell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a visualização de principais de serviço do Azure AD com o PowerShell, consulte [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


