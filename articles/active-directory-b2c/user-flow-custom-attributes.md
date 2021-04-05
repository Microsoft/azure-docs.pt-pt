---
title: Defina atributos personalizados no Azure Ative Directory B2C | Microsoft Docs
description: Defina atributos personalizados para a sua aplicação no Azure Ative Directory B2C para recolher informações sobre os seus clientes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607922"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Defina atributos personalizados no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Nas reclamações do Add e personalize a entrada do utilizador utilizando o artigo [de políticas personalizadas,](configure-user-input.md) aprende-se a utilizar atributos de perfil de [utilizador incorporados](user-profile-attributes.md). Neste artigo, você ativa um atributo personalizado no seu diretório Azure Ative Diretório B2C (Azure AD B2C). Mais tarde, pode utilizar o novo atributo como uma reivindicação personalizada nos [fluxos do utilizador](user-flow-overview.md) ou políticas [personalizadas](custom-policy-get-started.md) simultaneamente.

O diretório do Azure AD B2C inclui um [conjunto de atributos interno](user-profile-attributes.md). No entanto, muitas vezes é necessário criar os seus próprios atributos para gerir o seu cenário específico, por exemplo, quando:

* Uma aplicação voltada para o cliente precisa de persistir num atributo **LoyaltyId.**
* Um fornecedor de identidade tem um identificador de utilizador único, **uniqueUserGUID,** que deve ser persistido.
* Uma viagem personalizada de utilizador precisa de persistir o estado do utilizador, **migrationStatus,** para que outra lógica funcione.

Os termos *propriedade de extensão,* *atributo personalizado,* e *reivindicação personalizada* referem-se à mesma coisa no contexto deste artigo. O nome varia consoante o contexto, como aplicação, objeto ou política.

O Azure AD B2C permite-lhe alargar o conjunto de atributos armazenados em cada conta de utilizador. Também pode ler e escrever estes atributos utilizando a [API](microsoft-graph-operations.md)do Gráfico microsoft .

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, ao trocá-lo no canto superior direito do portal do Azure. Selecione as suas informações de subscrição e, em seguida, selecione **Trocar Diretório**.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os atributos do Utilizador** e, em seguida, selecione **Adicionar**.
1. Fornecer um **Nome** para o atributo personalizado (por exemplo, "ShoeSize")
1. Escolha um **tipo de dados.** Apenas **String,** **Boolean** e **Int** estão disponíveis.
1. Opcionalmente, introduza uma **Descrição** para fins informativos.
1. Clique em **Criar**.

O atributo personalizado está agora disponível na lista de **atributos** do Utilizador e para utilização nos fluxos do seu utilizador. Um atributo personalizado só é criado na primeira vez que é utilizado em qualquer fluxo de utilizador, e não quando o adiciona à lista de **atributos** do Utilizador .

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Use um atributo personalizado no fluxo do seu utilizador

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **os atributos do Utilizador** e, em seguida, selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **Guardar**.
1. Selecione **as reclamações de aplicação** e, em seguida, selecione o atributo personalizado.
1. Clique em **Guardar**.

Uma vez criado um novo utilizador utilizando um fluxo de utilizador, que utiliza o atributo personalizado recém-criado, o objeto pode ser consultado no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Em alternativa, pode utilizar a função [de fluxo do utilizador Run](./tutorial-create-user-flows.md) no fluxo do utilizador para verificar a experiência do cliente. Deverá agora ver **o ShoeSize** na lista de atributos recolhidos durante a jornada de inscrição e vê-lo no token enviado de volta para a sua aplicação.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>App de extensões AD B2C Azure AD

Os atributos de extensão só podem ser registados num objeto de aplicação, mesmo que possam conter dados para um utilizador. O atributo de extensão é anexado à aplicação denominada `b2c-extensions-app` . Não modifique esta aplicação, uma vez que é utilizada pelo Azure AD B2C para armazenar dados do utilizador. Pode encontrar esta aplicação em Azure AD B2C, registos de aplicações. Obtenha as propriedades da aplicação:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione Todas as **aplicações**.
1. Selecione a aplicação `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Copie os seguintes identificadores para a sua área de transferência e guarde-os:
    * **ID da Aplicação**. Exemplo: `11111111-1111-1111-1111-111111111111`.
    * **Identificação de objetos.** Exemplo: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>Modifique a sua política personalizada

Para ativar atributos personalizados na sua política, forneça **ID de Aplicação** e **ID de Objeto** de Aplicação nos metadados de perfil técnico AAD-Common. O perfil técnico *AAD-Common encontra-se* no perfil técnico do [Azure Ative Directory](active-directory-technical-profile.md) e fornece suporte para a gestão do utilizador Azure AD. Outros perfis técnicos AZure AD incluem o AAD-Common para alavancar a sua configuração. Anular o perfil técnico AAD-Common no ficheiro de extensão.

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Encontre o elemento ClaimsProviders. Adicione um novo Provider de Reclamações ao elemento ClaimsProviders.
1. Insira o **ID de aplicação** que registou anteriormente, entre os elementos de abertura `<Item Key="ClientId">` e `</Item>` fecho.
1. Insira o **ObjectID de aplicação** que registou anteriormente, entre os elementos de abertura `<Item Key="ApplicationObjectId">` e `</Item>` fecho.

    ```xml
    <!-- 
    <ClaimsProviders> -->
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
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>Faça upload da sua política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
4. Selecione **o Quadro de Experiência de Identidade.**
5. Selecione **''Personal's Personal Policy**' '' e, em seguida, faça o upload dos ficheiros de política TrustFrameworkExtensions.xml que alterou.

> [!NOTE]
> A primeira vez que o perfil técnico AZure AD persiste na reclamação ao diretório, verifica se o atributo personalizado existe. Caso contrário, cria o atributo personalizado.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Criar um atributo personalizado através do portal Azure

Os mesmos atributos de extensão são partilhados entre políticas incorporadas e personalizadas. Quando adiciona atributos personalizados através da experiência do portal, esses atributos são registados utilizando a **app de extensões b2c** que existe em cada inquilino B2C.

Pode criar estes atributos utilizando o UI do portal antes ou depois de os utilizar nas suas políticas personalizadas. Quando criar um **atributo de fidelid** no portal, deve consultar-se da seguinte forma:

|Name     |Usado em |
|---------|---------|
|`extension_loyaltyId`  | Política personalizada|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

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

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>Usando atributo personalizado com MS Graph API

A Microsoft Graph API suporta a criação e atualização de um utilizador com atributos de extensão. Os atributos de extensão na API do Gráfico são nomeados através da `extension_ApplicationClientID_attributename` convenção, onde é o `ApplicationClientID` **ID de Aplicação (cliente)** da `b2c-extensions-app` aplicação. Note que o **ID de Aplicação (cliente)** tal como está representado no nome do atributo de extensão não inclui hífenes. Por exemplo:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Passos seguintes

Siga as orientações para adicionar [reclamações e personalizar](configure-user-input.md)a entrada do utilizador utilizando políticas personalizadas . Esta amostra usa uma reivindicação incorporada de "cidade". Para utilizar um atributo personalizado, substitua a 'cidade' com os seus próprios atributos personalizados.
