---
title: Exemplos de transformação de afirmações de data para a identidade experiência de estrutura de esquema do Azure Active Directory B2C | Documentos da Microsoft
description: Data de exemplos de transformação de afirmações para a identidade experiência de estrutura de esquema do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b287e7f3846de4391de02cce2cedd6a5df3cbc4a
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167652"
---
# <a name="date-claims-transformations"></a>Transformações de afirmações de data

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de afirmações de data do esquema de arquitetura de experiências de identidade no Azure Active Directory (Azure AD) B2C. Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Verifica que uma data e hora afirmações (tipo de dados de cadeia de caracteres) é maior do que um segundo data e hora de afirmação (tipo de dados de cadeia de caracteres) e lança uma exceção.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | cadeia | Tipo de primeira afirmação, que deve ser maior que a segunda declaração. |
| InputClaim | rightOperand | cadeia | Tipo de segundo afirmação, o que deve ser menor que a primeira declaração. |
| InputParameter | AssertIfEqualTo | boolean | Especifica se esta asserção deve passar se o operando esquerdo for igual do operando direito. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Especifica se esta asserção deve passar se o operando direito está em falta. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Especifica o número de milissegundos para permitir entre os dois data horas a ter em consideração os tempos de igual (por exemplo, para a conta para distorção). |

O **AssertDateTimeIsGreaterThan** transformação de declarações é sempre executada a partir de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [autodeclarativas perfil técnico](self-asserted-technical-profile.md). O **DateTimeGreaterThan** metadados de declaração própria perfil técnico controla a mensagem de erro que o perfil técnico apresenta ao usuário.

![Execução de AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

O exemplo a seguir compara os `currentDateTime` afirmação com o `approvedDateTime` de afirmação. É gerado um erro se `currentDateTime` for superior a `approvedDateTime`. A transformação trata valores como igual que estejam dentro de diferença de (30000 milissegundos) de 5 minutos.

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

O `login-NonInteractive` chamadas de perfil técnico de validação a `AssertApprovedDateTimeLaterThanCurrentDateTime` transformação de afirmações.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico de declaração própria chama a validação **NonInteractive de início de sessão** perfil técnico.

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

- Afirmações de entrada:
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Resultado: Erro gerado


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Converte um **data** ClaimType para um **DateTime** ClaimType. A transformação de declarações converte o formato de hora e adiciona 00: 12:00 AM à data.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | date | ClaimType a converter. |
| outputClaim | outputClaim | DateTime | ClaimType produzido este ClaimsTransformation po vyvolání. |

O exemplo seguinte demonstra a conversão da declaração `dateOfBirth` (tipo de dados de data) para uma outra declaração `dateOfBirthWithTime` (tipo de dados dateTime).

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

- Afirmações de entrada:
    - **inputClaim**: 2019-06-01
- Afirmações de saída:
    - **outputClaim**: 1559347200 (1 de Junho de 2019 00: 12:00 AM)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtenha a atual data e hora UTC e adicione o valor para um ClaimType.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | currentDateTime | DateTime | ClaimType produzido este ClaimsTransformation po vyvolání. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

* Afirmações de saída:
    * **currentDateTime**: 1534418820 (16 de Agosto de 2018 27: 11:00)

## <a name="datetimecomparison"></a>DateTimeComparison

Determine se um dateTime é maior, menor ou igual a outro. O resultado é um valor booleano de ClaimType novo com um valor de VERDADEIRO ou FALSO.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | DateTime | A primeira dateTime para comparar. Valor nulo lançará uma exceção. |
| InputClaim | secondDateTime | DateTime | O segundo dateTime para concluir. Valor nulo trata como datetTime atual. |
| InputParameter | Operador | cadeia | Um dos seguintes valores: mesmo, posterior ou anterior. |
| InputParameter | timeSpanInSeconds | int | Adicione o período de tempo para a primeira datetime. |
| outputClaim | Resultado | boolean | ClaimType produzido este ClaimsTransformation po vyvolání. |

Utilize que este afirmações de transformação para determinar se dois ClaimTypes são igual, maior ou menor uns dos outros. Por exemplo, pode armazenar a última vez que um utilizador aceitou os termos de serviços (TOS). Depois de 3 meses, pode pedir ao utilizador aceder a TOS novamente.
Para executar a transformação de afirmação, tem primeiro de obter a data/hora atual e também o último utilizador de tempo aceita os guias de instruções.

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

- Afirmações de entrada:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Parâmetros de entrada:
    - **operador**: posterior a
    - **timeSpanInSeconds**: 7776000 (90 dias)
- Afirmações de saída: 
    - **resultado**: VERDADEIRO

