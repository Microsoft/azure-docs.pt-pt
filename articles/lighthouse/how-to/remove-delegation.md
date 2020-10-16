---
title: Remover o acesso a uma delegação
description: Saiba como remover o acesso aos recursos que tinham sido delegados a um prestador de serviços para o Farol de Azure.
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: 72a2c49dde8cccfcc298d4128384a10bb7e8840f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167236"
---
# <a name="remove-access-to-a-delegation"></a>Remover o acesso a uma delegação

Depois de a subscrição ou grupo de recursos de um cliente ter sido delegada num prestador de serviços para o [Farol Azure,](../overview.md)a delegação pode ser removida se necessário. Uma vez removida uma delegação, o [Azure delegava](../concepts/azure-delegated-resource-management.md) o acesso à gestão de recursos que foi previamente concedido aos utilizadores do arrendatário do prestador de serviços deixará de ser aplicável.

A remoção de uma delegação pode ser feita por um utilizador, quer no arrendatário do cliente, quer no arrendatário do prestador de serviços, desde que o utilizador tenha as permissões adequadas.

> [!TIP]
> Embora nos refiramos a prestadores de serviços e clientes neste tópico, [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar os mesmos processos.

## <a name="customers"></a>Clientes

Os utilizadores do inquilino do cliente que tenham a [função de Proprietário incorporado](../../role-based-access-control/built-in-roles.md#owner) para uma subscrição podem remover o acesso do prestador de serviços a essa subscrição (ou a grupos de recursos nessa subscrição). Para tal, um utilizador no arrendatário do cliente pode ir à página de [prestadores](view-manage-service-providers.md#add-or-remove-service-provider-offers) de serviços do portal Azure, encontrar a oferta no prestador de **serviços que oferece** ecrã e selecionar o ícone do caixote do lixo na fila para essa oferta.

Após confirmação da supressão, nenhum utilizadores no arrendatário do prestador de serviços poderá aceder aos recursos que tinham sido previamente delegados.

## <a name="service-providers"></a>Fornecedores de serviços

Os utilizadores de um inquilino gerente podem remover o acesso aos recursos delegados se lhes for concedido o Papel de Eliminação de [Registo de Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) para os recursos do cliente. Se esta função não tiver sido atribuída a nenhum utilizador prestador de serviços, a delegação só pode ser removida por um utilizador no arrendatário do cliente.

O exemplo abaixo mostra uma atribuição que concede a **Função de Registo de Registo de Serviços Geridos** que pode ser incluída num ficheiro de parâmetros durante o [processo de embarque:](onboard-customer.md)

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Esta função também pode ser selecionada numa **Autorização** ao [criar uma oferta de Serviço Gerido](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) para publicar no Azure Marketplace.

Um utilizador com esta permissão pode remover uma delegação de uma das seguintes formas.

### <a name="azure-portal"></a>Portal do Azure

1. Navegue para a [página dos meus clientes.](view-manage-customers.md)
2. Selecione **Delegações**.
3. Encontre a delegação que pretende remover e, em seguida, selecione o ícone do caixote do lixo que aparece na sua linha.

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
- [Ver e gerir clientes](view-manage-customers.md) indo para **os meus clientes** no portal Azure.
