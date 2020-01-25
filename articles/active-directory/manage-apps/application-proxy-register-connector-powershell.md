---
title: Instale silenciosamente o conector de procuração da Aplicação AD Azure  Microsoft Docs
description: Cobre como realizar uma instalação não acompanhada do Conector proxy de aplicação da Aplicação AD Azure para fornecer acesso remoto seguro às suas aplicações no local.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca40e9717394690374f5ca289a69e5c22551eb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712002"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Criar um script de instalação sem supervisão para o conector proxy de aplicação ad azure

Este tópico ajuda-o a criar um script Windows PowerShell que permite a instalação e registo sem supervisão do seu conector Proxy de Aplicação AD Azure.

Esta capacidade é útil quando se quer:

* Instale o conector nos servidores do Windows que não tenham interface de utilizador ativada ou que não possa aceder com o Remote Desktop.
* Instale e registe muitos conectores ao mesmo tempo.
* Integrar a instalação e o registo do conector como parte de outro procedimento.
* Crie uma imagem padrão do servidor que contenha as brocas do conector, mas que não esteja registada.

Para que o [conector Proxy](application-proxy-connectors.md) de aplicação funcione, tem de ser registado no seu diretório Azure AD utilizando um administrador de aplicação e uma palavra-passe. Normalmente, esta informação é introduzida durante a instalação do Connector numa caixa de diálogo pop-up, mas pode utilizar o PowerShell para automatizar este processo.

Há dois passos para uma instalação sem supervisão. Primeiro, instale o conector. Em segundo lugar, registe o conector com a AD Azure. 

## <a name="install-the-connector"></a>Instalar o conector
Utilize os seguintes passos para instalar o conector sem o registar:

1. Abra uma linha de comandos.
2. Executar o seguinte comando, no qual o /q significa instalação silenciosa. Uma instalação tranquila não o leva a aceitar o Contrato de Licença de Utilizador Final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registe o conector com a Azure AD
Existem dois métodos que pode utilizar para registar o conector:

* Registe o conector utilizando um objeto credencial Windows PowerShell
* Registe o conector utilizando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registe o conector utilizando um objeto credencial Windows PowerShell
1. Crie um objeto de credenciais Do Windows PowerShell `$cred` que contenha um nome de utilizador administrativo e uma palavra-passe para o seu diretório. Executar o seguinte comando, substituindo *\<nome de utilizador\>* e\<*palavra-passe\>:*
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Vá ao **C:\Program Files\Microsoft AAD App Proxy Connector** e execute o seguinte script usando o objeto `$cred` que criou:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registe o conector utilizando um token criado offline
1. Crie um token offline utilizando a classe AuthenticationContext utilizando os valores deste código ou cmdlets PowerShell abaixo:

    **Utilizando: C#**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("https://login.microsoftonline.com/common/oauth2/nativeclient");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }

    **Utilização do PowerShell:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "https://login.microsoftonline.com/common/oauth2/nativeclient" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. Assim que tiver o símbolo, crie um SecureString utilizando o símbolo:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Execute o seguinte comando Windows PowerShell, substituindo \<inquilino GUID\> pelo seu ID de diretório:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Passos seguintes 
* [Publicar aplicações com o seu próprio nome de domínio](application-proxy-configure-custom-domain.md)
* [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
* [Resolver problemas que possa ter com o Proxy de aplicações](application-proxy-troubleshoot.md)


