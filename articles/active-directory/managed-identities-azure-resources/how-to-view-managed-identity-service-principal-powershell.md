---
title: Ver o principal de serviço de uma identidade gerida usando PowerShell - Azure AD
description: Instruções passo a passo para visualizar o principal de serviço de uma identidade gerida utilizando o PowerShell.
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
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5536d18ed2af83da122f5748912a0544bbbf7b19
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074834"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Ver o principal de serviço de uma identidade gerida usando o PowerShell

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código. 

Neste artigo, você aprende a ver o principal de serviço de uma identidade gerida usando PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta Azure, [inscreva-se numa conta gratuita.](https://azure.microsoft.com/free/)
- Ativar a identidade atribuída ao sistema numa máquina ou [aplicação](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) [virtual.](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)
- Instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Ver o principal de serviço

Este comando seguinte demonstra como ver o principal de serviço de um VM ou aplicação com a identidade atribuída pelo sistema ativada. `<VM or application name>`Substitua-o por seus próprios valores.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a visualização dos principais do serviço Azure AD utilizando o PowerShell, consulte [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


