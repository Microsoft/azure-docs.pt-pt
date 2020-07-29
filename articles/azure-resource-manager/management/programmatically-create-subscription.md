---
title: Criar programáticamente subscrições do Azure
description: Saiba como criar subscrições adicionais do Azure programáticamente.
author: anuragdalmia
ms.topic: conceptual
ms.date: 07/09/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 20175e252d009620585e20cf76cdb634549b4f1d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323915"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Criar programáticamente subscrições Azure (pré-visualização)

Clientes Azure com um [Acordo Empresarial (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou Microsoft Partner Agreement [(MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) podem criar subscrições programáticamente. Neste artigo, aprende-se a criar subscrições programáticamente utilizando o Azure Resource Manager.

Quando cria uma subscrição Azure programáticamente, essa subscrição é regida pelo acordo ao abrigo do qual obteve serviços Azure da Microsoft ou de um revendedor autorizado. Para saber mais, consulte [a Microsoft Azure Legal Information](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Criar subscrições para uma conta de faturação EA

Utilize as informações nas seguintes secções para criar subscrições EA.

### <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma função de Proprietário numa Conta de Inscrição para criar uma subscrição. Há duas maneiras de conseguir o papel:

* O Administrador Empresarial da sua inscrição pode [torná-lo Proprietário de Conta](https://ea.azure.com/helpdocs/addNewAccount) (sinal em requerindo) o que o torna Proprietário da Conta de Inscrição.

* Um proprietário existente da Conta de Inscrição pode [conceder-lhe acesso.](grant-access-to-create-subscription.md) Da mesma forma, se pretender utilizar um principal de serviço para criar uma subscrição da EA, tem de [conceder a esse responsável de serviço a capacidade de criar subscrições.](grant-access-to-create-subscription.md)

### <a name="find-accounts-you-have-access-to"></a>Encontre contas a que tenha acesso

Depois de ser adicionado a uma Conta de Inscrição associada a um Proprietário de Conta, o Azure utiliza a relação conta-a-inscrição para determinar onde cobrar os encargos de subscrição. Todas as subscrições criadas na conta são faturadas para a inscrição da EA em que a conta está. Para criar subscrições, tem de passar em valores sobre a conta de inscrição e os principais utilizadores para possuir a subscrição.

Para executar os seguintes comandos, tem de fazer login no *diretório*de casa do Proprietário da Conta , que é o diretório em que as subscrições são criadas por padrão.

### <a name="rest"></a>[REST](#tab/rest)

Solicitação para listar todas as contas de inscrição a que tenha acesso:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

A resposta da API lista todas as contas de inscrição a que tem acesso:

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

Utilize a `principalName` propriedade para identificar a conta a que pretende que as subscrições sejam faturadas. Copie a `name` conta. Por exemplo, se quisesse criar subscrições na conta de SignUpEngineering@contoso.com inscrição, copiaria ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Este identificador é o iD do objeto da conta de inscrição. Cole este valor em algum lugar para que possa usá-lo no próximo passo como `enrollmentAccountObjectId` .

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Abra [a Azure Cloud Shell](https://shell.azure.com/) e selecione PowerShell.

Utilize o [Cmdlet Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de inscrição a que tenha acesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

A azure responde com uma lista de contas de inscrição a que tem acesso:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Utilize a `principalName` propriedade para identificar a conta a que pretende que as subscrições sejam faturadas. Copie a `ObjectId` conta. Por exemplo, se quisesse criar subscrições na conta de SignUpEngineering@contoso.com inscrição, copiaria ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Cole este objeto de identificação em algum lugar para que possa usá-lo no próximo passo como `enrollmentAccountObjectId` .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o comando [da lista de inscrições de faturação az](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de inscrição a que tenha acesso.

```azurecli-interactive
az billing enrollment-account list
```

A azure responde com uma lista de contas de inscrição a que tem acesso:

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

Utilize a `principalName` propriedade para identificar a conta a que pretende que as subscrições sejam faturadas. Copie a `name` conta. Por exemplo, se quisesse criar subscrições na conta de SignUpEngineering@contoso.com inscrição, copiaria ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Este identificador é o iD do objeto da conta de inscrição. Cole este valor em algum lugar para que possa usá-lo no próximo passo como `enrollmentAccountObjectId` .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Criar subscrições sob uma conta de inscrição específica

O exemplo a seguir cria uma subscrição *denominada Dev Team Subscription* na conta de inscrição selecionada no passo anterior. A oferta de subscrição é *MS-AZR-0017P* (Acordo Regular da Microsoft Enterprise Agreement). Também adiciona opcionalmente dois utilizadores como RBAC Owners para a subscrição.

### <a name="rest"></a>[REST](#tab/rest)

Faça o pedido a seguir ao substituir `<enrollmentAccountObjectId>` por `name`, copiado do primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se quiser especificar os proprietários, aprenda [a obter identificações de objetos de utilizador.](grant-access-to-create-subscription.md#userObjectId)

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
| `displayName` | Não      | Cadeia | O nome de exibição da subscrição. Se não for especificado, está definido para o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sim      | Cadeia | A oferta da assinatura. As duas opções para a EA são [a MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilização da produção) e [a MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/teste, precisa de ser [ligada através do portal EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `owners`      | Não       | Cadeia | O ID de objeto de qualquer utilizador que gostaria de adicionar como Um Proprietário RBAC na subscrição quando este for criado.  |

Na resposta, como parte do `Location` cabeçalho, recebes um url que podes consultar para obter o estado na operação de criação de subscrição. Quando a criação de subscrição estiver concluída, um GET on `Location` url devolverá um `subscriptionLink` objeto, que tem o ID de subscrição. Para mais detalhes, consulte a [documentação da API de subscrição](https://docs.microsoft.com/rest/api/subscription/)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para instalar a versão mais recente do módulo que contém o `New-AzSubscription` cmdlet, corra `Install-Module Az.Subscription` . Para instalar uma versão recente do PowerShellGet, consulte [get PowerShellGet Module](/powershell/scripting/gallery/installing-psget).

Executar o comando [New-AzSubscription](/powershell/module/az.subscription) abaixo, `<enrollmentAccountObjectId>` substituindo-o pelo `ObjectId` recolhido no primeiro passo ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` (). Se quiser especificar os proprietários, aprenda [a obter identificações de objetos de utilizador.](grant-access-to-create-subscription.md#userObjectId)

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome do Elemento  | Necessário | Tipo   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Não      | Cadeia | O nome de exibição da subscrição. Se não for especificado, está definido para o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sim      | Cadeia | A oferta da assinatura. As duas opções para a EA são [a MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilização da produção) e [a MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/teste, precisa de ser [ligada através do portal EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `EnrollmentAccountObjectId`      | Sim       | Cadeia | O ID do Objeto da conta de inscrição a que a subscrição é criada e faturada. Este valor é um GUID que `Get-AzEnrollmentAccount` obtém. |
| `OwnerObjectId`      | Não       | Cadeia | O ID de objeto de qualquer utilizador que gostaria de adicionar como Um Proprietário RBAC na subscrição quando este for criado.  |
| `OwnerSignInName`    | Não       | Cadeia | O endereço de e-mail de qualquer utilizador que gostaria de adicionar como Proprietário do RBAC na subscrição quando este for criado. Pode utilizar este parâmetro em vez de `OwnerObjectId` .|
| `OwnerApplicationId` | Não       | Cadeia | O ID de aplicação de qualquer diretor de serviço que gostaria de adicionar como Proprietário RBAC na subscrição quando for criado. Pode utilizar este parâmetro em vez de `OwnerObjectId` . Ao utilizar este parâmetro, o diretor de serviço deve ter [lido o acesso ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Para ver uma lista completa de todos os parâmetros, consulte [a Subscrição de New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription).


### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Em primeiro lugar, instale esta extensão de pré-visualização executando `az extension add --name subscription` .

Executar a [conta az criar](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) comando abaixo, substituindo-o `<enrollmentAccountObjectId>` `name` copiado no primeiro passo ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` (). Se quiser especificar os proprietários, aprenda [a obter identificações de objetos de utilizador.](grant-access-to-create-subscription.md#userObjectId)

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome do Elemento  | Necessário | Tipo   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Não      | Cadeia | O nome de exibição da subscrição. Se não for especificado, está definido para o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sim      | Cadeia | A oferta da assinatura. As duas opções para a EA são [a MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilização da produção) e [a MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/teste, precisa de ser [ligada através do portal EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `enrollment-account-object-id`      | Sim       | Cadeia | O ID do Objeto da conta de inscrição a que a subscrição é criada e faturada. Este valor é um GUID que `az billing enrollment-account list` obtém. |
| `owner-object-id`      | Não       | Cadeia | O ID de objeto de qualquer utilizador que gostaria de adicionar como Um Proprietário RBAC na subscrição quando este for criado.  |
| `owner-upn`    | Não       | Cadeia | O endereço de e-mail de qualquer utilizador que gostaria de adicionar como Proprietário do RBAC na subscrição quando este for criado. Pode utilizar este parâmetro em vez de `owner-object-id` .|
| `owner-spn` | Não       | Cadeia | O ID de aplicação de qualquer diretor de serviço que gostaria de adicionar como Proprietário RBAC na subscrição quando for criado. Pode utilizar este parâmetro em vez de `owner-object-id` . Ao utilizar este parâmetro, o diretor de serviço deve ter [lido o acesso ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Para ver uma lista completa de todos os parâmetros, consulte [a conta az criar](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da Azure Enterprise criação de subscrição API

- Apenas as subscrições da Azure Enterprise podem ser criadas usando esta API.
- Há um limite de 2000 assinaturas por conta de inscrição. Depois disso, mais subscrições da conta só podem ser criadas no portal Azure. Se quiser criar mais subscrições através da API, crie outra conta de inscrição.
- Os utilizadores que não são Proprietários de Contas, mas que foram adicionados a uma conta de inscrição via RBAC, não podem criar subscrições no portal Azure.
- Não pode selecionar o inquilino para a subscrição a ser criada. A subscrição é sempre criada no arrendatário da Conta Proprietário. Para mover a subscrição para um inquilino diferente, consulte [o inquilino de assinatura de mudança.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)


## <a name="create-subscriptions-for-an-mca-account"></a>Criar subscrições para uma conta MCA

### <a name="prerequisites"></a>Pré-requisitos

Deve ter uma função de criador de subscrição do proprietário, colaborador ou Azure numa secção de fatura ou papel de proprietário ou contribuinte num perfil de faturação ou numa conta de faturação para criar subscrições. Para obter mais informações, veja [Tarefas e funções da faturação da subscrição](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

O exemplo mostrado abaixo utiliza AS DE REST. Atualmente, o PowerShell e a CLI do Azure não são suportados.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Encontre contas de faturação a que tenha acesso

Faça o pedido abaixo para listar todas as contas de faturação.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
A resposta da API lista as contas de faturação a que tem acesso.

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
Utilize a `displayName` propriedade para identificar a conta de faturação para a qual pretende criar subscrições. Certifique-se de que o acordoType da conta é *MicrosoftCustomerAgreement*. Copiar a `name` conta.  Por exemplo, se quiser criar uma subscrição para `Contoso` a conta de faturação, copiaria `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` . Cole este valor algures para que possa utilizá-lo no próximo passo.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Encontre secções de fatura para criar subscrições

Os encargos para a sua subscrição aparecem numa secção da fatura de um perfil de faturação. Utilize a seguinte API para obter a lista de secções de fatura e perfis de faturação nos quais tem permissão para criar subscrições Azure.

Faça o pedido a seguir ao substituir `<billingAccountName>` por `name`, copiado do primeiro passo (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
A resposta da API lista todas as secções de fatura e os seus perfis de faturação nos quais tem acesso para criar subscrições:

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

Utilize o `invoiceSectionDisplayName` imóvel para identificar a secção de faturas para a qual pretende criar subscrições. Copie o `invoiceSectionId` , e um dos para a secção de `billingProfileId` `skuId` fatura. Por exemplo, se quiser criar uma subscrição de tipo para a secção de `Microsoft Azure plan` `Development` faturas, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX` `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` copiaria, e `0001` . Cole estes valores em algum lugar para que possa usá-los no próximo passo.

### <a name="create-a-subscription-for-an-invoice-section"></a>Criar uma subscrição para uma secção de fatura

O exemplo a seguir cria uma subscrição chamada *Dev Team do* tipo Microsoft *Azure Plan* para a secção de *faturas de desenvolvimento.* A subscrição será faturada para o perfil de faturação *da Ultimao finance* e aparecerá na secção *desenvolvimento* da sua fatura.

Faça o seguinte pedido, substituindo `<invoiceSectionId>` o `invoiceSectionId` copiado a partir do segundo passo ( ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX``` ). Precisa passar o `billingProfileId` e `skuId` copiado a partir do segundo passo nos parâmetros de pedido da API. Se quiser especificar os proprietários, aprenda [a obter identificações de objetos de utilizador.](grant-access-to-create-subscription.md#userObjectId)

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
| `displayName` | Sim      | Cadeia | O nome de exibição da subscrição.|
| `billingProfileId`   | Sim      | Cadeia | A identificação do perfil de faturação que será cobrado para as despesas da subscrição.  |
| `skuId` | Sim      | Cadeia | A identificação do sku que determina o tipo de plano Azure. |
| `owners`      | Não       | Cadeia | O ID de objeto de qualquer utilizador ou principal de serviço que gostaria de adicionar como Proprietário RBAC na subscrição quando este for criado.  |
| `costCenter` | Não      | Cadeia | O centro de custos associado à subscrição. Aparece no ficheiro csv de utilização. |
| `managementGroupId` | Não      | Cadeia | O ID do grupo de gestão ao qual a subscrição será adicionada. Para obter a lista de grupos de gestão, consulte [Grupos de Gestão - Lista API](/rest/api/resources/managementgroups/list). Utilize a identificação de um grupo de gestão da API. |

Na resposta, recebes um `subscriptionCreationResult` objeto para monitorização. Quando a criação de subscrição estiver concluída, o `subscriptionCreationResult` objeto devolveria um `subscriptionLink` objeto, que tem o ID de subscrição.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Criar subscrições para uma conta de faturação MPA

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter um papel de Administrador Global ou Agente Admin na conta de provedor de solução cloud da sua organização para criar subscrição para a sua conta de faturação. Para obter mais informações, consulte [Partner Center - Atribua as funções e permissões dos utilizadores.](/partner-center/permissions-overview)

O exemplo mostrado abaixo utiliza AS DE REST. Atualmente, o PowerShell e a CLI do Azure não são suportados.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Encontre as contas de faturação a que tem acesso

Faça o pedido abaixo para listar todas as contas de faturação a que tenha acesso.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
A lista de resposta da API às contas de faturação.

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
Utilize a `displayName` propriedade para identificar a conta de faturação para a qual pretende criar subscrições. Certifique-se de que otipo da conta é *MicrosoftPartnerAgreement*. Copie a `name` conta. Por exemplo, se quiser criar uma subscrição para `Contoso` a conta de faturação, copiaria `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` . Cole este valor algures para que possa utilizá-lo no próximo passo.

### <a name="find-customers-that-have-azure-plans"></a>Encontre clientes que tenham planos Azure

Faça o seguinte pedido, substituindo `<billingAccountName>` o `name` copiado do primeiro passo ( ```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx``` ) para listar todos os clientes na conta de faturação para os quais pode criar subscrições Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
A resposta da API lista os clientes na conta de faturação com os planos Azure. Pode criar subscrições para estes clientes.

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

Utilize o `displayName` imóvel para identificar o cliente para o qual pretende criar subscrições. Copie o `id` para o cliente. Por exemplo, se quiser criar uma subscrição `Fabrikam toys` para, copiaria `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Cole este valor em algum lugar para usá-lo nos passos seguintes.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcional para fornecedores indiretos: Obtenha os revendedores para um cliente

Se for um fornecedor indireto no modelo de dois níveis da CSP, pode especificar um revendedor enquanto cria subscrições para os clientes.

Faça o seguinte pedido, substituindo `<customerId>` o `id` copiado do segundo passo ( ```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ) para listar todos os revendedores que estão disponíveis para um cliente.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
A resposta da API lista os revendedores para o cliente:

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
Utilize a `description` propriedade para identificar o revendedor que estará associado à subscrição. Copie o `resellerId` para o revendedor. Por exemplo, se quiser `Wingtip` associar-se, copiará `3xxxxx` . Cole este valor algures para que possa utilizá-lo no próximo passo.

### <a name="create-a-subscription-for-a-customer"></a>Criar uma subscrição para um cliente

O exemplo a seguir cria uma subscrição chamada *Dev Team* para *brinquedos Fabrikam* e associar o revendedor *Wingtip* à subscrição. T

Faça o seguinte pedido, substituindo `<customerId>` o `id` copiado a partir do segundo passo ( ```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Passe o *revendedor* opcional Copiado a partir do segundo passo nos parâmetros de pedido da API.

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
| `displayName` | Sim      | Cadeia | O nome de exibição da subscrição.|
| `skuId` | Sim      | Cadeia | A identificação do plano Azure. Utilize *0001* para subscrições do tipo Microsoft Azure Plan |
| `resellerId`      | Não       | Cadeia | O ID MPN do revendedor que será associado à subscrição.  |

Na resposta, recebes um `subscriptionCreationResult` objeto para monitorização. Quando a criação de subscrição estiver concluída, o `subscriptionCreationResult` objeto devolveria um `subscriptionLink` objeto, que tem o ID de subscrição.

## <a name="next-steps"></a>Passos seguintes

* Para um exemplo sobre a criação de uma subscrição do Enterprise Agreement (EA) utilizando .NET, consulte [o código de amostra no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Agora que criou uma subscrição, pode conceder essa capacidade a outros utilizadores e diretores de serviços. Para obter mais informações, consulte [o acesso do Grant para criar subscrições da Azure Enterprise (pré-visualização)](grant-access-to-create-subscription.md).
* Para saber mais sobre a gestão de um grande número de subscrições utilizando grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure](../../governance/management-groups/overview.md)
