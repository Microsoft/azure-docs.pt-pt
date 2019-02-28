---
title: UserJourneys | Documentos da Microsoft
description: Especifique o elemento de UserJourneys de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8cda538cade4750e03ecb91dfb2c478df730e556
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961300"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Jornadas utilizador especificar caminhos explícitos, por meio do qual uma diretiva permite que um aplicativo de parte confiável obter as declarações pretendidas para um utilizador. O utilizador é direcionado por meio destes caminhos para obter as afirmações que são apresentados à entidade confiadora. Em outras palavras, jornadas de usuário definem a lógica de negócios do que um utilizador final aborda como os processos de arquitetura de experiências de identidade do Azure AD B2C, o pedido.

Estes jornadas de utilizador podem ser consideradas como modelos disponíveis para satisfazer a necessidade de núcleo de parte das várias entidades confiadoras da Comunidade de interesse. Jornadas utilizador facilitam a definição a parte da entidade confiadora de terceiros de uma política. Uma política pode definir vários jornadas de utilizador. Cada percurso do utilizador é uma seqüência de etapas da orquestração.

Para definir as Jornadas utilizador suportadas pela política, uma **UserJourneys** elemento é adicionado sob o elemento de nível superior do ficheiro de política. 

O **UserJourneys** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Um percurso do utilizador que define todas as construções necessárias para um fluxo de utilizador completo. | 

O **UserJourney** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador de um percurso do utilizador que pode ser utilizado para fazer referência a ela partir de outros elementos na política. O **DefaultUserJourney** elemento da [da entidade confiadora política de terceiros](relyingparty.md) aponta para esse atributo. |

O **UserJourney** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Uma sequência de orquestração que deve ser seguida por meio de uma transação concluída com êxito. Cada percurso do utilizador consiste numa lista ordenada de etapas da orquestração que são executadas em seqüência. Se falhar qualquer passo, a transação falhar. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Um percurso do utilizador é representado como uma sequência de orquestração que deve ser seguida por meio de uma transação concluída com êxito. Se falhar qualquer passo, a transação falhar. Estes passos de orquestração referenciam os blocos de construção e os fornecedores de afirmações permitido no ficheiro de política. Qualquer passo de orquestração responsável para mostrar ou compor uma experiência de utilizador também tem uma referência para o identificador de definição de conteúdo correspondente.

Etapas da orquestração podem ser condicionalmente executadas, com base em pré-condições definidas no elemento de passo de orquestração. Por exemplo, pode verificar para realizar um passo de orquestração, apenas se existir um afirmações específicas, ou se uma afirmação é igual ou não com o valor especificado. 

Para especificar a lista ordenada de passos de orquestração, um **OrchestrationSteps** elemento é adicionado como parte da política. Este elemento é necessário.

O **OrchestrationSteps** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Um passo de orquestração ordenada. | 

O **OrchestrationStep** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Encomenda | Sim | A ordem dos passos de orquestração. | 
| Type | Sim | O tipo de passo de orquestração. Valores possíveis: <ul><li>**ClaimsProviderSelection** -indica que o passo de orquestração apresenta vários fornecedores de afirmações para o usuário selecionar um.</li><li>**CombinedSignInAndSignUp** -indica que o passo de orquestração apresenta um provedor social combinado página de inscrição de conta de início de sessão e locais.</li><li>**ClaimsExchange** -indica que o passo de orquestração troca de afirmações com um fornecedor de afirmações.</li><li>**SendClaims** -indica que o passo de orquestração envia as afirmações para a entidade confiadora com um token emitido por um emissor de afirmações.</li></ul> | 
| ContentDefinitionReferenceId | Não | O identificador do [definição de conteúdo](contentdefinitions.md) associadas a este passo de orquestração. Normalmente, o identificador de referência de definição de conteúdo é definido no perfil técnico de declaração própria. Mas, existem alguns casos quando precisa exibir alguma coisa sem um perfil técnico do Azure AD B2C. Existem dois exemplos, se o tipo de passo de orquestração é um dos seguintes procedimentos: `ClaimsProviderSelection` ou `CombinedSignInAndSignUp`. O Azure AD B2C tem de apresentar a seleção do fornecedor de identidade sem ter um perfil técnico. | 
| CpimIssuerTechnicalProfileReferenceId | Não | O tipo de passo de orquestração é `SendClaims`. Esta propriedade define o identificador do perfil técnico do fornecedor de afirmações que emite o token para a entidade confiadora.  Se estiver ausente, não é criado nenhum token da entidade confiadora de terceiros. |


O **OrchestrationStep** elemento pode conter os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- | 
| Pré-condições | 0:n | Uma lista de pré-condições que devem ser satisfeitos para o passo de orquestração executar. | 
| ClaimsProviderSelections | 0:n | Uma lista de seleções de fornecedor de afirmações para o passo de orquestração. | 
| ClaimsExchanges | 0:n | Uma lista de trocas de afirmações para o passo de orquestração. | 

### <a name="preconditions"></a>Pré-condições

O **pré-condições** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- | 
| Pré-condição | 0:n | Dependendo do perfil técnico a ser utilizado, um redireciona o cliente de acordo com a seleção do fornecedor de afirmações ou faz uma chamada de servidor para a troca de afirmações. | 


#### <a name="precondition"></a>Pré-condição

O **pré-condição** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Type | Sim | O tipo de verificação ou a consulta para executar para esta pré-condição. O valor pode ser **ClaimsExist**, que especifica que as ações devem ser efetuadas se as afirmações especificadas existem no conjunto de afirmações atual do usuário, ou **ClaimEquals**, que especifica que as ações deve ser efetuada se a afirmação especificado existe e o valor é igual ao valor especificado. |
| ExecuteActionsIf | Sim | Utilize um teste de VERDADEIRO ou FALSO para decidir se as ações na pré-condição devem ser efetuadas. | 

O **pré-condição** elementos contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Value | 1:n | Um ClaimTypeReferenceId para ser consultada para. Outro elemento de valor contém o valor a ser verificado.</li></ul>|
| Ação | 1:1 | A ação que deve ser efetuada se a verificação de pré-condição dentro de um passo de orquestração for verdadeira. Se o valor do `Action` está definido como `SkipThisOrchestrationStep`, associada `OrchestrationStep` não deve ser executado. | 

#### <a name="preconditions-examples"></a>Exemplos de pré-condições

As seguintes pré-condições verifica a existência de objectId do utilizador. No percurso do utilizador, o usuário tiver selecionado para iniciar sessão com a conta local. Se existir o objectId, ignore este passo de orquestração.

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

As seguintes pré-condições verifica se o utilizador tiver iniciado sessão com uma conta de redes sociais. É efetuada uma tentativa de encontrar a conta de utilizador no diretório. Se o utilizador inicia sessão ou iniciar sessão com uma conta local ignore, este passo de orquestração.

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

Pré-condições podem verificar as pré-condições vários. O exemplo a seguir verifica se "objectId" ou "email" existe. Se a primeira condição for verdadeira, ignora a jornada para o próximo passo de orquestração.

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

Um passo de orquestração do tipo `ClaimsProviderSelection` ou `CombinedSignInAndSignUp` pode conter uma lista de fornecedores de afirmações que um utilizador pode iniciar sessão. A ordem dos elementos dentro do `ClaimsProviderSelections` elementos controla a ordem dos fornecedores de identidade apresentado ao usuário.

O **ClaimsProviderSelection** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 0:n | Fornece a lista de fornecedores de afirmações que podem ser selecionadas.|

O **ClaimsProviderSelection** elemento contém os seguintes atributos: 

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Não | O identificador da troca de afirmações, que é executada no próximo passo de orquestração da seleção do fornecedor de afirmações. Este atributo ou o atributo de ValidationClaimsExchangeId tem de ser especificado, mas não ambos. | 
| ValidationClaimsExchangeId | Não | O identificador da troca de afirmações, que é executado o passo de orquestração atual para validar a seleção do fornecedor de afirmações. Este atributo ou o atributo de TargetClaimsExchangeId tem de ser especificado, mas não ambos. |

### <a name="claimsproviderselection-example"></a>Exemplo de ClaimsProviderSelection

O seguinte passo de orquestração, o utilizador pode optar por iniciar sessão com Facebook, LinkIn, Twitter, Google ou uma conta local. Se o usuário seleciona um dos fornecedores de identidade de redes sociais, o segundo passo de orquestração é executado com o exchange de afirmação selecionado especificado no `TargetClaimsExchangeId` atributo. O segundo passo de orquestração redirecionará o usuário para o fornecedor de identidade de redes sociais para concluir o processo de início de sessão. Se o utilizador optar por iniciar sessão com a conta local, o Azure AD B2C mantém-se no passo de orquestração mesmo (a mesma página de inscrição ou início de sessão da página) e ignora o segundo passo de orquestração.

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

## <a name="claimsexchanges"></a>ClaimsExchanges

O **ClaimsExchanges** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ClaimsExchange | 0:n | Dependendo do perfil técnico a ser utilizado, um redireciona o cliente de acordo com o ClaimsProviderSelection que foi selecionado ou faz uma chamada de servidor para a troca de afirmações. | 

O **ClaimsExchange** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador do passo de exchange afirmações. O identificador é utilizado para a troca de afirmações entre uma seleção de fornecedor de afirmações passo na política de referência. | 
| TechnicalProfileReferenceId | Sim | O identificador do perfil técnico que está a ser executado. |
