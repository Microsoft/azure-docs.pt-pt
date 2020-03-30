---
title: Proteja um serviço repousante no seu Azure AD B2C
titleSuffix: Azure AD B2C
description: Proteja as suas trocas personalizadas de reclamações REST API no seu Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aa2e2fb4eb6e269f45494db6d87eef40182971a2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346932"
---
# <a name="secure-your-restful-services"></a>Proteja os seus serviços RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao integrar uma API REST dentro de uma viagem de utilizador Azure AD AD B2C, deve proteger o seu ponto final REST API com autenticação. Isto garante que apenas serviços que possuam credenciais adequadas, como o Azure AD B2C, podem fazer chamadas para o seu ponto final REST API.

Saiba como integrar uma API REST dentro da sua viagem de utilizador Azure AD AD B2C na entrada do [utilizador validado](custom-policy-rest-api-claims-validation.md) e [adicione a API rest a alegações de troca de artigos](custom-policy-rest-api-claims-exchange.md) de políticas personalizadas.

Este artigo explorará como garantir a sua API REST com certificado básico http, certificado de cliente ou autenticação OAuth2. 

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos num dos seguintes guias 'Como fazer':

- Integre as trocas de reclamações rest [API na sua viagem de utilizador Azure AD AD B2C para validar a entrada do utilizador](custom-policy-rest-api-claims-validation.md).
- [Adicionar rest API reclama trocas de créditos a políticas personalizadas](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Autenticação básica http

A autenticação básica HTTP é definida no [RFC 2617](https://tools.ietf.org/html/rfc2617). A autenticação básica funciona da seguinte forma: Azure AD B2C envia um pedido HTTP com as credenciais do cliente no cabeçalho de Autorização. As credenciais são formatadas como a cadeia codificada base64 "name:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Adicione o nome de utilizador rest API e as chaves da política de passwords

Para configurar um perfil técnico REST API com autenticação básica HTTP, crie as seguintes teclas criptográficas para armazenar o nome de utilizador e a palavra-passe:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
1. Selecione **Teclas de política**e, em seguida, selecione **Adicionar**.
1. Para **opções,** selecione **Manual**.
1. Para **nome**, **escreva RestApiUsername**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Secreta,** introduza o nome de utilizador REST API.
1. Para **a utilização da chave,** selecione **Encriptação**.
1. Selecione **Criar**.
1. Selecione **as Teclas de Política** novamente.
1. Selecione **Adicionar**.
1. Para **opções,** selecione **Manual**.
1. Para **nome**, **escreva RestApiPassword**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Secreta,** introduza a senha REST API.
1. Para **a utilização da chave,** selecione **Encriptação**.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Configure o seu perfil técnico REST API para utilizar a autenticação básica HTTP

Depois de criar as chaves necessárias, configure os metadados de perfil técnico REST API para fazer referência às credenciais.

1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico rest API. Por `REST-ValidateProfile`exemplo, `REST-GetProfile`ou .
1. Localize `<Metadata>` o elemento.
1. Mude o Tipo `Basic`de *Autenticação* para .
1. Alterar a *AllowInsecureAuthInProduction* para `false`.
1. Imediatamente após o `</Metadata>` elemento de fecho, adicione o seguinte corte XML:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Segue-se um exemplo de um perfil técnico RESTful configurado com autenticação básica HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Autenticação de certificado de cliente HTTPS

A autenticação do certificado de cliente é uma autenticação mútua baseada em certificados, onde o cliente, Azure AD B2C, fornece o seu certificado de cliente ao servidor para provar a sua identidade. Isto acontece como parte do aperto de mão da SSL. Apenas os serviços que possuem certificados adequados, como o Azure AD B2C, podem aceder ao seu serviço REST API. O certificado de cliente é um certificado digital X.509. Em ambientes de produção, deve ser assinado por uma autoridade de certificados.

### <a name="prepare-a-self-signed-certificate-optional"></a>Preparar um certificado auto-assinado (opcional)

Para ambientes não produtivos, se ainda não tiver um certificado, pode usar um certificado auto-assinado. No Windows, pode utilizar o cmdlet [New SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) da PowerShell para gerar um certificado.

1. Execute este comando PowerShell para gerar um certificado auto-assinado. Modifique `-Subject` o argumento conforme apropriado para a sua candidatura e nome de inquilino Azure AD B2C. Também pode ajustar `-NotAfter` a data para especificar uma expiração diferente para o certificado.
    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Open **Gerir certificados de utilizador** > **Certificados** > **pessoais** > **do utilizador** > yourappname.yourtenant.onmicrosoft.com *.*
1. Selecione o certificado > **Ação** > **Todas as Tarefas** > **Exportação**.
1. **Selecione Yes** > **Next** > **Yes, exporte a chave** > privada**Seguinte**.
1. Aceite as predefinições para formato de ficheiro de **exportação**.
1. Forneça uma senha para o certificado.

### <a name="add-a-client-certificate-policy-key"></a>Adicione uma chave de política de certificado de cliente

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
1. Selecione **Teclas de política**e, em seguida, selecione **Adicionar**.
1. Na caixa **Opções,** **selecione Upload**.
1. Na caixa **nome,** digite **RestApiClientCertificate**.
    O prefixo *B2C_1A_* é adicionado automaticamente.
1. Na caixa de upload do **Ficheiro,** selecione o ficheiro .pfx do seu certificado com uma chave privada.
1. Na caixa **password,** digite a palavra-passe do certificado.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Configure o seu perfil técnico REST API para utilizar a autenticação do certificado de cliente

Depois de criar a chave necessária, configure os metadados de perfil técnico REST API para fazer referência ao certificado de cliente.

1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico rest API. Por `REST-ValidateProfile`exemplo, `REST-GetProfile`ou .
1. Localize `<Metadata>` o elemento.
1. Mude o Tipo `ClientCertificate`de *Autenticação* para .
1. Alterar a *AllowInsecureAuthInProduction* para `false`.
1. Imediatamente após o `</Metadata>` elemento de fecho, adicione o seguinte corte XML:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Segue-se um exemplo de um perfil técnico RESTful configurado com um certificado de cliente HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>Autenticação do portador OAuth2 

A autenticação token do portador é definida no [Quadro de Autorização OAuth2.0: Utilização do token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Na autenticação simbólica do portador, o Azure AD B2C envia um pedido http com um token no cabeçalho de autorização.

```http
Authorization: Bearer <token>
```

Um símbolo portador é uma corda opaca. Pode ser um símbolo de acesso JWT ou qualquer corda que a API REST espera que o Azure AD B2C envie o cabeçalho de autorização. O Azure AD B2C suporta os seguintes tipos:

- **Símbolo do portador.** Para poder enviar o token ao portador no perfil técnico Restful, a sua política precisa primeiro de adquirir o token do portador e depois usá-lo no perfil técnico RESTful.  
- **Ficha estática do portador.** Use esta abordagem quando a sua API REST emitir um sinal de acesso a longo prazo. Para utilizar um símbolo estático do portador, crie uma chave de política e faça uma referência do perfil técnico RESTful à sua chave de política. 

## <a name="using-oauth2-bearer"></a>Usando o portador OAuth2  

Os seguintes passos demonstram como usar as credenciais do cliente para obter um token ao portador e passá-lo para o cabeçalho de Autorização das chamadas REST API.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Defina uma reivindicação para armazenar o token do portador

Uma reclamação fornece armazenamento temporário de dados durante uma execução política Azure AD B2C. O [esquema das alegações](claimsschema.md) é o lugar onde declaras as tuas alegações. O sinal de acesso deve ser guardado numa alegação a utilizar mais tarde. 

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Procure o elemento [BuildingBlocks.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione o porta-cidadeToken ao elemento **ClaimsSchema.**  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Adquirir um sinal de acesso 

Pode obter um sinal de acesso de várias maneiras: obtendo-o a partir de um fornecedor de [identidade federado,](idp-pass-through-custom.md)chamando uma API REST que devolve um sinal de acesso, utilizando um [fluxo ROPC,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)ou utilizando o fluxo de [credenciais](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)do cliente .  

O exemplo seguinte utiliza um perfil técnico REST API para fazer um pedido ao ponto final do token Azure AD utilizando as credenciais do cliente aprovadas como autenticação básica HTTP. Para configurar isto em Azure AD, consulte a plataforma de identidade da Microsoft e o fluxo de credenciais de [cliente OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Poderá ser necessário modificá-lo para interagir com o seu Fornecedor de Identidade. 

Para o ServiceUrl, substitua o seu nome de inquilino pelo nome do seu inquilino Azure AD. Consulte a referência de [perfil técnico RESTful](restful-technical-profile.md) para todas as opções disponíveis.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Alterar o perfil técnico REST para utilizar a autenticação token do portador

Para apoiar a autenticação simbólica do portador na sua política personalizada, modifique o perfil técnico rest API com o seguinte:

1. No seu diretório de trabalho, abra o ficheiro de política de extensão *TrustFrameworkExtensions.xml.*
1. Procure o `<TechnicalProfile>` nó que `Id="REST-API-SignUp"`inclui .
1. Localize `<Metadata>` o elemento.
1. Alterar o Tipo de *Autenticação* para *o Portador,* da seguinte forma:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Altere ou adicione o *UseClaimAsBearerToken* ao *bearerToken,* da seguinte forma. O *bearerToken* é o nome da alegação de que o token `SecureREST-AccessToken`portador será recuperado (a alegação de saída de ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Certifique-se de adicionar a reclamação acima utilizada como uma reivindicação de entrada:

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

Depois de adicionar os snippets acima, o seu perfil técnico deve parecer o seguinte código XML:

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Usando um portador oAuth2 estático 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Adicione a chave política de token do portador OAuth2

Crie uma chave de política para armazenar o valor simbólico do portador.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
1. Selecione **Teclas de política**e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha. `Manual`
1. Introduza um **nome** para a chave política. Por exemplo, `RestApiBearerToken`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Em **Segredo,** insira o seu segredo de cliente que gravou anteriormente.
1. Para **a utilização da chave,** selecione `Encryption`.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Configure o seu perfil técnico REST API para utilizar a chave política do token do portador

Depois de criar a chave necessária, configure os metadados de perfil técnico REST API para fazer referência ao token do portador.

1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico rest API. Por `REST-ValidateProfile`exemplo, `REST-GetProfile`ou .
1. Localize `<Metadata>` o elemento.
1. Mude o Tipo `Bearer`de *Autenticação* para .
1. Alterar a *AllowInsecureAuthInProduction* para `false`.
1. Imediatamente após o `</Metadata>` elemento de fecho, adicione o seguinte corte XML:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Segue-se um exemplo de um perfil técnico RESTful configurado com autenticação token ao portador:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o elemento de [perfil técnico Restful](restful-technical-profile.md) na referência IEF. 
