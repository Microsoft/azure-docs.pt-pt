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
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc5204518cb6e801ba661aecd5498a501122225f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473664"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Diretório Ativo Azure B2C: Ativar atributos personalizados numa política de perfil personalizado

No [Add reclama e personaliza a entrada](custom-policy-configure-user-input.md) do utilizador usando o artigo de políticas personalizadas, aprende-se a usar [atributos](user-profile-attributes.md)de perfil de utilizador incorporados . Neste artigo, permite um atributo personalizado no seu diretório Azure Ative Directory B2C (Azure AD B2C). Mais tarde, pode utilizar o novo atributo como uma reivindicação personalizada nos [fluxos de utilizador](user-flow-overview.md) ou [políticas personalizadas](custom-policy-get-started.md) simultaneamente.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos

Siga os passos do artigo [Azure Ative Directory B2C: Inicie com políticas personalizadas](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Use atributos personalizados para recolher informações sobre os seus clientes 

O seu diretório Azure AD B2C vem com um [conjunto incorporado de atributos.](user-profile-attributes.md) No entanto, muitas vezes precisa de criar os seus próprios atributos para gerir o seu cenário específico, por exemplo, quando:

* Uma aplicação voltada para o cliente precisa de persistir um atributo **LoyaltyId.**
* Um fornecedor de identidade tem um identificador de utilizador único, **exclusivoUserGUID,** que deve ser persistido.
* Uma viagem personalizada de utilizador precisa de persistir o estado do utilizador, **migrationStatus,** para que outra lógica funcione.

O Azure AD B2C permite-lhe alargar o conjunto de atributos armazenados em cada conta de utilizador. Também pode ler e escrever estes atributos utilizando o [Microsoft Graph API](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>App de extensões Azure AD B2C

Os atributos de extensão só podem ser registados num objeto de aplicação, mesmo que possam conter dados para um utilizador. O atributo de extensão está anexado à aplicação chamada b2c-extensions-app. Não modifique esta aplicação, uma vez que é utilizada pelo Azure AD B2C para armazenar dados dos utilizadores. Pode encontrar esta aplicação no Azure AD B2C, registos de aplicações.

Os termos *de extensão propriedade,* *atributo personalizado,* e *reivindicação personalizada* referem-se à mesma coisa no contexto deste artigo. O nome varia consoante o contexto, como aplicação, objeto ou política.

## <a name="get-the-application-properties"></a>Obtenha as propriedades da aplicação

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Todas as Aplicações**.
1. Selecione a `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` aplicação.
1. Copie os seguintes identificadores na sua área de prancheta e guarde-os:
    * **ID de aplicação**. Exemplo: `11111111-1111-1111-1111-111111111111`.
    * **Id do objeto**. Exemplo: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Modifique a sua política personalizada

Para ativar atributos personalizados na sua política, forneça **ID de aplicação** e ID de **objeto** de aplicação nos metadados de perfil técnico AAD-Common. O perfil técnico *AAD-Common encontra-se* no perfil técnico do [Azure Ative Directory](active-directory-technical-profile.md) base e fornece suporte para a gestão de utilizadores da AD Azure. Outros perfis técnicos da AD Azure incluem o AAD-Common para alavancar a sua configuração. Anular o perfil técnico AAD-Common no ficheiro de extensão.

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>.
1. Encontre o elemento ClaimsProviders. Adicione um novo Fornecedor de Reclamações ao elemento ClaimsProviders.
1. Substitua-a `ApplicationObjectId` com o ID do Objeto que gravou anteriormente. Em `ClientId` seguida, substitua-o pelo ID de aplicação que gravou anteriormente no corte abaixo.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      <TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Faça upload da sua política personalizada

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
4. Selecione Quadro de **Experiência de Identidade**.
5. Selecione **'Upload Custom Policy'** e, em seguida, faça upload dos ficheiros de política TrustFrameworkExtensions.xml que alterou.

> [!NOTE]
> A primeira vez que o perfil técnico da AD Azure persiste na reivindicação do diretório, verifica se o atributo personalizado existe. Caso contrário, cria o atributo personalizado.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Crie um atributo personalizado através do portal Azure

Os mesmos atributos de extensão são partilhados entre políticas incorporadas e personalizadas. Quando adiciona atributos personalizados através da experiência do portal, esses atributos são registados utilizando a **app b2c-extensões** que existe em cada inquilino B2C.

Pode criar estes atributos utilizando o portal UI antes ou depois de os utilizar nas suas políticas personalizadas. Siga as orientações para definir [atributos personalizados no Diretório Ativo Azure B2C](user-flow-custom-attributes.md). Quando cria um **atributo** de fidelidadeId no portal, deve referir-se a ele da seguinte forma:

|Nome     |Usado em |
|---------|---------|
|`extension_loyaltyId`  | Política personalizada|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

O exemplo que se segue demonstra o uso de atributos personalizados numa definição de reivindicação de política personalizada Azure AD B2C.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

O exemplo que se segue demonstra a utilização de um atributo personalizado na política personalizada Azure AD B2C num perfil técnico, entrada, saída e alegações persistentes.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Use um atributo personalizado numa política

Siga as orientações para como adicionar reclamações e personalizar a [entrada do utilizador usando políticas personalizadas](custom-policy-configure-user-input.md). Esta amostra usa uma reivindicação incorporada "cidade". Para utilizar um atributo personalizado, substitua a 'cidade' por seus próprios atributos personalizados.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

- [Atributos de perfil de utilizador Azure AD B2C](user-profile-attributes.md)
- [Definição de atributos de extensão](user-profile-attributes.md#extension-attributes)
- [Gerir contas de utilizadores do Azure AD B2C com o Microsoft Graph](manage-user-accounts-graph-api.md)
