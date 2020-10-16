---
title: UserJourneys [ Microsoft Docs
description: Especificar o elemento UserJourneys de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fda57ae152efbb04a793c6acf63465fe8d406a1a
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998659"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

As viagens de utilizador especificam caminhos explícitos através dos quais uma política permite que uma aplicação de partidos dependentes obtenha as reclamações desejadas para um utilizador. O utilizador é levado por estes caminhos para recuperar as alegações que devem ser apresentadas à parte que conta. Por outras palavras, as viagens de utilizador definem a lógica de negócio do que um utilizador final passa à medida que o Azure AD B2C Identity Experience Framework processa o pedido.

Estas viagens de utilizador podem ser consideradas como modelos disponíveis para satisfazer a necessidade central das várias partes dependentes da comunidade de interesse. As viagens de utilizador facilitam a definição da parte dependente de uma política. Uma política pode definir várias viagens de utilizador. Cada viagem de utilizador é uma sequência de passos de orquestração.

Para definir as viagens de utilizador suportadas pela política, é adicionado um elemento **UserJourneys** sob o elemento de nível superior do ficheiro de política.

O elemento **UserJourneys** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Uma viagem de utilizador que define todas as construções necessárias para um fluxo completo do utilizador. |

O elemento **UserJourney** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador de uma viagem de utilizador que pode ser usada para a referenciar a partir de outros elementos da política. O elemento **DefaultUserJourney** da política do [partido em apoio](relyingparty.md) aponta para este atributo. |

O elemento **UserJourney** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Passos de Orquestra | 1:n | Uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. Cada viagem de utilizador consiste numa lista ordenada de etapas de orquestração que são executadas em sequência. Se algum passo falhar, a transação falha. |

## <a name="orchestrationsteps"></a>Passos de Orquestra

Uma viagem de utilizador é representada como uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. Se algum passo falhar, a transação falha. Estes passos de orquestração referem-se tanto aos blocos de construção como aos fornecedores de sinistros permitidos no ficheiro político. Qualquer passo de orquestração responsável por mostrar ou tornar uma experiência de utilizador também tem uma referência ao identificador de definição de conteúdo correspondente.

Os passos de orquestração podem ser executados condicionalmente com base em condições prévias definidas no elemento do passo de orquestração. Por exemplo, só pode verificar para executar um passo de orquestração se existir uma reclamação específica, ou se uma reclamação é igual ou não ao valor especificado.

Para especificar a lista ordenada de etapas de orquestração, um elemento **OrchestrationSteps** é adicionado como parte da política. Este elemento é necessário.

O elemento **OrchestrationSteps** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| OrquestraçãoStep | 1:n | Um passo de orquestração ordenado. |

O elemento **OrchestrationStep** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Order` | Sim | A ordem dos passos de orquestração. |
| `Type` | Yes | O tipo de passo de orquestração. Valores possíveis: <ul><li>**SinistrosProviderSelection** - Indica que o passo de orquestração apresenta vários fornecedores de reclamações ao utilizador para selecionar um.</li><li>**CombinedSignInAndSignUp** - Indica que o passo de orquestração apresenta uma página de inscrição de fornecedor social combinada e de inscrição de conta local.</li><li>**ClaimsExchange** - Indica que a etapa de orquestração troca reclamações com um fornecedor de sinistros.</li><li>**GetClaims** - Especifica que o passo de orquestração deve processar os dados de reclamação enviados para Azure AD B2C da parte de relying através da sua `InputClaims` configuração.</li><li>**InvokeSubJourney** - Indica que o passo de orquestração troca reivindicações com uma [sub-jornada](subjourneys.md) (em pré-visualização pública).</li><li>**SendClaims** - Indica que o passo de orquestração envia as reivindicações para a parte de gestão com um símbolo emitido por um emitente de reclamações.</li></ul> |
| ConteúdoDefinitionReferenceId | Não | O identificador da [definição](contentdefinitions.md) de conteúdo associado a este passo de orquestração. Normalmente, o identificador de referência de definição de conteúdo é definido no perfil técnico autoafirmado. Mas, há alguns casos em que o Azure AD B2C precisa de mostrar algo sem um perfil técnico. Existem dois exemplos - se o tipo de passo de orquestração for um dos seguintes: `ClaimsProviderSelection` ou  `CombinedSignInAndSignUp` , O Azure AD B2C precisa de mostrar a seleção do fornecedor de identidade sem ter um perfil técnico. |
| CpimIssuerTechnicalProfileReferenceId | Não | O tipo de passo de orquestração `SendClaims` é. Este imóvel define o identificador de perfil técnico do prestador de sinistros que emite o símbolo para a parte que conta.  Se ausente, não se cria um símbolo do partido. |

O elemento **OrchestrationStep** pode conter os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Condições prévias | 0:n | Uma lista de pré-condições que devem ser satisfeitas para que o passo de orquestração seja executado. |
| ReivindicaçõesProviderSeleções | 0:n | Uma lista de seleções de fornecedores de sinistros para o passo de orquestração. |
| ReclamaçõesExchanges | 0:n | Uma lista de trocas de reclamações para o passo de orquestração. |
| Lista de Jornadas | 0:1 | Uma lista de candidatos à sub-jornada para o passo de orquestração. |

### <a name="preconditions"></a>Condições prévias

O elemento **pré-condições** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Pré-condição | 1:n | Dependendo do perfil técnico que está a ser utilizado, redireciona o cliente de acordo com a seleção do fornecedor de sinistros ou faz uma chamada de servidor para trocar reclamações. |


#### <a name="precondition"></a>Pré-condição

O **elemento pré-condição** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta a realizar para esta condição prévia. O valor pode ser **ClaimsExist,** que especifica que as ações devem ser executadas se as reclamações especificadas existirem no conjunto de reclamações corrente do utilizador, ou **ClaimEquals,** que especifica que as ações devem ser executadas se a reclamação especificada existir e o seu valor for igual ao valor especificado. |
| `ExecuteActionsIf` | Sim | Utilize um teste verdadeiro ou falso para decidir se as ações na condição prévia devem ser realizadas. |

Os **elementos de pré-condição** contêm os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Valor | 1:n | Um ClaimTypeReferenceId a ser questionado. Outro elemento de valor contém o valor a verificar.</li></ul>|
| Ação | 1:1 | A ação que deve ser executada se o controlo de pré-condição dentro de um passo de orquestração for verdadeiro. Se o valor do valor do `Action` for `SkipThisOrchestrationStep` definido, o associado `OrchestrationStep` não deve ser executado. |

#### <a name="preconditions-examples"></a>Pré-condições exemplos

As seguintes condições prévias verificam se o objectId do utilizador existe. Na viagem do utilizador, o utilizador selecionou para iniciar scontabilidade local. Se o objectId existir, ignore este passo de orquestração.

```xml
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

As seguintes condições prévias verificam se o utilizador assinou com uma conta social. É feita uma tentativa de encontrar a conta de utilizador no diretório. Se o utilizador se inscrever ou se inscrever numa conta local, ignore este passo de orquestração.

```xml
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

As condições prévias podem verificar várias condições prévias. O exemplo a seguir verifica se 'objectId' ou 'email' existem. Se a primeira condição for verdadeira, a viagem passa para o próximo passo de orquestração.

```xml
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

## <a name="claimsproviderselection"></a>ReivindicaçõesProviderSele

Um passo de orquestração do tipo `ClaimsProviderSelection` ou pode conter uma lista de `CombinedSignInAndSignUp` fornecedores de sinistros com os quais um utilizador pode iniciar sação. A ordem dos elementos dentro dos `ClaimsProviderSelections` elementos controla a ordem dos fornecedores de identidade apresentados ao utilizador.

O elemento **ClaimsProviderSelections** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| ReivindicaçõesProviderSele | 1:n | Fornece a lista de fornecedores de sinistros que podem ser selecionados.|

O elemento **ClaimsProviderSelections** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| DisplayOption| Não | Controla o comportamento de um caso em que está disponível uma única seleção do fornecedor de sinistros. Valores possíveis:  `DoNotShowSingleProvider`   (padrão) , o utilizador é redirecionado imediatamente para o fornecedor de identidade federado. Ou  `ShowSingleProvider`   Azure AD B2C apresenta a página de inscrição com a seleção de fornecedor de identidade única. Para utilizar este atributo, a [versão de definição](page-layout.md) de conteúdo deve ser  `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` e acima.|

O elemento **ClaimsProviderSelection** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Não | O identificador da troca de sinistros, que é executado na próxima etapa de orquestração da seleção do prestador de sinistros. Este atributo ou o atributo ValidationClaimsExchangeId devem ser especificados, mas não ambos. |
| ValidaçãoClaimsExchangeId | Não | O identificador da troca de sinistros, que é executado na atual etapa de orquestração para validar a seleção do prestador de sinistros. Este atributo ou o atributo TargetClaimsExchangeId devem ser especificados, mas não ambos. |

### <a name="claimsproviderselection-example"></a>Sinistros Exemplo de Seleção

No seguinte passo de orquestração, o utilizador pode optar por iniciar scontabilidade com facebook, LinkedIn, Twitter, Google ou uma conta local. Se o utilizador selecionar um dos fornecedores de identidade social, o segundo passo de orquestração executa com a troca de reclamações selecionada especificada no `TargetClaimsExchangeId` atributo. A segunda etapa de orquestração redireciona o utilizador para o fornecedor de identidade social para completar o processo de inscrição. Se o utilizador optar por iniciar seditação com a conta local, o Azure AD B2C permanece no mesmo passo de orquestração (a mesma página de inscrição ou página de inscrição) e salta o segundo passo de orquestração.

```xml
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

## <a name="claimsexchanges"></a>ReclamaçõesExchanges

O elemento **ClaimsExchanges** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| ReclamaçõesExchange | 1:n | Dependendo do perfil técnico que está a ser utilizado, redireciona o cliente de acordo com a Seleção de Reclamações que foi selecionada, ou faz uma chamada de servidor para trocar reclamações. |

O elemento **ClaimsExchange** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador do passo de troca de reclamações. O identificador é utilizado para fazer referência à troca de sinistros a partir de um passo de seleção do fornecedor de sinistros na apólice. |
| TécnicaProfileReferenceId | Yes | O identificador do perfil técnico que deve ser executado. |

## <a name="journeylist"></a>Lista de Jornadas

O elemento **JourneyList** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Candidato | 1:1 | Uma referência a uma sub-viagem a ser chamada. |

### <a name="candidate"></a>Candidato

O elemento **Candidato** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Sim | O identificador da [sub-viagem](subjourneys.md) que deve ser executado. |
