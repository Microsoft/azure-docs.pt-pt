---
title: Integrar um cliente na gestão de recursos delegados do Azure
description: Aprenda a embarcar um cliente para a gestão de recursos delegados do Azure, permitindo que os seus recursos sejam acedidos e geridos através do seu próprio inquilino.
ms.date: 01/20/2020
ms.topic: conceptual
ms.openlocfilehash: 33cf880098e174c2c230a3d78e125ad8df7d894a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649794"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Integrar um cliente na gestão de recursos delegados do Azure

Este artigo explica como você, como prestador de serviços, pode embarcar um cliente para a gestão de recursos delegados da Azure, permitindo que os seus recursos delegados (subscrições e/ou grupos de recursos) sejam acedidos e geridos através do seu próprio inquilino azure Ative Directory (Azure AD). Enquanto nos referimos a prestadores de serviços e clientes aqui, as empresas que [gerem vários inquilinos](../concepts/enterprise.md) podem usar o mesmo processo para consolidar a sua experiência de gestão.

Pode repetir este processo se estiver a gerir recursos para vários clientes. Em seguida, quando um utilizador autorizado se inscreve no seu inquilino, esse utilizador pode ser autorizado através de âmbitos de arrendamento do cliente para realizar operações de gestão sem ter que iniciar sessão em cada inquilino de cliente individual.

Para acompanhar o seu impacto através dos compromissos dos clientes e receber reconhecimento, associe o seu ID da Microsoft Partner Network (MPN) a pelo menos uma conta de utilizador que tenha acesso a cada uma das suas subscrições a bordo. Note que terá de realizar esta associação no seu prestador de serviços inquilino. Para a simplicidade, recomendamos a criação de uma conta principal de serviço no seu inquilino que esteja associada ao seu ID MPN, e que lhe conceda acesso ao Leitor a todos os clientes a bordo. Para mais informações, consulte [Link um parceiro ID para as suas contas Azure](../../billing/billing-partner-admin-link-started.md). 

> [!NOTE]
> Os clientes também podem ser a bordo quando compram uma oferta de serviços geridos (público ou privado) que publicou no Azure Marketplace. Para mais informações, consulte [a Publicação de Ofertas de Serviços Geridos para o Mercado Azure.](publish-managed-services-offers.md) Também pode utilizar o processo de embarque aqui descrito juntamente com uma oferta publicada no Azure Marketplace.

O processo de embarque requer que sejam tomadas medidas dentro do inquilino do prestador de serviços e do inquilino do cliente. Todos estes passos são descritos neste artigo.

> [!IMPORTANT]
> Atualmente, não pode embarcar numa subscrição (ou grupo de recursos dentro de uma subscrição) para a gestão de recursos delegados do Azure se a subscrição utilizar os Azure Databricks. Da mesma forma, se uma subscrição foi registada para o embarque com o fornecedor de recursos **Microsoft.ManagedServices,** não será capaz de criar um espaço de trabalho databricks para essa subscrição neste momento.

## <a name="gather-tenant-and-subscription-details"></a>Reúna detalhes de inquilino e subscrição

Para embarcar no inquilino de um cliente, deve ter uma subscrição azure ativa. Precisa saber o seguinte:

- A identificação do inquilino do inquilino do prestador de serviços (onde você estará gerindo os recursos do cliente)
- A identificação do inquilino do inquilino do cliente (que terá recursos geridos pelo prestador de serviços)
- As IDs de subscrição para cada subscrição específica no inquilino do cliente que serão geridas pelo prestador de serviços (ou que contém o(s) grupo de recursos que será gerido pelo prestador de serviços).

> [!NOTE]
> Mesmo que apenas deseje embarcar num ou mais grupos de recursos dentro de uma subscrição, a implementação deve ser feita ao nível da subscrição, pelo que necessitará do ID de subscrição.

Se ainda não tiver estes valores de identificação, pode recuperá-los de uma das seguintes formas. Certifique-se de que utiliza estes valores exatos na sua implantação.

### <a name="azure-portal"></a>Portal do Azure

O seu ID de inquilino pode ser visto pairando sobre o nome da sua conta no lado superior direito do portal Azure, ou selecionando **o diretório Switch**. Para selecionar e copiar o ID do seu inquilino, procure "Azure Ative Diretório" a partir do portal, em seguida, selecione **Propriedades** e copie o valor mostrado no campo de ID do **Diretório.** Para encontrar a identificação de uma subscrição no inquilino do cliente, procure "Subscrições" e, em seguida, selecione o ID de subscrição apropriado.

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
> Ao embarcar numa subscrição (ou um ou mais grupos de recursos dentro de uma subscrição) utilizando o processo aqui descrito, o fornecedor de recursos **Microsoft.ManagedServices** será registado para essa subscrição.

## <a name="define-roles-and-permissions"></a>Definir papéis e permissões

Como prestador de serviços, poderá querer realizar múltiplas tarefas para um único cliente, exigindo um acesso diferente para diferentes âmbitos. Pode definir as autorizações necessárias para atribuir funções de controlo de [acesso (RBAC) baseadas](../../role-based-access-control/built-in-roles.md) em papéis aos utilizadores do seu inquilino.

Para facilitar a gestão, recomendamos a utilização de grupos de utilizadores Da Azure AD para cada função, permitindo-lhe adicionar ou remover utilizadores individuais ao grupo em vez de atribuir permissões diretamente a esse utilizador. Também pode querer atribuir funções a um diretor de serviço. Certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para completar o seu trabalho. Para recomendações e informações sobre papéis suportados, consulte [Inquilinos, utilizadores e papéis em cenários do Farol Azure.](../concepts/tenants-users-roles.md)

Para definir as autorizações, terá de conhecer os valores de ID para cada utilizador, grupo de utilizadores ou diretor de serviço no inquilino do prestador de serviços a que pretende conceder acesso. Você também vai precisar da definição de papel ID para cada papel incorporado que você quer atribuir. Se ainda não os tiver, pode recuperá-los executando os comandos abaixo do inquilino do prestador de serviços.

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
> Recomendamos a atribuição da Função de Eliminação de [Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao embarcar num cliente, para que os utilizadores do seu inquilino possam [remover o acesso à delegação](#remove-access-to-a-delegation) mais tarde, se necessário. Se esta função não for atribuída, os recursos delegados só podem ser removidos por um utilizador no inquilino do cliente.

## <a name="create-an-azure-resource-manager-template"></a>Criar um modelo Azure Resource Manager

Para embarcar no seu cliente, terá de criar um modelo de Gestor de [Recursos Azure](../../azure-resource-manager/index.yml) para a sua oferta com as seguintes informações. Os valores **mspOfferName** e **mspOfferDescription** serão visíveis para o cliente ao visualizar em detalhes da oferta na página de fornecedores de [serviços](view-manage-service-providers.md) do portal Azure.

|Campo  |Definição  |
|---------|---------|
|**nome mspOffer**     |Um nome que descreve esta definição. Este valor é apresentado ao cliente como título da oferta.         |
|**mspOfferDescription**     |Uma breve descrição da sua oferta (por exemplo, "Oferta de gestão de VM Contoso").      |
|**managedByTenantId**     |Sua identificação do inquilino.          |
|**autorizações**     |Os valores **principais para** os utilizadores/grupos/SPNs do seu inquilino, cada um com um **nome principal IdDisplayName** para ajudar o seu cliente a compreender o propósito da autorização, e mapeados para um valor de **roleDefinitionId** incorporado para especificar o nível de acesso.      |

O processo de embarque requer um modelo de Gestor de Recursos Azure (fornecido no nosso repo de [amostras)](https://github.com/Azure/Azure-Lighthouse-samples/)e um ficheiro de parâmetros correspondente que modifica para corresponder à sua configuração e definir as suas autorizações.

O modelo que escolher dependerá se está a embarcar numa subscrição inteira, num grupo de recursos ou em vários grupos de recursos dentro de uma subscrição. Também fornecemos um modelo que pode ser usado para clientes que compraram uma oferta de serviço gerida que publicou no Azure Marketplace, se preferir embarcar na sua subscrição desta forma.

|Para embarcar esta  |Use este modelo de Gestor de Recursos Azure  |E modificar este ficheiro de parâmetro |
|---------|---------|---------|
|Subscrição   |[delegadoResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegadoResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Grupo de recursos   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Múltiplos grupos de recursos dentro de uma subscrição   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Subscrição (quando utilizar uma oferta publicada no Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> O processo aqui descrito requer uma implementação separada do nível de subscrição para cada subscrição a bordo, mesmo que esteja a embarcar subscrições no mesmo inquilino do cliente. Também são necessárias implementações separadas se estiver a embarcar em vários grupos de recursos dentro de diferentes subscrições no mesmo inquilino do cliente. No entanto, o embarque de vários grupos de recursos dentro de uma única subscrição pode ser feito numa única implementação de nível de subscrição.
>
> São também necessárias implementações separadas para que várias ofertas sejam aplicadas à mesma subscrição (ou grupos de recursos dentro de uma subscrição). Cada oferta aplicada deve utilizar um **nome diferente**de oferta .

O exemplo seguinte mostra um ficheiro **dedelegado modificadoResourceManagement.parameters.json** que pode ser usado para embarcar numa subscrição. Os ficheiros de parâmetros do grupo de recursos (localizados na pasta de [gestão de recursos delegados)](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) são semelhantes, mas também incluem um parâmetro **rgName** para identificar os grupos de recursos específicos a bordo.

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

A última autorização no exemplo acima adiciona um **principado** com a função de Administrador de Acesso ao Utilizador (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Ao atribuir esta função, deve incluir a propriedade **deRoleDefinitionIds delegada** e uma ou mais funções incorporadas. O utilizador criado nesta autorização poderá atribuir estas funções incorporadas a [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) no inquilino do cliente, o que é necessário para [implementar políticas que possam ser remediadas.](deploy-policy-remediation.md) Nenhuma outra permissão normalmente associada à função de Administrador de Acesso ao Utilizador será aplicada a este utilizador.

## <a name="deploy-the-azure-resource-manager-templates"></a>Implementar os modelos do Gestor de Recursos Azure

Uma vez atualizado o seu ficheiro de parâmetro, um utilizador no inquilino do cliente deve implementar o modelo do Gestor de Recursos Azure dentro do seu inquilino como uma implementação de nível de subscrição. É necessária uma implementação separada para cada subscrição que pretende embarcar na gestão de recursos delegados do Azure (ou para cada subscrição que contenha grupos de recursos que pretende embarcar).

Como se trata de uma implantação ao nível da subscrição, não pode ser iniciada no portal Azure. A implementação pode ser efetuada utilizando powerShell ou Azure CLI, como mostrado abaixo.

> [!IMPORTANT]
> Esta implantação ao nível da subscrição deve ser feita por uma conta não hóspede no inquilino do cliente que tenha o [papel integrado do Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) para a subscrição a bordo (ou que contém os grupos de recursos que estão a bordo). Para ver todos os utilizadores que possam delegar a subscrição, um utilizador no inquilino do cliente pode selecionar a subscrição no portal Azure, abrir o controlo de **acesso (IAM),** e [ver todos os utilizadores com a função Proprietário.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)

### <a name="powershell"></a>PowerShell

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
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Confirme o sucesso do embarque

Quando uma subscrição de um cliente tiver sido a bordo com sucesso para a gestão de recursos delegados do Azure, os utilizadores do inquilino do prestador de serviços poderão ver a subscrição e os seus recursos (se lhes foi concedido acesso através do processo acima, individualmente ou como membro de um grupo AD Azure com as permissões adequadas). Para confirmar isto, verifique se a subscrição aparece de uma das seguintes formas.  

### <a name="azure-portal"></a>Portal do Azure

No inquilino do prestador de serviços:

1. Navegue para a [página dos meus clientes.](view-manage-customers.md)
2. Selecione **Clientes**.
3. Confirme que pode ver a subscrição(s) com o nome de oferta que forneceu no modelo de Gestor de Recursos.

> [!IMPORTANT]
> Para ver a subscrição delegada nos [meus clientes,](view-manage-customers.md)os utilizadores do inquilino do prestador de serviços devem ter recebido a função [de Leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclui o acesso ao Leitor) quando a subscrição estava a bordo para a gestão de recursos delegados do Azure.

No inquilino do cliente:

1. Navegue para a página de fornecedores de [serviços.](view-manage-service-providers.md)
2. Selecione ofertas de **prestador de serviços**.
3. Confirme que pode ver a subscrição(s) com o nome de oferta que forneceu no modelo de Gestor de Recursos.

> [!NOTE]
> Pode demorar alguns minutos após a sua implementação estar concluída antes de as atualizações serem refletidas no portal Azure.

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

Por predefinição, os utilizadores do inquilino do cliente que possuam as permissões adequadas podem remover o acesso do prestador de serviços aos recursos delegados na página de prestadores de [serviços](view-manage-service-providers.md#add-or-remove-service-provider-offers) do portal Azure. Quando o fizerem, nenhum utilizador do inquilino do prestador de serviços poderá aceder aos recursos que tinham sido previamente delegados.

Se tiver a bordo utilizadores com a Função de Eliminação de [Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao embarcar num cliente para a gestão de recursos delegados do Azure, esses utilizadores também poderão remover a delegação.

O exemplo abaixo mostra uma atribuição que concede a Função de Eliminação de Atribuição de **Serviços Geridos** que pode ser incluída num ficheiro de parâmetros:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

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

- Conheça [as experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
- [Ver e gerir os clientes](view-manage-customers.md) indo para os meus **clientes** no portal Azure.
