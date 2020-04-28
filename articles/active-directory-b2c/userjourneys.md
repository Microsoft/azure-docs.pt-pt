---
title: UserJourneys [ UserJourneys] Microsoft Docs
description: Especifique o elemento UserJourneys de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78226999"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

As viagens de utilizador especificam caminhos explícitos através dos quais uma política permite que uma aplicação de parte dependente obtenha as reclamações desejadas para um utilizador. O utilizador é levado por estes caminhos para recuperar as reclamações que devem ser apresentadas à parte que depende. Por outras palavras, as viagens de utilizador definem a lógica de negócio do que um utilizador final passa à medida que o Quadro de Experiência de Identidade Azure AD AD B2C processa o pedido.

Estas viagens de utilizador podem ser consideradas como modelos disponíveis para satisfazer a necessidade central das várias partes dependentes da comunidade de interesse. As viagens dos utilizadores facilitam a definição da parte que depende de uma política. Uma política pode definir várias viagens de utilizador. Cada viagem do utilizador é uma sequência de passos de orquestração.

Para definir as viagens de utilizador suportadas pela apólice, um elemento **UserJourneys** é adicionado sob o elemento de nível superior do ficheiro de política.

O elemento **UserJourneys** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Jornada do Utilizador | 1:n | Uma viagem de utilizador que define todas as construções necessárias para um fluxo completo do utilizador. |

O elemento **UserJourney** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador de uma viagem de utilizador que pode ser usado para referenciar a partir de outros elementos da política. O elemento **DefaultUserJourney** da [política do partido que depende](relyingparty.md) aponta para este atributo. |

O elemento **UserJourney** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OrquestraçõesSteps | 1:n | Uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. Cada viagem de utilizador consiste numa lista ordenada de passos de orquestração que são executados em sequência. Se algum passo falhar, a transação falha. |

## <a name="orchestrationsteps"></a>OrquestraçõesSteps

Uma viagem de utilizador é representada como uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. Se algum passo falhar, a transação falha. Estes passos de orquestração referem tanto os blocos de construção como os fornecedores de sinistros permitidos no ficheiro político. Qualquer passo de orquestração responsável por mostrar ou renderizar uma experiência de utilizador também tem uma referência ao identificador de definição de conteúdo correspondente.

Os passos de orquestração podem ser executados condicionalmente com base nas condições prévias definidas no elemento passo da orquestração. Por exemplo, só pode verificar se existe uma reclamação específica ou se existe uma reclamação igual ou não ao valor especificado.

Para especificar a lista ordenada de passos de orquestração, um elemento **OrquestraçãoSteps** é adicionado como parte da política. Este elemento é necessário.

O elemento **OrchestrationSteps** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OrquestraçãoStep | 1:n | Um passo de orquestração ordenado. |

O elemento **OrchestrationStep** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| `Order` | Sim | A ordem dos passos da orquestração. |
| `Type` | Sim | O tipo de passo de orquestração. Valores possíveis: <ul><li>**ClaimsProviderSelection** - Indica que o passo da orquestração apresenta vários fornecedores de sinistros ao utilizador para selecionar um.</li><li>**CombinedSignInAndSignUp** - Indica que o passo da orquestração apresenta uma página combinada de inscrição de prestador social e de inscrição de conta local.</li><li>**ClaimsExchange** - Indica que o passo da orquestra troca reclamações com um prestador de sinistros.</li><li>**GetClaims** - Indica que o passo da orquestração lê as alegações de entrada.</li><li>**SendClaims** - Indica que o passo da orquestração envia as reivindicações para a parte que depende com um token emitido por um emitente de reclamações.</li></ul> |
| ContentDefinitionReferenceId | Não | O identificador da [definição](contentdefinitions.md) de conteúdo associado a este passo de orquestração. Normalmente, o identificador de referência de definição de conteúdo é definido no perfil técnico autoafirmado. Mas, há alguns casos em que o Azure AD B2C precisa de mostrar algo sem perfil técnico. Existem dois exemplos - se o tipo de passo `ClaimsProviderSelection` `CombinedSignInAndSignUp`de orquestração é um dos seguintes: ou, O Azure AD B2C precisa de mostrar a seleção do fornecedor de identidade sem ter um perfil técnico. |
| CpimIssuerTechnicalProfileReferenceId | Não | O tipo de passo `SendClaims`de orquestração é. Este imóvel define o identificador de perfil técnico do prestador de sinistros que emite o símbolo para a parte que depende.  Se ausente, não é criado nenhum símbolo do partido. |


O elemento **OrchestrationStep** pode conter os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Condições prévias | 0:n | Uma lista de condições prévias que devem ser satisfeitas para que o passo de orquestração seja executado. |
| Seleções ClaimsProviderS | 0:n | Uma lista de seleções de fornecedores de sinistros para o passo da orquestração. |
| ReclamaçõesTroca | 0:n | Uma lista de reclamações trocam pelo passo da orquestração. |

### <a name="preconditions"></a>Condições prévias

O elemento **Preconditions** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Pré-condição | 1:n | Dependendo do perfil técnico utilizado, ou redireciona o cliente de acordo com a seleção do fornecedor de sinistros ou faz uma chamada de servidor para trocar reclamações. |


#### <a name="precondition"></a>Pré-condição

O elemento **Pré-condição** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta a realizar para esta condição prévia. O valor pode ser **ClaimsExist**, que especifica que as ações devem ser executadas se as reclamações especificadas existirem no conjunto de reclamações do utilizador, ou **ClaimEquals,** que especifica que as ações devem ser executadas se a reclamação especificada existir e o seu valor for igual ao valor especificado. |
| `ExecuteActionsIf` | Sim | Utilize um teste verdadeiro ou falso para decidir se as ações no pré-estado devem ser executadas. |

Os elementos **de pré-condição** contêm os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Valor | 1:n | Um ClaimTypeReferenceId a ser consultado. Outro elemento de valor contém o valor a verificar.</li></ul>|
| Ação | 1:1 | A ação que deve ser executada se a verificação prévia dentro de um passo de orquestração for verdadeira. Se o valor `Action` do `SkipThisOrchestrationStep`for definido, `OrchestrationStep` o associado não deve ser executado. |

#### <a name="preconditions-examples"></a>Exemplos pré-condições

As seguintes condições prévias verificam se o objectid do utilizador existe. Na viagem de utilizador, o utilizador selecionou para iniciar sessão através da conta local. Se o objectid existir, ignore este passo de orquestração.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

As seguintes condições prévias verificase se o utilizador assinou com uma conta social. É feita uma tentativa de encontrar a conta de utilizador no diretório. Se o utilizador entrar ou se inscrever com uma conta local, ignore este passo de orquestração.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

As condições prévias podem verificar várias condições prévias. O exemplo que se segue verifica se existe 'objectId' ou 'email'. Se a primeira condição for verdadeira, a viagem salta para o próximo passo de orquestração.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Um passo de `ClaimsProviderSelection` orquestração de tipo ou `CombinedSignInAndSignUp` pode conter uma lista de fornecedores de reclamações com os quais um utilizador pode iniciar sessão. A ordem dos elementos `ClaimsProviderSelections` no interior dos elementos controla a ordem dos fornecedores de identidade apresentados ao utilizador.

O elemento **ClaimsProviderSelections** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | Fornece a lista de fornecedores de sinistros que podem ser selecionados.|

O elemento **ClaimsProviderSelections** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Opção de exibição| Não | Controla o comportamento de um caso em que está disponível uma única seleção de fornecedores de sinistros. Valores `DoNotShowSingleProvider` possíveis: (predefinido) , o utilizador é redirecionado imediatamente para o fornecedor de identidade federado. Ou `ShowSingleProvider` Azure AD B2C apresenta a página de entrada com a seleção de fornecedor de identidade única. Para utilizar este atributo, a `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` versão de [definição](page-layout.md) de conteúdo deve ser e acima.|

O elemento **ClaimsProviderSelection** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| TargetClaimsExchangeid | Não | O identificador da troca de sinistros, que é executada na próxima etapa de orquestração da seleção do prestador de sinistros. Este atributo ou o atributo ValidaçãoClaimsExchangeId deve ser especificado, mas não ambos. |
| ValidaçõesClaimsExchangeid | Não | O identificador da troca de sinistros, que é executada na atual etapa de orquestração para validar a seleção do prestador de sinistros. Este atributo ou o atributo TargetClaimsExchangeId devem ser especificados, mas não ambos. |

### <a name="claimsproviderselection-example"></a>Exemplo de Seleção ClaimsProvider

No seguinte passo de orquestração, o utilizador pode optar por iniciar sessão com o Facebook, LinkedIn, Twitter, Google ou uma conta local. Se o utilizador selecionar um dos fornecedores de identidade social, o segundo passo de `TargetClaimsExchangeId` orquestração executa com a troca de reclamações selecionada especificada no atributo. O segundo passo de orquestração redireciona o utilizador para o fornecedor de identidade social para completar o processo de início de sessão. Se o utilizador optar por iniciar sessão com a conta local, o Azure AD B2C permanece no mesmo passo de orquestração (a mesma página de inscrição ou página de inscrição) e salta o segundo passo de orquestração.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ReclamaçõesTroca

O elemento **Reclamações Exchanges** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ReclamaçõesTroca | 1:n | Dependendo do perfil técnico utilizado, ou redireciona o cliente de acordo com a Seleção Reclamada que foi selecionada, ou faz uma chamada de servidor para trocar reclamações. |

O elemento **ClaimsExchange** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador da etapa de troca de reclamações. O identificador é utilizado para fazer referência à troca de sinistros a partir de um passo de seleção do fornecedor de sinistros na apólice. |
| Perfil-referência técnico | Sim | O identificador do perfil técnico que deve ser executado. |
