---
title: Criptografia de token SAML no Azure Active Directory
description: Saiba como configurar Azure Active Directory criptografia de token SAML.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eafd209073b36265d24dbad4a66b3870d8f593db
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73148638"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption-preview"></a>Como configurar a criptografia de token SAML do Azure AD (versão prévia)

> [!NOTE]
> A criptografia de token é um recurso do Azure Active Directory (Azure AD) Premium. Para saber mais sobre as edições, os recursos e os preços do Azure AD, confira [preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

A criptografia de token SAML permite o uso de asserções SAML criptografadas com um aplicativo que dá suporte a ela. Quando configurado para um aplicativo, o Azure AD criptografará as asserções SAML que ele emite para o aplicativo usando a chave pública Obtida de um certificado armazenado no Azure AD. O aplicativo deve usar a chave privada correspondente para descriptografar o token antes que ele possa ser usado como evidência de autenticação para o usuário conectado.

Criptografar as asserções SAML entre o Azure AD e o aplicativo fornece garantia adicional de que o conteúdo do token não pode ser interceptado e dados pessoais ou corporativos comprometidos.

Mesmo sem a criptografia de token, os tokens SAML do Azure AD nunca são passados na rede em claro. O Azure AD requer trocas de solicitação/resposta de token para ocorrer em canais HTTPS/TLS criptografados para que as comunicações entre o IDP, o navegador e o aplicativo ocorram por meio de Links criptografados. Considere o valor de criptografia de token para sua situação em comparação com a sobrecarga de gerenciamento de certificados adicionais.   

Para configurar a criptografia de token, você precisa carregar um arquivo de certificado X. 509 que contém a chave pública para o objeto de aplicativo do Azure AD que representa o aplicativo. Para obter o certificado X. 509, você pode baixá-lo do próprio aplicativo ou obtê-lo do fornecedor do aplicativo em casos em que o fornecedor do aplicativo fornece chaves de criptografia ou em casos em que o aplicativo espera que você forneça uma chave privada, pode ser criados usando as ferramentas de criptografia, a parte da chave privada carregada no repositório de chaves do aplicativo e o certificado de chave pública correspondente carregado no Azure AD.

O Azure AD usa o AES-256 para criptografar os dados de Asserção SAML.

## <a name="configure-saml-token-encryption"></a>Configurar a criptografia de token SAML

Para configurar a criptografia de token SAML, siga estas etapas:

1. Obtenha um certificado de chave pública que corresponda a uma chave privada configurada no aplicativo.

    Crie um par de chaves assimétricas para usar na criptografia. Ou, se o aplicativo fornecer uma chave pública a ser usada para criptografia, siga as instruções do aplicativo para baixar o certificado X. 509.

    A chave pública deve ser armazenada em um arquivo de certificado X. 509 no formato. cer.

    Se o aplicativo usar uma chave que você cria para sua instância, siga as instruções fornecidas pelo seu aplicativo para instalar a chave privada que o aplicativo usará para descriptografar tokens do seu locatário do Azure AD.

1. Adicione o certificado à configuração do aplicativo no Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Para configurar a criptografia de token no portal do Azure

Você pode adicionar o certificado público à configuração do aplicativo dentro do portal do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

1. Vá para a folha **Azure Active Directory > aplicativos empresariais** e selecione o aplicativo para o qual você deseja configurar a criptografia de token.

1. Na página do aplicativo, selecione **criptografia de token**.

    ![Opção de criptografia de token no portal do Azure](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > A opção de **criptografia de token** só está disponível para aplicativos SAML que foram configurados na folha **aplicativos empresariais** na portal do Azure, seja na Galeria de aplicativos ou em um aplicativo inexistente na galeria. Para outros aplicativos, essa opção de menu é desabilitada. Para aplicativos registrados por meio da experiência de **registros de aplicativo** no portal do Azure, você pode configurar a criptografia para tokens SAML usando o manifesto do aplicativo, por meio de Microsoft Graph ou por meio do PowerShell.

1. Na página **criptografia de token** , selecione **importar certificado** para importar o arquivo. cer que contém seu certificado X. 509 público.

    ![Importe o arquivo. cer que contém o certificado X. 509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Depois que o certificado for importado e a chave privada estiver configurada para uso no lado do aplicativo, ative a criptografia selecionando o **...** ao lado do status da impressão digital e, em seguida, selecione **Ativar criptografia de token** nas opções do menu suspenso.

1. Selecione **Sim** para confirmar a ativação do certificado de criptografia de token.

1. Confirme se as asserções SAML emitidas para o aplicativo estão criptografadas.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Para desativar a criptografia de token no portal do Azure

1. Na portal do Azure, vá para **Azure Active Directory > aplicativos empresariais**e, em seguida, selecione o aplicativo que tem a criptografia de token SAML habilitada.

1. Na página do aplicativo, selecione **criptografia de token**, localize o certificado e, em seguida, selecione a opção **...** para mostrar o menu suspenso.

1. Selecione **desativar criptografia de token**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Configurar a criptografia de token SAML usando API do Graph, PowerShell ou manifesto de aplicativo

Os certificados de criptografia são armazenados no objeto de aplicativo no Azure AD com uma marca de uso `encrypt`. Você pode configurar vários certificados de criptografia e aquele que está ativo para criptografar tokens é identificado pelo atributo `tokenEncryptionKeyID`.

Você precisará da ID de objeto do aplicativo para configurar a criptografia de token usando Microsoft Graph API ou o PowerShell. Você pode encontrar esse valor programaticamente ou acessando a página de **Propriedades** do aplicativo no portal do Azure e observando o valor da **ID de objeto** .

Quando você configura uma keycredential usando o Graph, o PowerShell ou no manifesto do aplicativo, você deve gerar um GUID a ser usado para o keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Para configurar a criptografia de token usando Microsoft Graph

1. Atualize o `keyCredentials` do aplicativo com um certificado X. 509 para criptografia. O exemplo a seguir mostra como fazer isso.

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

1. Identifique o certificado de criptografia que está ativo para criptografar tokens. O exemplo a seguir mostra como fazer isso.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Para configurar a criptografia de token usando o PowerShell

Essa funcionalidade estará disponível em breve. 

<!--
1. Use the latest Azure AD PowerShell module to connect to your tenant.

1. Set the token encryption settings using the **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** command.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Read the token encryption settings using the following commands.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

-->

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Para configurar a criptografia de token usando o manifesto do aplicativo

1. Na portal do Azure, vá para **Azure Active Directory > registros de aplicativo**.

1. Selecione **todos os aplicativos** na lista suspensa para mostrar todos os aplicativos e, em seguida, selecione o aplicativo empresarial que você deseja configurar.

1. Na página do aplicativo, selecione **manifesto** para editar o [manifesto do aplicativo](../develop/reference-app-manifest.md).

1. Defina o valor para o atributo `tokenEncryptionKeyId`.

    O exemplo a seguir mostra um manifesto de aplicativo configurado com dois certificados de criptografia e com o segundo selecionado como o ativo usando o tokenEnryptionKeyId.

    ```json
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

## <a name="next-steps"></a>Passos seguintes

* Descubra [como o AD do Azure usa o protocolo SAML](../develop/active-directory-saml-protocol-reference.md)
* Aprenda o formato, as características de segurança e o conteúdo de [tokens SAML no Azure ad](../develop/reference-saml-tokens.md)