---
title: Ver o principal de serviço de uma identidade gerida usando powerShell - Azure AD
description: Instruções passo a passo para visualizar o diretor de serviço de uma identidade gerida utilizando o PowerShell.
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
ms.openlocfilehash: 33fc6fbfd7c47b5809e8aff8ee9806c9eeac1162
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298687"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Ver o principal de serviço de uma identidade gerida usando powerShell

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaD Azure, sem ter credenciais no seu código. 

Neste artigo, aprende-se a ver o principal de serviço de uma identidade gerida utilizando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md)
- Se ainda não tem uma conta Azure, [inscreva-se para uma conta gratuita.](https://azure.microsoft.com/free/)
- Ativar [o sistema de identidade atribuída numa máquina](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) virtual ou [aplicação](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Ver o diretor de serviço

Este comando seguinte demonstra como visualizar o diretor de serviço de um VM ou aplicação com identidade atribuída ao sistema. Substitua `<VM or application name>` com os seus próprios valores.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a visualização dos diretores de serviço saqueados do Azure AD utilizando o PowerShell, consulte [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


