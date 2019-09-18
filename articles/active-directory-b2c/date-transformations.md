---
title: Exemplos de transformação de declarações de data para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação data Claims para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 077915705c242805d3709b5d52d445288fa5336a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064358"
---
# <a name="date-claims-transformations"></a>Transformações de declarações de data

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações de data do esquema de estrutura de experiência de identidade no Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Verifica se uma declaração de data e hora (tipo de dados de cadeia de caracteres) é posterior à segunda declaração de data e hora (tipo de dados de cadeia de caracteres) e gera uma exceção.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | Cadeia de caracteres | O tipo da primeira declaração, que deve ser posterior à segunda declaração. |
| InputClaim | rightOperand | Cadeia de caracteres | O tipo da segunda declaração, que deve ser anterior à primeira declaração. |
| InputParameter | AssertIfEqualTo | boolean | Especifica se essa asserção deve ser aprovada se o operando esquerdo for igual ao operando à direita. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Especifica se esta asserção deve ser aprovada se o operando direito estiver ausente. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Especifica o número de milissegundos para permitir entre os dois horários de data para considerar o tempo igual (por exemplo, para levar em conta a distorção do relógio). |

A transformação declarações **AssertDateTimeIsGreaterThan** é sempre executada de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md). Os metadados do perfil técnico autodeclarado **DateTimeGreaterThan** controlam a mensagem de erro que o perfil técnico apresenta ao usuário.

![Execução de AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

O exemplo a seguir compara `currentDateTime` a declaração com `approvedDateTime` a declaração. Um erro será gerado se `currentDateTime` for posterior a `approvedDateTime`. A transformação trata os valores como iguais se eles estiverem dentro de 5 minutos (30000 milissegundos) de diferença.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

O `login-NonInteractive` perfil técnico de validação chama `AssertApprovedDateTimeLaterThanCurrentDateTime` a transformação declarações.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o logon de validação **–** perfil técnico não interativo.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **leftOperand**: 2018-10-01T15:00:00.0000000 Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Disso Erro gerado

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Converte um ClaimType de **Data** em um ClaimType de **DateTime** . A transformação declarações converte o formato de hora e adiciona 12:00:00 AM à data.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | date | O ClaimType a ser convertido. |
| OutputClaim | outputClaim | DateTime | O ClaimType que é produzido após esse ClaimsTransformation foi invocado. |

O exemplo a seguir demonstra a conversão da Declaração `dateOfBirth` (tipo de dados de data) em `dateOfBirthWithTime` outra declaração (tipo de dados DateTime).

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: 2019-06-01
- Declarações de saída:
    - **outputClaim**: 1559347200 (1º de junho de 2019 12:00:00 AM)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtenha a data e a hora UTC atuais e adicione o valor a um ClaimType.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | DateTime | O ClaimType que é produzido após esse ClaimsTransformation foi invocado. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

* Declarações de saída:
    * **currentDateTime**: 1534418820 (16 de agosto de 2018 11:27:00 AM)

## <a name="datetimecomparison"></a>DateTimeComparison

Determine se um dateTime é posterior, anterior ou igual a outro. O resultado é um novo booliano de declaração booliano com um `true` valor `false`de ou.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | DateTime | O primeiro dateTime a ser comparado se ele for anterior ou posterior ao segundo dateTime. O valor nulo gera uma exceção. |
| InputClaim | secondDateTime | DateTime | O segundo dateTime para comparar se ele é anterior ou posterior ao primeiro dateTime. O valor nulo é tratado como o datetTime atual. |
| InputParameter | operator | Cadeia de caracteres | Um dos seguintes valores: mesmo, posteriores ou anteriores a. |
| InputParameter | timeSpanInSeconds | int | Adicione o TimeSpan ao primeiro DateTime. |
| OutputClaim | Resultado | boolean | O ClaimType que é produzido após esse ClaimsTransformation foi invocado. |

Use essa transformação de declarações para determinar se duas ClaimTypes são iguais, mais tarde ou antes umas das outras. Por exemplo, você pode armazenar a última vez que um usuário aceitou seus termos de serviço (TOS). Após 3 meses, você pode pedir ao usuário para acessar o TOS novamente.
Para executar a transformação declaração, primeiro você precisa obter o dateTime atual e também a última vez que o usuário aceita o TOS.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Parâmetros de entrada:
    - **operador**: posterior a
    - **timeSpanInSeconds**: 7776000 (90 dias)
- Declarações de saída:
    - **resultado**: verdadeiro
