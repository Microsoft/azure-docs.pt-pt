---
title: REST API reclama trocas - Azure Ative Directory B2C
description: Adicione as trocas de reclamações rest API a políticas personalizadas no Diretório Ativo B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330727"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Walkthrough: Adicionar REST API reclama trocas a políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) permite aos desenvolvedores de identidade integrar uma interação com uma API RESTful numa viagem de utilizador. No final desta passagem, poderá criar uma viagem de utilizador Azure AD AD B2C que interage com [serviços RESTful.](custom-policy-rest-api-intro.md)

Neste cenário, enriquecemos os dados simbólicos do utilizador integrando-nos com um fluxo de trabalho de linha empresarial empresarial. Durante o início de inscrição ou inscrição com conta local ou federada, o Azure AD B2C invoca uma API REST para obter os dados de perfil alargados do utilizador a partir de uma fonte de dados remota. Nesta amostra, o Azure AD B2C envia o identificador único do utilizador, o objectid. A API REST devolve então o saldo da conta do utilizador (um número aleatório). Utilize esta amostra como ponto de partida para integrar com o seu próprio sistema de CRM, base de dados de marketing ou qualquer fluxo de trabalho de linha de negócio.

Também pode projetar a interação como um perfil técnico de validação. Isto é adequado quando a API REST irá validar dados no ecrã e devolver reclamações. Para mais informações, consulte Walkthrough: Integrar as trocas de reclamações da [Rest API na sua viagem de utilizador Azure AD B2C para validar a entrada](custom-policy-rest-api-claims-validation.md)do utilizador .

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Get started com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar sessão com contas locais.
- Saiba como integrar as bolsas de [reclamações da API rest na sua política personalizada Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Prepare um ponto final da API REST

Para esta passagem, deve ter uma API REST que valida se o objectId Azure AD B2C de um utilizador está registado no seu sistema de back-end. Se registado, a API REST devolve o saldo da conta do utilizador. Caso contrário, a API REST regista a nova conta no `50.00`diretório e devolve o saldo inicial.

O seguinte código JSON ilustra os dados que o Azure AD B2C enviará para o seu ponto final REST API. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

Uma vez que a sua API REST valida os dados, deve devolver um HTTP 200 (Ok), com os seguintes dados da JSON:

```json
{
    "balance": "760.50"
}
```

A configuração do ponto final da API REST está fora do âmbito deste artigo. Criámos uma amostra de [Funções Azure.](https://docs.microsoft.com/azure/azure-functions/functions-reference) Pode aceder ao código de função Completo do Azure no [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definir reclamações

Uma reclamação fornece armazenamento temporário de dados durante uma execução política Azure AD B2C. Pode declarar reclamações dentro da secção [de sinistros.](claimsschema.md) 

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Procure o elemento [BuildingBlocks.](buildingblocks.md) Se o elemento não existir, adicione-o.
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

## <a name="configure-the-restful-api-technical-profile"></a>Configure o perfil técnico restful DaPI 

Um [perfil técnico restful](restful-technical-profile.md) fornece suporte para interfaceção com o seu próprio serviço RESTful. O Azure AD B2C envia dados para `InputClaims` o serviço RESTful `OutputClaims` numa recolha e recebe dados numa recolha. Encontre o elemento **Reclamações Fornecedores** no seu <em>**`TrustFrameworkExtensions.xml`**</em> ficheiro e adicione um novo fornecedor de sinistros da seguinte forma:

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

Neste exemplo, `userLanguage` o serviço REST será `lang` enviado para o serviço REST como dentro da carga útil JSON. O valor `userLanguage` da reclamação contém o ID atual do idioma do utilizador. Para mais informações, consulte a [reclamação](claim-resolver-overview.md).

Os comentários `AuthenticationType` acima `AllowInsecureAuthInProduction` e especificar alterações que deve fazer quando se muda para um ambiente de produção. Para aprender a proteger as suas APIs RESTful para produção, consulte [Secure RESTful API](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Adicione um passo de orquestração

[As viagens de utilizador](userjourneys.md) especificam caminhos explícitos através dos quais uma política permite que uma aplicação de parte dependente obtenha as reclamações desejadas para um utilizador. Uma viagem de utilizador é representada como uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. Pode adicionar ou subtrair passos de orquestração. Neste caso, irá adicionar um novo passo de orquestração que é usado para aumentar as informações fornecidas à aplicação após o início do utilizador ou o início através da chamada REST API.

1. Abra o ficheiro base da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Procure o `<UserJourneys>` elemento. Copie todo o elemento e, em seguida, elimine-o.
1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Colhe `<UserJourneys>` o ficheiro de extensões no `<ClaimsProviders>` ficheiro de extensões, após o fecho do elemento.
1. Localize `<UserJourney Id="SignUpOrSignIn">`o passo de orquestração seguinte antes do último.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refactor o último passo de `Order` `8`orquestração mudando o para . Os seus dois últimos passos de orquestração devem parecer os seguintes:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Repita os dois últimos passos para as viagens de utilizador **Do ProfileEdit** e **PasswordReset.**


## <a name="include-a-claim-in-the-token"></a>Incluir uma reclamação no símbolo 

Para devolver `balance` a reclamação à aplicação da parte <em> `SocialAndLocalAccounts/` </em> que depende, adicione uma reclamação de saída ao ficheiro. A adição de uma reclamação de saída emitirá a reclamação no token após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico `balance` dentro da secção do partido de fiação para adicionar como uma reivindicação de saída.
 
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

Repita este passo para as viagens de **utilizador ProfileEdit.xml**e **PasswordReset.xml.**

Guarde os ficheiros que alterou: *TrustFrameworkBase.xml*e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml,* *ProfileEdit.xml*e *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
1. Selecione Quadro de **Experiência de Identidade**.
1. Selecione **'Upload Custom Policy**', ' e, em seguida, faça upload dos ficheiros de política que alterou: *TrustFrameworkBase.xml*, e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, and *PasswordReset.xml*. 
1. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
1. Deverá poder inscrever-se através de um endereço de e-mail ou de uma conta no Facebook.
1. O símbolo enviado de volta `balance` para o seu pedido inclui a reclamação.

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


## <a name="next-steps"></a>Passos seguintes

Para aprender a proteger as suas APIs, consulte os seguintes artigos:

- [Walkthrough: Integrar rest API reclama trocas na sua jornada de utilizador Azure AD B2C como passo de orquestração](custom-policy-rest-api-claims-exchange.md)
- [Proteja a sua API RESTful](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)
