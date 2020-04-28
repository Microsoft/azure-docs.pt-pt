---
title: Rest API reclama trocas como validação
titleSuffix: Azure AD B2C
description: Um passeio para criar uma viagem de utilizador Azure AD B2C que interage com serviços RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330823"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Walkthrough: Integrar rest API reclama trocas de créditos na sua viagem de utilizador Azure AD B2C para validar a entrada do utilizador

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Quadro de Experiência de Identidade (IEF) que sustenta o Diretório Ativo Azure B2C (Azure AD B2C) permite aos desenvolvedores de identidade integrar uma interação com uma API RESTful numa viagem de utilizador.  No final desta passagem, poderá criar uma viagem de utilizador Azure AD AD B2C que interage com [serviços RESTful](custom-policy-rest-api-intro.md) para validar a entrada do utilizador.

Neste cenário, vamos adicionar a capacidade para os utilizadores introduzirem um número de fidelização na página de inscrição do Azure AD B2C. Vamos validar se esta combinação de e-mail e número de fidelização está mapeada para um código promocional enviando estes dados para uma API REST. Se a API REST encontrar um código promocional para este utilizador, será devolvido ao Azure AD B2C. Finalmente, o código promocional será inserido nos pedidos simbólicos para o pedido de consumo.

Você também pode projetar a interação como um passo de orquestração. Isto é adequado quando a API REST não irá validar os dados no ecrã e sempre devolver reclamações. Para mais informações, consulte [Walkthrough: Integrar rest API reclama trocas de reclamações na sua viagem de utilizador Azure AD B2C como passo de orquestração](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Get started com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar sessão com contas locais.
- Saiba como integrar as bolsas de [reclamações da API rest na sua política personalizada Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Prepare um ponto final da API REST

Para este passe, você deve ter uma API REST que valida se um endereço de e-mail está registado no seu sistema back-end com um ID de fidelização. Se registado, a API REST deve devolver um código de promoção de registo, que o cliente pode usar para comprar bens dentro da sua aplicação. Caso contrário, a API REST deve devolver uma mensagem de erro HTTP 409: "Loyalty ID '{loyalty ID}' não está associada ao endereço de e-mail '{email}'.".

O seguinte código JSON ilustra os dados que o Azure AD B2C enviará para o seu ponto final REST API. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Uma vez que a sua API REST valida os dados, deve devolver um HTTP 200 (Ok), com os seguintes dados da JSON:

```json
{
    "promoCode": "24534"
}
```

Se a validação falhar, a API REST deve devolver um `userMessage` HTTP 409 (Conflito), com o elemento JSON. O IEF `userMessage` espera a alegação de que a API rest volta. Esta alegação será apresentada como uma corda ao utilizador se a validação falhar.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Configure o perfil técnico restful DaPI 

Um [perfil técnico restful](restful-technical-profile.md) fornece suporte para interfaceção com o seu próprio serviço RESTful. O Azure AD B2C envia dados para `InputClaims` o serviço RESTful `OutputClaims` numa recolha e recebe dados numa recolha. Encontre o elemento **ClaimsProviders** e adicione um novo fornecedor de sinistros da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Neste exemplo, `userLanguage` o serviço REST será `lang` enviado para o serviço REST como dentro da carga útil JSON. O valor `userLanguage` da reclamação contém o ID atual do idioma do utilizador. Para mais informações, consulte a [reclamação](claim-resolver-overview.md).

Os comentários `AuthenticationType` acima `AllowInsecureAuthInProduction` e especificar alterações que deve fazer quando se muda para um ambiente de produção. Para aprender a proteger as suas APIs RESTful para produção, consulte [Secure RESTful API](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Validar a entrada do utilizador

Para obter o número de fidelização do utilizador durante a inscrição, deve permitir que o utilizador introduza estes dados no ecrã. Adicione a alegação de saída **loyaltyId** à página de inscrição, adicionando-a ao elemento da secção de `OutputClaims` perfil técnico de inscrição existente. Especifique toda a lista de pedidos de saída para controlar a ordem que as reclamações são apresentadas no ecrã.  

Adicione a referência técnica de perfil de validação `REST-ValidateProfile`ao perfil técnico de inscrição, que chama a . O novo perfil técnico de validação `<ValidationTechnicalProfiles>` será adicionado ao topo da coleção definida na política de base. Este comportamento significa que só após validação bem sucedida, O Azure AD B2C passa a criar a conta no diretório.   

1. Encontre o elemento **ClaimsProviders.** Adicione um novo fornecedor de sinistros da seguinte forma:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Incluir uma reclamação no símbolo 

Para devolver a reclamação de código promocional de volta à <em> `SocialAndLocalAccounts/` </em> aplicação do partido que depende, adicione uma reclamação de saída ao ficheiro. A alegação de saída permitirá que a reclamação seja adicionada ao símbolo após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico dentro `promoCode` da secção do partido de fiação para adicionar o como uma reivindicação de saída.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
1. Selecione Quadro de **Experiência de Identidade**.
1. Selecione **'Upload Custom Policy**' e, em seguida, faça upload dos ficheiros de política que alterou: *TrustFrameworkExtensions.xml*, e *SignUpOrSignin.xml*. 
1. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
1. Deverá poder inscrever-se através de um endereço de e-mail.
1. Clique no link **'Iniciar a sessão'.**
1. No **ID de fidelização**, tipo 1234, e clique **em Continuar**. Neste momento, deve receber uma mensagem de erro de validação.
1. Mude para outro valor e clique **em Continuar**.
1. O símbolo enviado de volta `promoCode` para o seu pedido inclui a reclamação.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Passos seguintes

Para aprender a proteger as suas APIs, consulte os seguintes artigos:

- [Walkthrough: Integrar rest API reclama trocas na sua jornada de utilizador Azure AD B2C como passo de orquestração](custom-policy-rest-api-claims-exchange.md)
- [Proteja a sua API RESTful](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)
