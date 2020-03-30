---
title: Data reclama exemplos de transformação para políticas personalizadas
description: Data reclama exemplos de transformação para o quadro de experiência de identidade (IEF) do Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c02ac9392d6f3f95deef38ff86250e96dfb76d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476693"
---
# <a name="date-claims-transformations"></a>Data reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização da data de transformações do Quadro de Experiência de Identidade no Azure Ative Directory B2C (Azure AD B2C). Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertdateTimeisgreaterThan

Verifica se uma data e a reclamação de hora (tipo de dados de cordas) são posteriores a uma segunda data e pedido de hora (tipo de dados de cadeia), e lança uma exceção.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | leftOperand | string | O tipo de primeira reclamação, que deve ser mais tarde do que a segunda alegação. |
| Pedido de crédito | direitoOperand | string | O segundo tipo de reclamação, que deve ser mais cedo do que a primeira alegação. |
| EntradaParametro | AssertifEqualto | boolean | Especifica se esta afirmação deve passar se o operand esquerdo é igual ao operand direito. |
| EntradaParametro | AssertifRightOperandIsNotPresent | boolean | Especifica se esta afirmação deve passar se o operand certo está em falta. |
| EntradaParametro | TreatAsEqualIfWithinMillseconds | int | Especifica o número de milissegundos para permitir que entre os dois dias de data considere os tempos iguais (por exemplo, para contabilizar o desvio do relógio). |

A transformação de **afirmações AssertDateTimeIsGreaterThan** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md). Os metadados de perfil técnico **DateTimeGreaterThan** autoafirmados controlam a mensagem de erro que o perfil técnico apresenta ao utilizador. As mensagens de erro podem ser [localizadas.](localization-string-ids.md#claims-transformations-error-messages)

![AssertstringClaimsAreIgual execução](./media/date-transformations/assert-execution.png)

O exemplo seguinte `currentDateTime` compara `approvedDateTime` a reclamação com a alegação. Um erro é `currentDateTime` lançado se `approvedDateTime`for mais tarde que . A transformação trata os valores como iguais se estiverem dentro de 5 minutos (30000 milissegundos) de diferença.

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

O `login-NonInteractive` perfil técnico `AssertApprovedDateTimeLaterThanCurrentDateTime` de validação chama a transformação de sinistros.
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

- Créditos de entrada:
    - **esquerdaOperand**: 2020-03-01T15:00:00.00000000Z
    - **rightOperand**: 2020-03-01T14:00:00.00000000Z
- Resultado: Erro lançado

## <a name="convertdatetodatetimeclaim"></a>Convertendo DatatoDateClaim

Converte um Tipo de Reclamação de **Data** para um Tipo de Reclamação **datetime.** A transformação de sinistros converte o formato de tempo e adiciona às 12:00:00 da manhã.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputClaim | date | O ClaimType a converter. |
| Pedido de saída | saídaReclamada | dataTempo | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

O exemplo seguinte demonstra a `dateOfBirth` conversão da reclamação (tipo de data) para outra reclamação `dateOfBirthWithTime` (dataDataData data tipo de dados).

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

- Créditos de entrada:
    - **inputClaim**: 2020-15-03
- Alegações de saída:
    - **saídaSReclamação:** 2020-15-03T00:00:00.00000000Z

## <a name="convertdatetimetodateclaim"></a>ConvertedateTimetoDateClaim

Converte um Tipo de Reclamação **datetime** para um Tipo de Reclamação de **Data.** A transformação de sinistros remove o formato de tempo da data.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputClaim | dataTempo | O ClaimType a converter. |
| Pedido de saída | saídaReclamada | date | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

O exemplo seguinte demonstra a `systemDateTime` conversão da reclamação (dataDataData DataData data type) para outra reclamação `systemDate` (tipo de data).

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

- Créditos de entrada:
  - **inputSClaim**: 2020-15-03T11:34:22.00000000Z
- Alegações de saída:
  - **saídaSReclamação:** 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtenha a data e hora utc atuais e adicione o valor a um ClaimType.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de saída | actualDateTime | dataTempo | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

* Alegações de saída:
    * **actualDataTime**: 2020-15-03T11:40:35.00000000Z

## <a name="datetimecomparison"></a>DataTimeCompare

Determine se uma data Time é mais tarde, mais cedo ou igual a outra. O resultado é uma nova booleanClaimType `true` booleancom um valor de ou `false`.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | primeiroDataTime | dataTempo | O primeiro encontroTime para comparar se é mais cedo ou mais tarde do que o segundo encontroTime. O valor nulo abre uma exceção. |
| Pedido de crédito | segundaData | dataTempo | O segundo encontroTime para comparar se é mais cedo ou mais tarde do que o primeiro encontroTime. O valor nulo é tratado como a data actualTime. |
| EntradaParametro | operador | string | Um dos seguintes valores: o mesmo, mais tarde ou mais cedo que. |
| EntradaParametro | timespaninSeconds | int | Adicione o tempo na primeira data. |
| Pedido de saída | result | boolean | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

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

- Créditos de entrada:
    - **firstDateTime**: 2020-01-01T00:00:00.100000Z
    - **segundaDataTempo**: 2020-04-01T00:00:00.100000Z
- Parâmetros de entrada:
    - **operador**: mais tarde do que
    - **tempoSpanInSeconds**: 7776000 (90 dias)
- Alegações de saída:
    - **resultado**: verdadeiro
