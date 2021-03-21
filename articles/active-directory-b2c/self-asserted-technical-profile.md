---
title: Defina um perfil técnico autoafirmado numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico autoafirmado numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d3343838216522abfc11ec3f202ae2da1c0e38f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611883"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico autoafirmado numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas as interações no Azure Ative Directory B2C (Azure AD B2C) onde se espera que o utilizador forneça entradas são perfis técnicos autoafirmados. Por exemplo, uma página de inscrição, página de inscrição ou página de reset de palavra-passe.

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O atributo **do manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo utilizado pela Azure AD B2C, para autoafirmação: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico autoafirmado para inscrição de e-mail:

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Reclamações de entrada

Num perfil técnico autoafirmado, pode utilizar os **elementos InputClaims** e **InputClaimsTransformations** para pré-povoar o valor das alegações que aparecem na página autoafirmada (alegações de exibição). Por exemplo, na política de perfil de edição, a jornada do utilizador lê primeiro o perfil do utilizador a partir do serviço de diretório Azure AD B2C, em seguida, o perfil técnico autoafirmado define as alegações de entrada com os dados do utilizador armazenados no perfil do utilizador. Estas reclamações são recolhidas a partir do perfil do utilizador e depois apresentadas ao utilizador que pode depois editar os dados existentes.

```xml
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

A funcionalidade de reclamações do visor encontra-se atualmente em **pré-visualização**.

O elemento **DisplayClaims** contém uma lista de reclamações a apresentar no ecrã para recolher dados do utilizador. Para pré-povoar os valores das reclamações do visor, utilize as alegações de entrada que foram previamente descritas. O elemento também pode conter um valor predefinido.

A ordem das reclamações no **DisplayClaims** especifica a ordem em que a Azure AD B2C apresenta as reclamações no ecrã. Para forçar o utilizador a fornecer um valor para uma reclamação específica, desa estale o atributo **exigido** do elemento **DisplayClaim** para `true` .

O elemento **ClaimType** na coleção **DisplayClaims** necessita de definir o elemento **UserInputType** em qualquer tipo de entrada de utilizador suportado por Azure AD B2C. Por exemplo, `TextBox` ou `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Adicione uma referência a um DisplayControl

Na coleção de reclamações do visor, pode incluir uma referência a um [DisplayControl](display-controls.md) que criou. Um controlo de exibição é um elemento de interface do utilizador que tem uma funcionalidade especial e interage com o serviço de back-end Azure AD B2C. Permite ao utilizador executar ações na página que invocam um perfil técnico de validação na parte de trás. Por exemplo, verificar um endereço de e-mail, número de telefone ou número de fidelização do cliente.

O exemplo a seguir `TechnicalProfile` ilustra a utilização de reclamações de visualização com controlos de visualização.

* A primeira alegação do visor faz uma referência ao controlo do `emailVerificationControl` ecrã, que recolhe e verifica o endereço de e-mail.
* A quinta alegação do visor faz uma referência ao controlo do `phoneVerificationControl` visor, que recolhe e verifica um número de telefone.
* As outras alegações de exibição são ClaimTypes a serem recolhidas junto do utilizador.

```xml
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

Como mencionado, uma alegação de exibição com referência a um controlo de exibição pode executar a sua própria validação, por exemplo, verificando o endereço de e-mail. Além disso, a página autoafirmada suporta usando um perfil técnico de validação para validar toda a página, incluindo qualquer entrada do utilizador (tipos de reclamação ou controlos de exibição), antes de passar para o passo de orquestração seguinte.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Combine o uso de reclamações de exibição e reivindicações de saída cuidadosamente

Se especificar um ou mais elementos **Do DisplayClaim** num perfil técnico autoafirmado, deve utilizar um DisplayClaim para *cada* alegação que pretende apresentar no ecrã e recolher do utilizador. Nenhuma reclamação de saída é apresentada por um perfil técnico autoafirmado que contém pelo menos uma reivindicação de exibição.

Considere o exemplo a seguir no qual uma `age` reclamação é definida como uma reivindicação **de saída** numa política de base. Antes de adicionar quaisquer reclamações de exibição ao perfil técnico autoafirmado, a `age` alegação é apresentada no ecrã para recolha de dados do utilizador:

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Se uma política de folhas que herda essa base especificar posteriormente `officeNumber` como uma **reivindicação de exibição:**

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

A `age` alegação na política de base já não é apresentada no ecrã ao utilizador - é efetivamente "escondida". Para apresentar a `age` reclamação e recolher o valor de idade do utilizador, tem de adicionar um `age` **DisplayClaim**.

## <a name="output-claims"></a>Reclamações de saída

O elemento **OutputClaims** contém uma lista de reivindicações a serem devolvidas ao próximo passo de orquestração. O atributo **DefaultValue** só entra em vigor se a reclamação nunca tiver sido definida. Se foi definido numa etapa de orquestração anterior, o valor predefinido não entra em vigor mesmo que o utilizador deixe o valor vazio. Para forçar a utilização de um valor predefinido, deite o atributo **AlwaysUseDefaultValue** a `true` .

Por razões de segurança, um valor de reclamação de senha `UserInputType` (definido para `Password` ) está disponível apenas para os perfis técnicos de validação do perfil técnico autoafirmado. Não é possível utilizar a reclamação de palavra-passe nos próximos passos de orquestração. 

> [!NOTE]
> Em versões anteriores do Quadro de Experiência de Identidade (IEF), foram utilizadas alegações de produção para recolher dados do utilizador. Para recolher dados do utilizador, utilize uma recolha **DisplayClaims.**

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="when-you-should-use-output-claims"></a>Quando deve utilizar pedidos de saída

Num perfil técnico autoafirmado, a coleção de reclamações de saída devolve as reivindicações ao próximo passo de orquestração.

Utilize reclamações de saída quando:

- **As reclamações são a transformação de pedidos de produção por produção.**
- **Definir um valor predefinido numa reclamação de saída** sem recolher dados do utilizador ou devolver os dados do perfil técnico de validação. O `LocalAccountSignUpWithLogonEmail` perfil técnico autoafirmado define a reivindicação **executada-SelfAsserted-Input** para `true` .
- **Um perfil técnico de validação devolve as reclamações de saída** - O seu perfil técnico pode chamar um perfil técnico de validação que devolve algumas reclamações. Pode querer borbulhar as reclamações e devolvê-las aos próximos passos de orquestração na jornada do utilizador. Por exemplo, ao iniciar sessão com uma conta local, o perfil técnico autoafirmado chamado `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação denominado `login-NonInteractive` . Este perfil técnico valida as credenciais do utilizador e também devolve o perfil do utilizador. Como 'userPrincipalName', 'displayName', 'givenName' e 'surName'.
- **Um controlo de visualização devolve as reclamações de saída** - O seu perfil técnico pode ter uma referência a um [controlo de exibição](display-controls.md). O controlo do ecrã devolve algumas reclamações, tais como o endereço de e-mail verificado. Pode querer borbulhar as reclamações e devolvê-las aos próximos passos de orquestração na jornada do utilizador. A função de controlo do ecrã encontra-se atualmente em **pré-visualização**.

O exemplo a seguir demonstra a utilização de um perfil técnico autoafirmado que utiliza reivindicações de exibição e reivindicações de saída.

```xml
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

### <a name="output-claims-sign-up-or-sign-in-page"></a>Saída reclama inscrição ou página de inscrição

Numa página combinada de inscrição e inscrição, note o seguinte ao utilizar um elemento [DataUri](contentdefinitions.md#datauri) de definição de conteúdo que especifica um `unifiedssp` ou tipo de `unifiedssd` página:

- Apenas o nome de utilizador e as reclamações de palavra-passe são prestados.
- As duas primeiras reclamações de saída devem ser o nome de utilizador e a palavra-passe (nesta ordem). 
- Quaisquer outras reclamações não são prestadas; para estas reclamações, terá de definir ou invocar um perfil técnico de validação de `defaultValue` formulários de reclamações. 

## <a name="persist-claims"></a>Persistir reivindicações

O elemento PersistedClaims não é utilizado. O perfil técnico autoafirmado não persiste os dados do Azure AD B2C. Em vez disso, é feita uma chamada para um perfil técnico de validação que é responsável pela persistência dos dados. Por exemplo, a política de inscrição utiliza o `LocalAccountSignUpWithLogonEmail` perfil técnico autoafirmado para recolher o novo perfil de utilizador. O `LocalAccountSignUpWithLogonEmail` perfil técnico chama o perfil técnico de validação para criar a conta em Azure AD B2C.

## <a name="validation-technical-profiles"></a>Perfis técnicos de validação

É utilizado um perfil técnico de validação para validar algumas ou todas as reclamações de saída do perfil técnico de referência. As alegações de entrada do perfil técnico de validação devem figurar nas alegações de saída do perfil técnico autoafirmado. O perfil técnico de validação valida a entrada do utilizador e pode devolver um erro ao utilizador.

O perfil técnico de validação pode ser qualquer perfil técnico na política, como o [Azure Ative Directory](active-directory-technical-profile.md) ou um rest [API](restful-technical-profile.md) perfis técnicos. No exemplo anterior, o `LocalAccountSignUpWithLogonEmail` perfil técnico valida que o nome de inscrição não existe no diretório. Caso contrário, o perfil técnico de validação cria uma conta local e devolve o objectId, autenticaçãoSource, newUser. O `SelfAsserted-LocalAccountSignin-Email` perfil técnico chama o perfil técnico de `login-NonInteractive` validação para validar as credenciais do utilizador.

Também pode chamar um perfil técnico de API REST com a sua lógica de negócio, substituir as reclamações de entrada ou enriquecer os dados dos utilizadores, integrando-se ainda mais com a aplicação de linha de negócio corporativa. Para mais informações, consulte [o perfil técnico de Validação](validation-technical-profile.md)

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| definição.operandoMode <sup>1</sup>| No | Para uma página de entrada, esta propriedade controla o comportamento do campo de nome de utilizador, como validação de entradas e mensagens de erro. Valores esperados: `Username` ou `Email` .  |
| Permitir aGerationOfClaimsWithNullValues| No| Permitir gerar uma reclamação com valor nulo. Por exemplo, num caso, o utilizador não seleciona uma caixa de verificação.|
| ConteúdoDefinitionReferenceId | Yes | O identificador da [definição](contentdefinitions.md) de conteúdo associado a este perfil técnico. |
| Reforçar aVerificação | No | Para inscrição ou edição de perfil, aplica a verificação de e-mail. Valores possíveis: `true` (padrão), ou `false` . |
| definição.retripsLimit | No | Controla o número de vezes que um utilizador pode tentar fornecer os dados que são verificados com um perfil técnico de validação. Por exemplo, um utilizador tenta inscrever-se com uma conta que já existe e continua a tentar até que o limite seja atingido.
| SignUpTarget <sup>1</sup>| No | O identificador de troca de alvo de inscrição. Quando o utilizador clica no botão de inscrição, o Azure AD B2C executa o identificador de troca especificado. |
| definição.showCancelButton | No | Exibe o botão de cancelamento. Valores possíveis: `true` (padrão), ou `false` |
| setting.showContinueButton | No | Exibe o botão de continuidade. Valores possíveis: `true` (padrão), ou `false` |
| definição.showSignupLink <sup>2</sup>| No | Exibe o botão de inscrição. Valores possíveis: `true` (padrão), ou `false` |
| definição.forgotPasswordLinkLocation <sup>2</sup>| No| Apresenta o link de senha esquecido. Valores possíveis: `AfterLabel` (predefinido) exibe o link diretamente após a etiqueta ou após o campo de entrada de palavra-passe quando não há etiqueta,  `AfterInput` exibe o link após o campo de entrada da palavra-passe, exibe o link na parte inferior do formulário após os `AfterButtons` botões ou remove o link de senha `None` esquecido.|
| definição.enableRememberMe <sup>2</sup>| No| Exibe o [Keep me assinado na](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) caixa de verificação. Valores possíveis: `true` , ou `false` (predefinição). |
| definição.inputVerificationDelayTimeMliseconds <sup>3</sup>| No| Melhora a experiência do utilizador, esperando que o utilizador pare de digitar e, em seguida, valide o valor. Valor padrão 2000 milissegundos. |
| IncluirClaimResolvingInClaimsHandling  | No | Para pedidos de entradas e saídas, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` , ou `false` (predefinição). Se pretender utilizar uma reclamação no perfil técnico, desa um pouco `true` para . |
|esqueceuPasswordLinkOverride <sup>4</sup>| No | Uma palavra-passe reiniciou a troca de pedidos para ser executada. Para obter mais informações, consulte [a palavra-passe self-service reposta](add-password-reset-policy.md). |

Notas:
1. Disponível para definição de conteúdo [DataUri](contentdefinitions.md#datauri) tipo de `unifiedssp` , ou `unifiedssd` .
1. Disponível para definição de conteúdo [DataUri](contentdefinitions.md#datauri) tipo de `unifiedssp` , ou `unifiedssd` . [Versão de layout da página](page-layout.md) 1.1.0 e superior.
1. Disponível para [a versão de layout da página](page-layout.md) 1.2.0 ou superior.
1. Disponível para definição de conteúdo [DataUri](contentdefinitions.md#datauri) tipo de `unifiedssp` . [Versão de layout da página](page-layout.md) 2.1.2 e acima.

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** não é utilizado.
