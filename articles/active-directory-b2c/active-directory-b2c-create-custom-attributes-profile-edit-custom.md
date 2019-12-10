---
title: Adicionar seus próprios atributos a políticas personalizadas
titleSuffix: Azure AD B2C
description: Uma explicação sobre como usar propriedades de extensão e atributos personalizados e incluí-los na interface do usuário.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 92050261777482bae8055f697ef50c2295675c5b
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948782"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: usar atributos personalizados em uma política de edição de perfil personalizado

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, você cria um atributo personalizado no diretório Azure Active Directory B2C (Azure AD B2C). Você usará esse novo atributo como uma declaração personalizada na jornada do usuário de edição de perfil.

## <a name="prerequisites"></a>Pré-requisitos

Siga as etapas no artigo [Azure Active Directory B2C: introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Usar atributos personalizados para coletar informações sobre seus clientes no Azure AD B2C usando políticas personalizadas
Seu diretório de Azure AD B2C é fornecido com um conjunto interno de atributos. São fornecidos os exemplos **nome**, **sobrenome**, **cidade**, **CEP**e **userPrincipalName**. Geralmente, você precisa criar seus próprios atributos, como estes exemplos:
* Um aplicativo voltado para o cliente precisa manter um atributo como **LoyaltyNumber.**
* Um provedor de identidade tem um identificador de usuário exclusivo como **uniqueUserGUID** que deve ser salvo.
* Uma jornada de usuário personalizado precisa persistir para um estado de um usuário como **migrationStatus**.

Azure AD B2C estende o conjunto de atributos armazenados em cada conta de usuário. Você também pode ler e gravar esses atributos usando o [API do Graph do Azure ad](active-directory-b2c-devquickstarts-graph-dotnet.md).

As propriedades de extensão estendem o esquema dos objetos de usuário no diretório. A *propriedade de extensão*de termos, o *atributo personalizado*e a *declaração personalizada* referem-se à mesma coisa no contexto deste artigo. O nome varia dependendo do contexto, como aplicativo, objeto ou política.

As propriedades de extensão só podem ser registradas em um objeto de aplicativo, embora possam conter dados para um usuário. A propriedade é anexada ao aplicativo. O objeto de aplicativo deve ter acesso de gravação para registrar uma propriedade de extensão. Uma centena de propriedades de extensão, em todos os tipos e todos os aplicativos, pode ser gravada em qualquer objeto único. As propriedades de extensão são adicionadas ao tipo de diretório de destino e tornam-se imediatamente acessíveis no locatário do Azure AD B2C Directory.
Se o aplicativo for excluído, essas propriedades de extensão junto com quaisquer dados contidos neles para todos os usuários também serão removidas. Se uma propriedade de extensão for excluída pelo aplicativo, ela será removida nos objetos do diretório de destino e os valores serão excluídos.

As propriedades de extensão existem somente no contexto de um aplicativo registrado no locatário. A ID de objeto desse aplicativo deve ser incluída no **TechnicalProfile** que o utiliza.

>[!NOTE]
>O diretório Azure AD B2C normalmente inclui um aplicativo Web chamado `b2c-extensions-app`. Esse aplicativo é usado principalmente pelas políticas internas do B2C para as declarações personalizadas criadas por meio do portal do Azure. Recomendamos que somente usuários avançados registrem extensões para políticas personalizadas do B2C usando este aplicativo.
As instruções estão incluídas na seção **próximas etapas** neste artigo.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Criar um novo aplicativo para armazenar as propriedades de extensão

1. Abra uma sessão de navegação e navegue até a [portal do Azure](https://portal.azure.com). Entre com as credenciais administrativas do diretório B2C que você deseja configurar.
2. Selecione **Azure Active Directory** no menu de navegação à esquerda. Talvez seja necessário encontrá-lo selecionando **mais serviços**.
3. Selecione **Registos das aplicações**. Selecione **Novo registo de aplicação**.
4. Forneça as seguintes entradas:
    * Um nome para o aplicativo Web: **webapp-GraphAPI-DirectoryExtensions**.
    * O tipo de aplicativo: **aplicativo Web/API**.
    * A URL de logon: **https://{tenantname}. onmicrosoft. com/webapp-GraphAPI-DirectoryExtensions**.
5. Selecione **Criar**.
6. Selecione o aplicativo Web recém-criado.
7. Selecione **configurações** > **permissões necessárias**.
8. Selecione o Azure Active Directory de API do **Windows**.
9. Insira uma marca de seleção em permissões de aplicativo: **ler e gravar dados do diretório**. Em seguida, selecione **Guardar**.
10. Escolha **conceder permissões** e confirmar **Sim**.
11. Copie os seguintes identificadores para a área de transferência e salve-os:
    * **ID da Aplicação**. Exemplo: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **ID do objeto**. Exemplo: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Modifique sua política personalizada para adicionar o **ApplicationObjectId**

Quando você seguiu as etapas em [Azure Active Directory B2C: introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), você baixou e modificou [arquivos de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) denominados **TrustFrameworkBase. xml**, **TrustFrameworkExtensions. xml**, **SignUpOrSignin. xml**, **ProfileEdit. xml**e **PasswordReset. xml**. Nesta etapa, você faz mais modificações nesses arquivos.

* Abra o arquivo **TrustFrameworkBase. xml** e adicione a seção `Metadata`, conforme mostrado no exemplo a seguir. Insira a ID de objeto que você registrou anteriormente para o valor `ApplicationObjectId` e a ID do aplicativo que você registrou para o valor de `ClientId`:

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
> Quando o **TechnicalProfile** grava pela primeira vez na propriedade de extensão recém-criada, você pode experimentar um erro único. A propriedade de extensão é criada na primeira vez em que é usada.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Usar a nova propriedade de extensão ou o atributo personalizado em uma jornada do usuário

1. Abra o arquivo **ProfileEdit. xml** .
2. Adicione uma `loyaltyId`de declaração personalizada. Ao incluir a declaração personalizada no elemento `<RelyingParty>`, ela é incluída no token para o aplicativo.

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

3. Abra o arquivo **TrustFrameworkExtensions. xml** e adicione o elemento`<ClaimsSchema>` e seus elementos filho ao elemento `BuildingBlocks`:

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

4. Adicione a mesma definição de `ClaimType` a **TrustFrameworkBase. xml**. Não é necessário adicionar uma definição de `ClaimType` nos arquivos de extensões e base. No entanto, as próximas etapas adicionam o `extension_loyaltyId` ao **TechnicalProfiles** no arquivo base. Portanto, o validador de política rejeita o carregamento do arquivo base sem ele. Pode ser útil rastrear a execução da jornada do usuário chamada **ProfileEdit** no arquivo **TrustFrameworkBase. xml** . Procure o percurso do usuário com o mesmo nome em seu editor. Observe que a orquestração da etapa 5 invoca o **TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate**. Pesquise e inspecione este **TechnicalProfile** para se familiarizar com o fluxo.

5. Abra o arquivo **TrustFrameworkBase. xml** e adicione `loyaltyId` como uma declaração de entrada e saída no **TechnicalProfile SelfAsserted-ProfileUpdate**:

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

6. No arquivo **TrustFrameworkBase. xml** , adicione a declaração de `loyaltyId` para **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Essa adição persiste o valor da declaração na propriedade de extensão para o usuário atual no diretório:

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

7. No arquivo **TrustFrameworkBase. xml** , adicione a declaração de `loyaltyId` a **TechnicalProfile AAD-UserReadUsingObjectId** para ler o valor do atributo de extensão sempre que um usuário entrar. Até agora, os **TechnicalProfiles** foram alterados apenas no fluxo de contas locais. Se você quiser o novo atributo no fluxo de uma conta social ou federada, um conjunto diferente de **TechnicalProfiles** precisará ser alterado. Consulte a seção **próximas etapas** .

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

1. Abra a folha Azure AD B2C e navegue até **Identity Experience Framework** > **políticas personalizadas**.
1. Selecione a política personalizada que você carregou. Selecione **executar agora**.
1. Inscreva-se usando um endereço de email.

O token de ID enviado de volta para seu aplicativo inclui a nova propriedade de extensão como uma declaração personalizada precedida por **extension_loyaltyId**. Veja o seguinte exemplo:

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

1. Adicione a nova declaração aos fluxos para entrar em contas sociais alterando o seguinte **TechnicalProfiles**. As contas sociais e federadas usam esses dois **TechnicalProfiles** para entrar. Eles gravam e lêem dados do usuário usando o **alternativeSecurityId** como o localizador do objeto de usuário.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Use os mesmos atributos de extensão entre as políticas internas e personalizadas. Quando você adiciona atributos de extensão ou personalizados por meio da experiência do portal, esses atributos são registrados usando o **B2C-Extensions-app** que existe em todos os locatários B2C. Execute as seguintes etapas para usar atributos de extensão em sua política personalizada:

   a. Em seu locatário B2C no portal.azure.com, navegue até **Azure Active Directory** e selecione **registros de aplicativo**.
   b. Localize seu **B2C-Extensions-app** e selecione-o.
   c. Em **Essentials**, insira a **ID do aplicativo** e a **ID do objeto**.
   d. Incluí-los em seus metadados TechnicalProfile **comuns do AAD** :

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

3. Mantenha-se consistente com a experiência do Portal. Crie esses atributos usando a interface do usuário do portal antes de usá-los em suas políticas personalizadas. Ao criar um atributo **ActivationStatus** no portal, você deve consultá-lo da seguinte maneira:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Referência

Para obter mais informações sobre as propriedades de extensão, consulte o artigo [extensões de esquema de diretório | Conceitos de API do Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * Um **TechnicalProfile** é um tipo de elemento, ou função, que define o nome, os metadados e o protocolo de um ponto de extremidade. O **TechnicalProfile** detalha a troca de declarações que a estrutura de experiência de identidade executa. Quando essa função é chamada em uma etapa de orquestração ou de outro **TechnicalProfile**, **InputClaims** e **OutputClaims** são fornecidos como parâmetros pelo chamador.
> * Os atributos de extensão no API do Graph são nomeados usando a Convenção `extension_ApplicationObjectID_attributename`.
> * Políticas personalizadas referem-se aos atributos de extensão como **extension_attributename**. Essa referência omite o **ApplicationObjectId** em XML.
> * Você precisa especificar a ID do atributo no seguinte formato **extension_attributename** onde quer que esteja sendo referenciado.
