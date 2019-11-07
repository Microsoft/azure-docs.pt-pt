---
title: Integrar um cliente na gestão de recursos delegados do Azure
description: Saiba como integrar um cliente ao gerenciamento de recursos delegado do Azure, permitindo que seus recursos sejam acessados e gerenciados por meio de seu próprio locatário.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/29/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a96093c71658f53e372cbccb72b96da3ae4e593b
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615478"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Integrar um cliente na gestão de recursos delegados do Azure

Este artigo explica como você, como um provedor de serviços, pode integrar um cliente ao gerenciamento de recursos delegado do Azure, permitindo que seus recursos delegados (assinaturas e/ou grupos de recursos) sejam acessados e gerenciados por meio de seu próprio Azure Active Directory ( Azure AD) locatário. Embora possamos nos referir aos provedores de serviços e clientes aqui, as empresas que gerenciam vários locatários podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Você pode repetir esse processo se estiver gerenciando recursos para vários clientes. Em seguida, quando um usuário autorizado entra no seu locatário, esse usuário pode ser autorizado entre escopos de aluguel do cliente para executar operações de gerenciamento sem precisar entrar em cada locatário individual do cliente.

Você pode associar sua ID de Microsoft Partner Network (MPN) às assinaturas integradas para acompanhar o impacto nos compromissos do cliente e receber o reconhecimento. Para obter mais informações, consulte [vincular uma ID de parceiro às suas contas do Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Observe que você precisará executar essa associação em seu locatário do provedor de serviços.

> [!NOTE]
> Os clientes também podem ser integrados ao comprarem uma oferta de serviços gerenciados (pública ou privada) que você publicou no Azure Marketplace. Para obter mais informações, consulte [publicar ofertas de serviços gerenciados no Azure Marketplace](publish-managed-services-offers.md). Você também pode usar o processo de integração descrito aqui com uma oferta publicada no Azure Marketplace.

O processo de integração requer que as ações sejam executadas de dentro do locatário do provedor de serviços e do locatário do cliente. Todas essas etapas são descritas neste artigo.

> [!IMPORTANT]
> No momento, não é possível carregar uma assinatura (ou grupo de recursos em uma assinatura) para o gerenciamento de recursos delegado do Azure se a assinatura usar Azure Databricks. Da mesma forma, se uma assinatura tiver sido registrada para integração com o provedor de recursos **Microsoft. managedservices** , você não poderá criar um espaço de trabalho do databricks para essa assinatura no momento.

## <a name="gather-tenant-and-subscription-details"></a>Coletar detalhes do locatário e da assinatura

Para carregar o locatário de um cliente, ele deve ter uma assinatura ativa do Azure. Você precisará saber o seguinte:

- A ID do locatário do locatário do provedor de serviços (em que você gerenciará os recursos do cliente)
- A ID do locatário do locatário do cliente (que terá recursos gerenciados pelo provedor de serviços)
- As IDs de assinatura para cada assinatura específica no locatário do cliente que será gerenciado pelo provedor de serviços (ou que contém os grupos de recursos que serão gerenciados pelo provedor de serviços)

Se você ainda não tiver essas informações, poderá recuperá-las de uma das maneiras a seguir.

### <a name="azure-portal"></a>Portal do Azure

Sua ID de locatário pode ser vista passando o mouse sobre o nome da sua conta no lado superior direito da portal do Azure ou selecionando o **diretório de comutador**. Para selecionar e copiar sua ID de locatário, pesquise "Azure Active Directory" de dentro do portal, selecione **Propriedades** e copie o valor mostrado no campo **ID de diretório** . Para localizar a ID de uma assinatura, pesquise "assinaturas" e, em seguida, selecione a ID de assinatura apropriada.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Ao realizar a integração de uma assinatura (ou de um ou mais grupos de recursos em uma assinatura) usando o processo descrito aqui, o provedor de recursos **Microsoft. managedservices** será registrado para essa assinatura.

## <a name="define-roles-and-permissions"></a>Definir funções e permissões

Como um provedor de serviços, talvez você queira executar várias tarefas para um único cliente, exigindo acesso diferente para escopos diferentes. Você pode definir quantas autorizações forem necessárias para atribuir [funções internas de RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) a usuários em seu locatário.

Para facilitar o gerenciamento, é recomendável usar grupos de usuários do Azure AD para cada função, permitindo que você adicione ou remova usuários individuais do grupo em vez de atribuir permissões diretamente a esse usuário. Você também pode querer atribuir funções a uma entidade de serviço. Certifique-se de seguir o princípio de privilégios mínimos para que os usuários tenham apenas as permissões necessárias para concluir seu trabalho. Para obter recomendações e informações sobre as funções com suporte, consulte [locatários, usuários e funções em cenários de Lighthouse do Azure](../concepts/tenants-users-roles.md).

Para definir autorizações, você precisará saber os valores de ID para cada usuário, grupo de usuários ou entidade de serviço ao qual você deseja conceder acesso. Você também precisará da ID de definição de função para cada função interna que você deseja atribuir. Se você ainda não os tiver, poderá recuperá-los de uma das maneiras a seguir.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> É recomendável atribuir a [função de exclusão de atribuição de registro de serviços gerenciados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) ao integrar um cliente, para que os usuários em seu locatário possam [remover o acesso à delegação](#remove-access-to-a-delegation) posteriormente, se necessário. Se essa função não for atribuída, os recursos delegados só poderão ser removidos por um usuário no locatário do cliente.

## <a name="create-an-azure-resource-manager-template"></a>Criar um modelo de Azure Resource Manager

Para carregar seu cliente, você precisará criar um modelo de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) para sua oferta com as informações a seguir. Os valores **mspOfferName** e **mspOfferDescription** são visíveis para o cliente ao exibir detalhes da oferta na [página provedores de serviço](view-manage-service-providers.md) do portal do Azure.

|Campo  |Definição  |
|---------|---------|
|**mspOfferName**     |Um nome que descreve essa definição. Esse valor é exibido para o cliente como o título da oferta.         |
|**mspOfferDescription**     |Uma breve descrição da sua oferta (por exemplo, "oferta de gerenciamento de VM da Contoso")      |
|**managedByTenantId**     |Sua ID de locatário         |
|**autorizações**     |Os valores de **PrincipalId** para os usuários/grupos/SPNs do seu locatário, cada um com um **principalIdDisplayName** para ajudar seu cliente a entender a finalidade da autorização e mapeado para um valor **roleDefinitionId** interno para especificar o nível de acesso         |

Para carregar a assinatura de um cliente, use o modelo de Azure Resource Manager apropriado que fornecemos em nosso [repositório de exemplos](https://github.com/Azure/Azure-Lighthouse-samples/), junto com um arquivo de parâmetros correspondente que você modifica para corresponder à sua configuração e definir suas autorizações. Modelos separados são fornecidos dependendo se você está integrando uma assinatura inteira, um grupo de recursos ou vários grupos de recursos em uma assinatura. Também fornecemos um modelo que pode ser usado para clientes que compraram uma oferta de serviço gerenciado que você publicou no Azure Marketplace, se você preferir integrar suas assinaturas dessa maneira.

|**Para carregar este**  |**Usar este modelo de Azure Resource Manager**  |**E modificar esse arquivo de parâmetro** |
|---------|---------|---------|
|Subscrição   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Grupo de recursos   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Vários grupos de recursos em uma assinatura   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Assinatura (ao usar uma oferta publicada no Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> O processo descrito aqui requer uma implantação separada para cada assinatura que está sendo integrada. Implantações separadas também serão necessárias se você estiver integrando vários grupos de recursos em assinaturas diferentes. No entanto, a integração de vários grupos de recursos em uma única assinatura pode ser feita em uma implantação.
>
> Implantações separadas também são necessárias para várias ofertas sendo aplicadas à mesma assinatura (ou grupos de recursos dentro de uma assinatura). Cada oferta aplicada deve usar um **mspOfferName**diferente.

O exemplo a seguir mostra o arquivo **delegatedResourceManagement. Parameters. JSON** que será usado para carregar uma assinatura. Os arquivos de parâmetro do grupo de recursos (localizados na pasta [RG-delegued-Resource-Management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) ) são semelhantes, mas também incluem um parâmetro **rgName** para identificar os grupos de recursos específicos a serem integrados.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
A última autorização no exemplo acima adiciona um **PrincipalId** com a função de administrador de acesso do usuário (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Ao atribuir essa função, você deve incluir a propriedade **delegatedRoleDefinitionIds** e uma ou mais funções internas. O usuário criado nessa autorização poderá atribuir essas funções internas a [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Observe que nenhuma outra permissão normalmente associada à função Administrador de acesso do usuário será aplicada a esse usuário.

## <a name="deploy-the-azure-resource-manager-templates"></a>Implantar os modelos de Azure Resource Manager

Depois de atualizar o arquivo de parâmetros, o cliente deve implantar o modelo de gerenciamento de recursos no locatário do cliente como uma implantação em nível de assinatura. Uma implantação separada é necessária para cada assinatura que você deseja integrar ao gerenciamento de recursos delegado do Azure (ou para cada assinatura que contenha grupos de recursos que você deseja carregar).

> [!IMPORTANT]
> A implantação deve ser feita por uma conta que não seja de convidado no locatário do cliente que tem a [função interna de proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) para a assinatura que está sendo integrada (ou que contém os grupos de recursos que estão sendo integrados). Para ver todos os usuários que podem delegar a assinatura, um usuário no locatário do cliente pode selecionar a assinatura no portal do Azure, o **iam (Open Access Control)** e [Exibir todos os usuários com a função proprietário](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).


```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Confirmar integração bem-sucedida

Quando uma assinatura de cliente tiver sido integrada com êxito ao gerenciamento de recursos delegado do Azure, os usuários no locatário do provedor de serviços poderão ver a assinatura e seus recursos (se tiverem recebido acesso a ele por meio do processo acima) seja individualmente ou como um membro de um grupo do Azure AD com as permissões apropriadas). Para confirmar isso, verifique se a assinatura é exibida de uma das maneiras a seguir.  

### <a name="azure-portal"></a>Portal do Azure

No locatário do provedor de serviços:

1. Navegue até a [página meus clientes](view-manage-customers.md).
2. Selecione **clientes**.
3. Confirme que você pode ver as assinaturas com o nome da oferta fornecido no modelo do Resource Manager.

No locatário do cliente:

1. Navegue até a [página provedores de serviços](view-manage-service-providers.md).
2. Selecione **ofertas de provedor de serviço**.
3. Confirme que você pode ver as assinaturas com o nome da oferta fornecido no modelo do Resource Manager.

> [!NOTE]
> Pode levar alguns minutos depois que a implantação for concluída antes que as atualizações sejam refletidas na portal do Azure.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Remover o acesso a uma delegação

Por padrão, um usuário no locatário do cliente que tem as permissões apropriadas pode remover o acesso a recursos que foram delegados a um provedor de serviços na [página provedores de serviço](view-manage-service-providers.md#add-or-remove-service-provider-offers) do portal do Azure.

Se você tiver incluído usuários com a [atribuição de registro de serviços gerenciados excluir função](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) ao integrar o cliente para o gerenciamento de recursos delegado do Azure, esses usuários em seu locatário também poderão remover a delegação. Quando você fizer isso, nenhum usuário no locatário do provedor de serviços poderá acessar os recursos que foram previamente delegados.

O exemplo a seguir mostra uma atribuição que concede a **função de exclusão de atribuição de registro de serviços gerenciados** que pode ser incluída em um arquivo de parâmetro:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c" 
        } 
    ] 
```

Um usuário com essa permissão pode remover uma delegação de uma das seguintes maneiras.

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

az managedservices assignment delete –assignment <id or full resourceId>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **meus clientes** na portal do Azure.
