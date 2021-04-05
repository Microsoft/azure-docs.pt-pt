---
title: Sub-viagens em Azure Ative Directory B2C | Microsoft Docs
description: Especificar o elemento sub-viagens de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97386872"
---
# <a name="sub-journeys"></a>Sub-viagens

As subviagens podem ser usadas para organizar e simplificar o fluxo de passos de orquestração dentro de uma jornada de utilizador. Os [percursos do utilizador](userjourneys.md) especificam caminhos explícitos através dos quais uma política permite que uma aplicação de entidade confiadora obtenha as afirmações desejadas para um utilizador. O utilizador utiliza estes caminhos para obter as afirmações que devem ser apresentadas à entidade confiadora. Por outras palavras, as viagens de utilizador definem a lógica de negócio do que um utilizador final passa à medida que o Azure AD B2C Identity Experience Framework processa o pedido. Uma viagem de utilizador é representada como uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. O elemento [ClaimsExchange](userjourneys.md#claimsexchanges) de um passo de orquestração está ligado a um único [perfil técnico](technicalprofiles.md) que executa.

Uma sub-viagem é um agrupamento de passos de orquestração que podem ser invocados em qualquer ponto dentro de uma viagem de utilizador. Você pode usar sub-viagens para criar sequências de passo reutilizáveis ou implementar ramificação para melhor representar a lógica do negócio.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Ramificação da viagem do utilizador

As subviagens comportam-se como viagens de utilizador, uma vez que ambas são [representadas](userjourneys.md)como uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. As viagens de utilizador podem ser invocadas por si próprias e requerem um passo SendClaims para executar. As sub-viagens são componentes das viagens de utilizador e não podem ser invocadas de forma independente, e são sempre chamadas de uma viagem de utilizador.

O componente-chave da ramificação é permitir um melhor processamento da lógica de negócio numa viagem de utilizador. Os passos comuns de orquestração são agrupados em peças individuais para serem invocados separadamente. Uma sub-viagem pode simplificar uma jornada onde vários passos de orquestração são acoplidos (tendo as mesmas condições prévias). Uma sub-viagem é chamada apenas de uma viagem de utilizador, não deve chamar outra sub-viagem.

Existem dois tipos de sub-viagens:

- **Chamada** - Devolve o controlo ao ouvinte. A sub-jornada executa e, em seguida, o controlo é devolvido ao passo de orquestração que está atualmente a executar dentro da jornada do utilizador.
- **Transferência** - Transfere o controlo para a sub-viagem (ramificação irreversível). A sub-viagem deve ter um passo SendClaims para devolver as reclamações à aplicação do partido.

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="call-sub-journey"></a>Chamada sub jornada

Uma sub-viagem de chamada é útil nos seguintes cenários:

- Age Gating: Para a idade, existem muitos componentes partilhados entre as viagens do utilizador. A ramificação permite compilar os elementos comuns em componentes sharable.  
- Consentimento Parental: A ramificação permite a conveniência no design de consentimento parental, permitindo-nos aceder aos pedidos a partir da viagem de utilizador que o menor executou, além de poder ramificar-se numa viagem de utilizador de consentimento parental após encontrar o utilizador que necessita de consentimento. 
- Inscreva-se para se inscrever: Considere um cenário em que um utilizador já exista no diretório mas pode ter-se esquecido de que, de facto, tinha criado uma conta. Pode ser desejável, em tal caso, que em vez de dizer ao utilizador que as credenciais que inseriram já existem e forçar o utilizador a reiniciar a viagem que a política é capaz de executar um interruptor de um fluxo de inscrição para um sinal de fluxo para esse utilizador.  

### <a name="transfer-sub-journey"></a>Sub-viagem de transferência

Uma sub-viagem de transferência é útil nos seguintes cenários:

- Mostrando uma página de bloco.
- Teste a/B encaminhando o pedido para uma sub-viagem para executar e emitir um token.

## <a name="adding-a-subjourneys-element"></a>Adicionar um elemento SubJourneys

Segue-se um exemplo de um `SubJourney` elemento do tipo , que devolve o controlo à `Call` viagem do utilizador.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Segue-se um exemplo de um `SubJourney` elemento do `Transfer` tipo, que devolve um símbolo à aplicação do partido em gestão.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Invocar um passo de sub-viagem

Um novo passo de orquestração do tipo `InvokeSubJourney` é usado para executar uma sub-jornada. Segue-se um exemplo que mostra todos os elementos de execução deste passo de orquestração.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Uma sub-viagem não deve convocar outra sub-viagem.

## <a name="components"></a>Componentes

Para definir as sub-viagens suportadas pela política, adicione um elemento **SubJourneys** sob o elemento de nível superior do ficheiro de política.

O elemento **SubJourneys** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Sub-Journey | 1:n | Uma sub-viagem que define todas as construções necessárias para um fluxo completo do utilizador. |

O elemento **SubJourneys** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Yes | O identificador de sub-viagem que pode ser usado pela viagem do utilizador para fazer referência à sub-viagem na apólice. O elemento **SubJourneyReferenceId** do elemento [candidato](userjourneys.md#journeylist) aponta para este atributo. |
| Tipo | Yes | Valores possíveis: `Call` , ou `Transfer` . . Para mais informações, consulte [a ramificação da viagem do Utilizador](#user-journey-branching)|

O elemento **SubJourney** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Passos de Orquestra | 1:n | Uma sequência de orquestração que deve ser seguida para uma transação bem sucedida. Cada viagem de utilizador consiste numa lista ordenada de etapas de orquestração que são executadas em sequência. Se algum passo falhar, a transação falha. |

## <a name="orchestrationsteps"></a>Passos de Orquestra

Para obter a lista completa dos elementos do passo de orquestração, consulte [UserJourneys](userjourneys.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o UserJourneys](userjourneys.md)