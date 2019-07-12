---
title: Carregar um cliente para o Azure delegadas a gestão de recursos - Lighthouse do Azure
description: Saiba como carregar para um cliente para o Azure delegadas a gestão de recursos, permitindo que os seus recursos sejam acessados e gerenciados por meio de seu próprio inquilino.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1885a6220f14de234710b6980b5d3b6a6172bb7e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809863"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Carregar um cliente para o Azure delegadas a gestão de recursos

Este artigo explica como, como um fornecedor de serviços, pode carregar um cliente para gestão de recursos do Azure de delegados, permitindo que os seus recursos delegados (subscrições e/ou grupos de recursos) para ser acedida e geridas através de seu próprio (Azure Active Directory Inquilino do Azure AD). Enquanto faremos referência a fornecedores de serviços e clientes aqui, as empresas a gerenciar vários inquilinos podem utilizar o mesmo processo para consolidar sua experiência de gestão.

Pode repetir este processo, se estiver a gerir recursos para vários clientes. Em seguida, quando um utilizador autorizado inicia sessão com o seu inquilino, que o utilizador pode ser autorizado âmbitos de inquilinos do cliente para efetuar operações de gestão sem ter de iniciar sessão para cada inquilino do cliente individual.

Pode associar o seu ID do Microsoft Partner Network (MPN) com as suas subscrições integrado para controlar o impacto de envolvimento com o cliente. Para mais informações, veja [ligar um ID de parceiro a suas contas do Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

> [!NOTE]
> Os clientes podem ser automaticamente carregados ao comprar uma oferta de serviços geridos (pública ou privada) que publicou no Azure Marketplace. Para mais informações, veja [oferece de publicar serviços geridos pelo Azure Marketplace](publish-managed-services-offers.md). Também pode utilizar o processo de integração descrito aqui com uma oferta publicada no Azure Marketplace.

O processo de integração requer ações a tomar no inquilino de ambos do fornecedor de serviços e de inquilino do cliente. Todas essas etapas são descritas neste artigo.

> [!IMPORTANT]
> Atualmente, não é possível carregar uma subscrição (ou grupo de recursos numa subscrição) para o Azure delegadas a gestão de recursos se a subscrição utiliza o Azure Databricks. Da mesma forma, se uma subscrição tiver sido registrada para a integração com o **Microsoft.ManagedServices** fornecedor de recursos, não poderá criar uma área de trabalho do Databricks para essa subscrição neste momento.

## <a name="gather-tenant-and-subscription-details"></a>Recolher detalhes da subscrição e de inquilino

Para carregar o inquilino de um cliente, tem de ter uma subscrição do Azure Active Directory. Precisará saber o seguinte:

- O ID de inquilino do inquilino do fornecedor de serviços (em que pretende gerir recursos do cliente)
- O ID de inquilino do inquilino do cliente (o que terá recursos gerenciados pelo provedor de serviço)
- Os IDs de subscrição para cada subscrição específica no inquilino do cliente que serão geridos pelo fornecedor de serviço (ou que contém os grupos de recursos que serão geridos pelo fornecedor de serviço)

Se ainda não tiver estas informações, pode recuperá-la em uma das seguintes formas.

### <a name="azure-portal"></a>Portal do Azure

O ID de inquilino pode ser visto ao pairar o rato sobre o nome da sua conta no canto superior direito do portal do Azure, ou ao selecionar **trocar diretório**. Para selecionar e copiar o ID de inquilino, procure "Azure Active Directory" no portal, em seguida, selecione **propriedades** e copie o valor mostrado na **ID do diretório** campo. Para localizar o ID de uma subscrição, procure "Subscrições" e, em seguida, selecione o ID de subscrição adequada.

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


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>Certifique-se de que a subscrição do cliente está registrada para a integração

Cada subscrição tem de ser autorizada para a integração ao registar manualmente o **Microsoft.ManagedServices** fornecedor de recursos. O cliente pode registrar uma assinatura ao seguir os passos descritos em [fornecedores de recursos do Azure e tipos de](../../azure-resource-manager/resource-manager-supported-services.md).

O cliente pode confirmar que a subscrição está pronta para a integração de uma das seguintes formas.

### <a name="azure-portal"></a>Portal do Azure

1. No portal do Azure, selecione a subscrição.
1. Selecione **fornecedores de recursos**.
1. Confirme que **Microsoft.ManagedServices** é apresentado como **registado**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Isto deverá devolver resultados semelhantes ao seguinte:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

Isto deverá devolver resultados semelhantes ao seguinte:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>Definir funções e permissões

Como um fornecedor de serviços, pode querer utilizar várias ofertas com um único cliente, que necessitam de acesso diferentes para âmbitos diferentes.

Para facilitar a gestão, recomendamos que utilize grupos de utilizadores do Azure AD para cada função, permitindo-lhe adicionar ou remover utilizadores individuais para o grupo em vez de atribuir permissões diretamente a esse utilizador. Também poderá atribuir funções a um principal de serviço. Certifique-se de que acompanhar o princípio de privilégio mínimo, para que os utilizadores só têm as permissões necessárias para concluir seu trabalho, ajudando a reduzir a probabilidade de erros acidentais. Para mais informações, veja [práticas de segurança recomendadas](../concepts/recommended-security-practices.md).

> [!NOTE]
> Atribuições de função tem de utilizar o controlo de acesso baseado em funções (RBAC) [funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Todas as funções incorporadas são atualmente suportadas com a gestão de recursos do Azure de delegados, exceto o proprietário e quaisquer funções incorporadas com [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) permissão. A função incorporada de administrador de acesso de utilizador é suportada para utilização limitada, conforme descrito abaixo. Funções personalizadas e [funções de administrador de subscrição clássica](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) não também são suportadas.

Para definir as autorizações, precisará de saber os valores de ID para cada utilizador, grupo de utilizador ou principal de serviço ao qual pretende conceder acesso. Também será necessário o ID de definição de função para cada função incorporada que pretende atribuir. Se ainda não tivê-los, pode recuperá-los em uma das seguintes formas.



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
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId" –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Criar um modelo Azure Resource Manager

Para integrar seu cliente, precisará criar um [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) modelo que inclua o seguinte:

|Campo  |Definição  |
|---------|---------|
|**mspName**     |Nome do fornecedor de serviço         |
|**mspOfferDescription**     |Uma breve descrição da sua oferta (por exemplo, "Contoso gestão oferta de VM")      |
|**managedByTenantId**     |O ID de inquilino         |
|**authorizations**     |O **principalId** valores para os utilizadores/grupos/SPN no seu inquilino, cada um com um **principalIdDisplayName** para ajudar a compreender a finalidade da autorização de cliente e mapeado para uma incorporada **roleDefinitionId** valor a especificar o nível de acesso         |

Para carregar subscrição de um cliente, utilize o modelo Azure Resource Manager apropriado que fornecemos no nosso [repositório de exemplos](https://github.com/Azure/Azure-Lighthouse-samples/), juntamente com um ficheiro de parâmetros correspondentes que modifica para corresponder à sua configuração e definir sua autorizações. Modelos separados são fornecidos dependendo se estiver a integrar uma subscrição completa, um grupo de recursos ou vários grupos de recursos numa subscrição. Também fornecemos um modelo que pode ser utilizado para clientes que compraram uma oferta de serviço gerido que publicou no Azure Marketplace, se carregar suas subscrições desta forma.

|**Para carregar este**  |**Utilize este modelo do Azure Resource Manager**  |**E modificar este ficheiro de parâmetros** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Vários grupos de recursos dentro de uma subscrição   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Subscrição (quando utilizar uma oferta publicada no Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> O processo descrito aqui requer uma implementação separada para cada subscrição a que se pretende incluir.
> 
> Implementações separadas também são necessárias se estiver a integrar vários recursos de grupos dentro de diferentes subscrições. No entanto, integração de vários grupos de recursos dentro de uma única subscrição podem ser feitos numa implantação.

O exemplo seguinte mostra um modificado **resourceProjection.parameters.json** ficheiro que será utilizado para carregar uma subscrição. Os ficheiros de parâmetro do grupo de recursos (localizados no [rg-delegada--gestão de recursos](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) pasta) são semelhantes, mas também incluir um **rgName** parâmetro para identificar os grupos de recurso específico para ser carregadas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
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
A última autorização no exemplo acima adiciona um **principalId** com a função de administrador de acesso de utilizador (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Ao atribuir esta função, tem de incluir o **delegatedRoleDefinitionIds** propriedade e um ou mais funções incorporadas. O utilizador criado nesta autorização será capaz de atribuir essas funções internas para identidades geridas. Tenha em atenção que não existem outras permissões normalmente associadas à função Administrador de acesso de utilizador irão aplicar a este utilizador.

## <a name="deploy-the-azure-resource-manager-templates"></a>Implementar os modelos Azure Resource Manager

Depois de atualizar o ficheiro de parâmetros, o cliente tem de implementar o modelo de gestão de recursos no inquilino do seu cliente como uma implementação de nível de assinatura. Uma implementação separada é necessária para cada subscrição que pretende carregar gerenciamento delegado de recursos do Azure (ou para cada subscrição que contém os grupos de recursos que pretende carregar).

> [!IMPORTANT]
> A implementação deve ser feita por uma conta de não-convidado no inquilino do cliente que tem o [função incorporada de proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) para a subscrição que se pretende incluir (ou que contém os grupos de recursos que estão a ser carregados).

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

## <a name="confirm-successful-onboarding"></a>Confirmar a integração com êxito

Quando uma subscrição de cliente com êxito foi incluído para gestão de recursos do Azure de delegados, será capazes de ver a subscrição e os respetivos recursos (se eles foi concedidos acesso ao mesmo durante o processo acima, os utilizadores no inquilino do fornecedor de serviços o individualmente ou como membro de um grupo do Azure AD com as permissões adequadas). Para confirmar, verifique se que a subscrição será exibido em uma das seguintes formas.  

### <a name="azure-portal"></a>Portal do Azure

No inquilino do fornecedor de serviços:

1. Navegue para o [minha página de clientes](view-manage-customers.md).
2. Selecione **clientes**.
3. Certifique-se que pode ver as subscrições com o nome de oferta que indicou no modelo do Resource Manager.

No inquilino do cliente:

1. Navegue para o [página de provedores de serviço](view-manage-service-providers.md).
2. Selecione **ofertas de fornecedor de serviço**.
3. Certifique-se que pode ver as subscrições com o nome de oferta que indicou no modelo do Resource Manager.

> [!NOTE]
> Pode demorar alguns minutos após a implementação está concluída antes das atualizações são refletidas no portal do Azure.

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

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [experiências de gestão entre inquilinos](../concepts/cross-tenant-management-experience.md).
- [Ver e gerir os clientes](view-manage-customers.md) ao aceder **meus clientes** no portal do Azure.
