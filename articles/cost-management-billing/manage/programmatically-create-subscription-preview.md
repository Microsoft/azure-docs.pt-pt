---
title: Criar subscrições do Azure através de programação com APIs de pré-visualização
description: Saiba como criar subscrições do Azure adicionais através de programação com versões de pré-visualização da API REST, da CLI do Azure e do Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 68d890386d53b4115c773b128f8678bac9579e53
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844340"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Criar subscrições do Azure através de programação com APIs de pré-visualização

Este artigo ajuda-o a criar subscrições do Azure através de programação com a nossa API de pré-visualização mais antiga. Neste artigo, aprenderá a criar subscrições através de programação com o Azure Resource Manager.

Temos artigos novos para a última versão da API para utilização com diferentes tipos de subscrições de contrato do Azure:

- [Criar subscrições do EA através de programação com a API mais recente](programmatically-create-subscription-enterprise-agreement.md)
- [Criar subscrições do MCA através de programação com a API mais recente](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Criar subscrições do MPA através de programação com a API mais recente](Programmatically-create-subscription-microsoft-customer-agreement.md)

Contudo, caso não queira utilizar a versão mais recente, pode continuar a utilizar as informações neste artigo.

Os clientes do Azure com uma conta de faturação para os seguintes tipos de contrato podem criar subscrições através de programação:

- Contrato Enterprise
- Contrato de Cliente da Microsoft (MCA)
- Contrato de Parceiro da Microsoft (MPA)

Quando cria uma subscrição do Azure através de programação, a subscrição rege-se pelo contrato ao abrigo do qual adquiriu serviços do Azure junto da Microsoft ou de um revendedor autorizado. Para obter mais informações, veja [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Criar subscrições para uma conta de faturação de EA

Use as informações nas secções seguintes para criar subscrições de EA.

### <a name="prerequisites"></a>Pré-requisitos

Deve ter uma função de Proprietário numa Conta de Inscrição para criar uma subscrição. Existem duas formas de obter a função:

* O Administrador Enterprise da sua inscrição pode [torná-lo num Proprietário de Conta](https://ea.azure.com/helpdocs/addNewAccount) e tornar o início de sessão obrigatório, o que faz de si um Proprietário da Conta de Inscrição.
* Um Proprietário existente da Conta de Inscrição pode [conceder-lhe acesso](grant-access-to-create-subscription.md). Da mesma forma, para utilizar um principal de serviço para criar uma subscrição de EA, tem de [conceder a esse principal de serviço a capacidade de criar subscrições](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Localizar contas às quais tem acesso

Depois de ser adicionado a uma Conta de Inscrição associada a um Proprietário de Conta, o Azure usa a relação de conta para inscrição para determinar onde faturar os custos da subscrição. Todas as subscrições criadas no âmbito da conta são faturadas na inscrição de EA em que a conta está. Para criar subscrições, deve transmitir valores sobre a conta de inscrição e as entidades principais de utilizador para ser proprietário da subscrição.

Para executar os comandos seguintes, tem de ter sessão iniciada no *diretório raiz* do Proprietário da Conta, que é o diretório no qual as subscrições são criadas por predefinição.

### <a name="rest"></a>[REST](#tab/rest)

Pedido para listar todas as contas de inscrição às quais tem acesso:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

A resposta da API lista todas as contas de inscrição às quais tem acesso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Use a propriedade `principalName` para identificar a conta na qual pretende que as subscrições sejam faturadas. Copie o `name` dessa conta. Por exemplo, para criar subscrições na SignUpEngineering@contoso.com conta de inscrição, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. O identificador é o ID de objeto da conta de inscrição. Cole o valor algures para que possa utilizá-lo no passo seguinte como `enrollmentAccountObjectId`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Abra o [Azure Cloud Shell](https://shell.azure.com/) e selecione PowerShell.

Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de inscrição às quais tem acesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

O Azure responde com uma lista de contas de inscrição às quais tem acesso:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Use a propriedade `principalName` para identificar a conta na qual pretende que as subscrições sejam faturadas. Copie o `ObjectId` dessa conta. Por exemplo, para criar subscrições na SignUpEngineering@contoso.com conta de inscrição, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole o ID de objeto algures para que possa utilizá-lo no passo seguinte como `enrollmentAccountObjectId`.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [az billing enrollment-account list](/cli/azure/billing) para listar todas as contas de inscrição às quais tem acesso.

```azurecli-interactive
az billing enrollment-account list
```

O Azure responde com uma lista de contas de inscrição às quais tem acesso:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Use a propriedade `principalName` para identificar a conta na qual pretende que as subscrições sejam faturadas. Copie o `name` dessa conta. Por exemplo, para criar subscrições na SignUpEngineering@contoso.com conta de inscrição, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. O identificador é o ID de objeto da conta de inscrição. Cole o valor algures para que possa utilizá-lo no passo seguinte como `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Criar subscrições numa conta de registo específica

O exemplo a seguir cria uma subscrição denominada *Subscrição da Equipa de Desenvolvimento* na conta de inscrição selecionada no passo anterior. A oferta da subscrição é *MS-AZR-0017P* (Microsoft Enterprise Agreement regular). Também adiciona, opcionalmente, dois utilizadores como Proprietários RBAC do Azure à subscrição.

### <a name="rest"></a>[REST](#tab/rest)

Faça o pedido a seguir ao substituir `<enrollmentAccountObjectId>` por `name`, copiado do primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Para especificar proprietários, veja [como obter IDs de objeto de utilizador](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nome do Elemento  | Necessário | Tipo   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Não      | String | O nome a apresentar da subscrição. Se não for especificado, será definido como o nome da oferta, como “Microsoft Azure Enterprise”.                                 |
| `offerType`   | Sim      | String | A oferta da subscrição. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, tem de ser [ativado no portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Não       | String | O ID de Objeto de qualquer utilizador que queira adicionar como Proprietário de RBAC do Azure na subscrição quando esta é criada.  |

Na resposta, como parte da `Location` do cabeçalho, obtém um url que pode consultar para saber o estado na operação de criação da subscrição. Quando a criação da subscrição estiver concluída, um GET no url `Location` devolverá um objeto `subscriptionLink`, que tem o ID da subscrição. Para obter mais detalhes, veja a [Documentação da API da subscrição](/rest/api/subscription/)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para instalar a versão mais recente do módulo que contém o cmdlet `New-AzSubscription`, execute `Install-Module Az.Subscription`. Para instalar uma versão recente do PowerShellGet, veja [Obter o Módulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Execute o comando [New-AzSubscription](/powershell/module/az.subscription) abaixo, substituindo `<enrollmentAccountObjectId>` pelo `ObjectId` obtido no primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Para especificar proprietários, veja [como obter IDs de objeto de utilizador](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome do Elemento  | Necessário | Tipo   | Descrição |
|---------------|----------|--------|----|
| `Name` | Não      | String | O nome a apresentar da subscrição. Se não for especificado, será definido como o nome da oferta, como *Microsoft Azure Enterprise*. |
| `OfferType`   | Sim      | String | A oferta de subscrição. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, tem de ser [ativado no portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sim       | String | O ID de Objeto da conta de inscrição na qual a subscrição é criada e faturada. O valor é um GUID que obtém de `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Não       | String | O ID de Objeto de qualquer utilizador a adicionar como Proprietário de RBAC do Azure na subscrição quando esta é criada.  |
| `OwnerSignInName`    | Não       | String | O endereço de e-mail de qualquer utilizador a adicionar como Proprietário de RBAC do Azure na subscrição quando esta é criada. Pode utilizar o parâmetro em vez de `OwnerObjectId`.|
| `OwnerApplicationId` | Não       | String | O ID da aplicação de qualquer principal de serviço a adicionar como Proprietário de RBAC do Azure na subscrição quando esta é criada. Pode utilizar o parâmetro em vez de `OwnerObjectId`. Quando utilizar o parâmetro, o principal de serviço tem de ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Para ver uma lista completa de todos os parâmetros, veja [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, instale a extensão de pré-visualização ao executar `az extension add --name subscription`.

Execute o comando [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true) abaixo, substituindo `<enrollmentAccountObjectId>` pelo `name` que copiou no primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Para especificar proprietários, veja [como obter IDs de objeto de utilizador](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome do Elemento  | Necessário | Tipo   | Descrição |
|---------------|----------|--------|------------|
| `display-name` | Não      | String | O nome a apresentar da subscrição. Se não for especificado, será definido como o nome da oferta, como *Microsoft Azure Enterprise*.|
| `offer-type`   | Sim      | String | A oferta da subscrição. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, tem de ser [ativado no portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sim       | String | O ID de Objeto da conta de inscrição na qual a subscrição é criada e faturada. O valor é um GUID que obtém de `az billing enrollment-account list`. |
| `owner-object-id`      | Não       | String | O ID de Objeto de qualquer utilizador a adicionar como Proprietário de RBAC do Azure na subscrição quando esta é criada.  |
| `owner-upn`    | Não       | String | O endereço de e-mail de qualquer utilizador a adicionar como Proprietário de RBAC do Azure na subscrição quando esta é criada. Pode utilizar o parâmetro em vez de `owner-object-id`.|
| `owner-spn` | Não       | String | O ID da aplicação de qualquer principal de serviço a adicionar como Proprietário de RBAC do Azure na subscrição quando esta é criada. Pode utilizar o parâmetro em vez de `owner-object-id`. Quando utilizar o parâmetro, o principal de serviço tem de ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Para ver uma lista completa de todos os parâmetros, veja [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da API de criação de subscrições do Azure Enterprise

- Apenas as subscrições do Azure Enterprise podem ser criadas com esta API.
- Há um limite de 2000 subscrições por conta de inscrição. Depois disso, só podem ser criadas mais subscrições para a conta no portal do Azure. Se quiser criar mais subscrições através da API, crie outra conta de inscrição. As subscrições canceladas, eliminadas e transferidas contam para o limite de 2000.
- Os utilizadores que não sejam Proprietários de Conta, mas que tenham sido adicionados a uma conta de inscrição com o RBAC do Azure, não podem criar subscrições no portal do Azure.
- Não pode selecionar o inquilino onde a subscrição vai ser criada. A subscrição é sempre criada no inquilino principal do Proprietário da Conta. Para mover a assinatura para um inquilino diferente, veja [alterar inquilino de subscrição](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Criar subscrições para uma conta MCA

Utilize as informações nas secções seguintes para criar subscrições para uma conta MCA.

### <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma função de proprietário, contribuidor ou criador de subscrição do Azure numa secção da fatura ou uma função de proprietário ou contribuidor num perfil de faturação ou numa conta de faturação para criar subscrições. Para obter mais informações, veja [Tarefas e funções da faturação da subscrição](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Os exemplos que se seguem utilizam APIs REST. Atualmente, o PowerShell e a CLI do Azure não são suportados.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Localizar contas de faturação a que tem acesso

Faça o pedido seguinte para listar todas as contas de faturação.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
A resposta da API lista as contas de faturação às quais tem acesso.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use a propriedade `displayName` para identificar a conta de faturação para a qual pretende criar subscrições. Certifique-se de que o agreementType da conta é *MicrosoftCustomerAgreement*. Copie o `name` da conta.  Por exemplo, para criar uma subscrição para a conta de faturação `Contoso`, copie `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole o valor algures para que possa utilizá-lo no passo seguinte.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Localizar secções da fatura para criar subscrições

Os custos da subscrição aparecem numa secção da fatura de um perfil de faturação. Use a API seguinte para obter a lista de secções da fatura e perfis de faturação nos quais tem permissão para criar subscrições do Azure.

Faça o pedido a seguir ao substituir `<billingAccountName>` por `name`, copiado do primeiro passo (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

A resposta da API lista todas as secções das faturas e respetivos perfis de faturação nos quais tem acesso para criar subscrições:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Use a propriedade `invoiceSectionDisplayName` para identificar a secção da fatura para a qual pretende criar subscrições. Copie `invoiceSectionId`, `billingProfileId` e um dos `skuId` para a secção da fatura. Por exemplo, para criar uma subscrição do tipo `Microsoft Azure plan` para a secção `Development` da fatura, copie `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` e `0001`. Cole os valores algures para que possa utilizá-los no passo seguinte.

### <a name="create-a-subscription-for-an-invoice-section"></a>Criar uma subscrição para uma secção da fatura

O exemplo seguinte cria uma subscrição denominada *Subscrição da Equipa de Desenvolvimento* do tipo *Plano do Microsoft Azure* para a secção *Desenvolvimento* da fatura. A subscrição é faturada no perfil de faturação do *departamento financeiro da Contoso* e apresentada na secção *Desenvolvimento* desta fatura.

Faça o pedido seguinte ao substituir `<invoiceSectionId>` pelo `invoiceSectionId` copiado do segundo passo (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Transmita o `billingProfileId` e o `skuId` copiados do segundo passo nos parâmetros de pedido da API. Para especificar proprietários, veja [como obter IDs de objeto de utilizador](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Nome do Elemento  | Necessário | Tipo   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sim      | String | O nome a apresentar da subscrição.|
| `billingProfileId`   | Sim      | String | O ID do perfil de faturação ao qual são cobrados os custos da subscrição.  |
| `skuId` | Sim      | String | O ID da SKU que determina o tipo de plano do Azure. |
| `owners`      | Não       | String | O ID de Objeto de qualquer utilizador ou principal de serviço a adicionar como Proprietário de RBAC do Azure na subscrição quando esta é criada.  |
| `costCenter` | Não      | String | O centro de custos associado à subscrição. Este aparece no ficheiro CSV de utilização. |
| `managementGroupId` | Não      | String | O ID do grupo de gestão ao qual a subscrição será adicionada. Para obter a lista de grupos de gestão, veja [Grupos de Gestão – API de Lista](/rest/api/resources/managementgroups/list). Use o ID de um grupo de gestão a partir da API. |

Na resposta, recebe um objeto `subscriptionCreationResult` para monitorização. Quando a criação da subscrição estiver concluída, o objeto `subscriptionCreationResult` devolve um objeto `subscriptionLink`, o qual tem o ID da subscrição.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Criar subscrições para uma conta de faturação MPA

Utilize as informações nas secções seguintes para criar subscrições para uma conta de faturação MPA.

### <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma função de Administrador Global ou Agente de Administração na conta do Fornecedor de Soluções Cloud da sua organização para criar subscrições para a sua conta de faturação. Para obter mais informações, veja [Partner Center - Assign users roles and permissions](/partner-center/permissions-overview) (Centro de Parceiros – Atribuir funções e permissões de utilizadores).

Os exemplos que se seguem utilizam APIs REST. Atualmente, o PowerShell e a CLI do Azure não são suportados.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Localizar as contas de faturação às quais tem acesso

Faça o pedido abaixo para listar todas as contas de faturação às quais tem acesso.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

A resposta da API lista as contas de faturação.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use a propriedade `displayName` para identificar a conta de faturação para a qual pretende criar subscrições. Certifique-se de que o agreementType da conta é *MicrosoftPartnerAgreement*. Copie o `name` para a conta. Por exemplo, para criar uma subscrição para a conta de faturação `Contoso`, copie `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole o valor algures para que possa utilizá-lo no passo seguinte.

### <a name="find-customers-that-have-azure-plans"></a>Localizar clientes que têm planos do Azure

Faça o pedido seguinte, substituindo `<billingAccountName>` pelo `name` copiado no primeiro passo (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) para listar todos os clientes na conta de faturação para quem pode criar subscrições do Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
A resposta da API lista os clientes na conta de faturação com planos do Azure. Pode criar subscrições para os clientes.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Use a propriedade `displayName` para identificar o cliente para o qual pretende criar subscrições. Copie o `id` para o cliente. Por exemplo, para criar uma subscrição para `Fabrikam toys`, copie `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Cole o valor algures para utilizá-lo em passos posteriores.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcional para fornecedores Indiretos: Consiga os revendedores para um cliente

Se for um Fornecedor indireto no modelo de duas camadas do CSP, pode especificar um revendedor quando criar subscrições para os clientes.

Faça o pedido seguinte, substituindo `<customerId>` pelo `id` copiado no segundo passo (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) para listar todos os revendedores disponíveis para um cliente.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
A resposta da API lista os revendedores do cliente:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```

Utilize a propriedade `description` para identificar o revendedor a associar à subscrição. Copie o `resellerId` para o revendedor. Por exemplo, para associar `Wingtip`, copie `3xxxxx`. Cole o valor algures para que possa utilizá-lo no passo seguinte.

### <a name="create-a-subscription-for-a-customer"></a>Criar uma subscrição para um cliente

O exemplo seguinte cria uma subscrição denominada *Subscrição da Equipa de Desenvolvimento* para a *Fabrikam Toys* e associa o revendedor *Wingtip* à subscrição. 

Faça o pedido seguinte ao substituir `<customerId>` pelo `id` copiado do segundo passo (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Transmita o *resellerId* opcional copiado do segundo passo nos parâmetros de pedido da API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nome do Elemento  | Necessário | Tipo   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sim      | String | O nome a apresentar da subscrição.|
| `skuId` | Sim      | String | O ID da SKU do plano do Azure. Utilizar *0001* para subscrições do tipo Plano do Microsoft Azure |
| `resellerId`      | Não       | String | O ID da MPN do revendedor que será associado à subscrição.  |

Na resposta, recebe um objeto `subscriptionCreationResult` para monitorização. Quando a criação da subscrição estiver concluída, o objeto `subscriptionCreationResult` devolve um objeto `subscriptionLink`. Este tem o ID da subscrição.

## <a name="next-steps"></a>Passos seguintes

- Para ver um exemplo de como criar uma subscrição de Contrato Enterprise (EA) com o .NET, veja [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Agora que criou uma subscrição, pode conceder essa capacidade a outros entidades principais de utilizador e de serviço. Para obter mais informações, veja [Conceder acesso para criar subscrições Enterprise do Azure (pré-visualização)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerir grandes números de subscrições com grupos de gestão, veja [Organizar recursos com os grupos de gestão do Azure](../../governance/management-groups/overview.md).
