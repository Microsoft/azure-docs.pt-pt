---
title: Adicione atributos do utilizador e personalize a entrada do utilizador
titleSuffix: Azure AD B2C
description: Saiba como personalizar a entrada do utilizador e adicione atributos do utilizador à viagem de inscrição ou de entrada em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 698864a4dc1081cb8cad9036ff1cfc737a17473c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111480"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Adicione atributos do utilizador e personalize a entrada do utilizador no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Neste artigo, você recolhe um novo atributo durante a sua viagem de inscrição em Azure Ative Directory B2C (Azure AD B2C). Você vai obter a cidade dos utilizadores, configurá-la como uma entrega, e definir se é necessário fornecer.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Adicionar atributos do utilizador ao seu fluxo de utilizador

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **os atributos do Utilizador** e, em seguida, selecione o atributo do utilizador (por exemplo, "City"). 
1. Selecione **Guardar**.

## <a name="provide-optional-claims-to-your-app"></a>Fornecer reclamações opcionais à sua app

As reclamações da aplicação são valores que são devolvidos ao pedido. Atualize o fluxo do utilizador para conter as reclamações desejadas.

1. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **Afirmações de aplicação**.
1. Selecione os atributos que pretende enviar de volta para a sua aplicação (por exemplo, "City").
1. Selecione **Guardar**.
 
## <a name="configure-user-attributes-input-type"></a>Configure o tipo de entrada de atributos do utilizador

1. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **layouts de página**.
1. Selecione **a página de inscrição de conta local**.
1. Nos **atributos do Utilizador**, selecione **City**.
    1. No drop-down do tipo de entrada do **Utilizador,** selecione **DropdownSingleSelect**.
    1. No drop-down **opcional,** selecione **Nº**.
1. Selecione **Guardar**. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Fornecer uma lista de valores utilizando coleções localizadas

Para fornecer uma lista definida de valores para o atributo da cidade: 

1. [Permitir a personalização da linguagem no fluxo do utilizador](language-customization.md#support-requested-languages-for-ui_locales)
1. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Na página **idiomas** para o fluxo do utilizador, selecione o idioma que pretende personalizar.
1. Nos **ficheiros de recursos de nível de página**, selecione página de **inscrição de conta local**.
1. Selecione **Descarregamento predefinido** (ou **Descarregue sobreposições** se já tiver editado este idioma).
1. Criar um `LocalizedCollections` atributo.

É `LocalizedCollections` uma variedade de `Name` `Value` pares. A encomenda dos artigos será a ordem que são apresentadas. 

* `ElementId` é o atributo do utilizador a que este `LocalizedCollections` atributo é uma resposta.
* `Name` é o valor que é mostrado ao utilizador.
* `Value` é o que é devolvido na reclamação quando esta opção é selecionada.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Faça upload das suas alterações

1. Depois de completar as alterações ao seu ficheiro JSON, volte para o seu inquilino B2C.
1. Selecione **os fluxos do Utilizador** e selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **Línguas**.
1. Selecione o idioma para o quais pretende traduzir.
1. Selecione a **página de inscrição** da conta Local .
1. Selecione o ícone da pasta e selecione o ficheiro JSON para carregar. As alterações são guardadas automaticamente no fluxo do utilizador.

## <a name="test-your-user-flow"></a>Teste o fluxo do seu utilizador

1. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador executar**

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> Esta amostra usa a reivindicação incorporada "cidade". Em vez disso, pode escolher um dos [atributos incorporados Azure AD B2C suportados](user-profile-attributes.md) ou um atributo personalizado. Para utilizar um atributo personalizado, [ative atributos personalizados na sua política](custom-policy-custom-attributes.md). Para utilizar um atributo incorporado ou personalizado diferente, substitua a 'cidade' pelo atributo à sua escolha, por exemplo, o *atributo incorporado, o JobTit* ou um atributo personalizado como *extension_loyaltyId*.  

Pode recolher dados iniciais dos seus utilizadores utilizando a viagem de utilizador de inscrição ou de inscrição. As reclamações adicionais podem ser recolhidas mais tarde utilizando uma viagem de utilizador de edição de perfil. Sempre que a Azure AD B2C recolhe informações diretamente do utilizador interativamente, o Quadro de Experiência de Identidade utiliza o seu [perfil técnico autoafirmado.](self-asserted-technical-profile.md) Nesta amostra, você:

1. Defina uma reivindicação de "cidade". 
1. Peça ao utilizador pela sua cidade.
1. Persistir a cidade para o perfil de utilizador no diretório Azure AD B2C.
1. Leia a reclamação da cidade do diretório Azure AD B2C em cada inscrição.
1. Devolva a cidade à sua candidatura de festa de gestão após a inscrição ou inscrição.  

## <a name="define-a-claim"></a>Definir uma reclamação

Uma reclamação fornece um armazenamento temporário de dados durante uma execução política Azure AD B2C. O [esquema de reclamações](claimsschema.md) é o lugar onde declara as suas reivindicações. São utilizados os seguintes elementos para definir a alegação:

- **DisplayName** - Uma corda que define a etiqueta virada para o utilizador.
- [DataType](claimsschema.md#datatype) - O tipo de reclamação.
- **UserHelpText** - Ajuda o utilizador a compreender o que é necessário.
- [UserInputType](claimsschema.md#userinputtype) - O tipo de controlo de entrada, como caixa de texto, seleção de rádio, lista de abandono ou múltiplas seleções.

Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione a reivindicação da cidade ao elemento **ClaimsSchema.**  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Adicionar uma reclamação à interface de utilizador

Os seguintes perfis técnicos são [autoafirmados,](self-asserted-technical-profile.md)invocados quando se espera que um utilizador forneça informações:

- **LocalAccountSignUpWithLogonEmail** - Fluxo de inscrição de conta local.
- **SelfAsserted-Social** - Conta federada insinusado pela primeira vez.
- **SelfAsserted-ProfileUpdate** - Editar o fluxo de perfil.

Para recolher a reclamação da cidade durante a inscrição, deve ser adicionado como uma reivindicação de saída ao `LocalAccountSignUpWithLogonEmail` perfil técnico. Substitua este perfil técnico no ficheiro de extensão. Especifique toda a lista de pedidos de saída para controlar a ordem que as reclamações são apresentadas no ecrã. Encontre o elemento **ClaimsProviders.** Adicione um novo ClaimsProviders da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Para recolher a reclamação da cidade após a iniciação inicial com uma conta federada, deve ser adicionado como um pedido de saída ao `SelfAsserted-Social` perfil técnico. Para que os utilizadores de contas locais e federados possam editar os seus dados de perfil mais tarde, adicione a reivindicação de saída ao `SelfAsserted-ProfileUpdate` perfil técnico. Substitua estes perfis técnicos no ficheiro de extensão. Especifique toda a lista dos pedidos de saída para controlar a ordem que as reclamações são apresentadas no ecrã. Encontre o elemento **ClaimsProviders.** Adicione um novo ClaimsProviders da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Leia e escreva uma reclamação

Os seguintes perfis técnicos são [perfis técnicos do Ative Directory,](active-directory-technical-profile.md)que lêem e escrevem dados para o Azure Ative Directory.  
Utilize `PersistedClaims` para escrever dados para o perfil do utilizador e `OutputClaims` para ler dados a partir do perfil do utilizador dentro dos respetivos perfis técnicos do Ative Directory.

Substitua estes perfis técnicos no ficheiro de extensão. Encontre o elemento **ClaimsProviders.**  Adicione um novo ClaimsProviders da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Incluir uma reclamação no token 

Para devolver o pedido da cidade ao pedido do partido, adicione um pedido de saída ao <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ficheiro. A alegação de saída será adicionada ao token após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico dentro da secção do partido dependente para adicionar a cidade como uma reivindicação de saída.
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
4. Selecione **o Quadro de Experiência de Identidade.**
5. Selecione **''Personal's Personal Policy ' 'Upload'** e, em seguida, faça o upload dos dois ficheiros de política que alterou.
2. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
3. Deverá inscrever-se através de um endereço de e-mail.

::: zone-end

O ecrã de inscrição deve ser semelhante ao seguinte screenshot:

![Screenshot da opção de inscrição modificada](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

O token enviado de volta para o seu pedido inclui a `city` reclamação.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o elemento [ClaimsSchema](claimsschema.md) na referência IEF.
- Saiba como [usar atributos personalizados numa política de edição de perfis personalizados.](custom-policy-custom-attributes.md)

::: zone-end