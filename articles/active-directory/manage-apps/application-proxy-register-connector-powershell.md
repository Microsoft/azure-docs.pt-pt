---
title: Conector de Proxy de aplicação do Azure AD de instalação silenciosa | Documentos da Microsoft
description: Aborda como executar uma instalação autónoma do Azure AD de conector de Proxy de aplicações para fornecer acesso remoto seguro às suas aplicações no local.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb193119186c2cf9e758f8c74f99f18c5fb389b8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58792523"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Criar um script de instalação automática para o conector do Proxy de aplicações do Azure AD

Este tópico ajuda-o a criar um script do Windows PowerShell que permite a instalação automática e o registo do conector de Proxy de aplicações do Azure AD.

Esta funcionalidade é útil quando pretender:

* Instale o conector em servidores do Windows que não têm a interface do usuário ativado, ou que não pode acessar com o ambiente de trabalho remoto.
* Instale e registre muitos conetores ao mesmo tempo.
* Integre a instalação do conector e o registo como parte de outro procedimento.
* Crie uma imagem de servidor padrão que contém os bits de conector, mas não está registrada.

Para o [conector de Proxy de aplicações](application-proxy-connectors.md) funcione, tem de estar registado no diretório do Azure AD com um administrador da aplicação e a palavra-passe. Normalmente esta informação é introduzida durante a instalação de conector numa caixa de diálogo pop-up, mas pode utilizar o PowerShell para automatizar este processo em vez disso.

Há duas etapas para uma instalação autônoma. Em primeiro lugar, instale o conector. Em segundo lugar, registe o conector com o Azure AD. 

## <a name="install-the-connector"></a>Instalar o conector
Utilize os seguintes passos para instalar o conector sem Registro-lo:

1. Abra uma linha de comandos.
2. Execute o seguinte comando, em que o /q significa instalação silencioso. Uma instalação silencioso não pedir-lhe para aceitar o contrato de licença de utilizador final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registar o conector com o Azure AD
Existem dois métodos que pode utilizar para registar o conector:

* Registar o conector usando um objeto de credencial do Windows PowerShell
* Registar o conector através de um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registar o conector usando um objeto de credencial do Windows PowerShell
1. Criar um objeto de credenciais do Windows PowerShell `$cred` que contém um nome de utilizador administrativo e a palavra-passe para o seu diretório. Execute o seguinte comando, substituindo *\<nome de utilizador\>* e  *\<palavra-passe\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Aceda a **conector de Proxy de aplicações do C:\Program Files\Microsoft AAD** e execute o script seguinte com o `$cred` objeto que criou:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registar o conector através de um token criado offline
1. Crie um token offline usando a classe de AuthenticationContext utilizando os valores neste trecho de código ou os cmdlets do PowerShell abaixo:

    **Usando a linguagem c#:**

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
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

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

    **Com o PowerShell:**

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
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
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

2. Assim que tiver o token, crie uma SecureString com o token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Execute o seguinte comando do Windows PowerShell, substituindo \<GUID de inquilino\> com o seu ID de diretório:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Passos Seguintes 
* [Publicar aplicações com o seu próprio nome de domínio](application-proxy-configure-custom-domain.md)
* [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
* [Resolver problemas que possa ter com o Proxy de aplicações](application-proxy-troubleshoot.md)


