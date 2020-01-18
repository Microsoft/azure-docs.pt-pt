---
title: Criar um Recurso da Leitura Avançada
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como criar um novo recurso de leitor de imersão com um subdomínio personalizado e, em seguida, configurar o Azure AD em seu locatário do Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: a2a404a03c06dde59edc88436afdc9dba3d74797
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170171"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Criar um recurso de leitor de imersão e configurar a autenticação Azure Active Directory

Neste artigo, fornecemos um script que criará um recurso de leitor de imersão e configurará a autenticação do Azure Active Directory (Azure AD). Cada vez que um recurso de leitor de imersão é criado, seja com esse script ou no portal, ele também deve ser configurado com permissões do Azure AD. Esse script o ajudará com isso.

O script foi projetado para criar e configurar todos os recursos do leitor de imersão e do Azure AD necessários para você em uma única etapa. No entanto, você também pode apenas configurar a autenticação do Azure AD para um recurso de leitor de imersão existente, se, por exemplo, você já tiver criado um no portal do Azure.

Para alguns clientes, pode ser necessário criar vários recursos de leitor de imersão, para desenvolvimento versus produção, ou talvez para várias regiões diferentes em que seu serviço está implantado. Para esses casos, você pode voltar e usar o script várias vezes para criar diferentes recursos de leitor de imersão e obtê-los configurados com as permissões do Azure AD.

O script foi projetado para ser flexível. Ele primeiro procurará os recursos existentes do leitor de imersão e do Azure AD em sua assinatura e os criará somente conforme necessário, caso ainda não existam. Se for a primeira vez que você cria um recurso de leitor de imersão, o script fará tudo o que você precisa. Se você quiser usá-lo apenas para configurar o Azure AD para um recurso de leitor de imersão existente que foi criado no portal, ele também fará isso. Ele também pode ser usado para criar e configurar vários recursos de leitor de imersão.

## <a name="set-up-powershell-environment"></a>Configurar o ambiente do PowerShell

1. Comece abrindo a [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Verifique se o Cloud Shell está definido como PowerShell no menu suspenso superior esquerdo ou digitando `pwsh`.

1. Copie e cole o trecho de código a seguir no Shell.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Execute a função `Create-ImmersiveReaderResource`, fornecendo os parâmetros conforme apropriado.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName <SUBSCRIPTION_NAME> `
      -ResourceName <RESOURCE_NAME> `
      -ResourceSubdomain <RESOURCE_SUBDOMAIN> `
      -ResourceSKU <RESOURCE_SKU> `
      -ResourceLocation <RESOURCE_LOCATION> `
      -ResourceGroupName <RESOURCE_GROUP_NAME> `
      -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
      -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
      -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
      -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
    ```

    | Parâmetro | Comentários |
    | --- | --- |
    | SubscriptionName |Nome da assinatura do Azure a ser usada para o recurso de leitura de imersão. Você deve ter uma assinatura do para criar um recurso. |
    | ResourceName |  Deve ser alfanumérico e pode conter '-', desde que '-' não seja o primeiro ou o último caractere. O comprimento não pode exceder 63 caracteres.|
    | ResourceSubdomain |Um subdomínio personalizado é necessário para o recurso de leitura de imersão. O subdomínio é usado pelo SDK ao chamar o serviço de leitura de imersão para iniciar o leitor. O subdomínio deve ser globalmente exclusivo. O subdomínio deve ser alfanumérico e pode conter '-', desde que '-' não seja o primeiro ou o último caractere. O comprimento não pode exceder 63 caracteres. Esse parâmetro será opcional se o recurso já existir. |
    | ResourceSKU |Opções: `S0`. Visite nossa [página de preços de serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) para saber mais sobre cada SKU disponível. Esse parâmetro será opcional se o recurso já existir. |
    | ResourceLocation |Opções: `eastus`, `eastus2`, `southcentralus`, `westus`, `westus2`, `australiaeast`, `southeastasia`, `centralindia`, `japaneast`, `northeurope`, `uksouth`, `westeurope`. Esse parâmetro será opcional se o recurso já existir. |
    | ResourceGroupName |Os recursos são criados em grupos de recursos dentro de assinaturas. Forneça o nome de um grupo de recursos existente. Se o grupo de recursos ainda não existir, um novo com esse nome será criado. |
    | ResourceGroupLocation |Se o grupo de recursos não existir, você precisará fornecer um local no qual criar o grupo. Para localizar uma lista de locais, execute `az account list-locations`. Use a propriedade *Name* (sem espaços) do resultado retornado. Esse parâmetro será opcional se o grupo de recursos já existir. |
    | AADAppDisplayName |O nome de exibição do aplicativo Azure Active Directory. Se um aplicativo existente do Azure AD não for encontrado, um novo com esse nome será criado. Esse parâmetro será opcional se o aplicativo do Azure AD já existir. |
    | AADAppIdentifierUri |O URI para o aplicativo do Azure AD. Se um aplicativo do Azure AD existente não for encontrado, um novo com esse URI será criado. Por exemplo, `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Uma senha que você cria que será usada posteriormente para autenticar ao adquirir um token para iniciar o leitor de imersão. A senha deve ter pelo menos 16 caracteres de comprimento, conter pelo menos um caractere especial e conter pelo menos um caractere numérico. |

1. Copie a saída JSON em um arquivo de texto para uso posterior. O resultado deverá ser semelhante ao seguinte.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Passos seguintes

* Veja o guia de [início rápido do node. js](./quickstart-nodejs.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando node. js
* Veja o [tutorial do Python](./tutorial-python.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando o Python
* Veja o [tutorial do Swift](./tutorial-ios-picture-immersive-reader.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando o Swift
* Explore o [SDK do leitor de imersão](https://github.com/microsoft/immersive-reader-sdk) e a [referência do SDK do leitor de imersão](./reference.md)




