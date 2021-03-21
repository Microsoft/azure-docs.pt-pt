---
title: '| de ponto final do UserInfo Microsoft Docs'
description: Defina um ponto final do UserInfo numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500585"
---
# <a name="userinfo-endpoint"></a>Ponto final UserInfo

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O ponto final do UserInfo faz parte da especificação [padrão OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) e foi concebido para devolver reclamações sobre o utilizador autenticado. O ponto final do UserInfo é definido na política do partido de suporte utilizando o elemento [EndPoint.](relyingparty.md#endpoints)  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Visão geral do ponto final do UserInfo

A informação do utilizador UserJourney especifica:

- **Autorização**: O ponto final do UserInfo está protegido com um token ao portador. Um token de acesso emitido é apresentado no cabeçalho de autorização para o ponto final do UserInfo. A política especifica o perfil técnico que valida o token de entrada e extrai reclamações, como o objectId do utilizador. O objectId do utilizador é utilizado para recuperar as alegações a serem devolvidas na resposta à viagem do ponto final do UserInfo. 
- **Passo de orquestração:** 
  - Um passo de orquestração é usado para recolher informações sobre o utilizador. Com base nas reclamações dentro do token de acesso de entrada, a viagem do utilizador invoca um [perfil técnico do Azure Ative Directory](active-directory-technical-profile.md) para recuperar dados sobre o utilizador, por exemplo, lendo o utilizador pelo objectId. 
  - **Etapas de orquestração opcionais** - Pode adicionar mais passos de orquestração, como um perfil técnico da API REST para obter mais informações sobre o utilizador. 
  - **UserInfo Emitente** - Especifica a lista de reclamações que o ponto final do UserInfo devolve.

## <a name="create-a-userinfo-endpoint"></a>Criar um ponto final do UserInfo

### <a name="1-add-the-token-issuer-technical-profile"></a>1. Adicione o perfil técnico do emitente Token

1. Abra o ficheiro *TrustFrameworkExtensions.xml.*
1. Se já não existir, adicione um elemento ClaimsProvider e os seus elementos infantis como o primeiro elemento sob o elemento BuildingBlocks.
1. Adicione o seguinte fornecedor de sinistros:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. A secção InputClaims dentro do perfil técnico **Do UtilizadorInfoIssuer** especifica os atributos que pretende devolver. O perfil técnico do UserInfoIssuer é chamado no final da viagem do utilizador. 
1. O perfil técnico **de Autorimese deAuthorização do Utilizador** valida a assinatura, nome do emitente e público simbólico, e extrai a reclamação do token de entrada. Altere os seguintes metadados para refletir o seu ambiente:
    1. **emitente** - Este valor deve ser idêntico ao `iss` pedido no âmbito da reclamação simbólica de acesso. Os tokens emitidos pela Azure AD B2C utilizam um emitente no formato `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . Saiba mais sobre [a personalização simbólica.](configure-tokens.md)
    1. **IdTokenAudience** - Deve ser idêntico à `aud` reclamação dentro da reclamação simbólica de acesso. Em Azure AD B2C a `aud` reclamação é a identificação da sua aplicação de partido em gestão. Este valor é uma coleção e suporta múltiplos valores usando um limagem de vírgula.

        No seguinte token de acesso, o `iss` valor da reclamação é `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . O `aud` valor da reclamação `22222222-2222-2222-2222-222222222222` é.

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  O elemento OutputClaims do perfil técnico **userInfoAuthorization** especifica os atributos que pretende ler a partir do token de acesso. O **ClaimTypeReferenceId** é a referência a um tipo de reclamação. O **Parceiro OpcionalClaimType** é o nome da reclamação definida no token de acesso.



### <a name="2-add-the-userjourney-element"></a>2. Adicione o elemento UserJourney 

O elemento [UserJourney](userjourneys.md) define o caminho que o utilizador toma ao interagir com a sua aplicação. Adicione o elemento **UserJourneys** se não existir com a **Jornada do Utilizador** identificada `UserInfoJourney` como:

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. Incluir o ponto final na política do partido em que dependem

Para incluir o ponto final do UserInfo na aplicação do partido de suporte, adicione um elemento [Ponto Final](relyingparty.md#endpoints) ao ficheiro *SocialAndLocalAccounts/SignUpOrSignIn.xml.* 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

O elemento do partido de confiança completa será o seguinte:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. Faça upload dos ficheiros

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **o Quadro de Experiência de Identidade.**
1. Na página **de políticas personalizadas,** selecione **'Carregar' a política personalizada.**
1. Selecione **Overwrite a política personalizada se já existir**, e, em seguida, procure e selecione o ficheiro *TrustframeworkExtensions.xml.*
1. Clique em **Carregar**.
1. Repita os passos 5 a 7 para o ficheiro do partido, como *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Chamando o ponto final do UserInfo

O ponto final do UserInfo faz uso da API padrão do portador OAuth2, chamada através da utilização do token de acesso recebido quando recebe um símbolo para a sua aplicação. Devolve uma resposta JSON contendo alegações sobre o utilizador. O ponto final do UserInfo é hospedado no Azure AD B2C em:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

O ponto final de configuração /.bem conhecido (documento de descoberta OpenID Connect) lista o `userinfo_endpoint` campo. Pode descobrir programáticamente o ponto final do UserInfo utilizando o ponto final de configuração /.bem conhecido em: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Testar a política

1. De acordo com **as políticas Personalizadas,** selecione a política com a que integrou o ponto de terminação Do UtilizadorInfo. Por exemplo, *B2C_1A_SignUpOrSignIn.*
1. Selecione **Executar agora**. 
1. Sob **a aplicação Select**, selecione a sua aplicação que já se registou anteriormente. Para **Select response url**, escolha `https://jwt.ms` . Para mais informações, consulte [Registar uma aplicação web no Azure Ative Directory B2C](tutorial-register-applications.md).
1. Inscreva-se ou inscreva-se com um endereço de e-mail ou uma conta social.
1. Copie o id_token no seu formato codificado a partir do [https://jwt.ms](https://jwt.ms) site. Pode usá-lo para submeter um pedido GET ao ponto final do UserInfo e recuperar as informações do utilizador.
1. Envie um pedido GET para o ponto final do UserInfo e recupere as informações do utilizador.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

Uma resposta bem sucedida seria como:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Passos Seguintes

- Pode encontrar um exemplo de uma política de ponto final do UserInfo no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end
