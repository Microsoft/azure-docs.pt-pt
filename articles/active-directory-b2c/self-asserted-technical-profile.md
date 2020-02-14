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
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a16fb1184de5b545b3ef527b1a66ffb7b68d1ef4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197923"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico autoafirmado numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas as interações no Azure Ative Directory B2C (Azure AD B2C) onde se espera que o utilizador forneça entrada são perfis técnicos autoafirmados. Por exemplo, uma página de inscrição, página de início de sessão ou página de reset de palavra-passe.

## <a name="protocol"></a>Protocolo

O **nome** atributo do elemento **protocolo** tem de ser definido para `Proprietary`. O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolos que é utilizado pelo Azure AD B2C, para autoafirmação: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo seguinte mostra um perfil técnico autoafirmado para a inscrição por e-mail:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Reclamações de entrada

Num perfil técnico autoafirmado, pode utilizar os elementos **InputClaims** e **InputClaimsTransformations** para pré-povoar o valor das reclamações que aparecem na página autoafirmada (alegações de exibição). Por exemplo, na política de perfil de edição, a viagem de utilizador lê primeiro o perfil do utilizador a partir do serviço de diretório Sem Fins AD B2C do Azure, depois o perfil técnico autoafirmado define as reclamações de entrada com os dados do utilizador armazenados no perfil do utilizador. Estas reclamações são recolhidas a partir do perfil do utilizador e depois apresentadas ao utilizador que depois pode editar os dados existentes.

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

## <a name="display-claims"></a>Apresentar reclamações

A função de afirmação do visor encontra-se atualmente em **pré-visualização**.

O elemento **DisplayClaims** contém uma lista de reclamações a apresentar no ecrã para recolher dados do utilizador. Para pré-povoar os valores das reclamações de saída, utilize as alegações de entrada que foram previamente descritas. O elemento também pode conter um valor predefinido.

A ordem das reclamações no **DisplayClaims** especifica a ordem na qual o Azure AD B2C presta as reclamações no ecrã. Para forçar o utilizador a fornecer um valor para uma reclamação específica, detete o atributo **exigido** do elemento **DisplayClaim** para `true`.

O elemento **ClaimType** na coleção **DisplayClaims** precisa de definir o elemento **UserInputType** para qualquer tipo de entrada do utilizador suportado pelo Azure AD B2C. Por exemplo, `TextBox` ou `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Adicione uma referência a um DisplayControl

Na coleção de reclamações do ecrã, pode incluir uma referência a um [DisplayControl](display-controls.md) que criou. Um controlo de exibição é um elemento de interface de utilizador que tem uma funcionalidade especial e interage com o serviço back-end Azure AD B2C. Permite ao utilizador realizar ações na página que invocam um perfil técnico de validação na parte de trás. Por exemplo, verificar um endereço de e-mail, número de telefone ou número de fidelização do cliente.

O exemplo que se segue `TechnicalProfile` ilustra a utilização de reclamações de exibição com controlos de exibição.

* A primeira reclamação de exibição faz referência ao controlo de exibição `emailVerificationControl` que recolhe e verifica o endereço de e-mail.
* A quinta alegação de exibição faz uma referência ao controlo de visualização `phoneVerificationControl` que recolhe e verifica um número de telefone.
* As outras reclamações de ecrã são Alegações a recolher junto do utilizador.

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

Como mencionado, uma reclamação de exibição com referência a um controlo de exibição pode executar a sua própria validação, por exemplo, verificando o endereço de e-mail. Além disso, a página autoafirmada suporta a utilização de um perfil técnico de validação para validar toda a página, incluindo qualquer entrada do utilizador (tipos de reclamação ou controlos de exibição), antes de passar para o próximo passo de orquestração.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Combine cuidadosamente o uso das reclamações de exibição e as alegações de saída

Se especificar um ou mais elementos **DisplayClaim** num perfil técnico autoafirmado, deve utilizar um DisplayClaim para *cada* reclamação que pretenda visualizar no ecrã e recolher do utilizador. Nenhuma reclamação de saída é apresentada por um perfil técnico autoafirmado que contém pelo menos uma reclamação de exibição.

Considere o seguinte exemplo em que uma reivindicação `age` é definida como uma reivindicação de **saída** numa política de base. Antes de adicionar quaisquer reclamações de exibição ao perfil técnico autoafirmado, a alegação `age` é apresentada no ecrã para recolha de dados do utilizador:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Se uma política de folhas que herda essa base especifica rita `officeNumber` como reivindicação de **exibição:**

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

A alegação `age` na política de base já não é apresentada no ecrã ao utilizador - é efetivamente "escondida". Para apresentar a `age` reclamação e recolher o valor de idade do utilizador, deve adicionar um `age` **DisplayClaim**.

## <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de alegações a serem devolvidas ao próximo passo de orquestração. O atributo **DefaultValue** só entra em vigor se a reclamação nunca tiver sido definida. Se tiver sido definido numa etapa de orquestração anterior, o valor predefinido não entra em vigor mesmo que o utilizador deixe o valor vazio. Para forçar a utilização de um valor predefinido, detete o atributo **AlwaysUseDefaultValue** para `true`.

> [!NOTE]
> Em versões anteriores do Quadro de Experiência de Identidade (IEF), as alegações de saída foram utilizadas para recolher dados do utilizador. Para recolher dados do utilizador, utilize uma coleção **DisplayClaims.**

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

### <a name="when-you-should-use-output-claims"></a>Quando deve utilizar reclamações de saída

Num perfil técnico autoafirmado, a recolha de alegações de saída devolve as reivindicações ao próximo passo de orquestração.

Deve utilizar reclamações de saída quando:

- **As reclamações são de produção por transformação**de sinistros de saída.
- **Definindo um valor predefinido numa reclamação** de saída sem recolher dados do utilizador ou devolver os dados do perfil técnico de validação. O `LocalAccountSignUpWithLogonEmail` perfil técnico autoafirmado define a alegação **executada-Auto-Afirmação-Auto-Afirmação-Input** para `true`.
- **Um perfil técnico de validação devolve as reclamações de saída** - O seu perfil técnico pode chamar um perfil técnico de validação que devolve algumas reclamações. É melhor que as faça sabotar as reclamações e devolvê-las aos próximos passos de orquestração na jornada do utilizador. Por exemplo, ao iniciar sessão com uma conta local, o perfil técnico autoafirmado chamado `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação denominado `login-NonInteractive`. Este perfil técnico valida as credenciais do utilizador e também devolve o perfil do utilizador. Como 'userPrincipalName', 'displayName', 'givenName' e 'surName'.
- Um controlo de **visualização devolve as alegações** de saída - O seu perfil técnico pode ter uma referência a um controlo de [exibição](display-controls.md). O controlo do ecrã devolve algumas reclamações, como o endereço de e-mail verificado. É melhor que as faça sabotar as reclamações e devolvê-las aos próximos passos de orquestração na jornada do utilizador. A função de controlo do ecrã encontra-se atualmente em **pré-visualização**.

O exemplo que se segue demonstra a utilização de um perfil técnico autoafirmado que utiliza tanto as reclamações de exibição como as alegações de saída.

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

| Atributo | Necessário | Descrição |
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
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`, ou `false` (predefinido). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** não é utilizado.
