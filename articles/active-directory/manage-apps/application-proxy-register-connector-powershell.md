---
title: Silent instalar conector Proxy app Ad App / Microsoft Docs
description: Cobre como realizar uma instalação não acompanhada do Azure AD Application Proxy Connector para fornecer acesso remoto seguro às suas aplicações no local.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
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
ms.openlocfilehash: 0b959649074e68d50a266f05841ce4c87e2b3e20
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760019"
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

## <a name="install-the-connector"></a>Instale o conector
Utilize os seguintes passos para instalar o conector sem o registar:

1. Abra uma linha de comandos.
2. Executar o seguinte comando, no qual o /q significa instalação silenciosa. Uma instalação silenciosa não o leva a aceitar o Contrato de Licença do Utilizador Final.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registar o conector com Azure AD
Existem dois métodos que pode utilizar para registar o conector:

* Registe o conector utilizando um objeto credencial Windows PowerShell
* Registe o conector utilizando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registe o conector utilizando um objeto credencial Windows PowerShell
1. Crie um objeto de credenciais Windows PowerShell `$cred` que contenha um nome de utilizador administrativo e uma palavra-passe para o seu diretório. Executar o seguinte comando, substituição *\<username\>* *\<password\>* e:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Vá a **C:\Program Files\Microsoft AAD App Proxy Connector** e execute o seguinte script usando o `$cred` objeto que criou:

        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registe o conector utilizando um token criado offline
1. Crie um token offline utilizando a classe AuthenticationContext utilizando os valores deste corte de código ou cmdlets PowerShell abaixo:

    **Usando C#:**

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

    **Utilizando a PowerShell:**

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

2. Assim que tiver o token, crie um SecureString utilizando o token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Executar o seguinte comando Windows PowerShell, substituindo \<tenant GUID\> pelo seu iD do diretório:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Passos seguintes
* [Publicar aplicações com o seu próprio nome de domínio](application-proxy-configure-custom-domain.md)
* [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md)
* [Resolver problemas com o Proxy da Aplicação](application-proxy-troubleshoot.md)
