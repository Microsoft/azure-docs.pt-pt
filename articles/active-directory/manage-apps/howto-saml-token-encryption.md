---
title: Encriptação simbólica SAML no Diretório Ativo Azure
description: Saiba como configurar a encriptação de token SAML do Diretório Ativo Azure.
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
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0082d841faf22745e609d38444f4a97553b3c867
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365871"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Como: Configure Azure AD AD Token encriptação

> [!NOTE]
> A encriptação token é uma funcionalidade premium azure Ative Directory (Azure AD). Para saber mais sobre edições, funcionalidades e preços da Azure AD, consulte o preço da [AD Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

A encriptação token SAML permite o uso de afirmações SAML encriptadas com uma aplicação que a suporta. Quando configurado para uma aplicação, a Azure AD encriptará as afirmações da SAML que emite para essa aplicação utilizando a chave pública obtida a partir de um certificado armazenado em Azure AD. A aplicação deve utilizar a chave privada correspondente para desencriptar o símbolo antes de poder ser utilizado como prova de autenticação para o utilizador assinado.

Encriptar as afirmações da SAML entre a AD Azure e a aplicação fornece garantias adicionais de que o conteúdo do token não pode ser intercetado, e os dados pessoais ou corporativos comprometidos.

Mesmo sem encriptação simbólica, as fichas SAML da Azure AD nunca são transmitidas na rede de forma clara. A Azure AD exige que as trocas de pedidos/respostas simbólicas ocorram sobre os canais HTTPS/TLS encriptados para que as comunicações entre o IDP, browser e aplicação ocorram sobre links encriptados. Considere o valor da encriptação simbólica para a sua situação em comparação com a sobrecarga de gestão de certificados adicionais.   

Para configurar a encriptação simbólica, é necessário fazer o upload de um ficheiro de certificado X.509 que contenha a chave pública do objeto de aplicação Azure AD que representa a aplicação. Para obter o certificado X.509, pode descarregá-lo a partir da própria aplicação, ou obtê-lo do fornecedor de aplicações nos casos em que o fornecedor de aplicações fornece chaves de encriptação ou nos casos em que a aplicação espera que forneça uma chave privada, pode ser criado usando ferramentas de criptografia, a parte chave privada enviada para a loja chave da aplicação e o certificado de chave pública correspondente enviado para AD Azure.

A Azure AD utiliza o AES-256 para encriptar os dados de afirmação do SAML.

## <a name="configure-saml-token-encryption"></a>Configurar encriptação token SAML

Para configurar a encriptação token SAML, siga estes passos:

1. Obtenha um certificado de chave pública que corresponda a uma chave privada que está configurada na aplicação.

    Crie um par de chaves assimétricas para usar para encriptação. Ou, se a aplicação fornecer uma chave pública para a encriptação, siga as instruções da aplicação para descarregar o certificado X.509.

    A chave pública deve ser armazenada num ficheiro de certificado X.509 em formato .cer.

    Se a aplicação utilizar uma chave que cria para a sua instância, siga as instruções fornecidas pela sua aplicação para instalar a chave privada que a aplicação utilizará para desencriptar fichas do seu inquilino Azure AD.

1. Adicione o certificado à configuração da aplicação em Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Para configurar encriptação simbólica no portal Azure

Pode adicionar o cert público à sua configuração de aplicação dentro do portal Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

1. Vá à lâmina de **aplicações azure Ative Directory > Enterprise** e, em seguida, selecione a aplicação para a que pretende configurar encriptação simbólica.

1. Na página da aplicação, selecione **encriptação Token**.

    ![Opção de encriptação token no portal Azure](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > A opção de **encriptação Token** só está disponível para aplicações SAML que tenham sido criadas a partir da lâmina de **aplicações enterprise** no portal Azure, seja a partir da Galeria de Aplicações ou de uma aplicação Não-Galeria. Para outras aplicações, esta opção de menu é desativada. Para aplicações registadas através da experiência de **registos** da App no portal Azure, pode configurar a encriptação para tokens SAML utilizando o manifesto de aplicação, através do Microsoft Graph ou através do PowerShell.

1. Na página de **encriptação Token,** selecione Certificado de **Importação** para importar o ficheiro .cer que contém o seu certificado público X.509.

    ![Importar o ficheiro .cer que contém o certificado X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Uma vez importado o certificado, e a chave privada é configurada para utilização no lado da aplicação, ativar a encriptação selecionando o **...** ao lado do estado da impressão digital e, em seguida, selecione **encriptação token Activate** a partir das opções no menu dropdown.

1. Selecione **Sim** para confirmar a ativação do certificado de encriptação token.

1. Confirme que as afirmações do SAML emitidas para a aplicação estão encriptadas.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Para desativar encriptação simbólica no portal Azure

1. No portal Azure, vá a **aplicações Azure Ative Directory > Enterprise**e, em seguida, selecione a aplicação que tem encriptação token SAML ativada.

1. Na página da aplicação, selecione **encriptação Token,** encontre o certificado e, em seguida, selecione a **opção ...** para mostrar o menu de dropdown.

1. **Selecione Deactivate encriptação token**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Configure encriptação token SAML usando gráfico API, PowerShell ou manifesto de aplicação

Os certificados de encriptação são armazenados no objeto de aplicação em Azure AD com uma etiqueta de utilização `encrypt`. Pode configurar vários certificados de encriptação e o que está ativo para encriptar fichas é identificado pelo atributo `tokenEncryptionKeyID`.

Vai precisar do ID do objeto da aplicação para configurar encriptação simbólica utilizando a Microsoft Graph API ou powerShell. Pode encontrar este valor programáticamente, ou indo para a página **Propriedades** da aplicação no portal Azure e notando o valor de ID do **Objeto.**

Quando configurar uma teclaCredential utilizando o Graph, powerShell ou no manifesto de aplicação, deve gerar um GUID para utilizar para o keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Para configurar encriptação simbólica usando o Microsoft Graph

1. Atualize o `keyCredentials` da aplicação com um certificado X.509 para encriptação. O exemplo que se segue mostra como fazê-lo.

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

1. Identifique o certificado de encriptação que está ativo para encriptar fichas. O exemplo que se segue mostra como fazê-lo.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Para configurar encriptação simbólica usando powerShell

1. Utilize o mais recente módulo Azure AD PowerShell para se ligar ao seu inquilino.

1. Detete as definições de encriptação do símbolo utilizando o comando **[Set-AzureApplication.](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Leia as definições de encriptação do símbolo utilizando os seguintes comandos.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Para configurar encriptação simbólica usando o manifesto de aplicação

1. A partir do portal Azure, vá para as inscrições do **Azure Ative Directory > App.**

1. Selecione **Todas as aplicações** a partir do dropdown para mostrar todas as aplicações e, em seguida, selecione a aplicação da empresa que pretende configurar.

1. Na página da aplicação, selecione **Manifesto** para editar o manifesto de [inscrição](../develop/reference-app-manifest.md).

1. Detete o valor para o atributo `tokenEncryptionKeyId`.

    O exemplo seguinte mostra um manifesto de aplicação configurado com dois certificados de encriptação, e com o segundo selecionado como o ativo usando o tokenEnryptionKeyId.

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

* Saiba [como a Azure AD utiliza o protocolo SAML](../develop/active-directory-saml-protocol-reference.md)
* Conheça o formato, as características de segurança e o conteúdo das [fichas SAML em Azure AD](../develop/reference-saml-tokens.md)