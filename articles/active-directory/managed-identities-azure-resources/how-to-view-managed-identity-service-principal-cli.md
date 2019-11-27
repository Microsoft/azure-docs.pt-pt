---
title: Exibir a entidade de serviço de uma identidade gerenciada-CLI do Azure-Azure AD
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando o CLI do Azure.
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
ms.openlocfilehash: ba60fdfefb5d741e92a56d2dc61eb058ac26b5ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224610"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Exibir a entidade de serviço de uma identidade gerenciada usando CLI do Azure

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dê suporte à autenticação do Azure AD sem ter credenciais em seu código. 

Neste artigo, você aprenderá a exibir a entidade de serviço de uma identidade gerenciada usando o CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md).
- Se você ainda não tiver uma conta do Azure, [Inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- Habilite [a identidade atribuída pelo sistema em uma máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicativo](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Para executar os exemplos de script da CLI, tem três opções:
    - Use [Azure cloud Shell](../../cloud-shell/overview.md) da portal do Azure (consulte a próxima seção).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se preferir usar um console da CLI local e entrar no Azure usando `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

Este comando a seguir demonstra como exibir a entidade de serviço de uma VM ou aplicativo com identidade gerenciada habilitada. Substitua `<VM or application name>` pelos seus próprios valores. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como gerenciar entidades de serviço do Azure AD usando CLI do Azure, consulte [AZ ad SP](/cli/azure/ad/sp).


