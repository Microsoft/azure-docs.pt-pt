---
title: Criar programaticamente subscrições de Contrato Enterprise do Azure com as APIs mais recentes
description: Saiba como criar subscrições do Azure Enterprise Agreement programáticamente utilizando as versões mais recentes dos modelos REST API, Azure CLI, Azure PowerShell e Azure Resource Manager.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 3275fe0a72b70038cf834436e8290b9c55643414
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963296"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Criar programaticamente subscrições de Contrato Enterprise do Azure com as APIs mais recentes

Este artigo ajuda-o a criar subscrições de Contrato Enterprise (EA) do Azure para uma conta de faturação do EA através de programação com as versões de API mais recentes. Se continuar a utilizar a versão de pré-visualização mais antiga, veja [Criar subscrições do Azure através de programação com APIs de pré-visualização](programmatically-create-subscription-preview.md). 

Neste artigo, aprenderá a criar subscrições através de programação com o Azure Resource Manager.

Quando cria uma subscrição do Azure através de programação, essa subscrição é regida pelo contrato sob o qual adquiriu os serviços do Azure à Microsoft ou a um revendedor autorizado. Para obter mais informações, veja [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Deve ter uma função de Proprietário numa Conta de Inscrição para criar uma subscrição. Existem duas formas de obter a função:

* O Administrador Enterprise da sua inscrição pode [torná-lo um Proprietário de Conta](https://ea.azure.com/helpdocs/addNewAccount) (início de sessão obrigatório), o que o torna um Proprietário da Conta de Inscrição.
* Um Proprietário existente da Conta de Inscrição pode [conceder-lhe acesso](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Da mesma forma, para utilizar um principal de serviço para criar uma subscrição de EA, tem de [conceder a esse principal de serviço a capacidade de criar subscrições](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put).  
    Se estiver a utilizar um SPN para criar subscrições, utilize o ObjectId do Registo de Aplicações AD Azure como o Principal ObjectId do Serviço utilizando o PowerShell ou o Azure Ative [Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) ou [Azure CLI](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_list).
  > [!NOTE]
  > Confirme se utiliza a versão correta da API para conceder permissões de proprietário à conta de inscrição. Para este artigo e para as APIs documentadas no mesmo, utilize a API [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Se estiver a migrar para utilizar as APIs mais recentes, tem de conceder a permissão de proprietário novamente com a versão [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). A configuração anterior que fez com a [versão 2015-07-01](grant-access-to-create-subscription.md) não é convertida automaticamente para utilização com as APIs mais recentes.

## <a name="find-accounts-you-have-access-to"></a>Localizar contas às quais tem acesso

Depois de ser adicionado a uma Conta de Inscrição associada a um Proprietário de Conta, o Azure usa a relação de conta para inscrição para determinar onde faturar os custos da subscrição. Todas as subscrições criadas no âmbito da conta são faturadas na inscrição de EA em que a conta está. Para criar subscrições, deve transmitir valores sobre a conta de inscrição e as entidades principais de utilizador para ser proprietário da subscrição.

Para executar os comandos seguintes, deve ter sessão iniciada no *diretório raiz* do Proprietário da Conta, que é o diretório no qual as subscrições são criadas por predefinição.

### <a name="rest"></a>[REST](#tab/rest)

Pedido para listar todas as contas de inscrição às quais tem acesso:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

A resposta da API lista todas as contas de inscrição às quais tem acesso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

Os valores para um âmbito de faturação e `id` são a mesma coisa. O `id` da sua conta de inscrição é o âmbito da faturação no qual o pedido de subscrição é iniciado. É importante saber o ID porque este é um parâmetro obrigatório que irá utilizar mais tarde no artigo para criar uma subscrição.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Por favor, utilize a Azure CLI ou a REST API para obter este valor.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pedido para listar todas as contas de inscrição às quais tem acesso:

```azurecli-interactive
> az billing account list
```

A resposta lista todas as contas de inscrição a que tem acesso

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

Os valores para um âmbito de faturação e `id` são a mesma coisa. O `id` da sua conta de inscrição é o âmbito da faturação no qual o pedido de subscrição é iniciado. É importante saber o ID porque este é um parâmetro obrigatório que irá utilizar mais tarde no artigo para criar uma subscrição.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Criar subscrições numa conta de registo específica

O exemplo a seguir cria uma subscrição denominada *Subscrição da Equipa de Desenvolvimento* na conta de inscrição selecionada no passo anterior. 

### <a name="rest"></a>[REST](#tab/rest)

Chame a API PUT para criar uma subscrição/alias de criação de subscrição.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

No corpo do pedido, indique o `billingScope` e o `id` de uma das suas `enrollmentAccounts`.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Os valores permitidos para `Workload` são `Production` e `DevTest`.

#### <a name="response"></a>Resposta

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

Pode chamar GET no mesmo URL para obter o estado do pedido.

### <a name="request"></a>Pedir

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Resposta

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

É devolvido um estado em curso como um estado `Accepted` em `provisioningState`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para instalar a versão mais recente do módulo que contém o cmdlet `New-AzSubscriptionAlias`, execute `Install-Module Az.Subscription`. Para instalar uma versão recente do PowerShellGet, veja [Obter o Módulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Execute o comando [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) através do âmbito da faturação `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

Obtém o subscriptionId como parte da resposta do comando.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, instale a extensão ao executar `az extension add --name account` e `az extension add --name alias`.

Execute o comando [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create) e indique `billing-scope` e `id` de uma das suas `enrollmentAccounts`. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

Obtém o subscriptionId como parte da resposta do comando.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template"></a>Utilizar o modelo do Resource Manager

A secção anterior mostrou como criar uma subscrição com PowerShell, CLI ou REST API. Se precisar automatizar a criação de subscrições, considere utilizar um modelo de Gestor de Recursos Azure (modelo ARM).

O modelo a seguir cria uma subscrição. Para `billingScope` , fornecer o ID da conta de inscrição. Para `targetManagementGroup` , forneça o grupo de gestão onde pretende criar a subscrição.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        },
        "targetManagementGroup": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the target management group to place the subscription."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]",
                "managementGroupId": "[tenantResourceId('Microsoft.Management/managementGroups/', parameters('targetManagementGroup'))]"
            }
        }
    ],
    "outputs": {}
}
```

Implemente o modelo ao nível do [grupo de gestão.](../../azure-resource-manager/templates/deploy-to-management-group.md)

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

Com um corpo de pedido:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      },
      "targetManagementGroup": {
        "value": "mg2"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321' targetManagementGroup=mg2
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da API de criação de subscrições do Azure Enterprise

- Apenas as subscrições do Azure Enterprise são criadas com esta API.
- Há um limite de 2000 subscrições por conta de inscrição. Depois disso, só podem ser criadas mais subscrições para a conta no portal do Azure. Para criar mais subscrições através da API, crie outra conta de inscrição. As subscrições canceladas, eliminadas e transferidas contam para o limite de 2000.
- Os utilizadores que não sejam Proprietários de Conta, mas que tenham sido adicionados a uma conta de inscrição via RBAC do Azure, não podem criar subscrições no portal do Azure.
- Não pode selecionar o inquilino onde a subscrição vai ser criada. A subscrição é sempre criada no inquilino principal do Proprietário da Conta. Para mover a assinatura para um inquilino diferente, veja [alterar inquilino de subscrição](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Passos seguintes

* Agora que criou uma subscrição, pode conceder essa capacidade a outros entidades principais de utilizador e de serviço. Para obter mais informações, veja [Conceder acesso para criar subscrições Enterprise do Azure (pré-visualização)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerir grandes números de subscrições com grupos de gestão, veja [Organizar recursos com os grupos de gestão do Azure](../../governance/management-groups/overview.md).
* Para alterar o grupo de gestão para uma subscrição, consulte [as subscrições Move](../../governance/management-groups/manage.md#move-subscriptions).
