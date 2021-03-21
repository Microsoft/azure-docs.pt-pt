---
title: Criar programaticamente subscrições do Azure para um Contrato de Parceiro da Microsoft com as APIs mais recentes
description: Saiba como criar subscrições Azure para um Acordo de Parceiros da Microsoft utilizando programáticamente as versões mais recentes dos modelos REST API, Azure CLI, Azure PowerShell e Azure Resource Manager.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/12/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 5a731aab924e63eac468a22862f35aeff76bc068
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593974"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>Criar programaticamente subscrições do Azure para um Contrato de Parceiro da Microsoft com as APIs mais recentes

Este artigo ajuda-o a criar subscrições do Azure para um Contrato de Parceiro da Microsoft através de programação com as versões de API mais recentes. Se continuar a utilizar a versão de pré-visualização mais antiga, veja [Criar subscrições do Azure através de programação com APIs de pré-visualização](programmatically-create-subscription-preview.md). 

Neste artigo, aprenderá a criar subscrições através de programação com o Azure Resource Manager.

Quando cria uma subscrição do Azure através de programação, essa subscrição é regida pelo contrato sob o qual adquiriu os serviços do Azure à Microsoft ou a um revendedor autorizado. Para obter mais informações, veja [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Deve ter uma função de Administrador Global ou Agente de Administração na conta do fornecedor de soluções de nuvem da sua organização para criar uma subscrição para a sua conta de faturação. Para obter mais informações, veja [Partner Center - Assign users roles and permissions](/partner-center/permissions-overview) (Centro de Parceiros – Atribuir funções e permissões de utilizadores).

Se não sabe se tem acesso a uma conta de Contrato de Parceiro da Microsoft, veja [Verificar o acesso a um Contrato de Parceiro da Microsoft](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement).

Os exemplos que se seguem utilizam APIs REST. Atualmente, o PowerShell e a CLI do Azure não são suportados.

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>Localizar as contas de faturação às quais tem acesso

Faça o pedido seguinte para listar todas as contas de faturação às quais tem acesso.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

A resposta da API lista as contas de faturação.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Use a propriedade `displayName` para identificar a conta de faturação para a qual pretende criar subscrições. Certifique-se de que o agreementType da conta é *MicrosoftPartnerAgreement*. Copie o `name` para a conta. Por exemplo, para criar uma subscrição para a conta de faturação `Contoso`, copie `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole o valor algures para que possa utilizá-lo no passo seguinte.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Por favor, utilize a Azure CLI ou a REST API para obter este valor.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az billing account list
```
Receberá uma lista de todas as contas de faturação a que tem acesso.

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Partner",
    "agreementType": "MicrosoftPartnerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

Utilize a propriedade displayName para identificar a conta de faturação para a qual pretende criar subscrições. Certifique-se de que o agreementType da conta é MicrosoftPartnerAgreement. Copie o nome da conta. Por exemplo, para criar uma subscrição para a conta de faturação Contoso, cópia 99a13315-xxxx-xxxx-xxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx-xx.xx. Cole o valor algures para que possa utilizá-lo no passo seguinte.

---

## <a name="find-customers-that-have-azure-plans"></a>Localizar clientes que têm planos do Azure

Faça o pedido seguinte, com o `name` copiado no primeiro passo (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) para listar todos os clientes na conta de faturação para quem pode criar subscrições do Azure.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

A resposta da API lista os clientes na conta de faturação com planos do Azure. Pode criar subscrições para estes clientes.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

Use a propriedade `displayName` para identificar o cliente para o qual pretende criar subscrições. Copie o `id` para o cliente. Por exemplo, para criar uma subscrição para `Fabrikam toys`, copie `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Cole o valor algures para utilizá-lo em passos posteriores.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Por favor, utilize a Azure CLI ou a REST API para obter este valor.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az billing customer list --account-name 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

A resposta da API lista os clientes na conta de faturação com planos do Azure. Pode criar subscrições para estes clientes.

```json
[
  {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Fabrikam toys",
    "id": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  },
  {
    "billingProfileDisplayName": "Contoso toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Contoso toys",
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "d49c364c-f866-4cc2-a284-d89f369b7951",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  }
]

```

Use a propriedade `displayName` para identificar o cliente para o qual pretende criar subscrições. Copie o `id` para o cliente. Por exemplo, para criar uma subscrição para `Fabrikam toys`, copie `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Cole o valor algures para utilizá-lo em passos posteriores.

---

## <a name="get-the-resellers-for-a-customer"></a>Consiga os revendedores para um cliente

Esta secção é opcional apenas para os Fornecedores indiretos.

Se for um Fornecedor indireto no modelo de duas camadas do CSP, pode especificar um revendedor quando criar subscrições para os clientes.

### <a name="rest"></a>[REST](#tab/rest)

Faça o pedido seguinte, com o `id` copiado no segundo passo (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) para listar todos os revendedores disponíveis para um cliente.

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
A resposta da API lista os revendedores do cliente:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

Utilize a propriedade `description` para identificar o revendedor que está associado à subscrição. Copie o `resellerId` para o revendedor. Por exemplo, para associar `Wingtip`, copie `3xxxxx`. Cole o valor algures para que possa utilizá-lo no passo seguinte.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Por favor, utilize a Azure CLI ou a REST API para obter este valor.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Faça o seguinte pedido, com o `name` copiado do primeiro passo ( ```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx``` ) e o cliente `name` copiado do passo anterior ( ```acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ).

```azurecli
 az billing customer show --expand "enabledAzurePlans,resellers" --account-name "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --name "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

A resposta da API lista os revendedores do cliente:

```json
{
  "billingProfileDisplayName": "Fabrikam toys Billing Profile",
  "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
  "displayName": "Fabrikam toys",
  "enabledAzurePlans": [
    {
      "skuDescription": "Microsoft Azure Plan",
      "skuId": "0001"
    }
  ],
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resellers": [
    {
      "description": "Wingtip",
      "resellerId": "3xxxxx"
    }
  ],
  "type": "Microsoft.Billing/billingAccounts/customers"
}

```

Utilize a propriedade `description` para identificar o revendedor que está associado à subscrição. Copie o `resellerId` para o revendedor. Por exemplo, para associar `Wingtip`, copie `3xxxxx`. Cole o valor algures para que possa utilizá-lo no passo seguinte.

---

## <a name="create-a-subscription-for-a-customer"></a>Criar uma subscrição para um cliente

O exemplo seguinte cria uma subscrição denominada *Subscrição da Equipa de Desenvolvimento* para a *Fabrikam Toys* e associa o revendedor *Wingtip* à subscrição. Utilizará o âmbito da faturação copiado do passo anterior: `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Corpo do pedido

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>Resposta

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

Transmita o *resellerId* opcional copiado no segundo passo no corpo do pedido da API.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para instalar a versão mais recente do módulo que contém o cmdlet `New-AzSubscriptionAlias`, execute `Install-Module Az.Subscription`. Para instalar uma versão recente do PowerShellGet, veja [Obter o Módulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Execute o comando [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) através do âmbito da faturação `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`. 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

Obtém o subscriptionId como parte da resposta do comando.

```json
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

Transmita o *resellerId* opcional copiado no segundo passo na chamada `New-AzSubscriptionAlias`.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, instale a extensão ao executar `az extension add --name account` e `az extension add --name alias`.

Execute o comando [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create). 

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
```

Obtém o subscriptionId como parte da resposta do comando.

```json
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

Transmita o *resellerId* opcional copiado no segundo passo na chamada `az account alias create`.

---

## <a name="use-arm-template"></a>Utilizar o modelo do Resource Manager

A secção anterior mostrou como criar uma subscrição com PowerShell, CLI ou REST API. Se precisar automatizar a criação de subscrições, considere utilizar um modelo de Gestor de Recursos Azure (modelo ARM).

O modelo a seguir cria uma subscrição. Para `billingScope` , fornecer a identificação do cliente. Para `targetManagementGroup` , forneça o grupo de gestão onde pretende criar a subscrição.

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
        "value": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
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

```azurepowershell
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx' targetManagementGroup=mg2
```

---

## <a name="next-steps"></a>Passos seguintes

* Agora que criou uma subscrição, pode conceder essa capacidade a outros entidades principais de utilizador e de serviço. Para obter mais informações, veja [Conceder acesso para criar subscrições Enterprise do Azure (pré-visualização)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerir grandes números de subscrições com grupos de gestão, veja [Organizar recursos com os grupos de gestão do Azure](../../governance/management-groups/overview.md).
