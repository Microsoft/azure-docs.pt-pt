---
title: Adicionar seus próprios atributos para as políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Um passo a passo sobre como utilizar propriedades de extensão e os atributos personalizados e incluí-los na interface do usuário.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6be2a2d6febfe927cadbdeb12dc91b0e103d6ac4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094631"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Utilizar atributos personalizados num perfil personalizado do Editar política

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, vai criar um atributo personalizado no seu diretório do Azure Active Directory (Azure AD) B2C. Usará esse novo atributo como uma declaração personalizada no percurso do utilizador de Editar perfil.

## <a name="prerequisites"></a>Pré-requisitos

Siga os passos no artigo [Azure Active Directory B2C: Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Utilizar atributos personalizados para recolher informações sobre os seus clientes no Azure AD B2C ao utilizar políticas personalizadas
O diretório do Azure AD B2C vem com um conjunto interno de atributos. Os exemplos são **nome fornecido**, **Apelido**, **City**, **Código Postal**, e **userPrincipalName**. Muitas vezes, terá de criar seus próprios atributos, como nestes exemplos:
* Precisa de uma aplicação do lado do cliente para persistir para um atributo como **LoyaltyNumber.**
* Um fornecedor de identidade tem um identificador de utilizador exclusivo, como **uniqueUserGUID** que tem de ser guardado.
* Precisa de um percurso do utilizador personalizado para persistir para um Estado de um utilizador, como **migrationStatus**.

O Azure AD B2C estende o conjunto de atributos armazenados em cada conta de utilizador. Também pode ler e gravar esses atributos utilizando o [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Propriedades de extensão expandem o esquema dos objetos de utilizador no diretório. Os termos *propriedade de extensão*, *atributo personalizado*, e *afirmação personalizada* consulte a mesma coisa no contexto deste artigo. O nome varia consoante o contexto, como o aplicativo, de objeto ou de política.

Propriedades de extensão só podem ser registadas num objeto de aplicativo, mesmo que eles podem conter dados de um utilizador. A propriedade é anexada à aplicação. O objeto de aplicação tem de ter acesso de escrita para registar uma propriedade de extensão. Propriedades de extensão de uma centena, em todos os tipos e todas as aplicações, podem ser escritas para qualquer objeto único. Propriedades de extensão são adicionadas para o tipo de diretório de destino e ficam imediatamente acessíveis no inquilino do diretório do Azure AD B2C.
Se a aplicação for eliminada, essas propriedades de extensão, juntamente com quaisquer dados contidos nos mesmos para todos os utilizadores também são removidas. Se uma propriedade de extensão for eliminada pela aplicação, este é removido nos objetos de diretório de destino e os valores são eliminados.

Propriedades de extensão existem apenas no contexto de uma aplicação registada no inquilino. O objeto ID dessa aplicação tem de ser incluído nos **TechnicalProfile** que o utiliza.

>[!NOTE]
>O diretório do Azure AD B2C normalmente inclui uma aplicação web com o nome `b2c-extensions-app`. Esta aplicação é principalmente utilizada pelas políticas internas de B2C para as afirmações personalizadas criadas através do portal do Azure. Recomendamos que apenas os utilizadores avançados registem extensões para as políticas personalizadas do B2C ao utilizar esta aplicação.  
As instruções estão incluídas no **próximos passos** secção deste artigo.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Criar uma nova aplicação para armazenar as propriedades de extensão

1. Abra uma sessão de navegação e navegue para o [portal do Azure](https://portal.azure.com). Inicie sessão com as credenciais administrativas do diretório B2C que pretende configurar.
2. Selecione **do Azure Active Directory** no menu de navegação esquerdo. Poderá ter de encontrá-lo selecionando **mais serviços**.
3. Selecione **Registos das aplicações**. Selecione **Novo registo de aplicação**.
4. Forneça as seguintes entradas:
    * Um nome para a aplicação web: **WebApp-GraphAPI-DirectoryExtensions**.
    * O tipo de aplicação: **Aplicação/API Web**.
    * O URL de início de sessão: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Selecione **Criar**.
6. Selecione a aplicação web recentemente criada.
7. Selecione **configurações** > **permissões obrigatórias**.
8. Selecione a API **Windows do Azure Active Directory**.
9. Introduza uma marca de verificação nas permissões de aplicação: **Ler e escrever dados no diretório**. Em seguida, selecione **Guardar**.
10. Escolher **conceder permissões** e confirme **Sim**.
11. Copie os seguintes identificadores para sua área de transferência e salvá-los:
    * **ID da aplicação**. Exemplo: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **ID de objeto**. Exemplo: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Modificar a política personalizada para adicionar o **ApplicationObjectId**

Quando tiver seguido os passos em [Azure Active Directory B2C: Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), transferiu e alterou [arquivos de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) com o nome **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml**, e **PasswordReset.xml**. Neste passo, certifique-se mais modificações a esses ficheiros.

* Abra o **TrustFrameworkBase.xml** de ficheiros e adicionar o `Metadata` secção conforme mostrado no exemplo a seguir. Insira o ID de objeto que registou anteriormente para o `ApplicationObjectId` valor e o ID da aplicação que registou para o `ClientId` valor: 

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
> Quando o **TechnicalProfile** escreve pela primeira vez para a propriedade de extensão criado recentemente, poderá ocorrer um erro de uso individual. A propriedade de extensão é criada na primeira vez que é utilizado.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Utilizar a nova propriedade de extensão ou o atributo personalizado num percurso do utilizador

1. Abra o **ProfileEdit.xml** ficheiro.
2. Adicione uma declaração personalizada `loyaltyId`. Ao incluir o personalizado de afirmação no `<RelyingParty>` elemento, ele está incluído no token para a aplicação.
    
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

3. Abra o **TrustFrameworkExtensions.xml** de ficheiros e adicionar o`<ClaimsSchema>` elemento e seus elementos subordinados para o `BuildingBlocks` elemento:

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

4. Adicionar a mesma `ClaimType` definição à **TrustFrameworkBase.xml**. Não é necessário adicionar um `ClaimType` definição na base e os ficheiros de extensões. No entanto, adicionar os passos seguintes a `extension_loyaltyId` para **os TechnicalProfiles** no ficheiro de base. Portanto, o validador de política rejeita o carregamento do ficheiro base sem ele. Poderá ser útil rastrear a execução do percurso do utilizador com o nome **ProfileEdit** no **TrustFrameworkBase.xml** ficheiro. Procure o percurso do utilizador com o mesmo nome no seu editor. Observe que o passo de orquestração 5 invoca o **TechnicalProfileReferenceID = "SelfAsserted ProfileUpdate**. Pesquisar e inspecionar isso **TechnicalProfile** para se familiarizar com o fluxo.

5. Abra o **TrustFrameworkBase.xml** de ficheiros e adicione `loyaltyId` como uma entrada e saída de afirmações no **TechnicalProfile SelfAsserted-ProfileUpdate**:

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

6. Na **TrustFrameworkBase.xml** do ficheiro, adicione o `loyaltyId` alegam **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Esta adição persistir o valor da afirmação na propriedade de extensão para o utilizador atual no diretório:

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

7. Na **TrustFrameworkBase.xml** de ficheiros, adicione o `loyaltyId` alegam **TechnicalProfile AAD-UserReadUsingObjectId** para ler o valor do atributo de extensão, sempre que um utilizador inicia sessão. Até agora, o **os TechnicalProfiles** foram alterados no fluxo de apenas contas locais. Se pretender que o novo atributo no fluxo de uma conta de redes sociais ou federado, um conjunto diferente de **os TechnicalProfiles** precisa ser alterada. Consulte a **próximos passos** secção.

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

1. Abra o painel do Azure AD B2C e navegue para **arquitetura de experiências de identidade** > **políticas personalizadas**.
1. Selecione a política personalizada que carregou. Selecione **executar agora**.
1. Inscreva-se utilizando um endereço de e-mail.

O token de ID enviados de volta à sua aplicação inclui a nova propriedade de extensão, como uma declaração personalizada precedida por **extension_loyaltyId**. Veja o seguinte exemplo:

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

## <a name="next-steps"></a>Passos Seguintes

1. Adicionar a nova afirmação aos fluxos para iniciar sessão em contas de redes sociais, alterando o seguinte procedimento **os TechnicalProfiles**. Contas de redes sociais e federadas usar esses dois **os TechnicalProfiles** para iniciar sessão. Eles escrever e ler dados de utilizador com o **alternativeSecurityId** como o localizador de objeto do usuário.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Utilize os mesmos atributos de extensão entre as políticas incorporadas e personalizadas. Quando adiciona os atributos de extensão ou personalizado, por meio da experiência do portal, esses atributos são registados utilizando o **b2c-extensions-app** que existe no cada inquilino do B2C. Siga os passos seguintes a utilização de atributos de extensão na sua política personalizada:

   a. No seu inquilino de B2C em portal.azure.com, navegue até **do Azure Active Directory** e selecione **registos das aplicações**.  
   b. Encontre seu **b2c-extensions-app** e selecioná-lo.  
   c. Sob **Essentials**, introduza o **ID de aplicação** e o **ID de objeto**.  
   d. Incluí-los no seu **comuns de AAD** TechnicalProfile metadados:  

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

3. Manter a consistência com a experiência do portal. Utilizar a IU do portal, antes de usá-los em suas políticas personalizadas para criar esses atributos. Quando cria um atributo **ActivationStatus** no portal, deve fazer referência a ele da seguinte forma:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Referência

Para obter mais informações sobre as propriedades de extensão, consulte o artigo [extensões de esquema do | Conceitos da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * R **TechnicalProfile** é um tipo de elemento ou função, que define o nome de um ponto de extremidade, metadados e protocolo. O **TechnicalProfile** fornece detalhes sobre a troca de afirmações que efetua o Framework de experiência de identidade. Quando esta função é chamada num passo de orquestração ou de outro **TechnicalProfile**, o **InputClaims** e **OutputClaims** são fornecidos como parâmetros ao chamador .  
> * Atributos de extensão na API do Graph são nomeados com a Convenção `extension_ApplicationObjectID_attributename`.  
> * As políticas personalizadas, consulte a atributos de extensão, como **extension_attributename**. Esta referência omite a **ApplicationObjectId** em XML.
