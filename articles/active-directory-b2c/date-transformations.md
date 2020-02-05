---
title: Data reclama exemplos de transformação para políticas personalizadas
description: Data reclama exemplos de transformação para o quadro de experiência de identidade (IEF) do Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b831a3175e1dc8b19395d1c923b076ac9428690c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982913"
---
# <a name="date-claims-transformations"></a>Data reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização da data de transformações do Quadro de Experiência de Identidade no Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Verifica se uma data e a reclamação de hora (tipo de dados de cordas) são posteriores a uma segunda data e pedido de hora (tipo de dados de cadeia), e lança uma exceção.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | string | O tipo de primeira reclamação, que deve ser mais tarde do que a segunda alegação. |
| InputClaim | direitoOperand | string | O segundo tipo de reclamação, que deve ser mais cedo do que a primeira alegação. |
| InputParameter | AssertIfEqualTo | boolean | Especifica se esta afirmação deve passar se o operand esquerdo é igual ao operand direito. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Especifica se esta afirmação deve passar se o operand certo está em falta. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Especifica o número de milissegundos para permitir que entre os dois dias de data considere os tempos iguais (por exemplo, para contabilizar o desvio do relógio). |

A transformação de **afirmações AssertDateTimeIsGreaterThan** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md). Os metadados de perfil técnico **DateTimeGreaterThan** autoafirmados controlam a mensagem de erro que o perfil técnico apresenta ao utilizador.

![AssertstringClaimsAreIgual execução](./media/date-transformations/assert-execution.png)

O exemplo que se segue compara a reivindicação `currentDateTime` com a reivindicação `approvedDateTime`. Um erro é lançado se `currentDateTime` for mais tarde do que `approvedDateTime`. A transformação trata os valores como iguais se estiverem dentro de 5 minutos (30000 milissegundos) de diferença.

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

O perfil técnico de validação `login-NonInteractive` chama a transformação de reivindicações `AssertApprovedDateTimeLaterThanCurrentDateTime`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autoafirmado chama o perfil técnico **login-nonInteractive** de validação.

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
    - **esquerdaOperand**: 2018-10-01T15:00:00.00000000Z
    - **rightOperand**: 2018-10-01T14:00:00.00000000Z
- Resultado: Erro lançado

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Converte um Tipo de Reclamação de **Data** para um Tipo de Reclamação **datetime.** A transformação de sinistros converte o formato de tempo e adiciona às 12:00:00 da manhã.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | date | O ClaimType a converter. |
| OutputClaim | outputClaim | DateTime | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

O exemplo seguinte demonstra a conversão da `dateOfBirth` de reclamação (tipo de dados de data) para outra `dateOfBirthWithTime` de reclamação (tipo de dados dataTime).

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
    - **saídasReclamação:** 1559347200 (1 de junho de 2019 12:00:00 AM)

## <a name="convertdatetimetodateclaim"></a>ConvertedateTimetoDateClaim 

Converte um Tipo de Reclamação **datetime** para um Tipo de Reclamação de **Data.** A transformação de sinistros remove o formato de tempo da data.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | DateTime | O ClaimType a converter. |
| OutputClaim | outputClaim | date | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

O exemplo seguinte demonstra a conversão da `systemDateTime` de reclamação (dataDataData DataData data) para outra reclamação `systemDate` (tipo de data).

```XML
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputSClaim**: 1559347200 (1 de junho de 2019 12:00:00 AM)
- Declarações de saída:
  - **saídaSReclamação**: 2019-06-01

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtenha a data e hora utc atuais e adicione o valor a um ClaimType.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | DateTime | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

* Declarações de saída:
    * **actualDataTime**: 1534418820 (16 de agosto de 2018 11:27:00 AM)

## <a name="datetimecomparison"></a>DateTimeComparison

Determine se uma data Time é mais tarde, mais cedo ou igual a outra. O resultado é uma nova booleanclaimType booleancom um valor de `true` ou `false`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | DateTime | O primeiro encontroTime para comparar se é mais cedo ou mais tarde do que o segundo encontroTime. O valor nulo abre uma exceção. |
| InputClaim | secondDateTime | DateTime | O segundo encontroTime para comparar se é mais cedo ou mais tarde do que o primeiro encontroTime. O valor nulo é tratado como a data actualTime. |
| InputParameter | operator | string | Um dos seguintes valores: o mesmo, mais tarde ou mais cedo que. |
| InputParameter | timeSpanInSeconds | int | Adicione o tempo na primeira data. |
| OutputClaim | Resultado | boolean | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

Utilize esta transformação de sinistros para determinar se dois Tipos de Reclamação são iguais, posteriormente ou mais cedo do que os outros. Por exemplo, pode armazenar a última vez que um utilizador aceitou os seus termos de serviços (TOS). Após 3 meses, pode pedir ao utilizador para voltar a aceder ao TOS.
Para executar a transformação da reclamação, primeiro precisa de obter a data atualTime e também a última vez que o utilizador aceita o TOS.

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
    - **segundaDataTempo**: 2018-04-01T00:00:00.100000Z
- Parâmetros de entrada:
    - **operador**: mais tarde do que
    - **tempoSpanInSeconds**: 7776000 (90 dias)
- Declarações de saída:
    - **resultado**: verdadeiro
