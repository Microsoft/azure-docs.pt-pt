---
title: Remover o acesso a uma delegação
description: Saiba como remover o acesso aos recursos que tinham sido delegados a um prestador de serviços para a gestão de recursos delegados do Azure.
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: d0db809eb057f8b4bb48bdf9dd127f4d488f0406
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149453"
---
# <a name="remove-access-to-a-delegation"></a>Remover o acesso a uma delegação

Depois de a subscrição ou grupo de recursos de um cliente ter sido delegado a um prestador de serviços para a [gestão de recursos delegados do Azure,](../concepts/azure-delegated-resource-management.md)a delegação pode ser removida se necessário. Uma vez removida uma delegação, o acesso previamente concedido aos utilizadores do prestador de serviços deixará de ser aplicável.

A remoção de uma delegação pode ser feita por um utilizador no inquilino do cliente ou pelo inquilino do prestador de serviços, desde que o utilizador tenha as permissões adequadas.

## <a name="customers"></a>Clientes

Os utilizadores do inquilino do cliente que tenham a [função de proprietário integrado](../../role-based-access-control/built-in-roles.md#owner) para uma subscrição podem remover o acesso do prestador de serviços a essa subscrição (ou a grupos de recursos nessa subscrição). Para tal, um utilizador do inquilino do cliente pode ir à página de [fornecedores](view-manage-service-providers.md#add-or-remove-service-provider-offers) de serviços do portal Azure, encontrar a oferta no **serviço de ofertas** de suporte, e selecionar o ícone do caixote do lixo na fila para essa oferta.

Após a confirmação da eliminação, nenhum utilizador do inquilino do prestador de serviços poderá aceder aos recursos que tinham sido previamente delegados.

## <a name="service-providers"></a>Fornecedores de serviços

Os utilizadores de um inquilino gerente podem remover o acesso aos recursos delegados se lhes for concedida a Função de Eliminação de [Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) para os recursos do cliente. Se esta função não tiver sido atribuída a nenhum utilizador do prestador de serviços, a delegação só pode ser removida por um utilizador do inquilino do cliente.

O exemplo abaixo mostra uma atribuição que concede a Função de Eliminação de Atribuição de **Serviços Geridos** que pode ser incluída num ficheiro de parâmetrodurante o processo de [embarque:](onboard-customer.md)

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Esta função também pode ser selecionada numa **Autorização** na [criação de uma oferta](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) de Serviço Gerido para publicar no Azure Marketplace.

Um utilizador com esta permissão pode remover uma delegação de uma das seguintes formas.

### <a name="azure-portal"></a>Portal do Azure

1. Navegue para a [página dos meus clientes.](view-manage-customers.md)
2. Selecione **Delegações**.
3. Encontre a delegação que pretende remover e, em seguida, selecione o ícone do caixote do lixo que aparece na sua fileira.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Gestão de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md).
- [Ver e gerir os clientes](view-manage-customers.md) indo para os meus **clientes** no portal Azure.
