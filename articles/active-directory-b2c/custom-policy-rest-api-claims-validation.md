---
title: REST API reclama trocas como validação
titleSuffix: Azure AD B2C
description: Uma passagem para a criação de uma viagem de utilizador Azure AD B2C que interage com os serviços RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30273c0103d8a0fde12b1b7c6f66d16dd4ea84cb
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089524"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Walkthrough: Integrar as trocas de reclamações da API REST na sua viagem de utilizador Azure AD B2C para validar a entrada do utilizador

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Quadro de Experiência de Identidade (IEF) que sustenta o Azure Ative Directory B2C (Azure AD B2C) permite que os desenvolvedores de identidade integrem uma interação com uma API RESTful numa viagem de utilizador.  No final desta passagem, poderá criar uma viagem de utilizador Azure AD B2C que interage com [os serviços RESTful](custom-policy-rest-api-intro.md) para validar a entrada do utilizador.

Neste cenário, adicionaremos a capacidade de os utilizadores introduzirem um número de fidelização na página de inscrição Azure AD B2C. Validaremos se esta combinação de e-mail e número de fidelização está mapeada para um código promocional enviando estes dados para uma API REST. Se a API REST encontrar um código promocional para este utilizador, será devolvido ao Azure AD B2C. Finalmente, o código promocional será inserido nos pedidos simbólicos para a aplicação a consumir.

Também pode projetar a interação como um passo de orquestração. Isto é adequado quando a API REST não validará dados no ecrã e sempre devolverá as reclamações. Para obter mais informações, consulte [Walkthrough: Integre as trocas de reclamações da API rest na sua jornada de utilizador Azure AD B2C como um passo de orquestração](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Pré-requisitos

- Complete os passos em [Começar com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar s inscrição com contas locais.
- Saiba como [integrar as reclamações da API REST na sua política personalizada Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>Preparar um ponto final da API REST

Para este passo, você deve ter uma API REST que valida se um endereço de e-mail está registado no seu sistema back-end com um ID de fidelização. Se registada, a API REST deve devolver um código de promoção de registo, que o cliente pode utilizar para comprar bens dentro da sua aplicação. Caso contrário, a API REST deve devolver uma mensagem de erro HTTP 409: "Loyalty ID '{loyalty ID}} não está associada a endereço de e-mail '{email}'.".

O seguinte código JSON ilustra os dados que a Azure AD B2C enviará para o seu ponto final REST API. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Uma vez que a API REST valida os dados, deve devolver um HTTP 200 (Ok), com os seguintes dados JSON:

```json
{
    "promoCode": "24534"
}
```

Se a validação falhar, a API REST deve devolver um HTTP 409 (Conflito), com o `userMessage` elemento JSON. O IEF espera a `userMessage` alegação de que a API resta. Esta alegação será apresentada como um string ao utilizador se a validação falhar.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

A configuração do ponto final da API REST está fora do âmbito deste artigo. Criámos uma amostra [de Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-reference) Pode aceder ao código de função Azure completo no [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definir reclamações

Uma reclamação fornece armazenamento temporário de dados durante uma execução política Azure AD B2C. Pode declarar reclamações dentro da secção [de esquemas de reclamações.](claimsschema.md) 

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
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

## <a name="add-the-restful-api-technical-profile"></a>Adicione o perfil técnico restful da API 

Um [perfil técnico repousante](restful-technical-profile.md) fornece suporte para a interligagem ao seu próprio serviço RESTful. A Azure AD B2C envia dados para o serviço RESTful numa `InputClaims` recolha e recebe dados de volta numa `OutputClaims` recolha. Encontre o elemento **ClaimsProviders** e adicione um novo fornecedor de sinistros da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
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

Neste exemplo, o `userLanguage` será enviado para o serviço REST como dentro da carga útil `lang` JSON. O valor da `userLanguage` reclamação contém o ID do idioma do utilizador atual. Para mais informações, consulte [a reclamação.](claim-resolver-overview.md)

### <a name="configure-the-restful-api-technical-profile"></a>Configurar o perfil técnico restful da API 

Depois de implementar a sua API REST, desaver os metadados do `REST-ValidateProfile` perfil técnico para refletir a sua própria API REST, incluindo:

- **ServiceUrl**. Desaponte o URL do ponto final da API REST.
- **Enviar EmailIn**. Especifique como os pedidos de entrada são enviados para o provedor de reclamações RESTful.
- **AutenticaçãoType**. Desacorda o tipo de autenticação que está a ser realizada pelo provedor de reclamações RESTful. 
- **AllowInsecureAuthInProduction**. Em um ambiente de produção, certifique-se de definir estes metadados para `true`
    
Consulte os [metadados de perfil técnico RESTful](restful-technical-profile.md#metadata) para obter mais configurações.

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que deve fazer quando se muda para um ambiente de produção. Para aprender a proteger as suas APIs RESTful para produção, consulte [API Restful Secure](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Validar a entrada do utilizador

Para obter o número de fidelização do utilizador durante a inscrição, deve permitir ao utilizador introduzir estes dados no ecrã. Adicione a reivindicação de saída **loyaltyId** à página de inscrição adicionando-a ao elemento de perfil técnico de inscrição `OutputClaims` existente. Especifique toda a lista de pedidos de saída para controlar a ordem que as reclamações são apresentadas no ecrã.  

Adicione a referência técnica de validação ao perfil técnico de inscrição, que chama de `REST-ValidateProfile` . O novo perfil técnico de validação será adicionado ao topo da `<ValidationTechnicalProfiles>` coleção definida na política de base. Este comportamento significa que só após validação bem sucedida, a Azure AD B2C avança para criar a conta no diretório.   

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

## <a name="include-a-claim-in-the-token"></a>Incluir uma reclamação no token 

Para devolver o pedido de código promocional à aplicação do partido, adicione uma reclamação de saída ao <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ficheiro. A alegação de saída permitirá que a reclamação seja adicionada ao token após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico dentro da secção do partido dependente para adicionar o `promoCode` como uma reivindicação de saída.
 
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

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **o Quadro de Experiência de Identidade.**
1. Selecione **Carregar a Política Personalizada**e, em seguida, fazer o upload dos ficheiros de política que alterou: *TrustFrameworkExtensions.xml*e *SignUpOrSignin.xml*. 
1. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
1. Deverá inscrever-se através de um endereço de e-mail.
1. Clique na ligação **de inscrição agora.**
1. No **ID**de lealdade, tipo 1234, e clique **em Continuar.** Neste momento, deverá receber uma mensagem de erro de validação.
1. Mude para outro valor e clique em **Continuar.**
1. O token enviado de volta para o seu pedido inclui a `promoCode` reclamação.

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

- [Walkthrough: Integrar as bolsas de reclamações da REST no seu Azure AD B2C como um passo de orquestração](custom-policy-rest-api-claims-exchange.md)
- [Proteja a sua API RESTful](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)
