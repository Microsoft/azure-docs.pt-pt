---
title: Adicione os seus próprios atributos a políticas personalizadas
titleSuffix: Azure AD B2C
description: Um walkthrough sobre a utilização de propriedades de extensão e atributos personalizados e incluindo-os na interface do utilizador.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c145b0efa2f1c06710e1d41f606f918a7439820a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189502"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Diretório Ativo Azure B2C: Use atributos personalizados numa política de edição de perfil personalizado

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, cria um atributo personalizado no seu diretório Azure Ative Directory B2C (Azure AD B2C). Utilizará este novo atributo como uma reivindicação personalizada na viagem de utilizador de edição de perfil.

## <a name="prerequisites"></a>Pré-requisitos

Siga os passos do artigo [Azure Ative Directory B2C: Inicie com políticas personalizadas](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Utilize atributos personalizados para recolher informações sobre os seus clientes no Azure AD B2C utilizando políticas personalizadas
O seu diretório Azure AD B2C vem com um conjunto incorporado de atributos. Exemplos são **Nome Dado,** **Sobrenome,** **Cidade,** **Código Postal,** e **userPrincipalName**. Muitas vezes precisa de criar os seus próprios atributos como estes exemplos:
* Uma aplicação virada para o cliente precisa de persistir para um atributo como O Número de **Fidelidade.**
* Um fornecedor de identidade tem um identificador de utilizador único, como **o UserGUID único,** que deve ser guardado.
* Uma viagem personalizada de utilizador precisa de persistir para um estado de utilizador como a **migraçãoStatus**.

O Azure AD B2C alarga o conjunto de atributos armazenados em cada conta de utilizador. Também pode ler e escrever estes atributos utilizando o [Microsoft Graph API](manage-user-accounts-graph-api.md).

As propriedades de extensão estendem o esquema dos objetos do utilizador no diretório. Os termos *de extensão propriedade,* *atributo personalizado,* e *reivindicação personalizada* referem-se à mesma coisa no contexto deste artigo. O nome varia consoante o contexto, como aplicação, objeto ou política.

As propriedades de extensão só podem ser registadas num objeto de aplicação, mesmo que possam conter dados para um utilizador. A propriedade está anexada ao pedido. O objeto de aplicação deve ter acesso por escrito para registar uma propriedade de extensão. Uma centena de propriedades de extensão, em todos os tipos e todas as aplicações, pode ser escrita a qualquer objeto. As propriedades de extensão são adicionadas ao tipo de diretório-alvo e tornam-se imediatamente acessíveis no inquilino do diretório Azure AD B2C.
Se a aplicação for eliminada, essas propriedades de extensão, juntamente com quaisquer dados contidos nas mesmas para todos os utilizadores, também são removidos. Se uma propriedade de extensão for eliminada pela aplicação, é removida nos objetos de diretório-alvo e os valores são eliminados.

Os imóveis de extensão existem apenas no contexto de uma aplicação registada no arrendatário. O ID do objeto dessa aplicação deve ser incluído no **Perfil Técnico** que a utiliza.

>[!NOTE]
>O diretório Azure AD B2C normalmente inclui uma aplicação web chamada `b2c-extensions-app`. Esta aplicação é usada principalmente pelas políticas integradas b2C para as reivindicações personalizadas criadas através do portal Azure. Recomendamos que apenas utilizadores avançados registem extensões para políticas personalizadas B2C utilizando esta aplicação.
As instruções estão incluídas na secção **de passos seguintes** neste artigo.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Criar uma nova aplicação para armazenar as propriedades de extensão

1. Abra uma sessão de navegação e navegue até ao [portal Azure.](https://portal.azure.com) Inscreva-se com as credenciais administrativas do diretório B2C que pretende configurar.
2. Selecione **Azure Ative Directory** no menu de navegação à esquerda. Pode ser necessário encontrá-lo selecionando **mais serviços.**
3. Selecione **Registos das aplicações**. Selecione **Novo registo de aplicação**.
4. Fornecer as seguintes entradas:
    * Um nome para a aplicação web: **WebApp-GraphAPI-Directórios**.
    * O tipo de aplicação: **Web app/API**.
    * O URL de inscrição: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Selecione **Criar**.
6. Selecione a aplicação web recém-criada.
7. Selecione **Definições** > **Permissões Necessárias**.
8. Selecione o Diretório Ativo API **Windows Azure**.
9. Introduza uma marca de verificação nas Permissões de Aplicação: **Leia e escreva dados**de diretório . Em seguida, selecione **Guardar**.
10. Escolha **permissões de Concessão** e confirme **Sim**.
11. Copie os seguintes identificadores na sua área de prancheta e guarde-os:
    * **ID de aplicação**. Exemplo: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Id do objeto**. Exemplo: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Modifique a sua política personalizada para adicionar o **ApplicationObjectId**

Quando seguiu os passos no [Diretório Ativo Azure B2C: Inicie com políticas personalizadas,](custom-policy-get-started.md)descarregou e modificou ficheiros de [amostras](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) denominadotrustFrameworkBase.xml , **TrustFrameworkExtensions.xml,** **SignUpOrSignin.xml,** **ProfileEdit.xml**, e **PasswordReset.xml**. Neste passo, faz estoques mais nesses ficheiros.

* Abra o ficheiro **TrustFrameworkBase.xml** e adicione a secção `Metadata`, como mostra o seguinte exemplo. Insira o ID do objeto que registou anteriormente para o valor `ApplicationObjectId` e o ID de aplicação que registou para o valor `ClientId`:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> Quando o **TechnicalProfile** escrever pela primeira vez para a propriedade de extensão recém-criada, poderá experimentar um erro único. A propriedade de extensão é criada na primeira vez que é usada.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Utilize a nova propriedade de extensão ou atributo personalizado numa viagem de utilizador

1. Abra o ficheiro **ProfileEdit.xml.**
2. Adicione uma reclamação personalizada `loyaltyId`. Ao incluir a alegação personalizada no elemento `<RelyingParty>`, está incluído no símbolo para a aplicação.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Abra o ficheiro **TrustFrameworkExtensions.xml** e adicione o elemento`<ClaimsSchema>` e os seus elementos infantis ao elemento `BuildingBlocks`:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Adicione a mesma definição `ClaimType` a **TrustFrameworkBase.xml**. Não é necessário adicionar uma definição `ClaimType` tanto na base como nos ficheiros de extensões. No entanto, os próximos passos adicionam a `extension_loyaltyId` aos **Perfis Técnicos** no ficheiro base. Assim, o validador de políticas rejeita o upload do ficheiro base sem ele. Pode ser útil rastrear a execução da viagem de utilizador chamada **ProfileEdit** no ficheiro **TrustFrameworkBase.xml.** Procure a viagem do utilizador com o mesmo nome no seu editor. Observe que o passo de orquestração 5 invoca o **TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate**. Procure e inspecione este **Perfil Técnico** para se familiarizar com o fluxo.

5. Abra o ficheiro **TrustFrameworkBase.xml** e adicione `loyaltyId` como uma alegação de entrada e saída no **TechnicalProfile SelfAsserted-ProfileUpdate:**

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. No ficheiro **TrustFrameworkBase.xml,** adicione a `loyaltyId` alegação ao **Perfil Técnico AAD-UserWriteProfileUsingObjectId**. Esta adição persiste o valor da reclamação na propriedade de extensão para o utilizador atual no diretório:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. No ficheiro **TrustFrameworkBase.xml,** adicione a `loyaltyId` alegação ao **TechnicalProfile AAD-UserReadObjectId** para ler o valor do atributo de extensão sempre que um utilizador faz a entrada. Até agora, os **Perfis Técnicos** foram alterados apenas no fluxo de contas locais. Se quiser o novo atributo no fluxo de uma conta social ou federada, é necessário alterar um conjunto diferente de **Perfis Técnicos.** Consulte a secção **seguintes passos.**

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Abra a lâmina Azure AD B2C e navegue para **o Quadro de Experiência de Identidade** > políticas **personalizadas.**
1. Selecione a política personalizada que fez o upload. Selecione **Executar agora**.
1. Inscreva-se utilizando um endereço de e-mail.

O símbolo de identificação enviado de volta para a sua aplicação inclui a nova propriedade de extensão como uma reivindicação personalizada precedida por **extension_loyaltyId**. Veja o seguinte exemplo:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Passos seguintes

1. Adicione a nova reivindicação aos fluxos de entrada nas contas sociais alterando os **seguintes Perfis Técnicos**. Contas sociais e federadas usam estes dois **Perfis Técnicos** para iniciar sessão. Escrevem e lêem os dados dos utilizadores utilizando o **SecurityId alternativo** como localizador do objeto do utilizador.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Use os mesmos atributos de extensão entre políticas incorporadas e personalizadas. Quando adiciona atributos de extensão, ou personalizados através da experiência do portal, esses atributos são registados utilizando a **app b2c-extensões** que existe em cada inquilino B2C. Tome os seguintes passos para utilizar atributos de extensão na sua política personalizada:

   a. Dentro do seu inquilino B2C em portal.azure.com, navegue para **o Azure Ative Directory** e selecione registos de **Aplicações**.
   b. Encontre a sua **aplicação b2c-extensões** e selecione-a.
   c. Em **Essencial,** introduza o ID de **aplicação** e o ID do **objeto**.
   d. Inclua-os nos seus metadados **AAD-Common** TechnicalProfile:

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Mantenha-se consistente com a experiência do portal. Crie estes atributos utilizando o portal UI antes de os utilizar nas suas políticas personalizadas. Quando criar um atributo **ActivaçãoStatus** no portal, deve referir-se ao mesmo:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Referência

Para obter mais informações sobre propriedades de extensão, consulte o artigo [Adicione dados personalizados aos recursos usando extensões](https://docs.microsoft.com/graph/extensibility-overview).

> [!NOTE]
> * Um **Perfil Técnico** é um tipo de elemento, ou função, que define o nome, metadados e protocolo de um ponto final. O **Perfil Técnico** detalha a troca de alegações que o Quadro de Experiência de Identidade realiza. Quando esta função é chamada num passo de orquestração ou de outro **Perfil Técnico,** as **InputClaims** e **OutputClaims** são fornecidas como parâmetros pelo chamador.
> * Os atributos de extensão na API do gráfico são nomeados utilizando a convenção `extension_ApplicationObjectID_attributename`.
> * As políticas personalizadas referem-se a atributos de extensão como **extension_attributename**. Esta referência omite o **ApplicationObjectId** em XML.
> * Tem de especificar o ID do atributo no seguinte formato **extension_attributename** onde quer que esteja a ser referenciado.
