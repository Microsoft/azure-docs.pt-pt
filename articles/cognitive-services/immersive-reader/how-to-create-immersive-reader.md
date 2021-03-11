---
title: Criar um Recurso da Leitura Avançada
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como criar um novo recurso Immersive Reader com um subdomínio personalizado e, em seguida, configurar Azure AD no seu inquilino Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: d7a8a598ab2d0c873cf07510cf40a08c22c0f1d4
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608602"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Crie um recurso imersivo do Leitor e configuure a autenticação do Azure Ative Directory

Neste artigo, fornecemos um script que criará um recurso imersivo do Leitor e configurará a autenticação do Azure Ative Directory (Azure AD). Cada vez que um recurso Imersivo do Leitor é criado, seja com este script ou no portal, também deve ser configurado com permissões AD AZure. Este guião vai ajudar-te com isso.

O script foi concebido para criar e configurar todos os recursos imersivos necessários para o Leitor imersivo e para a AD AZure para todos vocês num só passo. No entanto, também pode configurar a autenticação Azure AD para um recurso de Leitor Imersivo existente, se por acaso já tiver criado uma no portal Azure.

Para alguns clientes, pode ser necessário criar múltiplos recursos imersivos do Leitor, para desenvolvimento vs. produção, ou talvez para várias regiões diferentes em que o seu serviço é implantado. Nesses casos, pode voltar e usar o script várias vezes para criar diferentes recursos imersivos do Leitor e configurá-los com as permissões AD do Azure.

O guião foi concebido para ser flexível. Primeiro procurará os recursos Imersivos existentes do Leitor e AD da Azure na sua subscrição, e criá-los-á apenas se já não existirem. Se for a primeira vez que cria um recurso Immersive Reader, o script fará tudo o que precisa. Se quiser usá-lo apenas para configurar o AD AZure para um recurso de Leitor Imersivo existente que foi criado no portal, também o fará. Também pode ser usado para criar e configurar múltiplos recursos imersivos do Leitor.

## <a name="set-up-powershell-environment"></a>Configurar o ambiente PowerShell

1. Comece por abrir a [Azure Cloud Shell](../../cloud-shell/overview.md). Certifique-se de que a Cloud Shell está definida para PowerShell no dropdown superior esquerdo ou digitando `pwsh` .

1. Copie e cole o seguinte corte de código na casca.

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
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
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
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
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

1. Executar a `Create-ImmersiveReaderResource` função, fornecendo os espaços reservados '<PARAMETER_VALUES>' abaixo com os seus próprios valores, conforme adequado.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource -SubscriptionName '<SUBSCRIPTION_NAME>' -ResourceName '<RESOURCE_NAME>' -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' -ResourceSKU '<RESOURCE_SKU>' -ResourceLocation '<RESOURCE_LOCATION>' -ResourceGroupName '<RESOURCE_GROUP_NAME>' -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>' -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    O comando completo será parecido com o seguinte. Aqui colocamos cada parâmetro na sua própria linha para a clareza, para que possa ver todo o comando. Não copie nem utilize este comando como está. Copie e use o comando acima com os seus próprios valores. Este exemplo tem valores falsos para o "<PARAMETER_VALUES>" acima. O seu será diferente, pois vai inventar os seus próprios nomes para estes valores.

    ```
    Create-ImmersiveReaderResource
        -SubscriptionName 'MyOrganizationSubscriptionName'
        -ResourceName 'MyOrganizationImmersiveReader'
        -ResourceSubdomain 'MyOrganizationImmersiveReader'
        -ResourceSKU 'S0'
        -ResourceLocation 'westus2'
        -ResourceGroupName 'MyResourceGroupName'
        -ResourceGroupLocation 'westus2'
        -AADAppDisplayName 'MyOrganizationImmersiveReaderAADApp'
        -AADAppIdentifierUri 'https://MyOrganizationImmersiveReaderAADApp'
        -AADAppClientSecret 'SomeStrongPassword'
        -AADAppClientSecretExpiration '2021-12-31'
    ```

    | Parâmetro | Comentários |
    | --- | --- |
    | SubscriptionName |Nome da subscrição Azure para utilizar para o seu recurso Leitor Imersivo. Tem de ter uma subscrição para criar um recurso. |
    | ResourceName |  Deve ser alfanumérico, e pode conter '--,, desde que o '-' não seja o primeiro ou último carácter. O comprimento não pode exceder 63 caracteres.|
    | ResourceSubdomain |Um subdomínio personalizado é necessário para o seu recurso Immersive Reader. O subdomínio é utilizado pelo SDK quando chama o serviço De Leitor Imersivo para lançar o Leitor. O subdomínio deve ser globalmente único. O subdomínio deve ser alfanumérico, e pode conter '--', desde que o '-' não seja o primeiro ou último carácter. O comprimento não pode exceder 63 caracteres. Este parâmetro é opcional se o recurso já existir. |
    | ResourceSKU |Opções: `S0` (Nível padrão) ou `S1` (Organizações de Educação/Sem Fins Lucrativos). Visite a nossa [página de preços dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) para saber mais sobre cada SKU disponível. Este parâmetro é opcional se o recurso já existir. |
    | ResourceLocation |Opções: `eastus` , , , , , , , , `eastus2` , , , `southcentralus` , , `westus` `westus2` `australiaeast` `southeastasia` `centralindia` `japaneast` `northeurope` `uksouth` `westeurope` . Este parâmetro é opcional se o recurso já existir. |
    | ResourceGroupName |Os recursos são criados em grupos de recursos dentro de subscrições. Forneça o nome de um grupo de recursos existente. Se o grupo de recursos já não existir, será criado um novo com este nome. |
    | ResourceGroupLocation |Se o seu grupo de recursos não existir, precisa fornecer um local para criar o grupo. Para encontrar uma lista de locais, `az account list-locations` corra. Utilize a propriedade *do nome* (sem espaços) do resultado devolvido. Este parâmetro é opcional se o seu grupo de recursos já existir. |
    | Nome AADAppDisplay |O nome de visualização da aplicação do Azure Ative Directory. Se não for encontrada uma aplicação AD Azure existente, será criada uma nova com este nome. Este parâmetro é opcional se a aplicação AD Azure já existir. |
    | AADAppIdentifierUri |O URI para a aplicação AD Azure. Se não for encontrada uma aplicação AD AZure existente, será criada uma nova com este URI. Por exemplo, `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Uma palavra-passe que criará que será usada mais tarde para autenticar ao adquirir um token para lançar o Leitor Imersivo. A palavra-passe deve ter pelo menos 16 caracteres de comprimento, conter pelo menos 1 carácter especial e conter pelo menos 1 caracteres numéricos. Para gerir os segredos dos clientes da aplicação Azure AD depois de ter criado este recurso visite https://portal.azure.com e vá ao Home -> Azure Ative Directory -> Registos de aplicações -> `[AADAppDisplayName]` -> Certificados e Segredos -> secção de Segredos de Cliente (como mostra abaixo a imagem "Gerencie os seus segredos de aplicação AZure AD"). |
    | AADAppClientSecretExpiration |A data ou data após a qual o seu `[AADAppClientSecret]` expirará (por exemplo, '2020-12-31T11:59:59+00:00' ou '2020-12-31'). |

    Gerencie os seus segredos de aplicação AZure AD

    ![Certificados e segredos do Portal Azure](./media/client-secrets-blade.png)

1. Copie a saída JSON num ficheiro de texto para utilização posterior. A saída deve parecer-se com a seguinte.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Passos seguintes

* Veja o [Node.js arranque rápido](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) para ver o que mais pode fazer com o SDK do leitor imersivo usando Node.js
* Veja o [tutorial do Android](./tutorial-android.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Java ou Kotlin para Android
* Veja o tutorial do [iOS](./tutorial-ios.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Swift para iOS
* Veja o [tutorial python](./tutorial-python.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Python
* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)