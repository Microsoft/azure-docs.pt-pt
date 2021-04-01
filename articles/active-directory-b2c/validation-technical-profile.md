---
title: Definir um perfil técnico de validação numa política personalizada
titleSuffix: Azure AD B2C
description: Validar as reclamações utilizando um perfil técnico de validação numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d4c538a9292698fecc8b44c055ab201748e292c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85202998"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de validação numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico de validação é um perfil técnico ordinário de qualquer protocolo, como [o Azure Ative Directory](active-directory-technical-profile.md) ou um [REST API](restful-technical-profile.md). O perfil técnico de validação devolve as reclamações de saída, ou devolve o código de estado 4xx HTTP, com os seguintes dados. Para obter mais informações, consulte [a mensagem de erro de retorno](restful-technical-profile.md#returning-validation-error-message)

```json
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

O âmbito das reclamações de saída de um perfil técnico de validação [limita-se ao perfil técnico autoafirmado](self-asserted-technical-profile.md) que invoca o perfil técnico de validação e os seus perfis técnicos de validação. Se pretender utilizar as reclamações de saída no próximo passo de orquestração, adicione as alegações de saída ao perfil técnico autoafirmado que invoca o perfil técnico de validação.

Os perfis técnicos de validação são executados na sequência que aparecem no elemento **ValidationTechnicalProfiles.** Pode configurar num perfil técnico de validação se a execução de quaisquer perfis técnicos de validação subsequentes deve continuar se o perfil técnico de validação levantar um erro ou for bem sucedido.

Um perfil técnico de validação pode ser executado condicionalmente com base em condições prévias definidas no elemento **ValidationTechnicalProfile.** Por exemplo, pode verificar se existe uma reclamação específica ou se uma reclamação é igual ou não ao valor especificado.

Um perfil técnico autoafirmado pode definir um perfil técnico de validação a utilizar para validar algumas ou todas as suas reclamações de saída. Todas as alegações de entrada do perfil técnico referenciado devem figurar nos pedidos de saída do perfil técnico de validação de referência.

> [!NOTE]
> Apenas perfis técnicos autoafirmados podem utilizar perfis técnicos de validação. Se precisar de validar as reclamações de saída de perfis técnicos não autoafirmados, considere utilizar um passo adicional de orquestração na sua jornada de utilizador para acomodar o perfil técnico responsável pela validação.

## <a name="validationtechnicalprofiles"></a>ValidaçãoTechnicalProfiles

O elemento **ValidationTechnicalProfiles** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| ValidaçãoTechnicalProfile | 1:n | Um perfil técnico a utilizar para validar algumas ou todas as reivindicações de saída do perfil técnico de referência. |

O elemento **ValidationTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Um identificador de um perfil técnico já definido na política ou na política dos pais. |
|ContinueOnError|No| Indicar se a validação de quaisquer perfis técnicos de validação subsequentes deve continuar se este perfil técnico de validação levantar um erro. Valores possíveis: `true` ou `false` (por defeito, o processamento de perfis de validação adicionais irá parar e um erro devolvido). |
|ContinueOnSuccess | No | Indicar se a validação de quaisquer perfis de validação subsequentes deve continuar se este perfil técnico de validação for bem sucedido. Valores possíveis: `true` ou `false` . O padrão é `true` , o que significa que o processamento de perfis de validação adicionais continuará. |

O elemento **ValidationTechnicalProfile** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Condições prévias | 0:1 | Uma lista de pré-condições que devem ser satisfeitas para que o perfil técnico de validação seja executado. |

O **elemento pré-condição** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta a efetuar para a pré-condição. Ou `ClaimsExist` é especificado para garantir que as ações devem ser executadas se as alegações especificadas existirem no conjunto de reclamações atuais do utilizador, ou se for especificado que as `ClaimEquals` ações devem ser executadas se a reclamação especificada existir e o seu valor for igual ao valor especificado. |
| `ExecuteActionsIf` | Yes | Indica se as ações na condição prévia devem ser executadas se o teste for verdadeiro ou falso. |

O **elemento pré-condição** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Valor | 1:n | Os dados que são utilizados pelo cheque. Se o tipo desta verificação `ClaimsExist` for, este campo especifica uma ClaimTypeReferenceId para consulta. Se o tipo de verificação `ClaimEquals` for, este campo especifica uma ClaimTypeReferenceId para consulta. Enquanto outro elemento de valor contém o valor a ser verificado.|
| Ação | 1:1 | A ação que deve ser tomada se o controlo de pré-condição dentro de um passo de orquestração for verdadeiro. O valor da **Ação** está definido para `SkipThisValidationTechnicalProfile` . Especifica que o perfil técnico de validação associado não deve ser executado. |

### <a name="example"></a>Exemplo

O exemplo seguinte utiliza estes perfis técnicos de validação:

1. O primeiro perfil técnico de validação verifica as credenciais do utilizador e não continua se ocorrer um erro, como nome de utilizador inválido ou má palavra-passe.
2. O próximo perfil técnico de validação, não executa se a alegação do userType não existir, ou se o valor do userType for `Partner` . O perfil técnico de validação tenta ler o perfil do utilizador a partir da base de dados interna do cliente e continuar se ocorrer um erro, como o serviço REST API não disponível, ou qualquer erro interno.
3. O último perfil técnico de validação, não executa se a alegação userType não existiu, ou se o valor do userType for `Customer` . O perfil técnico de validação tenta ler o perfil do utilizador a partir da base de dados do parceiro interno e continua se ocorrer um erro, como o serviço REST API não disponível, ou qualquer erro interno.

```xml
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
