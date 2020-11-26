---
title: Como encontrar o seu ID de inquilino - Azure Ative Directory
description: Instruções sobre como encontrar e identificação do inquilino do Azure Ative Directory para uma assinatura Azure existente.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32526a31a9d52fbfac57f1d384b25e0939b5e297
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172878"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Como encontrar o seu ID do seu inquilino Azure Ative Directory

As subscrições da Azure têm uma relação de confiança com o Azure Ative Directory (Azure AD). A Azure AD é confiável para autenticar utilizadores, serviços e dispositivos para a subscrição. Cada subscrição tem um ID de inquilino associado a ele, e há algumas maneiras de encontrar o ID do inquilino para a sua assinatura.

## <a name="find-tenant-id-through-the-azure-portal"></a>Encontre a ID do inquilino através do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
 
1. Selecione **Azure Active Directory**.

1. Selecione **Propriedades**.

1. Em seguida, desça até o campo **de identificação** do inquilino. Sua identificação de inquilino estará na caixa.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Diretório Ativo Azure - Imóveis - ID inquilino - Campo de ID do Inquilino":::

## <a name="find-tenant-id-with-powershell"></a>Encontre iD do inquilino com PowerShell

Você também pode encontrar o inquilino programáticamente. Para encontrar o ID do inquilino com a Azure PowerShell, utilize o cmdlet `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Para obter mais informações, consulte esta referência de cmdlet Azure PowerShell para [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Encontre iD do inquilino com CLI
Se quiser utilizar uma interface de linha de comando para encontrar o ID do inquilino, pode fazê-lo com [Azure CLI](/cli/azure/install-azure-cli) ou [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/). 

Para o Azure CLI, utilize um dos comandos **az login**, **lista de conta az**, ou **lista de inquilinos de conta az** como mostrado no exemplo seguinte. Note a propriedade **inquilinaD** para cada uma das suas subscrições na saída de cada comando.

```azurecli-interactive
az login
az account list
az account tenant list
```

Para mais informações, consulte a referência do comando [de login az,](/cli/azure/reference-index#az_login) referência de comando [de conta az](/cli/azure/ext/account/account) ou referência de comando [do inquilino de conta az.](/cli/azure/ext/account/account/tenant)


Para o Microsoft 365 CLI, utilize o **id do inquilino** cmdlet como mostrado no seguinte exemplo:
 
```cli
m365 tenant id get
```

Para mais informações, consulte o [id do inquilino](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) Microsoft 365 obter referência de comando.


## <a name="next-steps"></a>Passos seguintes

- Para criar um novo inquilino AZure AD, consulte [Quickstart: Criar um novo inquilino no Azure Ative Directory](active-directory-access-create-new-tenant.md).

- Para aprender a associar ou adicionar uma subscrição a um inquilino, consulte [Associate ou adicione uma subscrição Azure ao seu inquilino Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md).

- Para aprender a encontrar o ID do objeto, consulte [encontre o ID do objeto do utilizador](/partner-center/find-ids-and-domain-names#find-the-user-object-id).