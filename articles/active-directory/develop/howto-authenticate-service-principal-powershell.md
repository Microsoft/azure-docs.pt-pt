---
title: Criar a identidade da aplicação Azure com o PowerShell | Microsoft Docs
description: Descreve como utilizar o Azure PowerShell para criar uma aplicação do Azure Active Directory e um principal de serviço, e conceder acesso a recursos através do controlo de acesso baseado em funções. Mostra como a autenticar a aplicação com um certificado.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: tomfitz
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12bfcea70c80929ade656bc5e23f8b95fce44a54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410404"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Como: Utilizar o Azure PowerShell para criar um principal de serviço com um certificado

Quando tem uma aplicação ou script que precisa de aceder a recursos, pode configurar uma identidade para a aplicação e autenticar a aplicação com as suas próprias credenciais. Esta identidade é conhecida como um principal de serviço. Esta abordagem permite-lhe:

* Atribuir permissões para a identidade da aplicação, que são diferentes das suas própria permissões. Normalmente, estas permissões estão restritas a exatamente aquilo que a aplicação precisa de fazer.
* Utilize um certificado para autenticação ao executar um script automático.

> [!IMPORTANT]
> Em vez de criar um principal de serviço, considere a utilização de identidades geridas para recursos do Azure para a sua identidade da aplicação. Se o seu código é executado num serviço que suporta recursos de acessos que suportem a autenticação do Azure Active Directory (Azure AD) e de identidades geridas, identidades geridas são uma opção melhor para. Para saber mais sobre identidades geridas para recursos do Azure, incluindo os serviços atualmente suportam, consulte [o que há de identidades geridas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

Este artigo mostra como criar um principal de serviço que faz a autenticação com um certificado. Para configurar um principal de serviço com palavra-passe, veja [Criar um principal de serviço do Azure com o Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Para este artigo, tem de ter a [versão mais recente](/powershell/azure/install-az-ps) do PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Permissões obrigatórias

Para concluir este artigo, tem de ter permissões suficientes no seu Azure AD e subscrição do Azure. Especificamente, tem de ser capaz de criar uma aplicação no Azure AD e atribuir o principal de serviço a uma função.

A forma mais fácil de verificar se a sua conta tem permissões adequadas é utilizar o portal. Veja [Permissões obrigatórias](howto-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Criar um principal de serviço com um certificado autoassinado

O exemplo seguinte inclui um cenário simples. Ele usa [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) para criar um principal de serviço com um certificado autoassinado e utiliza [New-AzureRmRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para atribuir o [contribuinte](../../role-based-access-control/built-in-roles.md#contributor) função ao principal de serviço. A atribuição da função está confinada à sua subscrição do Azure atualmente selecionada. Para selecionar uma subscrição diferente, utilize [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

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
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

O exemplo permanecerá suspenso durante 20 segundos permitir que algum tempo para o novo serviço principal para propagar ao longo do Azure AD. Se o seu script não tem de aguardar tempo suficiente, verá um erro a indicar: "Principal {ID} não existe no diretório {DIR-ID}." Para resolver este erro, aguarde um momento, em seguida, execute o **New-AzRoleAssignment** novamente o comando.

Pode definir o âmbito de atribuição da função para um grupo de recursos específico, utilizando o parâmetro **ResourceGroupName**. Pode definir o âmbito para um recurso específico, utilizando também os parâmetros **ResourceType** e **ResourceName**. 

Se **não tiver o Windows 10 ou o Windows Server 2016**, precisa de descarregar o [Gerador de certificados autoassinados](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) a partir do Microsoft Script Center. Extraia o respetivo conteúdo e importe o cmdlet de que precisa.

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

Sempre que iniciar sessão como um principal de serviço, tem de fornecer o ID de inquilino do diretório para a sua aplicação AD. Um inquilino é uma instância do Azure AD.

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

O exemplo seguinte utiliza um certificado emitido por uma Autoridade de Certificação para criar o principal de serviço. A atribuição é confinada à subscrição do Azure especificada. Adiciona o principal de serviço à função [Contribuidor](../../role-based-access-control/built-in-roles.md#contributor). Se ocorrer um erro durante a atribuição da função, repete a atribuição.

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
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer o certificado através do script do PowerShell automatizado
Sempre que iniciar sessão como um principal de serviço, tem de fornecer o ID de inquilino do diretório para a sua aplicação AD. Um inquilino é uma instância do Azure AD.

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

Para alterar as credenciais para uma aplicação do AD, devido a um comprometimento de segurança ou expiração de credenciais, utilize o [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) e [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) cmdlets.

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

* **"Authentication_Unauthorized"** ou **"Nenhuma subscrição encontrada no contexto."** -Vir este erro quando a sua conta não tem o [permissões obrigatórias](#required-permissions) no Azure AD para registar uma aplicação. Normalmente, se este erro ocorrer quando apenas os utilizadores de administração do Azure Active Directory podem registar aplicações e a sua conta não é um administrador. Peça ao seu administrador para lhe atribuir uma função de administrador ou para permitir que os utilizadores registem aplicações.

* Sua conta **"não tem autorização para realizar a ação 'Microsoft.Authorization/roleAssignments/write' no âmbito"/ subscrições / {guid}"."**  -Vir este erro quando a sua conta não tem permissões suficientes para atribuir uma função para uma identidade. Peça ao administrador da sua subscrição para adicioná-lo à função Administrador de Acesso dos Utilizadores.

## <a name="next-steps"></a>Passos Seguintes

* Para configurar um principal de serviço com palavra-passe, veja [Criar um principal de serviço do Azure com o Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Para obter passos detalhados sobre como integrar uma aplicação no Azure para gerir recursos, veja [Guia para programadores para autorização com a API do Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Para obter uma explicação mais detalhada de principais de serviço e de aplicações, veja [Objetos de Aplicações e Objetos de Principais de Serviço](app-objects-and-service-principals.md).
* Para obter mais informações sobre a autenticação do Azure AD, consulte [cenários de autenticação do Azure AD](authentication-scenarios.md).
