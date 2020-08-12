---
title: Incluir um cliente no Azure Lighthouse
description: Saiba como embarcar um cliente no Farol Azure, permitindo que os seus recursos sejam acedidos e geridos através do seu próprio inquilino utilizando a gestão de recursos delegada da Azure.
ms.date: 05/26/2020
ms.topic: how-to
ms.openlocfilehash: cac40a835ff3227a31611b31655865d43fa378ab
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118880"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Incluir um cliente no Azure Lighthouse

Este artigo explica como você, como prestador de serviços, pode embarcar um cliente para o Farol Azure. Ao fazê-lo, os recursos delegados do cliente (subscrições e/ou grupos de recursos) podem ser acedidos e geridos através do seu próprio inquilino Azure Ative Directory (Azure AD), utilizando a [gestão de recursos delegada da Azure.](../concepts/azure-delegated-resource-management.md)

Pode repetir este processo se estiver a gerir recursos para vários clientes. Em seguida, quando um utilizador autorizado assina no seu inquilino, esse utilizador pode ser autorizado através de âmbitos de arrendamento do cliente para realizar operações de gestão, sem ter que assinar em cada cliente inquilino individual.

Para acompanhar o seu impacto através dos compromissos com os clientes e receber reconhecimento, associe o seu ID da Microsoft Partner Network (MPN) a pelo menos uma conta de utilizador que tenha acesso a cada uma das suas subscrições a bordo. Note que você precisará realizar esta associação no seu inquilino prestador de serviços. Para simplificar, recomendamos a criação de uma conta principal de serviço no seu inquilino que esteja associada ao seu ID MPN, e concedendo-lhe acesso reader a todos os clientes a bordo. Para obter mais informações, consulte [Link um ID do parceiro nas suas contas Azure.](../../cost-management-billing/manage/link-partner-id.md) 

> [!NOTE]
> Os clientes também podem ser acedidos ao Farol de Azure quando adquirem uma oferta de serviços geridos (públicos ou privados) que publicou no Azure Marketplace. Para mais informações, consulte [as ofertas de Serviços Geridos da Publicação ao Azure Marketplace.](publish-managed-services-offers.md) Também pode utilizar o processo de embarque descrito aqui juntamente com uma oferta publicada no Azure Marketplace.

O processo de embarque requer a ações a tomar tanto dentro do arrendatário do prestador de serviços como do arrendatário do cliente. Todos estes passos são descritos neste artigo.

> [!TIP]
> Apesar de nos referirmos a prestadores de serviços e clientes neste tópico, [as empresas que gerem vários inquilinos](../concepts/enterprise.md) podem usar o mesmo processo para criar o Farol Azure e consolidar a sua experiência de gestão.

## <a name="gather-tenant-and-subscription-details"></a>Recolha detalhes do inquilino e da subscrição

Para embarcar no inquilino de um cliente, deve ter uma subscrição ativa do Azure. Precisa saber o seguinte:

- A ID do inquilino do inquilino do prestador de serviços (onde estará a gerir os recursos do cliente)
- O ID do inquilino do inquilino do cliente (que terá recursos geridos pelo prestador de serviços)
- Os IDs de subscrição de cada subscrição específica no arrendatário do cliente que serão geridos pelo prestador de serviços (ou que contém o(s) grupo de recursos que será gerido pelo prestador de serviços).

> [!NOTE]
> Mesmo que deseje apenas embarcar um ou mais grupos de recursos dentro de uma subscrição, a implementação deve ser feita ao nível da subscrição, pelo que necessitará do ID de subscrição.

Se ainda não tiver estes valores de identificação, pode recuperá-los de uma das seguintes formas. Certifique-se de que utiliza estes valores exatos na sua implantação.

### <a name="azure-portal"></a>Portal do Azure

A identificação do seu inquilino pode ser vista pairando sobre o nome da sua conta no lado superior direito do portal Azure, ou selecionando o **diretório da Switch**. Para selecionar e copiar o ID do seu inquilino, procure "Azure Ative Directory" dentro do portal, em seguida, selecione **Propriedades** e copie o valor indicado no campo ID do **Diretório.** Para encontrar o ID de uma subscrição no inquilino do cliente, procure por "Subscrições" e, em seguida, selecione o ID de subscrição apropriado.

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

Como prestador de serviços, poderá querer executar múltiplas tarefas para um único cliente, requerendo acesso diferente para diferentes âmbitos. Pode definir as autorizações necessárias para atribuir [funções de controlo de acesso (RBAC) baseadas em funções](../../role-based-access-control/built-in-roles.md) para utilizadores do seu inquilino.

Para facilitar a gestão, recomendamos a utilização de grupos de utilizadores AZure AD para cada função, permitindo-lhe adicionar ou remover utilizadores individuais ao grupo em vez de atribuir permissões diretamente a esse utilizador. Também pode querer atribuir funções a um diretor de serviço. Certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para completar o seu trabalho. Para recomendações e informações sobre funções [apoiadas, consulte Inquilinos, utilizadores e papéis em cenários do Farol Azure.](../concepts/tenants-users-roles.md)

> [!IMPORTANT]
> Para adicionar permissões para um grupo AD Azure, o **tipo de grupo** deve ser a **Segurança** e não o **Office 365**. Esta opção é selecionada quando o grupo é criado. Para obter mais informações, consulte [Criar um grupo básico e adicionar membros utilizando o Azure Ative Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Para definir as autorizações, você precisará saber os valores de ID para cada utilizador, grupo de utilizadores ou principal de serviço no inquilino do prestador de serviços a que deseja conceder acesso. Também vai precisar da definição de papel ID para cada papel incorporado que pretende atribuir. Se ainda não os tiver, pode recuperá-los executando os comandos abaixo a partir do inquilino do prestador de serviços.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Recomendamos a atribuição da [Função de Registo de Registo de Serviços Geridos](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao embarcar num cliente, para que os utilizadores do seu inquilino possam [remover o acesso à delegação](remove-delegation.md) mais tarde, se necessário. Se esta função não for atribuída, os recursos delegados só podem ser removidos por um utilizador no arrendatário do cliente.

## <a name="create-an-azure-resource-manager-template"></a>Crie um modelo de gestor de recursos Azure

Para embarcar no seu cliente, terá de criar um modelo [de Gestor de Recursos Azure](../../azure-resource-manager/index.yml) para a sua oferta com as seguintes informações. Os valores **mspOfferName** e **mspOfferDescription** serão visíveis para o cliente ao visualizar detalhes da oferta na página de [prestadores](view-manage-service-providers.md) de serviços do portal Azure.

|Campo  |Definição  |
|---------|---------|
|**mspOfferName**     |Um nome descrevendo esta definição. Este valor é apresentado ao cliente como título da oferta.         |
|**mspOfferDescription**     |Uma breve descrição da sua oferta (por exemplo, "Oferta de gestão de Contoso VM").      |
|**managedByTenantId**     |Sua identificação de inquilino.          |
|**autorizações**     |Os valores **principais** para os utilizadores/grupos/SPNs do seu inquilino, cada um com um **nome principalIdDisplayName** para ajudar o seu cliente a compreender o propósito da autorização, e mapeado para uma **função incorporadaDefinitionId** valor para especificar o nível de acesso.      |

O processo de embarque requer um modelo de Gestor de Recursos Azure (fornecido nas [nossas amostras repo)](https://github.com/Azure/Azure-Lighthouse-samples/)e um ficheiro de parâmetros correspondente que modifica para corresponder à sua configuração e definir as suas autorizações.

O modelo que escolher dependerá se estiver a embarcar numa subscrição inteira, num grupo de recursos ou em vários grupos de recursos dentro de uma subscrição. Também fornecemos um modelo que pode ser usado para clientes que compraram uma oferta de serviço gerida que publicou no Azure Marketplace, se preferir embarcar nas suas subscrições desta forma.

|Para embarcar isto  |Use este modelo de Gestor de Recursos Azure  |E modificar este arquivo de parâmetros |
|---------|---------|---------|
|Subscrição   |[delegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Grupo de recursos   |[rgDelegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Múltiplos grupos de recursos numa subscrição   |[multipleRgDelegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Subscrição (ao utilizar uma oferta publicada no Azure Marketplace)   |[marketplaceDelegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> O processo aqui descrito requer uma implementação separada do nível de subscrição para cada subscrição a bordo, mesmo que esteja a bordo de subscrições no mesmo cliente. São também necessárias implementações separadas se estiver a bordo de vários grupos de recursos dentro de diferentes subscrições no mesmo cliente. No entanto, a bordo de vários grupos de recursos dentro de uma única subscrição pode ser feita numa implementação de nível de subscrição.
>
> São também necessárias implementações separadas para que várias ofertas sejam aplicadas à mesma subscrição (ou grupos de recursos dentro de uma subscrição). Cada oferta aplicada deve utilizar um **nome de MSPOffer Diferente**.

O exemplo a seguir mostra umadelegatedResourceManagement.parameters.jsmodificada **no** ficheiro que pode ser usado para embarcar numa subscrição. Os ficheiros de parâmetros do grupo de recursos (localizados na pasta [de gestão de recursos delegados rg)](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) são semelhantes, mas também incluem um parâmetro **rgName** para identificar o(s) grupo de recursos específicos a bordo.

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

A última autorização no exemplo acima adiciona um **principalid** com a função de Administrador de Acesso ao Utilizador (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Ao atribuir esta função, deve incluir a propriedade **delegadoRoleDefinitionIds** e uma ou mais funções incorporadas. O utilizador criado nesta autorização poderá atribuir estas funções incorporadas a [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) no arrendatário do cliente, o que é necessário para [implementar políticas que possam ser remediadas.](deploy-policy-remediation.md)  O utilizador também é capaz de criar incidentes de suporte.  Nenhuma outra permissão normalmente associada à função de Administrador de Acesso ao Utilizador será aplicada a este utilizador.

## <a name="deploy-the-azure-resource-manager-templates"></a>Implementar os modelos do Gestor de Recursos Azure

Uma vez atualizado o seu ficheiro de parâmetros, um utilizador no inquilino do cliente deve implementar o modelo do Gestor de Recursos Azure dentro do seu inquilino como uma implementação de nível de subscrição. É necessária uma implementação separada para cada subscrição que pretende embarcar (ou para cada subscrição que contenha grupos de recursos que pretende embarcar).

Por se trata de uma implementação de nível de subscrição, não pode ser iniciada no portal Azure. A colocação pode ser feita utilizando PowerShell ou Azure CLI, como mostrado abaixo.

> [!IMPORTANT]
> Esta implementação ao nível de subscrição deve ser feita por uma conta não hóspede no arrendatário do cliente que tenha a [função de Proprietário incorporada](../../role-based-access-control/built-in-roles.md#owner) para a subscrição a bordo (ou que contenha os grupos de recursos que estão a ser a bordo). Para ver todos os utilizadores que possam delegar a subscrição, um utilizador no arrendatário do cliente pode selecionar a subscrição no portal Azure, abrir o **controlo de acesso (IAM)** e [ver todos os utilizadores com a função Proprietário.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)
>
> Se a subscrição foi criada através do [programa Cloud Solution Provider (CSP),](../concepts/cloud-solution-provider.md)qualquer utilizador que tenha o papel de Agente [Administrativo](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) no seu inquilino fornecedor de serviços pode realizar a implementação.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
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

Quando uma subscrição do cliente tiver sido acedida com sucesso ao Farol de Azure, os utilizadores do arrendatário do prestador de serviços poderão ver a subscrição e os seus recursos (se lhes for concedido acesso através do processo acima, individualmente ou como membro de um grupo AZure AD com as permissões adequadas). Para confirmar isto, verifique se a subscrição aparece de uma das seguintes formas.  

### <a name="azure-portal"></a>Portal do Azure

No arrendatário do prestador de serviços:

1. Navegue para a [página dos meus clientes.](view-manage-customers.md)
2. Selecione **Clientes**.
3. Confirme que pode ver a subscrição(s) com o nome de oferta que forneceu no modelo de Gestor de Recursos.

> [!IMPORTANT]
> Para ver a subscrição delegada nos [meus clientes, os](view-manage-customers.md)utilizadores do arrendatário do prestador de serviços devem ter recebido a função [Reader](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclua o acesso ao Reader) quando a subscrição foi a bordo.

No inquilino do cliente:

1. Navegue para a [página de fornecedores de serviços.](view-manage-service-providers.md)
2. Selecione **Ofertas de fornecedores de serviços**.
3. Confirme que pode ver a subscrição(s) com o nome de oferta que forneceu no modelo de Gestor de Recursos.

> [!NOTE]
> Pode demorar alguns minutos após a sua implementação estar concluída antes que as atualizações sejam refletidas no portal Azure.

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

## <a name="next-steps"></a>Passos seguintes

- Conheça as [experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
- [Ver e gerir clientes](view-manage-customers.md) indo para **os meus clientes** no portal Azure.
- Saiba como [remover o acesso a uma delegação](remove-delegation.md) que estava previamente a bordo.
