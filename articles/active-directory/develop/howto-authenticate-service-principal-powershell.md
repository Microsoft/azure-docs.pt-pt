---
title: Crie uma identidade de aplicativo Azure (PowerShell) | Rio Azure
titleSuffix: Microsoft identity platform
description: Descreve como utilizar o Azure PowerShell para criar uma aplicação do Azure Active Directory e um principal de serviço, e conceder acesso a recursos através do controlo de acesso baseado em funções. Mostra como a autenticar a aplicação com um certificado.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev , devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.date: 02/22/2021
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: b27af53d615fa9c0c46699a52a004098dc46b7b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688540"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Como: Use a Azure PowerShell para criar um principal de serviço com um certificado

Quando tem uma aplicação ou script que precisa de aceder a recursos, pode configurar uma identidade para a aplicação e autenticar a aplicação com as suas próprias credenciais. Esta identidade é conhecida como um principal de serviço. Esta abordagem permite-lhe:

* Atribuir permissões para a identidade da aplicação, que são diferentes das suas própria permissões. Normalmente, estas permissões estão restritas a exatamente aquilo que a aplicação precisa de fazer.
* Utilize um certificado para autenticação ao executar um script automático.

> [!IMPORTANT]
> Em vez de criar um principal de serviço, considere usar identidades geridas para recursos Azure para a sua identidade de aplicação. Se o seu código funciona num serviço que suporta identidades geridas e acede a recursos que suportam a autenticação do Azure Ative Directory (Azure AD), as identidades geridas são uma opção melhor para si. Para saber mais sobre identidades geridas para os recursos da Azure, incluindo quais os serviços que atualmente o suportam, veja [o que é gerida identidades para os recursos do Azure?](../managed-identities-azure-resources/overview.md)

Este artigo mostra como criar um principal de serviço que faz a autenticação com um certificado. Para configurar um principal de serviço com palavra-passe, veja [Criar um principal de serviço do Azure com o Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Para este artigo, tem de ter a [versão mais recente](/powershell/azure/install-az-ps) do PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Permissões obrigatórias

Para completar este artigo, você deve ter permissões suficientes tanto na sua assinatura AZure AD como Azure. Especificamente, você deve ser capaz de criar uma app no AD Azure, e atribuir o principal do serviço a um papel.

A forma mais fácil de verificar se a sua conta tem permissões adequadas é utilizar o portal. Veja [Permissões obrigatórias](howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="assign-the-application-to-a-role"></a>Atribuir a candidatura a uma função
Para aceder aos recursos na sua subscrição, tem de atribuir a aplicação a uma função. Decida qual a função que oferece as permissões certas para o pedido. Para conhecer os papéis disponíveis, consulte as [funções integradas do Azure.](../../role-based-access-control/built-in-roles.md)

Pode definir o âmbito ao nível da subscrição, grupo de recursos ou recurso. As permissões são herdadas para níveis mais baixos de âmbito. Por exemplo, adicionar uma aplicação à função *Reader* para um grupo de recursos significa que pode ler o grupo de recursos e quaisquer recursos que contenha. Para permitir que a aplicação execute ações como reiniciar, iniciar e parar instâncias, selecione a *função Contribuinte.*

## <a name="create-service-principal-with-self-signed-certificate"></a>Criar um principal de serviço com um certificado autoassinado

O exemplo seguinte inclui um cenário simples. Utiliza [o New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) para criar um principal de serviço com um certificado auto-assinado e utiliza [a Assinatura New-AzRole](/powershell/module/az.resources/new-azroleassignment) para atribuir o papel de [Leitor](../../role-based-access-control/built-in-roles.md#reader) ao principal do serviço. A atribuição da função está confinada à sua subscrição do Azure atualmente selecionada. Para selecionar uma subscrição diferente, utilize [o Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> O New-SelfSignedCertificate cmdlet e o módulo PKI não são atualmente suportados no PowerShell Core. 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

O exemplo dorme durante 20 segundos para permitir que o novo diretor de serviço se propague por toda a Azure AD. Se o script não aguardar o tempo suficiente, verá um erro que indica: "{ID} de principal não existe no diretório {DIR-ID}." Para resolver este erro, aguarde um momento e volte a executar o comando **de New-AzRoleAssignment.**

Pode definir o âmbito de atribuição da função para um grupo de recursos específico, utilizando o parâmetro **ResourceGroupName**. Pode definir o âmbito para um recurso específico, utilizando também os parâmetros **ResourceType** e **ResourceName**. 

Se **não tiver o Windows 10 ou o Windows Server 2016,** descarregue o [cmdlet New-SelfSignedCertificateEx](https://www.pkisolutions.com/tools/pspki/New-SelfSignedCertificateEx/) da PKI Solutions. Extraia o respetivo conteúdo e importe o cmdlet de que precisa.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

No script, substitua as duas linhas seguintes para gerar o certificado.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer o certificado através do script do PowerShell automatizado

Sempre que se inscrever como diretor de serviço, forneça a identificação do diretório do inquilino para a sua aplicação AD. Um inquilino é um exemplo de Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

$Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Criar um principal de serviço com um certificado da Autoridade de Certificação

O exemplo seguinte utiliza um certificado emitido por uma Autoridade de Certificação para criar o principal de serviço. A atribuição é confinada à subscrição do Azure especificada. Adiciona o principal do serviço ao papel [de Leitor.](../../role-based-access-control/built-in-roles.md#reader) Se ocorrer um erro durante a atribuição da função, repete a atribuição.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }

 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer o certificado através do script do PowerShell automatizado
Sempre que se inscrever como diretor de serviço, forneça a identificação do diretório do inquilino para a sua aplicação AD. Um inquilino é um exemplo de Azure AD.

```powershell
Param (

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

O ID da aplicação e o ID de inquilino não são sensíveis, pelo que pode incorporá-los diretamente no seu script. Se precisar de obter o ID do inquilino, utilize:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Se precisar de obter o ID da aplicação, utilize:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Alterar credenciais

Para alterar as credenciais de uma aplicação AD, seja por causa de um compromisso de segurança ou de uma expiração credencial, utilize os cmdlets [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) e [New-AzADAppCredential.](/powershell/module/az.resources/new-azadappcredential)

Para remover todas as credenciais para uma aplicação, utilize:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Para adicionar um valor de certificado, crie um certificado autoassinado, conforme explicado neste artigo. Em seguida, utilize:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Depurar

Pode obter os seguintes erros ao criar um principal de serviço:

* **"Authentication_Unauthorized"** ou **"Nenhuma subscrição encontrada no contexto."** - Vê este erro quando a sua conta não tem as [permissões necessárias](#required-permissions) no AD Azure para registar uma aplicação. Normalmente, vê-se este erro quando apenas os utilizadores administrativos do seu Diretório Azure Ative podem registar aplicações, e a sua conta não é administradora. Peça ao seu administrador para atribuí-lo a uma função de administrador ou para permitir que os utilizadores registem aplicações.

* A sua conta **"não tem autorização para executar ações 'Microsoft.Authorization/roleAssignments/write' over scope '/subscriptions/{guid}'."** - Vê este erro quando a sua conta não tem permissões suficientes para atribuir uma função a uma identidade. Peça ao administrador da sua subscrição para adicioná-lo à função Administrador de Acesso dos Utilizadores.

## <a name="next-steps"></a>Passos seguintes

* Para configurar um principal de serviço com palavra-passe, veja [Criar um principal de serviço do Azure com o Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Para obter uma explicação mais detalhada de principais de serviço e de aplicações, veja [Objetos de Aplicações e Objetos de Principais de Serviço](app-objects-and-service-principals.md).
* Para obter mais informações sobre a autenticação Azure AD, consulte [Cenários de Autenticação para Azure AD](./authentication-vs-authorization.md).
* Para obter informações sobre como trabalhar com registos de aplicações utilizando o **Microsoft Graph,** consulte a referência API [de aplicações.](/graph/api/resources/application)
