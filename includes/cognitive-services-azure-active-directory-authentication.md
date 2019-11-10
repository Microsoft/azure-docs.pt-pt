---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: fb3795ff807a87e9bac4d95400f5e446c68d1e4d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73897540"
---
## <a name="authenticate-with-azure-active-directory"></a>Autenticar com o Azure Active Directory

> [!IMPORTANT]
> 1. Atualmente, **somente** o API da Pesquisa Visual Computacional, API de Detecção Facial, API de análise de texto, leitor de imersão, reconhecedor de formulário, detector de anomalias e todos os serviços do Bing, exceto pesquisa personalizada do Bing dar suporte à autenticação usando o Azure Active Directory (AAD).
> 2. A autenticação do AAD precisa ser sempre usada junto com o nome de subdomínio personalizado do recurso do Azure. Os [pontos de extremidade regionais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) não dão suporte à autenticação do AAD.

Nas seções anteriores, mostramos como se autenticar nos serviços cognitivas do Azure usando uma chave de assinatura de serviço único ou de vários serviços. Embora essas chaves forneçam um caminho rápido e fácil para iniciar o desenvolvimento, elas ficam curtas em cenários mais complexos que exigem controles de acesso baseados em função. Vamos dar uma olhada no que é necessário para autenticar usando o Azure Active Directory (AAD).

Nas seções a seguir, você usará o ambiente de Azure Cloud Shell ou o CLI do Azure para criar um subdomínio, atribuir funções e obter um token de portador para chamar os serviços cognitivas do Azure. Se você ficar preso, os links serão fornecidos em cada seção com todas as opções disponíveis para cada comando no Azure Cloud Shell/CLI do Azure.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Criar um recurso com um subdomínio personalizado

A primeira etapa é criar um subdomínio personalizado. Se você quiser usar um recurso de serviços cognitivas existente que não tem um nome de subdomínio personalizado, siga as instruções em [subdomínios personalizados de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) para habilitar o subdomínio personalizado para seu recurso.

1. Comece abrindo a Azure Cloud Shell. em seguida, [Selecione uma assinatura](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzureSubscription -SubscriptionName <YOUR_SUBCRIPTION>
   ```

2. Em seguida, [crie um recurso de serviços cognitivas](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) com um subdomínio personalizado. O nome do subdomínio precisa ser globalmente exclusivo e não pode incluir caracteres especiais, como: ".", "!", ",".

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Se for bem-sucedido, o **ponto de extremidade** deverá mostrar o nome do subdomínio exclusivo para seu recurso.


### <a name="assign-a-role-to-a-service-principal"></a>Atribuir uma função a uma entidade de serviço

Agora que você tem um subdomínio personalizado associado ao recurso, será necessário atribuir uma função a uma entidade de serviço.

> [!NOTE]
> Tenha em mente que as atribuições de função do AAD podem levar até cinco minutos para serem propagadas.

1. Primeiro, vamos registrar um [aplicativo do AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Você precisará do **ApplicationId** na próxima etapa.

2. Em seguida, você precisa [criar uma entidade de serviço](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) para o aplicativo do AAD.

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Se você registrar um aplicativo no portal do Azure, essa etapa será concluída para você.

3. A última etapa é [atribuir a função "usuário de serviços cognitivas"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) à entidade de serviço (com escopo para o recurso). Ao atribuir uma função, você está concedendo acesso de entidade de serviço a esse recurso. Você pode conceder o mesmo acesso de entidade de serviço a vários recursos em sua assinatura.
   >[!NOTE]
   > O ObjectId da entidade de serviço é usado, não o ObjectId do aplicativo.
   > O ACCOUNT_ID será a ID de recurso do Azure da conta de serviços cognitivas que você criou. Você pode encontrar a ID de recurso do Azure de "Propriedades" do recurso em portal do Azure.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Exemplo de solicitação

Neste exemplo, uma senha é usada para autenticar a entidade de serviço. O token fornecido é usado para chamar o API da Pesquisa Visual Computacional.

1. Obtenha sua **tenantid**:
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Obter um token:
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Chame o API da Pesquisa Visual Computacional:
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Como alternativa, a entidade de serviço pode ser autenticada com um certificado. Além da entidade de serviço, a entidade de usuário também tem suporte ao ter permissões delegadas por meio de outro aplicativo do AAD. Nesse caso, em vez de senhas ou certificados, os usuários devem solicitar a autenticação de dois fatores ao adquirir o token.
