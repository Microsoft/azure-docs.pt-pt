---
title: Data reclama exemplos de transformação para políticas personalizadas
description: Data reclama exemplos de transformação para o esquema do Quadro de Experiência de Identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eaf58b964517162ee7f7eb925e1e64830eedc087
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202556"
---
# <a name="date-claims-transformations"></a>Data reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização da data alega transformações do esquema do Quadro de Experiência de Identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [SinistrosTransformações](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Verifica se uma reclamação de data e hora (tipo de dados de cadeia) é mais tarde do que uma segunda data e reclamação de hora (tipo de dados de cadeia), e lança uma exceção.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | esquerdaOperand | string | O tipo da primeira reclamação, que deve ser mais tarde do que a segunda reclamação. |
| InputClaim | direitoOperação | string | O tipo de segunda reclamação, que deve ser mais cedo do que a primeira reclamação. |
| InputParameter | AssertIfEqualTo | boolean | Especifica se esta afirmação deve passar se a ópera esquerda é igual à ópera direita. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Especifica se esta afirmação deve passar se a ópera certa estiver faltando. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Especifica o número de milissegundos para permitir que entre as duas datas considere os tempos iguais (por exemplo, para ter em conta o desvio do relógio). |

O **AssertDateTimeIsGreaterThan** afirma que a transformação é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado.](self-asserted-technical-profile.md) Os metadados de perfil técnico **dataTimeGreaterThan** autoafirmados controlam a mensagem de erro que o perfil técnico apresenta ao utilizador. As mensagens de erro podem ser [localizadas](localization-string-ids.md#claims-transformations-error-messages).

![Execução assertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

O exemplo a seguir compara a `currentDateTime` reclamação com a `approvedDateTime` reclamação. Um erro é lançado se `currentDateTime` for mais tarde que `approvedDateTime` . A transformação trata os valores como iguais se estiverem dentro de 5 minutos (30000 milissegundos) de diferença.

```xml
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

O `login-NonInteractive` perfil técnico de validação chama a transformação de `AssertApprovedDateTimeLaterThanCurrentDateTime` sinistros.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autoafirmado chama o perfil técnico **de login-NonInteractive de** validação.

```xml
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

- Reclamações de entrada:
    - **esquerdaOperand**: 2020-03-01T15:00:00.000000Z
    - **direitoOperand**: 2020-03-01T14:00:00.000000Z
- Resultado: Erro lançado

## <a name="convertdatetodatetimeclaim"></a>ConverteDateToDateTimeClaim

Converte **umTip** de reclamação de data para umTip de reclamação **de data.** A transformação de sinistros converte o formato de tempo e adiciona 12:00:00 AM à data.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | O ClaimType a ser convertido. |
| OutputClaim | outputClaim | dataTime | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. |

O exemplo a seguir demonstra a conversão da reclamação `dateOfBirth` (tipo de dados de data) para outra reclamação `dateOfBirthWithTime` (tipo de dados dataTime).

```xml
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

- Reclamações de entrada:
    - **inputClaim**: 2020-15-03
- Reclamações de saída:
    - **outputClaim**: 2020-15-03T00:00:00.000000Z

## <a name="convertdatetimetodateclaim"></a>ConverteDateTimeToDateClaim

Converte **umTip de reclamação de datatime** para umTip de reclamação de **data.** A transformação de sinistros remove o formato de tempo da data.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | dataTime | O ClaimType a ser convertido. |
| OutputClaim | outputClaim | date | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. |

O exemplo a seguir demonstra a conversão da reclamação `systemDateTime` (tipo de dados dataTime) para outra reclamação `systemDate` (tipo de dados de data).

```xml
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

- Reclamações de entrada:
  - **inputClaim**: 2020-15-03T11:34:22.000000Z
- Reclamações de saída:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtenha a data e hora do UTC atuais e adicione o valor a um ClaimType.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | atualDateTime | dataTime | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. |

```xml
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

* Reclamações de saída:
    * **atualDateTime**: 2020-15-03T11:40:35.000000Z

## <a name="datetimecomparison"></a>DataTimeComparison

Determine se uma dataTime é mais tarde, mais cedo ou igual a outra. O resultado é um novo boolean ClaimType boolean boolean com um valor de `true` ou `false` .

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dataTime | A primeira dataTime para comparar se é mais cedo ou mais tarde do que a segunda dataTime. O valor nulo abre uma exceção. |
| InputClaim | secondDateTime | dataTime | A segunda dataTime para comparar se é mais cedo ou mais tarde do que a primeira dataTime. O valor nulo é tratado como a data atual. |
| InputParameter | operador | string | Um dos seguintes valores: o mesmo, mais tarde ou mais cedo do que. |
| InputParameter | timeSpanInSeconds | int | Adicione o intervalo de tempo à primeira data. |
| OutputClaim | result | boolean | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. |

Utilize esta transformação de sinistros para determinar se dois ClaimTypes são iguais, mais tarde ou mais cedo do que os outros. Por exemplo, pode armazenar a última vez que um utilizador aceitou os seus termos de serviços (TOS). Após 3 meses, pode pedir ao utilizador que volte a aceder ao TOS.
Para executar a transformação da reclamação, primeiro precisa de obter a data atualTime e também a última vez que o utilizador aceita o TOS.

```xml
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

- Reclamações de entrada:
    - **firstDateTime**: 2020-01-01T00:00:00.100000Z
    - **secondDateTime**: 2020-04-01T00:00:00.100000Z
- Parâmetros de entrada:
    - **operador**: mais tarde
    - **timeSpanInSeconds**: 7776000 (90 dias)
- Reclamações de saída:
    - **resultado**: verdadeiro
