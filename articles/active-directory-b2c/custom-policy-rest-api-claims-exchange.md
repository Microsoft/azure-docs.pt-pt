---
title: REST API reclama trocas - Azure Ative Directory B2C
description: Adicione rest API reclama trocas de trocas para políticas personalizadas no Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94949501"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Walkthrough: Add REST API reclama trocas de trocas para políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) permite que os desenvolvedores de identidade integrem uma interação com uma API RESTful numa viagem de utilizador. No final desta passagem, poderá criar uma viagem de utilizador Azure AD B2C que interage com [os serviços RESTful.](custom-policy-rest-api-intro.md)

Neste cenário, enriquecemos os dados simbólicos do utilizador integrando-se com um fluxo de trabalho de linha de negócio corporativo. Durante a inscrição ou sessão com conta local ou federada, o Azure AD B2C invoca uma API REST para obter os dados de perfil estendidos do utilizador a partir de uma fonte de dados remoto. Nesta amostra, o Azure AD B2C envia o identificador único do utilizador, o objectId. Em seguida, a API REST devolve o saldo da conta do utilizador (um número aleatório). Utilize esta amostra como ponto de partida para integrar-se com o seu próprio sistema crm, base de dados de marketing ou qualquer fluxo de trabalho de linha de negócio.

Também pode projetar a interação como um perfil técnico de validação. Isto é adequado quando a API REST irá validar dados no ecrã e devoluções de reclamações. Para obter mais informações, consulte [Walkthrough: Integre as trocas de reclamações da API REST na sua viagem de utilizador Azure AD B2C para validar a entrada do utilizador](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Começar com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar s inscrição com contas locais.
- Saiba como [integrar as reclamações da API REST na sua política personalizada Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>Preparar um ponto final da API REST

Para esta passagem, deverá ter uma API REST que valide se o objeto AD B2C de um utilizador está registado no seu sistema back-end. Se registada, a API REST devolve o saldo da conta de utilizador. Caso contrário, a API REST regista a nova conta no diretório e devolve o saldo `50.00` inicial.

O seguinte código JSON ilustra os dados que a Azure AD B2C enviará para o seu ponto final REST API. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Uma vez que a API REST valida os dados, deve devolver um HTTP 200 (Ok), com os seguintes dados JSON:

```json
{
    "balance": "760.50"
}
```

A configuração do ponto final da API REST está fora do âmbito deste artigo. Criámos uma amostra [de Azure Functions.](../azure-functions/functions-reference.md) Pode aceder ao código de função Azure completo no [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definir reclamações

Uma reclamação fornece armazenamento temporário de dados durante uma execução política Azure AD B2C. Pode declarar reclamações dentro da secção [de esquemas de reclamações.](claimsschema.md) 

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione as seguintes reclamações ao elemento **ClaimsSchema.**  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Adicione o perfil técnico restful da API 

Um [perfil técnico repousante](restful-technical-profile.md) fornece suporte para a interligagem com o seu próprio serviço RESTful. A Azure AD B2C envia dados para o serviço RESTful numa `InputClaims` recolha e recebe dados de volta numa `OutputClaims` recolha. Encontre o elemento **ClaimsProviders** no seu <em>**`TrustFrameworkExtensions.xml`**</em> ficheiro e adicione um novo fornecedor de reclamações da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

Neste exemplo, o `userLanguage` será enviado para o serviço REST como dentro da carga útil `lang` JSON. O valor da `userLanguage` reclamação contém o ID do idioma do utilizador atual. Para mais informações, consulte [a reclamação.](claim-resolver-overview.md)

### <a name="configure-the-restful-api-technical-profile"></a>Configurar o perfil técnico restful da API 

Depois de implementar a sua API REST, desaver os metadados do `REST-ValidateProfile` perfil técnico para refletir a sua própria API REST, incluindo:

- **ServiceUrl**. Desaponte o URL do ponto final da API REST.
- **Enviar EmailIn**. Especifique como os pedidos de entrada são enviados para o provedor de reclamações RESTful.
- **AutenticaçãoType**. Desacorda o tipo de autenticação que está a ser realizada pelo provedor de reclamações RESTful. 
- **AllowInsecureAuthInProduction**. Em um ambiente de produção, certifique-se de definir estes metadados para `true`
    
Consulte os [metadados de perfil técnico RESTful](restful-technical-profile.md#metadata) para obter mais configurações.

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que deve fazer quando se muda para um ambiente de produção. Para aprender a proteger as suas APIs RESTful para produção, consulte [API Restful Secure](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Adicione um passo de orquestração

Os [percursos do utilizador](userjourneys.md) especificam caminhos explícitos através dos quais uma política permite que uma aplicação de entidade confiadora obtenha as afirmações desejadas para um utilizador. Uma viagem de utilizador é representada como uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. Pode adicionar ou subtrair passos de orquestração. Neste caso, irá adicionar um novo passo de orquestração que é utilizado para aumentar as informações fornecidas à aplicação após a inscrição ou inscrição do utilizador através da chamada REST API.

1. Abra o arquivo base da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> .
1. Procure o `<UserJourneys>` elemento. Copie todo o elemento e, em seguida, elimine-o.
1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Cole o `<UserJourneys>` ficheiro de extensões, após o fecho do `<ClaimsProviders>` elemento.
1. Localize o `<UserJourney Id="SignUpOrSignIn">` , e adicione o seguinte passo de orquestração antes do último.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refactor o último passo de orquestração mudando o `Order` de `8` . Os seus dois últimos passos de orquestração devem parecer os seguintes:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Repita os dois últimos passos para as viagens de utilizador **ProfileEdit** e **PasswordReset.**


## <a name="include-a-claim-in-the-token"></a>Incluir uma reclamação no token 

Para devolver o `balance` pedido de retorno ao pedido do partido, adicione um pedido de saída ao <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ficheiro. A adição de uma reclamação de saída emitirá a reclamação no token após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico dentro da secção do partido de suporte para adicionar `balance` como uma reivindicação de saída.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Repita este passo para o **ProfileEdit.xml**, e **PasswordReset.xml** viagens de utilizador.

Guarde os ficheiros que alterou: *TrustFrameworkBase.xml*, e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* e *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **o Quadro de Experiência de Identidade.**
1. Selecione **Carregar a Política Personalizada** e, em seguida, fazer o upload dos ficheiros de política que alterou: *TrustFrameworkBase.xml*, e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, e *PasswordReset.xml*. 
1. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
1. Deverá poder inscrever-se através de um endereço de e-mail ou de uma conta no Facebook.
1. O token enviado de volta para o seu pedido inclui a `balance` reclamação.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Passos seguintes

Para aprender a proteger as suas APIs, consulte os seguintes artigos:

- [Walkthrough: Integrar as bolsas de reclamações da REST no seu Azure AD B2C como um passo de orquestração](custom-policy-rest-api-claims-exchange.md)
- [Proteja a sua API RESTful](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)