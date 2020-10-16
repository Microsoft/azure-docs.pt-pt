---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: 235b7946fbcfc2322878428cce72e77ecceb9cfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88011022"
---
## <a name="authenticate-with-azure-active-directory"></a>Autenticar com o Azure Active Directory

> [!IMPORTANT]
> 1. Atualmente, **apenas** a API de Visão De Computador, API facial, API de Análise de Texto, Leitor Imersivo, Reconhecedor de Formulários, Detetor de Anomalias e todos os serviços de Bing, exceto a autenticação de suporte de pesquisa personalizada Bing usando O Diretório Ativo Azure (AAD).
> 2. A autenticação AAD deve ser sempre utilizada juntamente com o nome subdomínio personalizado do seu recurso Azure. [Os pontos finais regionais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) não suportam a autenticação AAD.

Nas secções anteriores, mostrámos-lhe como autenticar contra os Serviços Cognitivos Azure utilizando uma chave de subscrição de um único serviço ou multi-serviço. Embora estas teclas ofereçam um caminho rápido e fácil para iniciar o desenvolvimento, elas ficam aquém de cenários mais complexos que requerem controlo de acesso baseado em funções Azure (Azure RBAC). Vamos dar uma olhada no que é necessário para autenticar usando o Azure Ative Directory (AAD).

Nas secções seguintes, você usará o ambiente Azure Cloud Shell ou o CLI Azure para criar um subdomínio, atribuir papéis e obter um símbolo ao portador para chamar os Serviços Cognitivos Azure. Se ficar preso, são fornecidas ligações em cada secção com todas as opções disponíveis para cada comando em Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Criar um recurso com um subdomínio personalizado

O primeiro passo é criar um subdomínio personalizado. Se pretender utilizar um recurso existente em Serviços Cognitivos que não tenha nome de subdomínio personalizado, siga as instruções em [Serviços Cognitivos Subdomínios Personalizados](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) para permitir subdomínio personalizado para o seu recurso.

1. Comece por abrir a Azure Cloud Shell. Em seguida, [selecione uma subscrição:](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Em seguida, [crie um recurso de Serviços Cognitivos](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) com um subdomínio personalizado. O nome do subdomínio tem de ser globalmente único e não pode incluir personagens especiais, como: ".", "!", "!".

   ```powershell-interactive
   $account = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Se for bem sucedido, o **Endpoint** deve mostrar o nome do subdomínio exclusivo do seu recurso.


### <a name="assign-a-role-to-a-service-principal"></a>Atribuir um papel a um diretor de serviço

Agora que tem um subdomínio personalizado associado ao seu recurso, terá de atribuir um papel a um diretor de serviço.

> [!NOTE]
> Tenha em mente que as atribuições de funções do Azure podem demorar até cinco minutos a propagar-se.

1. Primeiro, vamos registar uma [aplicação AAD.](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   $app = New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Vais precisar do **ApplicationId** no próximo passo.

2. Em seguida, você precisa [criar um principal de serviço](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) para a aplicação AAD.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Se registar uma inscrição no portal Azure, este passo está concluído para si.

3. O último passo é [atribuir o papel de "Utilizador de Serviços Cognitivos"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) ao principal de serviço (abrangido pelo recurso). Ao atribuir um papel, está a conceder acesso principal a este recurso. Pode conceder ao mesmo serviço o acesso principal a múltiplos recursos na sua subscrição.
   >[!NOTE]
   > O ObjectId do principal de serviço é utilizado, não o ObjectId para a aplicação.
   > O ACCOUNT_ID será o ID de recursos Azure da conta dos Serviços Cognitivos que criou. Pode encontrar o Id de recursos Azure a partir de "propriedades" do recurso no portal Azure.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Pedido de amostra

Nesta amostra, é utilizada uma palavra-passe para autenticar o principal do serviço. O token fornecido é então usado para chamar a API de Visão de Computador.

1. Obtenha o seu **TenantId:**
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Obter um símbolo:
   > [!NOTE]
   > Se estiver a usar a Azure Cloud Shell, a `SecureClientSecret` aula não está disponível. 

   #### <a name="powershell"></a>[PowerShell](#tab/powershell)
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
   
   #### <a name="azure-cloud-shell"></a>[Azure Cloud Shell](#tab/azure-cloud-shell)
   ```Azure Cloud Shell
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, <YOUR_PASSWORD>
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ``` 

   ---

3. Ligue para a API de Visão De Computador:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Em alternativa, o principal de serviço pode ser autenticado com um certificado. Além do principal serviço, o principal utilizador também é apoiado por ter permissões delegadas através de outra aplicação AAD. Neste caso, em vez de senhas ou certificados, os utilizadores seriam solicitados para a autenticação de dois fatores na aquisição de token.

## <a name="authorize-access-to-managed-identities"></a>Autorizar o acesso a identidades geridas
 
Serviços Cognitivos apoiam a autenticação do Azure Ative Directory (Azure AD) com [identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Identidades geridas para recursos Azure podem autorizar o acesso aos recursos dos Serviços Cognitivos utilizando credenciais AZure AD a partir de aplicações em execução em máquinas virtuais Azure (VMs), aplicações de funções, conjuntos de escala de máquinas virtuais e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação Azure AD, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem.  

### <a name="enable-managed-identities-on-a-vm"></a>Permitir identidades geridas num VM

Antes de utilizar identidades geridas para recursos Azure para autorizar o acesso aos recursos dos Serviços Cognitivos a partir do seu VM, deve ativar identidades geridas para recursos Azure no VM. Para aprender como permitir identidades geridas para recursos Azure, consulte:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
- [CLI do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
- [Modelo Azure Resource Manager](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
- [Bibliotecas de clientes Azure Resource Manager](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

Para obter mais informações sobre identidades geridas, consulte [identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
