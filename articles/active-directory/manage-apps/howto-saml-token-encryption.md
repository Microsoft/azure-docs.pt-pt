---
title: Encriptação de tokens SAML no Azure Active Directory
description: Saiba como configurar a encriptação de tokens SAML de diretório Active Directory do Azure.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: celested
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a7a12cf677661c36a42df36a8fdc6b8f4a8ef75
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669895"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption-preview"></a>Como: Configurar a encriptação de tokens SAML do Azure AD (pré-visualização)

> [!NOTE]
> Encriptação de tokens é uma funcionalidade do Azure Active Directory (Azure AD) premium. Para saber mais sobre o Azure AD edições, as funcionalidades e preços, veja [preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

Encriptação de tokens SAML permite o uso de asserções de SAML encriptados com uma aplicação que o suporte. Quando configurado para uma aplicação, o Azure AD vai encriptar as asserções SAML emite para o aplicativo usando a chave pública obtida a partir de um certificado armazenado no Azure AD. A aplicação tem de utilizar a chave privada correspondente para descriptografar o token antes de poder ser utilizada como prova de autenticação para o utilizador com sessão iniciada.

Encriptar as asserções SAML entre o Azure AD e o aplicativo fornece segurança adicional que o conteúdo do token não pode ser intercetado e dados pessoais ou empresariais comprometidos.

Mesmo sem encriptação de tokens, os tokens SAML do Azure AD são nunca transmitidos na rede de forma. Azure AD requer trocas de token de solicitação/resposta seja realizada através de canais do HTTPS/TLS encriptados para que as comunicações entre o IDP, browser e aplicativo ocorrem através de ligações encriptadas. Considere o valor de encriptação de tokens para a sua situação em comparação com a sobrecarga de gerenciamento de certificados adicionais.   

Para configurar a encriptação de tokens, terá de carregar um ficheiro de certificado X.509 que contém a chave pública para o objeto de aplicação do Azure AD que representa o aplicativo. Para obter o certificado X.509, pode baixá-lo da aplicação em si, ou get-lo a partir do fornecedor do aplicativo em casos em que o fornecedor do aplicativo fornece as chaves de encriptação ou em casos onde a aplicação espera que forneça uma chave privada, pode ser criado com as ferramentas de criptografia, a parte de chave privada carregada para o arquivo de chaves da aplicação e o certificado de chave público correspondente é carregado para o Azure AD.

Azure AD utiliza AES-256 para encriptar os dados de asserção de SAML.

## <a name="configure-saml-token-encryption"></a>Configurar a encriptação de tokens SAML

Para configurar a encriptação de tokens SAML, siga estes passos:

1. Obter um certificado de chave pública que corresponde a uma chave privada que está configurada no aplicativo.

    Crie um par de chaves assimétricas para utilizar para a encriptação. Em alternativa, se o aplicativo fornece uma chave pública a utilizar para a encriptação, siga instruções do aplicativo para transferir o certificado X.509.

    A chave pública deve ser armazenada num arquivo de certificados X.509 no formato. cer.

    Se o aplicativo usa uma chave que criou para a sua instância, siga as instruções fornecidas pela sua aplicação para instalar a chave privada que a aplicação irá utilizar para desencriptar tokens no seu inquilino do Azure AD.

1. Adicione o certificado para a configuração da aplicação no Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Para configurar a encriptação de tokens no portal do Azure

Pode adicionar o certificado público a configuração da sua aplicação no portal do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

1. Vá para o **do Azure Active Directory > aplicações empresariais** painel e, em seguida, selecione a aplicação que pretende configurar a encriptação de tokens para.

1. Na página do aplicativo, selecione **encryption do Token**.

    ![Opção de encriptação de tokens no portal do Azure](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > O **encriptação de Token** opção só está disponível para aplicações SAML que foram definidas da **aplicações empresariais** painel no portal do Azure, a partir da Galeria de aplicações ou uma Aplicações externas à galeria. Para outras aplicações, esta opção de menu está desativada. Para aplicativos registado através do **registos das aplicações** experiência no portal do Azure, pode configurar a encriptação para o manifesto de tokens SAML com a aplicação, através do Microsoft Graph ou através do PowerShell.

1. Sobre o **encriptação de Token** página, selecione **importar certificado** para importar o ficheiro. cer que contém o certificado X.509 público.

    ![Importar o ficheiro. cer que contém o certificado X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Depois do certificado é importado e a chave privada está configurada para utilização no lado do aplicativo, ativar a encriptação, selecionando o **...**  seguinte para o estado de thumbprint e, em seguida, selecione **ativar a encriptação de tokens** entre as opções no menu pendente.

1. Selecione **Sim** para confirmar a ativação do certificado de encriptação de tokens.

1. Confirme que as asserções SAML emitidas para a aplicação são encriptadas.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Para desativar a encriptação de tokens no portal do Azure

1. No portal do Azure, aceda a **do Azure Active Directory > aplicações empresariais**e, em seguida, selecione a aplicação que tem a encriptação de token SAML ativada.

1. Na página do aplicativo, selecione **encryption do Token**, localize o certificado e, em seguida, selecione o **...**  opção para mostrar o menu suspenso.

1. Selecione **desativar a encriptação de tokens**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Configurar a encriptação de tokens SAML com o Graph API, o PowerShell ou o manifesto da aplicação

Certificados de encriptação são armazenados no objeto aplicação no Azure AD com um `encrypt` etiqueta de utilização. Pode configurar vários certificados de encriptação e aquele que está ativa para encriptação de tokens é identificado pelo `tokenEncryptionKeyID` atributo.

Terá de ID de objeto do aplicativo para configurar a encriptação de tokens com API do Microsoft Graph ou o PowerShell. Pode encontrar este valor através de programação, ou ao aceder ao aplicativo **propriedades** página no portal do Azure e observando a **ID de objeto** valor.

Quando configura um keyCredential usando gráfico, PowerShell, ou no manifesto do aplicativo, deve gerar um GUID a utilizar para o keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Para configurar a encriptação de tokens com o Microsoft Graph

1. Atualizar a aplicação `keyCredentials` com um certificado X.509 para encriptação. O exemplo seguinte mostra como fazer isso.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identifica o certificado de encriptação ativo para encriptação de tokens. O exemplo seguinte mostra como fazer isso.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Para configurar a encriptação de tokens com o PowerShell

Esta funcionalidade estará disponível brevemente. 

<!--
1. Use the latest Azure AD PowerShell module to connect to your tenant.

1. Set the token encryption settings using the **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** command.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials “<KeyCredentialsObject>”  -TokenEncryptionKeyId <keyID>
    ```

1. Read the token encryption settings using the following commands.

    ```
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```
-->

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Para configurar a encriptação de tokens usando o manifesto do aplicativo

1. A partir do portal do Azure, aceda a **do Azure Active Directory > registos das aplicações**.

1. Selecione **todas as aplicações** na lista pendente para mostrar todas as aplicações e, em seguida, selecione a aplicação de enterprise que pretende configurar.

1. Na página do aplicativo, selecione **manifesto** para editar o [manifesto do aplicativo](../develop/reference-app-manifest.md).

1. Defina o valor para o `tokenEncryptionKeyId` atributo.

    A exemplo a seguir mostra um manifesto de aplicação configurado com dois certificados de encriptação e com a segunda selecionado como o Active Directory usando o tokenEnryptionKeyId.

    ```
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Passos Seguintes

* Descubra [como o Azure AD utiliza o protocolo SAML](../develop/active-directory-saml-protocol-reference.md)
* Saiba o formato, características de segurança e conteúdo de [tokens SAML no Azure AD](../develop/reference-saml-tokens.md)
