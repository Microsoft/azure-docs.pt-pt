---
title: Criar um Recurso da Leitura Avançada
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como criar um novo recurso Imersivo leitor com um subdomínio personalizado e, em seguida, configurar O Anúncio Azure no seu inquilino Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78330724"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Criar um recurso Imersivo do Leitor e configurar a autenticação do Diretório Ativo Azure

Neste artigo, fornecemos um script que criará um recurso Imersivo do Leitor e configurará a autenticação azure Ative Directory (Azure AD). Cada vez que um recurso Imersivo do Leitor é criado, seja com este script ou no portal, também deve ser configurado com permissões Azure AD. Este guião vai ajudar-te com isso.

O script foi concebido para criar e configurar todos os recursos imersivos e de AD do Azure necessários para todos num só passo. No entanto, também pode configurar a autenticação Azure AD para um recurso imersivo existente do Leitor, se por exemplo, já criou um no portal Azure.

Para alguns clientes, pode ser necessário criar múltiplos recursos imersivos do Reader, para desenvolvimento vs produção, ou talvez para várias regiões diferentes em que o seu serviço está implantado. Para esses casos, pode voltar e usar o script várias vezes para criar diferentes recursos imersivos do Leitor e configurá-los com as permissões da AD Azure.

O guião foi concebido para ser flexível. Procurará primeiro os recursos adsimerivos e azure existentes na sua subscrição, e criá-los-á apenas se não existirem. Se é a sua primeira vez a criar um recurso Imersivo do Leitor, o script fará tudo o que precisa. Se quiser usá-lo apenas para configurar o Azure AD para um recurso imersivo existente que foi criado no portal, também o fará. Também pode ser usado para criar e configurar vários recursos imersivos do Leitor.

## <a name="set-up-powershell-environment"></a>Configurar o ambiente PowerShell

1. Comece por abrir a [Casca de Nuvem Azure.](https://docs.microsoft.com/azure/cloud-shell/overview) Certifique-se de que a casca de nuvem está definida para `pwsh`PowerShell no dropdown da mão superior esquerda ou digitando .

1. Copie e cole o seguinte corte de código na concha.

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
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

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

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

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

1. Executar a `Create-ImmersiveReaderResource`função, fornecendo os parâmetros conforme apropriado.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | Parâmetro | Comentários |
    | --- | --- |
    | SubscriptionName |Nome da subscrição Azure a utilizar para o seu recurso Imersivo leitor. Deve ter uma subscrição para criar um recurso. |
    | ResourceName |  Deve ser alfanumérico, e pode conter '-', desde que o '-' não seja o primeiro ou último personagem. O comprimento não pode exceder 63 caracteres.|
    | Subdomínio de Recursos |Um subdomínio personalizado é necessário para o seu recurso Imersivo reader. O subdomínio é utilizado pelo SDK quando chama o serviço De leitor imersivo para lançar o Leitor. O subdomínio deve ser globalmente único. O subdomínio deve ser alfanumérico e pode conter '-', desde que o '-' não seja o primeiro ou último personagem. O comprimento não pode exceder 63 caracteres. Este parâmetro é opcional se o recurso já existir. |
    | RecursosKU |Opções: `S0`. Visite a nossa página de [preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) para saber mais sobre cada SKU disponível. Este parâmetro é opcional se o recurso já existir. |
    | ResourceLocation |`eastus`Opções: `eastus2` `southcentralus`, `westus` `westus2`, `australiaeast` `southeastasia`, `centralindia` `japaneast`, `northeurope` `uksouth`, `westeurope`, , , , . Este parâmetro é opcional se o recurso já existir. |
    | ResourceGroupName |Os recursos são criados em grupos de recursos dentro de subscrições. Forneça o nome de um grupo de recursos existente. Se o grupo de recursos ainda não existir, será criado um novo com este nome. |
    | Localização de Grupo de Recursos |Se o seu grupo de recursos não existir, precisa fornecer um local para criar o grupo. Para encontrar uma lista de `az account list-locations`locais, corra. Utilize a *propriedade do nome* (sem espaços) do resultado devolvido. Este parâmetro é opcional se o seu grupo de recursos já existir. |
    | Nome de exibição adappapp |O nome de exibição da aplicação Azure Ative Diretório. Se não for encontrada uma aplicação Azure AD existente, será criada uma nova com este nome. Este parâmetro é opcional se a aplicação Azure AD já existir. |
    | AADAppIdentifierUri |O URI para a aplicação Azure AD. Se não for encontrada uma aplicação Azure AD existente, será criada uma nova com este URI. Por exemplo, `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Uma palavra-passe que criará que será usada mais tarde para autenticar ao adquirir um símbolo para lançar o Leitor Imersivo. A palavra-passe deve ter pelo menos 16 caracteres de comprimento, conter pelo menos 1 caracteres especiais e conter pelo menos 1 caracteres numéricos. |

1. Copie a saída JSON num ficheiro de texto para posterior utilização. A saída deve parecer a seguinte.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Passos seguintes

* Veja o [Node.js quickstart](./quickstart-nodejs.md) para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Node.js
* Veja o [tutorial](./tutorial-python.md) python para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Python
* Veja o [tutorial Swift](./tutorial-ios-picture-immersive-reader.md) para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Swift
* Explore o [SDK imersivo do leitor](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)




