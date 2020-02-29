---
title: Defina um perfil técnico de validação numa política personalizada
titleSuffix: Azure AD B2C
description: Validar as reclamações utilizando um perfil técnico de validação numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 65a2eab05e7c475431602d9c2d3fc44b59bbc8f7
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185731"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de validação numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico de validação é um perfil técnico comum de qualquer protocolo, como [o Azure Ative Directory](active-directory-technical-profile.md) ou um [REST API](restful-technical-profile.md). O perfil técnico de validação devolve reclamações de saída ou devolve uma mensagem de erro HTTP 409 (código de estado de resposta a conflitos), com os seguintes dados:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

As reclamações devolvidas de um perfil técnico de validação são adicionadas ao saco de reclamações. Pode utilizar essas reclamações nos perfis técnicos de validação seguintes.

Os perfis técnicos de validação são executados na sequência que aparecem no elemento **ValidaçãoPerfis Técnicos.** Pode configurar num perfil técnico de validação se a execução de quaisquer perfis técnicos de validação subsequentedeve continuar se o perfil técnico de validação levantar um erro ou for bem sucedido.

Um perfil técnico de validação pode ser executado condicionalmente com base nas condições prévias definidas no elemento **ValidaçãoTechnicalProfile.** Por exemplo, pode verificar se existe uma reclamação específica, ou se uma reclamação é igual ou não ao valor especificado.

Um perfil técnico autoafirmado pode definir um perfil técnico de validação a utilizar para validar algumas ou todas as suas reclamações de saída. Todas as reclamações de entrada do perfil técnico referenciado devem figurar nas alegações de saída do perfil técnico de validação de referência.

> [!NOTE]
> Apenas perfis técnicos autoafirmados podem utilizar perfis técnicos de validação. Se precisar de validar as reclamações de saída de perfis técnicos não autoafirmados, considere utilizar um passo de orquestração adicional na sua viagem de utilizador para acomodar o perfil técnico responsável pela validação.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

O elemento **ValidaçãoPerfis Técnicos** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Um perfil técnico a utilizar para validar algumas ou todas as reclamações de saída do perfil técnico de referência. |

O elemento **ValidaçãoTechnicalProfile** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Identificador de um perfil técnico já definido na política ou na política dos pais. |
|ContinueOnError|Não| Indicando se a validação de quaisquer perfis técnicos de validação subsequente deve continuar se este perfil técnico de validação levantar um erro. Valores possíveis: `true` ou `false` (padrão, o processamento de perfis de validação posteriores para e um erro devolvido). |
|ContinueOnSuccess | Não | Indicando se a validação de quaisquer perfis de validação subsequentes deve continuar se este perfil técnico de validação for bem sucedido. Valores possíveis: `true` ou `false`. O padrão é `true`, o que significa que o processamento de perfis de validação continuará. |

O elemento **ValidaçãoTechnicalProfile** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Condições prévias | 0:1 | Uma lista de condições prévias que deve ser satisfeita para que o perfil técnico de validação seja executado. |

O elemento **Pré-condição** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta a realizar para a pré-condição. Ou é especificado `ClaimsExist` para garantir que as ações devem ser executadas se as reclamações especificadas existirem no conjunto de reclamações do utilizador, ou `ClaimEquals` é especificado que as ações devem ser executadas se a reclamação especificada existir e o seu valor for igual ao valor especificado. |
| `ExecuteActionsIf` | Sim | Indica se as ações na condição prévia devem ser realizadas se o teste for verdadeiro ou falso. |

O elemento **Pré-condição** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Valor | 1:n | Os dados que são usados pela verificação. Se o tipo desta verificação for `ClaimsExist`, este campo especifica um ClaimTypeReferenceId para consultar. Se o tipo de verificação for `ClaimEquals`, este campo especifica um ClaimTypeReferenceId para consultar. Enquanto outro elemento de valor contém o valor a ser verificado.|
| Ação | 1:1 | A ação que deve ser tomada se o controlo prévio dentro de um passo de orquestração for verdadeira. O valor da **Ação** está definido para `SkipThisValidationTechnicalProfile`. Especifica que o perfil técnico de validação associado não deve ser executado. |

### <a name="example"></a>Exemplo

O exemplo seguinte utiliza estes perfis técnicos de validação:

1. O primeiro perfil técnico de validação verifica as credenciais do utilizador e não continua se ocorrer um erro, como o nome de utilizador inválido ou a má palavra-passe.
2. O próximo perfil técnico de validação não executa se a reclamação do userType não existir, ou se o valor do utilizadorType for `Partner`. O perfil técnico de validação tenta ler o perfil do utilizador a partir da base de dados interna do cliente e continuar se ocorrer um erro, como o serviço REST API não disponível, ou qualquer erro interno.
3. O último perfil técnico de validação não executa se a reclamação do utilizadorType não existiu, ou se o valor do utilizadorType for `Customer`. O perfil técnico de validação tenta ler o perfil do utilizador a partir da base de dados interna do parceiro e continua se ocorrer um erro, como o serviço REST API não disponível, ou qualquer erro interno.

```XML
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
