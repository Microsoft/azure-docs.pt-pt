---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262374"
---
## <a name="authenticate-with-azure-active-directory"></a>Autenticar com o Azure Active Directory

> [!IMPORTANT]
> 1. Atualmente, **apenas** o API de Visão Computacional, API Facial, API de Análise de Texto, Leitor Imersivo, Reconhecimento de Formulários, Detetor de Anomalias, e todos os serviços bing personalizados de suporte de pesquisa usando O Diretório Ativo Azure (AAD).
> 2. A autenticação AAD tem de ser sempre utilizada juntamente com o nome personalizado de subdomínio do seu recurso Azure. [Os pontos finais regionais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) não suportam a autenticação aD.

Nas secções anteriores, mostrámos-lhe como autenticar os Serviços Cognitivos Azure utilizando uma chave de subscrição de serviço único ou multi-serviço. Embora estas teclas ofereçam um caminho rápido e fácil para iniciar o desenvolvimento, elas ficam aquém de cenários mais complexos que requerem controlos de acesso baseados em papéis. Vamos ver o que é necessário para autenticar usando o Azure Ative Directory (AAD).

Nas seguintes secções, utilizará o ambiente Azure Cloud Shell ou o Azure CLI para criar um subdomínio, atribuir funções e obter um símbolo ao portador para ligar para os Serviços Cognitivos Azure. Se ficar preso, as ligações são fornecidas em cada secção com todas as opções disponíveis para cada comando em Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Criar um recurso com um subdomínio personalizado

O primeiro passo é criar um subdomínio personalizado. Se pretender utilizar um recurso de Serviços Cognitivos existente que não tenha nome de subdomínio personalizado, siga as instruções em [Subdomínios Personalizados de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) para ativar o subdomínio personalizado para o seu recurso.

1. Comece por abrir a Casca de Nuvem Azure. Em seguida, [selecione uma subscrição:](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Em seguida, [crie um recurso de Serviços Cognitivos](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) com um subdomínio personalizado. O nome do subdomínio tem de ser globalmente único e não pode incluir personagens especiais, como: ".", "", "".

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Se for bem sucedido, o **Ponto Final** deve mostrar o nome de subdomínio exclusivo do seu recurso.


### <a name="assign-a-role-to-a-service-principal"></a>Atribuir um papel a um diretor de serviço

Agora que tem um subdomínio personalizado associado ao seu recurso, terá de atribuir um papel a um diretor de serviço.

> [!NOTE]
> Tenha em mente que as atribuições de funções da AAD podem demorar até cinco minutos para se propagar.

1. Primeiro, vamos registar uma [aplicação AAD.](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Vai precisar do Id de **aplicação** no próximo passo.

2. Em seguida, você precisa [criar um diretor](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) de serviço para a aplicação AAD.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Se registar uma candidatura no portal Azure, este passo está concluído para si.

3. O último passo é [atribuir a função "Utilizador de Serviços Cognitivos"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) ao diretor de serviço (com o recurso). Ao atribuir um papel, está a conceder o acesso principal ao serviço a este recurso. Pode conceder o mesmo serviço principal de acesso a vários recursos na sua subscrição.
   >[!NOTE]
   > O ObjectId do diretor de serviço é utilizado, não o ObjectId para a aplicação.
   > O ACCOUNT_ID será o recurso Azure Id da conta de Serviços Cognitivos que criou. Pode encontrar id de recurso Azure a partir de "propriedades" do recurso no portal Azure.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Pedido de exemplo

Nesta amostra, é utilizada uma palavra-passe para autenticar o diretor de serviço. O símbolo fornecido é então usado para chamar a API da Visão Computacional.

1. Obtenha o seu **TenantId:**
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Pegue um símbolo:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Ligue para a API da Visão Computacional:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Em alternativa, o diretor de serviço pode ser autenticado com um certificado. Além do diretor de serviço, o diretor de serviço também é suportado por ter permissões delegadas através de outra aplicação AAD. Neste caso, em vez de senhas ou certificados, os utilizadores seriam solicitados para autenticação de dois fatores ao adquirirem token.
