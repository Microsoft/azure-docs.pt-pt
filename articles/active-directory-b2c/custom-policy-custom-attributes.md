---
title: Adicione os seus próprios atributos a políticas personalizadas
titleSuffix: Azure AD B2C
description: Uma passagem pela utilização de propriedades de extensão e atributos personalizados e incluindo-os na interface do utilizador.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dbeb2540084fad2cfab3ce360dd15b60a75e5e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389331"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Ative Directory B2C: Ative atributos personalizados numa política de perfil personalizado

Nas reclamações do Add e personalize a entrada do utilizador utilizando o artigo [de políticas personalizadas,](custom-policy-configure-user-input.md) aprende-se a utilizar atributos de perfil de [utilizador incorporados](user-profile-attributes.md). Neste artigo, você ativa um atributo personalizado no seu diretório Azure Ative Diretório B2C (Azure AD B2C). Mais tarde, pode utilizar o novo atributo como uma reivindicação personalizada nos [fluxos do utilizador](user-flow-overview.md) ou políticas [personalizadas](custom-policy-get-started.md) simultaneamente.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos

Siga os passos no artigo [Azure Ative Directory B2C: Começar com políticas personalizadas](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Use atributos personalizados para recolher informações sobre os seus clientes 

O seu diretório Azure AD B2C vem com um [conjunto de atributos incorporados](user-profile-attributes.md). No entanto, muitas vezes é necessário criar os seus próprios atributos para gerir o seu cenário específico, por exemplo, quando:

* Uma aplicação voltada para o cliente precisa de persistir num atributo **LoyaltyId.**
* Um fornecedor de identidade tem um identificador de utilizador único, **uniqueUserGUID,** que deve ser persistido.
* Uma viagem personalizada de utilizador precisa de persistir o estado do utilizador, **migrationStatus,** para que outra lógica funcione.

O Azure AD B2C permite-lhe alargar o conjunto de atributos armazenados em cada conta de utilizador. Também pode ler e escrever estes atributos utilizando a [API](manage-user-accounts-graph-api.md)do Gráfico microsoft .

## <a name="azure-ad-b2c-extensions-app"></a>App de extensões AD B2C Azure AD

Os atributos de extensão só podem ser registados num objeto de aplicação, mesmo que possam conter dados para um utilizador. O atributo de extensão é anexado à aplicação chamada b2c-extensions-app. Não modifique esta aplicação, uma vez que é utilizada pelo Azure AD B2C para armazenar dados do utilizador. Pode encontrar esta aplicação em Azure AD B2C, registos de aplicações.

Os termos *propriedade de extensão,* *atributo personalizado,* e *reivindicação personalizada* referem-se à mesma coisa no contexto deste artigo. O nome varia consoante o contexto, como aplicação, objeto ou política.

## <a name="get-the-application-properties"></a>Obtenha as propriedades da aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App**e, em seguida, selecione Todas as **aplicações**.
1. Selecione a aplicação `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Copie os seguintes identificadores para a sua área de transferência e guarde-os:
    * **ID da Aplicação**. Exemplo: `11111111-1111-1111-1111-111111111111`.
    * **Identificação de objetos.** Exemplo: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Modifique a sua política personalizada

Para ativar atributos personalizados na sua política, forneça **ID de Aplicação** e **ID de Objeto** de Aplicação nos metadados de perfil técnico AAD-Common. O perfil técnico *AAD-Common encontra-se* no perfil técnico do [Azure Ative Directory](active-directory-technical-profile.md) e fornece suporte para a gestão do utilizador Azure AD. Outros perfis técnicos AZure AD incluem o AAD-Common para alavancar a sua configuração. Anular o perfil técnico AAD-Common no ficheiro de extensão.

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Encontre o elemento ClaimsProviders. Adicione um novo Provider de Reclamações ao elemento ClaimsProviders.
1. `ApplicationObjectId`Substitua-o pelo ID do objeto que gravou anteriormente. Em seguida, `ClientId` substitua-o pelo ID de aplicação que já gravou no corte abaixo.

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
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Faça upload da sua política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
4. Selecione **o Quadro de Experiência de Identidade.**
5. Selecione **''Personal's Personal Policy**' '' e, em seguida, faça o upload dos ficheiros de política TrustFrameworkExtensions.xml que alterou.

> [!NOTE]
> A primeira vez que o perfil técnico AZure AD persiste na reclamação ao diretório, verifica se o atributo personalizado existe. Caso contrário, cria o atributo personalizado.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Criar um atributo personalizado através do portal Azure

Os mesmos atributos de extensão são partilhados entre políticas incorporadas e personalizadas. Quando adiciona atributos personalizados através da experiência do portal, esses atributos são registados utilizando a **app de extensões b2c** que existe em cada inquilino B2C.

Pode criar estes atributos utilizando o UI do portal antes ou depois de os utilizar nas suas políticas personalizadas. Siga as orientações para definir [atributos personalizados no Azure Ative Directory B2C](user-flow-custom-attributes.md). Quando criar um **atributo de fidelid** no portal, deve consultar-se da seguinte forma:

|Nome     |Usado em |
|---------|---------|
|`extension_loyaltyId`  | Política personalizada|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

O exemplo a seguir demonstra a utilização de atributos personalizados numa definição de reivindicação de política personalizada Azure AD B2C.

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

O exemplo a seguir demonstra a utilização de um atributo personalizado na política personalizada Azure AD B2C num perfil técnico, entrada, saída e reclamações persistentes.

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

Siga as orientações para adicionar [reclamações e personalizar](custom-policy-configure-user-input.md)a entrada do utilizador utilizando políticas personalizadas . Esta amostra usa uma reivindicação incorporada de "cidade". Para utilizar um atributo personalizado, substitua a 'cidade' com os seus próprios atributos personalizados.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

- [Atributos de perfil de utilizador Azure AD B2C](user-profile-attributes.md)
- [Definição de atributos de extensão](user-profile-attributes.md#extension-attributes)
- [Gerir contas de utilizadores AZure AD B2C com Gráfico microsoft](manage-user-accounts-graph-api.md)
