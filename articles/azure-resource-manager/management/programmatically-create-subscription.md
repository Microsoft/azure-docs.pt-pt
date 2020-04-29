---
title: Criar programáticamente subscrições do Azure
description: Saiba como criar subscrições adicionais do Azure programáticamente.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460403"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Criar programáticamente subscrições do Azure (pré-visualização)

Os clientes Azure com um [Acordo Empresarial (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) contas de faturação podem criar subscrições programáticamente. Neste artigo, aprende-se a criar subscrições programáticamente utilizando o Gestor de Recursos Do Azure.

Quando cria uma subscrição Azure programáticamente, essa subscrição rege-se pelo acordo ao abrigo do qual obteve serviços Azure da Microsoft ou de um revendedor autorizado. Para saber mais, consulte a [Microsoft Azure Legal Information](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Criar subscrições para uma conta de faturação eA

Utilize as informações nas seguintes secções para criar subscrições de EA.

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter um papel proprietário em uma Conta de Inscrição para criar uma subscrição. Há duas maneiras de conseguir o papel:

* O Administrador Da Empresa da sua inscrição pode [fazer-lhe um Proprietário](https://ea.azure.com/helpdocs/addNewAccount) de Conta (inscreva-se obrigatório) o que o torna proprietário da Conta de Inscrição.

* Um proprietário existente da Conta de Inscrição pode [conceder-lhe acesso](grant-access-to-create-subscription.md). Da mesma forma, se pretender utilizar um diretor de serviço para criar uma subscrição EA, deve conceder a esse serviço principal a capacidade de [criar subscrições.](grant-access-to-create-subscription.md)

### <a name="find-accounts-you-have-access-to"></a>Encontre contas a que tem acesso

Depois de ser adicionado a uma Conta de Inscrição associada a um Proprietário de Conta, o Azure usa a relação conta-a-inscrição para determinar onde cobrar os encargos de subscrição. Todas as subscrições criadas na conta são faturadas para a inscrição da EA em que a conta se encontra. Para criar subscrições, tem de passar em valores sobre a conta de inscrição e os diretores de utilizador para possuir a subscrição.

Para executar os seguintes comandos, deve ser registado no *diretório*do Proprietário da Conta , que é o diretório em que as subscrições são criadas por padrão.

### <a name="rest"></a>[REST](#tab/rest)

Pedido para listar todas as contas de inscrição a que tem acesso:

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

Utilize `principalName` a propriedade para identificar a conta a que pretende que as subscrições sejam faturadas. Copie `name` a conta. Por exemplo, se quisesse criar subscrições na conta SignUpEngineering@contoso.com de ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```inscrição, copiaria. Este identificador é o objeto de identificação da conta de inscrição. Colar este valor em algum lugar para que possa `enrollmentAccountObjectId`usá-lo no próximo passo como .

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Abra a [Cloud Shell Azure](https://shell.azure.com/) e selecione PowerShell.

Utilize o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de inscrição a que tem acesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

O Azure responde com uma lista de contas de inscrição a que tem acesso:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Utilize `principalName` a propriedade para identificar a conta a que pretende que as subscrições sejam faturadas. Copie `ObjectId` a conta. Por exemplo, se quisesse criar subscrições na conta SignUpEngineering@contoso.com de ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```inscrição, copiaria. Repasse esta identificação do objeto em algum lugar para `enrollmentAccountObjectId`que possa usá-lo no próximo passo como .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o comando da [lista de listas de inscrição az](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de inscrição a que tem acesso.

```azurecli-interactive
az billing enrollment-account list
```

O Azure responde com uma lista de contas de inscrição a que tem acesso:

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

Utilize `principalName` a propriedade para identificar a conta a que pretende que as subscrições sejam faturadas. Copie `name` a conta. Por exemplo, se quisesse criar subscrições na conta SignUpEngineering@contoso.com de ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```inscrição, copiaria. Este identificador é o objeto de identificação da conta de inscrição. Colar este valor em algum lugar para que possa `enrollmentAccountObjectId`usá-lo no próximo passo como .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Criar subscrições sob uma conta de inscrição específica

O exemplo seguinte cria uma subscrição chamada *Dev Team Subscription* na conta de inscrição selecionada na etapa anterior. A oferta de subscrição é *MS-AZR-0017P* (acordo regular da Microsoft Enterprise Agreement). Também opcionalmente adiciona dois utilizadores como Proprietários RBAC para a subscrição.

### <a name="rest"></a>[REST](#tab/rest)

Faça o pedido a seguir ao substituir `<enrollmentAccountObjectId>` por `name`, copiado do primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se quiser especificar os proprietários, aprenda [a obter iDs](grant-access-to-create-subscription.md#userObjectId)de objetos de utilizador .

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
| `displayName` | Não      | String | O nome de exibição da subscrição. Se não for especificado, está definido para o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sim      | String | A oferta da subscrição. As duas opções para a EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilização de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/teste, precisa de ser [ativada utilizando o portal EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `owners`      | Não       | String | O Id do Objeto de qualquer utilizador que deseja adicionar como Proprietário RBAC na subscrição quando for criado.  |

Na resposta, recebes `subscriptionOperation` um objeto para monitorização. Quando a criação de `subscriptionOperation` subscrição `subscriptionLink` estiver concluída, o objeto devolveria um objeto, que tem o ID de subscrição.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Em primeiro lugar, instale este módulo de pré-visualização executando `Install-Module Az.Subscription -AllowPrerelease`. Para se `-AllowPrerelease` certificar de que funciona, instale uma versão recente do PowerShellGet do [Módulo Get PowerShellGet](/powershell/scripting/gallery/installing-psget).

Executar o comando [New-AzSubscription](/powershell/module/az.subscription) `<enrollmentAccountObjectId>` abaixo, `ObjectId` substituindo pelo```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```recolhido no primeiro passo ( ). Se quiser especificar os proprietários, aprenda [a obter iDs](grant-access-to-create-subscription.md#userObjectId)de objetos de utilizador .

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome do Elemento  | Necessário | Tipo   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Não      | String | O nome de exibição da subscrição. Se não for especificado, está definido para o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sim      | String | A oferta da subscrição. As duas opções para a EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilização de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/teste, precisa de ser [ativada utilizando o portal EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `EnrollmentAccountObjectId`      | Sim       | String | O Id do Objeto da conta de inscrição a que a subscrição é criada e faturada. Este valor é um GUID `Get-AzEnrollmentAccount`de que obtém. |
| `OwnerObjectId`      | Não       | String | O Id do Objeto de qualquer utilizador que deseja adicionar como Proprietário RBAC na subscrição quando for criado.  |
| `OwnerSignInName`    | Não       | String | O endereço de e-mail de qualquer utilizador que deseja adicionar como Proprietário RBAC na subscrição quando for criado. Pode utilizar este parâmetro `OwnerObjectId`em vez de .|
| `OwnerApplicationId` | Não       | String | O ID de aplicação de qualquer diretor de serviço que gostaria de adicionar como Proprietário RBAC na subscrição quando for criado. Pode utilizar este parâmetro `OwnerObjectId`em vez de . Ao utilizar este parâmetro, o diretor de serviço deve ter [lido o acesso ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Para ver uma lista completa de todos os parâmetros, consulte [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Em primeiro lugar, instale esta extensão de pré-visualização executando `az extension add --name subscription`.

Executar a [conta az criar](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) `<enrollmentAccountObjectId>` comando `name` abaixo, substituindo-o```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```pelo copiado no primeiro passo ( ). Se quiser especificar os proprietários, aprenda [a obter iDs](grant-access-to-create-subscription.md#userObjectId)de objetos de utilizador .

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome do Elemento  | Necessário | Tipo   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Não      | String | O nome de exibição da subscrição. Se não for especificado, está definido para o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sim      | String | A oferta da subscrição. As duas opções para a EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilização de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/teste, precisa de ser [ativada utilizando o portal EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `enrollment-account-object-id`      | Sim       | String | O Id do Objeto da conta de inscrição a que a subscrição é criada e faturada. Este valor é um GUID `az billing enrollment-account list`de que obtém. |
| `owner-object-id`      | Não       | String | O Id do Objeto de qualquer utilizador que deseja adicionar como Proprietário RBAC na subscrição quando for criado.  |
| `owner-upn`    | Não       | String | O endereço de e-mail de qualquer utilizador que deseja adicionar como Proprietário RBAC na subscrição quando for criado. Pode utilizar este parâmetro `owner-object-id`em vez de .|
| `owner-spn` | Não       | String | O ID de aplicação de qualquer diretor de serviço que gostaria de adicionar como Proprietário RBAC na subscrição quando for criado. Pode utilizar este parâmetro `owner-object-id`em vez de . Ao utilizar este parâmetro, o diretor de serviço deve ter [lido o acesso ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Para ver uma lista completa de todos os parâmetros, consulte a [az account criar](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da criação de subscrição da Azure Enterprise API

- Apenas as subscrições da Azure Enterprise podem ser criadas usando esta API.
- Há um limite de 500 assinaturas por conta de inscrição. Depois disso, mais subscrições para a conta só podem ser criadas no portal Azure. Se quiser criar mais subscrições através da API, crie outra conta de inscrição.
- Os utilizadores que não são Proprietários de Conta, mas foram adicionados a uma conta de inscrição via RBAC, não podem criar subscrições no portal Azure.
- Não é possível selecionar o inquilino para a subscrição ser criada. A subscrição é sempre criada no arrendatário da Conta Proprietário. Para mover a subscrição para um inquilino diferente, consulte [alterar o inquilino de subscrição.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)


## <a name="create-subscriptions-for-an-mca-account"></a>Criar subscrições para uma conta MCA

### <a name="prerequisites"></a>Pré-requisitos

Deve ter um papel de criador de subscrição do proprietário, colaborador ou Azure numa secção de faturaou ou papel de proprietário ou colaborador num perfil de faturação ou numa conta de faturação para criar subscrições. Para obter mais informações, veja [Tarefas e funções da faturação da subscrição](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

O exemplo abaixo indicado utiliza as APIs REST. Atualmente, o PowerShell e a CLI do Azure não são suportados.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Encontre contas de faturação a que tem acesso

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
Utilize `displayName` a propriedade para identificar a conta de faturação para a qual pretende criar subscrições. Certifique-se de que o acordoTipo da conta é *o MicrosoftCustomerAgreement*. Copie `name` a conta.  Por exemplo, se quiser criar uma `Contoso` subscrição para a `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`conta de faturação, copiaria. Cole este valor algures para que possa utilizá-lo no próximo passo.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Encontre secções de faturas para criar subscrições

As tarifas para a sua subscrição aparecem numa secção da fatura de um perfil de faturação. Utilize a Seguinte API para obter a lista de secções de faturação e perfis de faturação em que tem permissão para criar subscrições do Azure.

Faça o pedido a seguir ao substituir `<billingAccountName>` por `name`, copiado do primeiro passo (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
A resposta da API lista todas as secções de faturação e os seus perfis de faturação nos quais tem acesso para criar subscrições:

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

Utilize `invoiceSectionDisplayName` a propriedade para identificar a secção de faturas para a qual pretende criar subscrições. Copie `invoiceSectionId` `billingProfileId` o e `skuId` um dos para a secção de fatura. Por exemplo, se quiser criar uma `Microsoft Azure plan` `Development` subscrição de tipo para `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX` `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` secção `0001`de faturas, copiaria, e . Cola estes valores em algum lugar para que possa usá-los no próximo passo.

### <a name="create-a-subscription-for-an-invoice-section"></a>Criar uma subscrição para uma secção de faturas

O exemplo seguinte cria uma subscrição chamada *Dev Team subscrição* do tipo *Microsoft Azure Plan* para a secção de faturas de *desenvolvimento.* A subscrição será faturada para o perfil de faturação *das finanças contoso* e aparecerá na secção *desenvolvimento* da sua fatura.

Faça o seguinte pedido, `<invoiceSectionId>` `invoiceSectionId` substituindo o copiado do segundo passo (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Teria de passar o `billingProfileId` `skuId` e copiado do segundo passo nos parâmetros de pedido da API. Se quiser especificar os proprietários, aprenda [a obter iDs](grant-access-to-create-subscription.md#userObjectId)de objetos de utilizador .

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
| `displayName` | Sim      | String | O nome de exibição da subscrição.|
| `billingProfileId`   | Sim      | String | A identificação do perfil de faturação que será cobrado para as acusações da subscrição.  |
| `skuId` | Sim      | String | O sku ID que determina o tipo de plano Azure. |
| `owners`      | Não       | String | O Id do Objeto de qualquer utilizador ou diretor de serviço que gostaria de adicionar como Proprietário RBAC na subscrição quando for criado.  |
| `costCenter` | Não      | String | O centro de custos associado à subscrição. Aparece no ficheiro csv de uso. |
| `managementGroupId` | Não      | String | A identificação do grupo de gestão a que a subscrição será adicionada. Para obter a lista de grupos de gestão, consulte Grupos de [Gestão - Lista API](/rest/api/resources/managementgroups/list). Utilize a identificação de um grupo de gestão da API. |

Na resposta, recebes `subscriptionCreationResult` um objeto para monitorização. Quando a criação de `subscriptionCreationResult` subscrição `subscriptionLink` estiver concluída, o objeto devolveria um objeto, que tem o ID de subscrição.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Criar subscrições para uma conta de faturação de MPA

### <a name="prerequisites"></a>Pré-requisitos

Você deve ter um papel global de Administrador ou Agente De Administrador na conta de fornecedor de soluções cloud da sua organização para criar subscrição para a sua conta de faturação. Para mais informações, consulte [Partner Center - Atribuir aos utilizadores funções e permissões](https://docs.microsoft.com/partner-center/permissions-overview).

O exemplo abaixo indicado utiliza as APIs REST. Atualmente, o PowerShell e a CLI do Azure não são suportados.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Encontre as contas de faturação a que tem acesso

Faça o pedido abaixo para listar todas as contas de faturação a que tem acesso.

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
Utilize `displayName` a propriedade para identificar a conta de faturação para a qual pretende criar subscrições. Certifique-se de que o acordoTipo da conta é *o MicrosoftPartnerAgreement*. Copie `name` a conta. Por exemplo, se quiser criar uma `Contoso` subscrição para a `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`conta de faturação, copiaria. Cole este valor algures para que possa utilizá-lo no próximo passo.

### <a name="find-customers-that-have-azure-plans"></a>Encontre clientes que tenham planos Azure

Faça o seguinte pedido, `<billingAccountName>` `name` substituindo o copiado do primeiro passo para```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```listar todos os clientes na conta de faturação para quem pode criar subscrições Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
A resposta da API lista os clientes na conta de faturação com planos Azure. Pode criar subscrições para estes clientes.

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

Utilize `displayName` a propriedade para identificar o cliente para o qual pretende criar subscrições. Copie `id` o para o cliente. Por exemplo, se quiser criar `Fabrikam toys`uma subscrição `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`para, copiaria. Colar este valor em algum lugar para usá-lo nos passos seguintes.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcional para fornecedores indiretos: Obtenha os revendedores para um cliente

Se for um fornecedor indireto no modelo de dois níveis cSP, pode especificar um revendedor enquanto cria subscrições para clientes.

Faça o seguinte pedido, `<customerId>` `id` substituindo o copiado do segundo passo para```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```listar todos os revendedores disponíveis para um cliente.

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
Utilize `description` a propriedade para identificar o revendedor que estará associado à subscrição. Copie `resellerId` o para o revendedor. Por exemplo, se quiser `Wingtip`associar, copiaria. `3xxxxx` Cole este valor algures para que possa utilizá-lo no próximo passo.

### <a name="create-a-subscription-for-a-customer"></a>Criar uma subscrição para um cliente

O exemplo seguinte cria uma subscrição chamada *subscrição de Dev Team* para *brinquedos Fabrikam* e revendedor de *Wingtip* associado à subscrição. T

Faça o seguinte pedido, `<customerId>` `id` substituindo o copiado do segundo passo (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Passe o *revendedor opcionalId* copiado a partir do segundo passo nos parâmetros de pedido da API.

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
| `displayName` | Sim      | String | O nome de exibição da subscrição.|
| `skuId` | Sim      | String | A identificação do plano Azure. Use *0001* para subscrições do tipo Microsoft Azure Plan |
| `resellerId`      | Não       | String | A identificação mpn do revendedor que estará associado à subscrição.  |

Na resposta, recebes `subscriptionCreationResult` um objeto para monitorização. Quando a criação de `subscriptionCreationResult` subscrição `subscriptionLink` estiver concluída, o objeto devolveria um objeto, que tem o ID de subscrição.

## <a name="next-steps"></a>Passos seguintes

* Por exemplo, na criação de uma subscrição do Acordo Empresarial (EA) utilizando .NET, consulte [o código da amostra no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Agora que criou uma subscrição, pode conceder essa capacidade a outros utilizadores e diretores de serviço. Para mais informações, consulte o Acesso ao Grant para criar subscrições da [Azure Enterprise (pré-visualização)](grant-access-to-create-subscription.md).
* Para saber mais sobre a gestão de um grande número de subscrições usando grupos de gestão, consulte Organizar os seus recursos com grupos de [gestão Azure](../../governance/management-groups/overview.md)
