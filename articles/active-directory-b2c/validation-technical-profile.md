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
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1eaf159149bb353b1cf0474aad5bc233decddc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481573"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de validação numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico de validação é um perfil técnico comum de qualquer protocolo, como [o Azure Ative Directory](active-directory-technical-profile.md) ou um [REST API](restful-technical-profile.md). O perfil técnico de validação devolve reclamações de saída ou devolve o código de estado 4xx HTTP, com os seguintes dados. Para mais informações, consulte [a mensagem de erro de devolução](restful-technical-profile.md#returning-error-message)

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

O âmbito das alegações de saída de um perfil técnico de validação [limita-se ao perfil técnico autoafirmado](self-asserted-technical-profile.md) que invoca o perfil técnico de validação e os seus perfis técnicos de validação. Se pretender utilizar as alegações de saída no próximo passo de orquestração, adicione as alegações de saída ao perfil técnico autoafirmado que invoca o perfil técnico de validação.

Os perfis técnicos de validação são executados na sequência que aparecem no elemento **ValidaçãoPerfis Técnicos.** Pode configurar num perfil técnico de validação se a execução de quaisquer perfis técnicos de validação subsequentedeve continuar se o perfil técnico de validação levantar um erro ou for bem sucedido.

Um perfil técnico de validação pode ser executado condicionalmente com base nas condições prévias definidas no elemento **ValidaçãoTechnicalProfile.** Por exemplo, pode verificar se existe uma reclamação específica, ou se uma reclamação é igual ou não ao valor especificado.

Um perfil técnico autoafirmado pode definir um perfil técnico de validação a utilizar para validar algumas ou todas as suas reclamações de saída. Todas as reclamações de entrada do perfil técnico referenciado devem figurar nas alegações de saída do perfil técnico de validação de referência.

> [!NOTE]
> Apenas perfis técnicos autoafirmados podem utilizar perfis técnicos de validação. Se precisar de validar as reclamações de saída de perfis técnicos não autoafirmados, considere utilizar um passo de orquestração adicional na sua viagem de utilizador para acomodar o perfil técnico responsável pela validação.

## <a name="validationtechnicalprofiles"></a>ValidaçãoPerfis Técnicos

O elemento **ValidaçãoPerfis Técnicos** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidaçãoPerfil Técnico | 1:n | Um perfil técnico a utilizar para validar algumas ou todas as reclamações de saída do perfil técnico de referência. |

O elemento **ValidaçãoTechnicalProfile** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Identificador de um perfil técnico já definido na política ou na política dos pais. |
|Continuando Erro|Não| Indicando se a validação de quaisquer perfis técnicos de validação subsequente deve continuar se este perfil técnico de validação levantar um erro. Valores `true` possíveis: ou `false` (padrão, o processamento de perfis de validação posteriorpara e um erro devolvido). |
|Continuar O Sucesso | Não | Indicando se a validação de quaisquer perfis de validação subsequentes deve continuar se este perfil técnico de validação for bem sucedido. Valores `true` possíveis: ou `false`. O padrão `true`é , o que significa que o processamento de perfis de validação adicionais continuará. |

O elemento **ValidaçãoTechnicalProfile** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Condições prévias | 0:1 | Uma lista de condições prévias que deve ser satisfeita para que o perfil técnico de validação seja executado. |

O elemento **Pré-condição** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta a realizar para a pré-condição. Ou `ClaimsExist` é especificado para garantir que as ações devem ser executadas se as `ClaimEquals` reclamações especificadas existirem no conjunto de reclamações do utilizador, ou for especificado que as ações devem ser executadas se a reclamação especificada existir e o seu valor for igual ao valor especificado. |
| `ExecuteActionsIf` | Sim | Indica se as ações na condição prévia devem ser realizadas se o teste for verdadeiro ou falso. |

O elemento **Pré-condição** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Valor | 1:n | Os dados que são usados pela verificação. Se o tipo desta `ClaimsExist`verificação for, este campo especifica um ClaimTypeReferenceId para consultar. Se o tipo `ClaimEquals`de verificação for, este campo especifica um ClaimTypeReferenceId para consultar. Enquanto outro elemento de valor contém o valor a ser verificado.|
| Ação | 1:1 | A ação que deve ser tomada se o controlo prévio dentro de um passo de orquestração for verdadeira. O valor da **Ação** `SkipThisValidationTechnicalProfile`está definido para . Especifica que o perfil técnico de validação associado não deve ser executado. |

### <a name="example"></a>Exemplo

O exemplo seguinte utiliza estes perfis técnicos de validação:

1. O primeiro perfil técnico de validação verifica as credenciais do utilizador e não continua se ocorrer um erro, como o nome de utilizador inválido ou a má palavra-passe.
2. O próximo perfil técnico de validação não executa se a reclamação do userType não existir, ou se o valor do utilizadorType for `Partner`. O perfil técnico de validação tenta ler o perfil do utilizador a partir da base de dados interna do cliente e continuar se ocorrer um erro, como o serviço REST API não disponível, ou qualquer erro interno.
3. O último perfil técnico de validação não executa se a alegação do utilizadorType `Customer`não existir, ou se o valor do utilizadorType for . O perfil técnico de validação tenta ler o perfil do utilizador a partir da base de dados interna do parceiro e continua se ocorrer um erro, como o serviço REST API não disponível, ou qualquer erro interno.

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
