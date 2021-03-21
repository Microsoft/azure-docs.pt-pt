---
title: Instalação silenciosa Azure AD App Proxy conector | Microsoft Docs
description: Cobre como realizar uma instalação não acompanhada do Azure AD Application Proxy Connector para fornecer acesso remoto seguro às suas aplicações no local.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac442e895850de04bedf7673ffe267ac8697d26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258155"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Criar um script de instalação não acompanhado para o conector proxy aplicação AD Azure

Este tópico ajuda-o a criar um script Windows PowerShell que permite a instalação e registo não acompanhados para o conector Proxy da aplicação AD Azure.

Esta capacidade é útil quando deseja:

* Instale o conector em servidores windows que não tenham a interface do utilizador ativada ou que não possa aceder com o Ambiente de Trabalho Remoto.
* Instale e registe muitos conectores ao mesmo tempo.
* Integre a instalação e o registo do conector como parte de outro procedimento.
* Crie uma imagem padrão do servidor que contenha os bits do conector, mas não está registada.

Para que o [conector Proxy de aplicação](application-proxy-connectors.md) funcione, tem de ser registado no seu diretório AZure AD utilizando um administrador de aplicação e senha. Normalmente, esta informação é inserida durante a instalação do Conector numa caixa de diálogo pop-up, mas pode utilizar o PowerShell para automatizar este processo.

Há dois passos para uma instalação não acompanhada. Primeiro, instale o conector. Em segundo lugar, registe o conector com a Azure AD.

> [!IMPORTANT]
> Se estiver a instalar o conector para a nuvem do Governo Azure, reveja os [passos de pré-requisitos](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) e [de instalação](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Isto requer permitir o acesso a um conjunto diferente de URLs e um parâmetro adicional para executar a instalação.

## <a name="install-the-connector"></a>Instale o conector
Utilize os seguintes passos para instalar o conector sem o registar:

1. Abra uma linha de comandos.
2. Executar o seguinte comando, no qual o /q significa instalação silenciosa. Uma instalação silenciosa não o leva a aceitar o Contrato de Licença End-User.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Registar o conector com Azure AD
Existem dois métodos que pode utilizar para registar o conector:

* Registe o conector utilizando um objeto credencial Windows PowerShell
* Registe o conector utilizando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registe o conector utilizando um objeto credencial Windows PowerShell
1. Crie um objeto de credenciais Windows PowerShell `$cred` que contenha um nome de utilizador administrativo e uma palavra-passe para o seu diretório. Executar o seguinte comando, substituição *\<username\>* *\<password\>* e:

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. Vá a **C:\Program Files\Microsoft AAD App Proxy Connector** e execute o seguinte script usando o `$cred` objeto que criou:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Registe o conector utilizando um token criado offline
1. Crie um token offline utilizando a classe AuthenticationContext utilizando os valores deste corte de código ou cmdlets PowerShell abaixo:

   **Usando C#:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **Utilizando a PowerShell:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. Assim que tiver o token, crie um SecureString utilizando o token:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Executar o seguinte comando Windows PowerShell, substituindo \<tenant GUID\> pelo seu iD do diretório:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Passos seguintes
* [Publicar aplicações com o seu próprio nome de domínio](application-proxy-configure-custom-domain.md)
* [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
* [Resolver problemas com o Proxy da Aplicação](application-proxy-troubleshoot.md)