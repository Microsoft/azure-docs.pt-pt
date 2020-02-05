---
title: Defina um perfil técnico autoafirmado numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico autoafirmado numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6c70e1a5c7e5b81157c09a794ff75e276a20d1f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982743"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico autoafirmado numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas as interações no Azure Ative Directory B2C (Azure AD B2C) onde se espera que o utilizador forneça entrada são perfis técnicos autoafirmados. Por exemplo, uma página de inscrição, página de início de sessão ou página de reset de palavra-passe.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolos que é utilizado pelo Azure AD B2C, para autoafirmação: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo seguinte mostra um perfil técnico autoafirmado para a inscrição por e-mail:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Declarações de entrada

Em um perfil técnico autodeclarado, você pode usar os elementos **InputClaims** e **InputClaimsTransformations** para pré-popular o valor das declarações que aparecem na página autodeclarada (Exibir declarações). Por exemplo, na política de perfil de edição, a viagem de utilizador lê primeiro o perfil do utilizador a partir do serviço de diretório Sem Fins AD B2C do Azure, depois o perfil técnico autoafirmado define as reclamações de entrada com os dados do utilizador armazenados no perfil do utilizador. Estas reclamações são recolhidas a partir do perfil do utilizador e depois apresentadas ao utilizador que depois pode editar os dados existentes.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Exibir declarações

O recurso Exibir declarações está em **Visualização**no momento.

O elemento **DisplayClaims** contém uma lista de declarações a serem apresentadas na tela para coletar dados do usuário. Para preencher previamente os valores das declarações de saída, use as declarações de entrada que foram descritas anteriormente. O elemento também pode conter um valor predefinido.

A ordem das declarações em **DisplayClaims** especifica a ordem na qual Azure ad B2C renderiza as declarações na tela. Para forçar o usuário a fornecer um valor para uma declaração específica, defina o atributo **Required** do elemento **DisplayClaim** como `true`.

O elemento **ClaimType** na coleção **DisplayClaims** precisa definir o elemento **userinputtype** como qualquer tipo de entrada de usuário com suporte pelo Azure ad B2C. Por exemplo, `TextBox` ou `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Adicionar uma referência a um DisplayControl

Na coleção exibir declarações, você pode incluir uma referência a um [DisplayControl](display-controls.md) que você criou. Um controle de exibição é um elemento de interface do usuário que tem funcionalidade especial e interage com o serviço de back-end Azure AD B2C. Ele permite que o usuário execute ações na página que invocam um perfil técnico de validação no back-end. Por exemplo, verificar um endereço de email, número de telefone ou número de fidelidade do cliente.

O exemplo a seguir `TechnicalProfile` ilustra o uso de declarações de exibição com controles de exibição.

* A primeira declaração de exibição faz uma referência para o `emailVerificationControl` controle de exibição que coleta e verifica o endereço de email.
* A quinta declaração de exibição faz uma referência para o `phoneVerificationControl` controle de exibição que coleta e verifica um número de telefone.
* As outras declarações de exibição são ClaimTypes a serem coletadas do usuário.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Como mencionado, uma declaração de exibição com uma referência a um controle de exibição pode executar sua própria validação, por exemplo, verificar o endereço de email. Além disso, a página autodeclarada dá suporte ao uso de um perfil técnico de validação para validar a página inteira, incluindo qualquer entrada do usuário (tipos de declaração ou controles de exibição), antes de passar para a próxima etapa de orquestração.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Combine o uso de declarações de exibição e declarações de saída com cuidado

Se você especificar um ou mais elementos **DisplayClaim** em um perfil técnico autodeclarado, deverá usar um DisplayClaim para *cada* declaração que você deseja exibir na tela e coletar do usuário. Nenhuma declaração de saída é exibida por um perfil técnico autodeclarado que contém pelo menos uma declaração de exibição.

Considere o exemplo a seguir no qual uma declaração de `age` é definida como uma declaração de **saída** em uma política de base. Antes de adicionar qualquer declaração de exibição ao perfil técnico autodeclarado, a declaração de `age` é exibida na tela para coleta de dados do usuário:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Se uma política de folha que herda essa base subsequentemente especificar `officeNumber` como uma declaração de **exibição** :

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

A declaração de `age` na política de base não é mais apresentada na tela ao usuário; ela é efetivamente "oculta". Para exibir a declaração de `age` e coletar o valor de idade do usuário, você deve adicionar um `age` **DisplayClaim**.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações a serem retornadas para a próxima etapa de orquestração. O atributo **DefaultValue** terá efeito somente se a declaração nunca tiver sido definida. Se ele foi definido em uma etapa de orquestração anterior, o valor padrão não terá efeito mesmo que o usuário deixe o valor vazio. Para forçar a utilização de um valor predefinido, detete o atributo **AlwaysUseDefaultValue** para `true`.

> [!NOTE]
> Em versões anteriores do IEF (Identity Experience Framework), as declarações de saída eram usadas para coletar dados do usuário. Para coletar dados do usuário, use uma coleção **DisplayClaims** em vez disso.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

### <a name="when-you-should-use-output-claims"></a>Quando você deve usar declarações de saída

Em um perfil técnico autodeclarado, a coleção de declarações de saída retorna as declarações para a próxima etapa de orquestração.

Você deve usar declarações de saída quando:

- As **declarações são saídas pela transformação declarações de saída**.
- **Definir um valor padrão em uma declaração de saída** sem coletar dados do usuário ou retornar os dados do perfil técnico de validação. O `LocalAccountSignUpWithLogonEmail` perfil técnico autoafirmado define a alegação **executada-Auto-Afirmação-Auto-Afirmação-Input** para `true`.
- **Um perfil técnico de validação devolve as reclamações de saída** - O seu perfil técnico pode chamar um perfil técnico de validação que devolve algumas reclamações. É melhor que as faça sabotar as reclamações e devolvê-las aos próximos passos de orquestração na jornada do utilizador. Por exemplo, ao iniciar sessão com uma conta local, o perfil técnico autoafirmado chamado `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação denominado `login-NonInteractive`. Este perfil técnico valida as credenciais do utilizador e também devolve o perfil do utilizador. Como 'userPrincipalName', 'displayName', 'givenName' e 'surName'.
- **Um controle de exibição retorna as declarações de saída** – seu perfil técnico pode ter uma referência a um [controle de exibição](display-controls.md). O controle de exibição retorna algumas declarações, como o endereço de email verificado. É melhor que as faça sabotar as reclamações e devolvê-las aos próximos passos de orquestração na jornada do utilizador. O recurso de controle de exibição está atualmente em **Visualização**.

O exemplo a seguir demonstra o uso de um perfil técnico autodeclarado que usa declarações de exibição e declarações de saída.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Persistir reivindicações

Se o elemento **PersistedClaims** estiver ausente, o perfil técnico autoafirmado não persiste os dados para o Azure AD B2C. Em vez disso, é feita uma chamada para um perfil técnico de validação responsável por persistir os dados. Por exemplo, a política de inscrição utiliza o `LocalAccountSignUpWithLogonEmail` perfil técnico autoafirmado para recolher o novo perfil de utilizador. O perfil técnico `LocalAccountSignUpWithLogonEmail` chama o perfil técnico de validação para criar a conta em Azure AD B2C.

## <a name="validation-technical-profiles"></a>Perfis técnicos de validação

Um perfil técnico de validação é utilizado para validar algumas ou todas as reclamações de saída do perfil técnico de referência. As alegações de entrada do perfil técnico de validação devem figurar nas alegações de saída do perfil técnico autoafirmado. O perfil técnico de validação valida a entrada do utilizador e pode devolver um erro ao utilizador.

O perfil técnico de validação pode ser qualquer perfil técnico na política, como [o Azure Ative Directory](active-directory-technical-profile.md) ou um perfil técnico [REST API.](restful-technical-profile.md) No exemplo anterior, o perfil técnico `LocalAccountSignUpWithLogonEmail` valida que o signinName não existe no diretório. Caso contrário, o perfil técnico de validação cria uma conta local e devolve o objectid, autenticaçãoSource, newUser. O perfil técnico `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação `login-NonInteractive` para validar as credenciais do utilizador.

Também pode ligar para um perfil técnico REST API com a sua lógica de negócio, substituir reclamações de entrada ou enriquecer os dados dos utilizadores, integrando-se ainda mais com a aplicação de linha de negócio corporativa. Para mais informações, consulte [perfil técnico de validação](validation-technical-profile.md)

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| setting.operatingMode | Não | Para uma página de entrada, esta propriedade controla o comportamento do campo username, como a validação de entrada e mensagens de erro. Valores esperados: `Username` ou `Email`. |
| Permitir Gerações De Reclamações Com Valores Nulos| Não| Permitir gerar uma reclamação com valor nulo. Por exemplo, num caso, o utilizador não seleciona uma caixa de verificação.|
| ContentDefinitionReferenceId | Sim | O identificador da [definição](contentdefinitions.md) de conteúdo associado a este perfil técnico. |
| EnforceEmailVerification | Não | Para a inscrição ou edição de perfil, impõe a verificação de e-mail. Valores possíveis: `true` (predefinido) ou `false`. |
| setting.retryLimit | Não | Controla o número de vezes que um utilizador pode tentar fornecer os dados verificados com um perfil técnico de validação . Por exemplo, um utilizador tenta inscrever-se numa conta que já existe e continua a tentar até que o limite atinja.
| Alvo de inscrição | Não | O identificador de troca de alvos de inscrição. Quando o utilizador clica no botão de inscrição, o Azure AD B2C executa o identificador de troca especificado. |
| setting.showCancelButton | Não | Exibe o botão de cancelamento. Valores possíveis: `true` (padrão) ou `false` |
| setting.showContinueButton | Não | Exibe o botão de continuação. Valores possíveis: `true` (padrão) ou `false` |
| setting.showSignupLink | Não | Exibe o botão de inscrição. Valores possíveis: `true` (padrão) ou `false` |
| definição.esqueciPasswordLinkLocation| Não| Exibe o link de senha esquecido. Valores possíveis: `AfterInput` (predefinido) o link é apresentado na parte inferior da página ou `None` remove o link de senha esquecido.| 
## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** não é utilizado.
