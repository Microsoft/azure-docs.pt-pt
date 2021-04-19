---
title: Proteja um serviço repousante no seu Azure AD B2C
titleSuffix: Azure AD B2C
description: Proteja as suas trocas de reclamações de RESE REST personalizadas no seu Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 462d69a8bde0dec2689ac30620276b5bcd335410
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717698"
---
# <a name="secure-your-restful-services"></a>Proteja os seus serviços RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao integrar uma API REST dentro de uma viagem de utilizador Azure AD B2C, deve proteger o seu ponto final REST API com autenticação. Isto garante que apenas os serviços que possuam credenciais adequadas, como o Azure AD B2C, podem fazer chamadas para o seu ponto final da API REST.

Saiba como integrar uma API REST dentro da sua viagem de utilizador Azure AD B2C na [entrada validada](custom-policy-rest-api-claims-validation.md) do utilizador e [Adicione as reclamações da API REST para artigos de políticas personalizadas.](custom-policy-rest-api-claims-exchange.md)

Este artigo explorará como proteger a sua API REST com http basic, certificado de cliente ou autenticação OAuth2. 

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos num dos seguintes guias 'Como fazer':

- [Integre as trocas de reclamações da API rest na sua viagem de utilizador Azure AD B2C para validar a entrada do utilizador](custom-policy-rest-api-claims-validation.md).
- [Adicionar rest API reclama trocas de trocas a políticas personalizadas](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Autenticação básica HTTP

A autenticação básica HTTP é definida no [RFC 2617](https://tools.ietf.org/html/rfc2617). Trabalhos de autenticação básica da seguinte forma: A Azure AD B2C envia um pedido HTTP com as credenciais do cliente no cabeçalho de Autorização. As credenciais são formatadas como a cadeia codificada base64 "nome:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Adicione o nome de utilizador REST E as chaves da política de senhas REST

Para configurar um perfil técnico da API REST com a autenticação básica HTTP, crie as seguintes teclas criptográficas para armazenar o nome de utilizador e a palavra-passe:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione **Identity Experience Framework**.
1. Selecione **as teclas de** política e, em seguida, selecione **Adicionar**.
1. Para **opções**, selecione **Manual**.
1. Para **nome,** **escreva RestApiUsername**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Secret,** insira o nome de utilizador REST API.
1. Para **a utilização da chave**, selecione **Encriptação**.
1. Selecione **Criar**.
1. Selecione **as teclas de** política novamente.
1. Selecione **Adicionar**.
1. Para **opções**, selecione **Manual**.
1. Para **nome,** **escreva RestApiPassword**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Secret,** insira a senha DE API REST.
1. Para **a utilização da chave**, selecione **Encriptação**.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Configure o seu perfil técnico REST API para utilizar a autenticação básica HTTP

Depois de criar as teclas necessárias, configuure os metadados de perfil técnico REST API para fazer referência às credenciais.

1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico da API REST. Por `REST-ValidateProfile` exemplo, ou `REST-GetProfile` . .
1. Localize o `<Metadata>` elemento.
1. Altere *oTipo de Autenticação* para `Basic` .
1. Altere a *AllowInsecureAuthInProduction* para `false` .
1. Imediatamente após o elemento de `</Metadata>` fecho, adicione o seguinte corte XML:
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

A autenticação do certificado de cliente é uma autenticação baseada em certificados mútuos, onde o cliente, Azure AD B2C, fornece o seu certificado de cliente ao servidor para provar a sua identidade. Isto acontece como parte do aperto de mão SSL. Apenas os serviços que possuam certificados adequados, como o Azure AD B2C, podem aceder ao seu serviço REST API. O certificado de cliente é um certificado digital X.509. Em ambientes de produção, deve ser assinado por uma autoridade de certificados.

### <a name="prepare-a-self-signed-certificate-optional"></a>Preparar um certificado auto-assinado (opcional)

Para ambientes não produtivos, se ainda não tiver um certificado, pode utilizar um certificado auto-assinado. No Windows, pode utilizar o cmdlet [New-SelfSignedCertificate da PowerShell](/powershell/module/pkiclient/new-selfsignedcertificate) para gerar um certificado.

1. Execute este comando PowerShell para gerar um certificado auto-assinado. Modifique o `-Subject` argumento conforme apropriado para a sua aplicação e nome de inquilino Azure AD B2C. Também pode ajustar a `-NotAfter` data para especificar uma validade diferente para o certificado.
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Abra **os certificados de utilizador** De corrente yourappname.yourtenant.onmicrosoft.com de  >    >  certificados **pessoais**  >    >  *do* utilizador.
1. Selecione o certificado > **Action**  >  **All Tasks**  >  **Export**.
1. Selecione **Yes**  >  **Next**  >  **Yes, exporte a chave privada**  >  **Next**.
1. Aceite os predefinidos do **formato de ficheiro de exportação.**
1. Forneça uma senha para o certificado.

### <a name="add-a-client-certificate-policy-key"></a>Adicione uma chave de política de certificado de cliente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione **Identity Experience Framework**.
1. Selecione **as teclas de** política e, em seguida, selecione **Adicionar**.
1. Na caixa **Opções,** **selecione Upload**.
1. Na caixa **Nome,** **escreva RestApiClientCertificate**.
    O prefixo *B2C_1A_* é adicionado automaticamente.
1. Na caixa **de upload do Ficheiro,** selecione o ficheiro .pfx do certificado com uma chave privada.
1. Na **caixa de senha,** digite a senha do certificado.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Configure o seu perfil técnico REST API para utilizar a autenticação do certificado do cliente

Depois de criar a chave necessária, configuure os metadados de perfil técnico REST API para fazer referência ao certificado do cliente.

1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico da API REST. Por `REST-ValidateProfile` exemplo, ou `REST-GetProfile` . .
1. Localize o `<Metadata>` elemento.
1. Altere *oTipo de Autenticação* para `ClientCertificate` .
1. Altere a *AllowInsecureAuthInProduction* para `false` .
1. Imediatamente após o elemento de `</Metadata>` fecho, adicione o seguinte corte XML:
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

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

A autenticação do símbolo do portador é definida no [Quadro de Autorização OAuth2.0: Utilização do Token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Na autenticação de fichas ao portador, a Azure AD B2C envia um pedido HTTP com um sinal no cabeçalho de autorização.

```http
Authorization: Bearer <token>
```

Um símbolo portador é uma corda opaca. Pode ser um token de acesso JWT ou qualquer cadeia que a API REST espera que a Azure AD B2C envie o cabeçalho de autorização. AZure AD B2C suporta os seguintes tipos:

- **Ficha do portador.** Para poder enviar o token ao portador no perfil técnico do Restful, a sua política precisa primeiro de adquirir o token ao portador e depois usá-lo no perfil técnico RESTful.  
- **Ficha estática do portador.** Utilize esta abordagem quando a sua API REST emitir um token de acesso a longo prazo. Para utilizar um token estático do portador, crie uma chave de política e faça uma referência do perfil técnico RESTful à sua chave política. 


## <a name="using-oauth2-bearer"></a>Usando o portador OAuth2  

Os passos seguintes demonstram como usar as credenciais do cliente para obter um token ao portador e passá-lo para o cabeçalho de autorização das chamadas DE API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Defina uma reivindicação para armazenar o símbolo do portador

Uma reclamação fornece armazenamento temporário de dados durante uma execução política Azure AD B2C. O [esquema de reclamações](claimsschema.md) é o lugar onde declara as suas reivindicações. O token de acesso deve ser guardado num crédito a utilizar posteriormente. 

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione as seguintes reclamações ao elemento **ClaimsSchema.**  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Aquisição de um token de acesso 

Pode obter um token de acesso de uma de várias formas: obtendo-o a [partir de um fornecedor de identidade federado,](idp-pass-through-user-flow.md)chamando uma API DE REST que devolve um token de acesso, utilizando um fluxo [ROPC,](../active-directory/develop/v2-oauth-ropc.md)ou utilizando o [fluxo de credenciais](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)do cliente. O fluxo de credenciais do cliente é comumente usado para interações servidor-a-servidor que devem ser executadas em segundo plano, sem interação imediata com um utilizador.

#### <a name="acquiring-an-azure-ad-access-token"></a>Aquisição de um token de acesso AD AZure 

O exemplo a seguir utiliza um perfil técnico da API REST para fazer um pedido ao ponto final da Azure AD usando as credenciais do cliente passadas como autenticação básica HTTP. Para obter mais informações, consulte [a plataforma de identidade da Microsoft e o fluxo de credenciais de cliente OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). 

Para adquirir um token de acesso AZure AD, crie uma aplicação no seu inquilino AZure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino AD Azure.
1. No menu esquerdo, selecione **Azure Ative Directory**. Ou, selecione **Todos os serviços** e procure e selecione **Azure Ative Directory**.
1. Selecione **as inscrições da App** e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *Client_Credentials_Auth_app.*
1. Nos **tipos de conta suportada,** selecione Contas **apenas neste diretório organizacional**.
1. Selecione **Registar**.
2. Registar o **ID de Aplicação (cliente).** 


Para um fluxo de credenciais de cliente, você precisa criar um segredo de aplicação. O segredo do cliente também é conhecido como uma senha de aplicação. O segredo será usado pela sua aplicação para adquirir um token de acesso.

1. Na página **Azure AD B2C - Registos de aplicações,** selecione a aplicação que criou, por exemplo *Client_Credentials_Auth_app*.
1. No menu esquerdo, em **Manage,** selecione **Certificates & secrets**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição.** Por exemplo, *o segredo de clientes1*.
1. Em **Expira**, selecione uma duração para a qual o segredo é válido e, em seguida, selecione **Adicionar**.
1. Grave o **valor** secreto para uso no código de aplicação do seu cliente. Este valor secreto nunca mais é exibido depois de deixar esta página. Utiliza este valor como segredo de aplicação no código da sua aplicação.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Criar chaves de política Azure AD B2C

Você precisa armazenar a identificação do cliente e o segredo do cliente que você já gravou no seu inquilino Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Na página 'Visão Geral', selecione **Identity Experience Framework**.
5. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções,** escolha `Manual` .
7. Introduza um **Nome** para a chave de política, `SecureRESTClientId` . O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
8. Em **Segredo,** insira a identificação do seu cliente que gravou anteriormente.
9. Para **a utilização da chave**, selecione `Signature` .
10. Clique em **Criar**.
11. Criar outra chave de política com as seguintes definições:
    -   **Nome:** `SecureRESTClientSecret` .
    -   **Segredo:** insira o segredo do seu cliente que gravou anteriormente

Para o ServiceUrl, substitua o seu nome de inquilino pelo nome do seu inquilino Azure AD. Consulte a referência [de perfil técnico RESTful](restful-technical-profile.md) para todas as opções disponíveis.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Alterar o perfil técnico REST para utilizar a autenticação simbólica do portador

Para suportar a autenticação simbólica do portador na sua política personalizada, modifique o perfil técnico da API REST com o seguinte:

1. No seu diretório de trabalho, abra o ficheiro de política de extensão *TrustFrameworkExtensions.xml.*
1. Procure o `<TechnicalProfile>` nó que inclui `Id="REST-API-SignUp"` .
1. Localize o `<Metadata>` elemento.
1. Altere *oTipo de Autenticação* para *Portador,* da seguinte forma:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Altere ou adicione o *UseClaimAsBearerToken* ao *bearerToken*, da seguinte forma. O *bearerToken* é o nome da alegação de que o sinal do portador será recuperado (a reivindicação de saída de `SecureREST-AccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Certifique-se de que adiciona a reclamação acima utilizada como uma reivindicação de entrada:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Depois de adicionar os snippets acima, o seu perfil técnico deve parecer o seguinte código XML:

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
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Usando um portador estático OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Adicione a chave de política de símbolos do portador OAuth2

Para configurar um perfil técnico da API REST com um token portador de OAuth2, obtenha um token de acesso do proprietário da API REST. Em seguida, crie a seguinte chave criptográfica para armazenar o token portador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione **Identity Experience Framework**.
1. Selecione **as teclas de** política e, em seguida, selecione **Adicionar**.
1. Para **Opções,** escolha `Manual` .
1. Insira um **Nome** para a chave de política. Por exemplo, `RestApiBearerToken`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
1. Para **a utilização da chave**, selecione `Encryption` .
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Configure o seu perfil técnico rest API para usar a chave de política de fichas ao portador

Depois de criar a chave necessária, configuure os metadados de perfil técnico REST API para fazer referência ao token do portador.

1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico da API REST. Por `REST-ValidateProfile` exemplo, ou `REST-GetProfile` . .
1. Localize o `<Metadata>` elemento.
1. Altere *oTipo de Autenticação* para `Bearer` .
1. Altere a *AllowInsecureAuthInProduction* para `false` .
1. Imediatamente após o elemento de `</Metadata>` fecho, adicione o seguinte corte XML:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Segue-se um exemplo de um perfil técnico RESTful configurado com autenticação simbólica ao portador:

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

## <a name="api-key-authentication"></a>Autenticação de chave API

A chave API é um identificador único usado para autenticar um utilizador para aceder a um ponto final rest API. A chave é enviada num cabeçalho HTTP personalizado. Por exemplo, o [gatilho Azure Functions HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) utiliza o `x-functions-key` cabeçalho HTTP para identificar o solicitador.  

### <a name="add-api-key-policy-keys"></a>Adicionar chaves de política de chave API

Para configurar um perfil técnico da API REST com a autenticação da chave API, crie a seguinte chave criptográfica para armazenar a chave API:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione **Identity Experience Framework**.
1. Selecione **as teclas de** política e, em seguida, selecione **Adicionar**.
1. Para **opções**, selecione **Manual**.
1. Para **nome**, **escreva RestApiKey**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Secret,** insira a chave API REST.
1. Para **a utilização da chave**, selecione **Encriptação**.
1. Selecione **Criar**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Configure o seu perfil técnico REST API para utilizar a autenticação da chave API

Depois de criar a chave necessária, configuure os metadados de perfil técnico REST API para fazer referência às credenciais.

1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).
1. Procure o perfil técnico da API REST. Por `REST-ValidateProfile` exemplo, ou `REST-GetProfile` . .
1. Localize o `<Metadata>` elemento.
1. Altere *oTipo de Autenticação* para `ApiKeyHeader` .
1. Altere a *AllowInsecureAuthInProduction* para `false` .
1. Imediatamente após o elemento de `</Metadata>` fecho, adicione o seguinte corte XML:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

O **ID** da tecla criptográfica define o cabeçalho HTTP. Neste exemplo, a chave API é enviada como **chave x-funções**.

Segue-se um exemplo de um perfil técnico RESTful configurado para chamar uma Função Azure com autenticação de chave API:

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
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o elemento [de perfil técnico do Restful](restful-technical-profile.md) na referência IEF.
