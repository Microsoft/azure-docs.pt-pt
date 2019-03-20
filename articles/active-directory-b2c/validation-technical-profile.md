---
title: Definir um perfil de técnicas de validação numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico do Azure Active Directory numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 3172354752e2c6a1a7368b297e3cc6e763cd2fcb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900578"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil de técnicas de validação numa política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil de técnicas de validação é um perfil técnico comum de qualquer protocolo, como [do Azure Active Directory](active-directory-technical-profile.md) ou uma [REST API](restful-technical-profile.md). O perfil técnico de validação devolve afirmações de saída ou devolve uma mensagem de erro de HTTP 409 (conflito resposta código de estado), com os seguintes dados:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Afirmações que são devolvidas a partir de um perfil de técnicas de validação são adicionadas novamente para o conjunto de afirmações. Pode usar essas declarações nos perfis de técnicas de validação seguintes.

Perfis de técnicos de validação são executados na sequência que aparecem no **ValidationTechnicalProfiles** elemento. Pode configurar um perfil de técnicas de validação se a execução de quaisquer perfis de técnicas de validação subsequentes deve continuar se o perfil técnico de validação gera um erro ou for concluída com êxito.

Uma validação perfil técnico pode ser executado condicionalmente com base em pré-condições definidas no **ValidationTechnicalProfile** elemento. Por exemplo, pode verificar se existe um afirmações específicas, ou se uma afirmação é igual ou não com o valor especificado.

Um perfil técnico de declaração própria pode definir um perfil de técnicas de validação a ser utilizado para validar a algumas ou todas as suas declarações de saída. Todas as declarações de entrada do perfil técnico referenciado tem de aparecer nas afirmações de saída do perfil de técnicas de validação referência.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

O **ValidationTechnicalProfiles** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Um perfil técnico a ser utilizado para validar a algumas ou todas as afirmações de saída do perfil técnico referência. |

O **ValidationTechnicalProfile** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido a política ou a política de principal. |
|ContinueOnError|Não| Que indica se a validação de todos os perfis técnicas de validação subsequentes deve continuar se este perfil de técnicas de validação gera um erro. Valores possíveis: `true` ou `false` (o padrão, deixará de processamento de ainda mais os perfis de validação e devolvido um erro). |
|ContinueOnSuccess | Não | Que indica se a validação de todos os perfis de validação subsequentes deve continuar se este perfil de técnicas de validação é bem-sucedida. Valores possíveis: `true` ou `false`. A predefinição é `true`, que significa que o processamento de ainda mais os perfis de validação irá continuar. |

O **ValidationTechnicalProfile** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Pré-condições | 0:1 | Uma lista de pré-condições que devem ser satisfeitos para o perfil técnico de validação executar. |

O **pré-condição** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Type | Sim | O tipo de verificação ou a consulta para efetuar durante a pré-condição. Qualquer um dos `ClaimsExist` for especificado, para garantir que ações devem ser realizadas se as afirmações especificadas existem no conjunto de afirmações atual do usuário, ou `ClaimEquals` é especificada a que as ações devem ser efetuadas se a afirmação especificado existe e o valor é igual à valor especificado. |
| ExecuteActionsIf | Sim | Indica se as ações na pré-condição devem ser efetuadas se o teste for VERDADEIRO ou FALSO. |

O **pré-condição** elemento contém seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Value | 1:n | Os dados que são utilizados pela verificação. Se o tipo desta verificação é `ClaimsExist`, este campo especifica um ClaimTypeReferenceId para consultar. Se o tipo de verificação é `ClaimEquals`, este campo especifica um ClaimTypeReferenceId para consultar. Enquanto outro elemento de valor contém o valor a ser verificado.|
| Ação | 1:1 | A ação que deve ser tomada caso a verificação de pré-condição dentro de um passo de orquestração é verdadeira. O valor do **ação** está definida como `SkipThisValidationTechnicalProfile`. Especifica que o perfil técnico de validação associados não deve ser executado. |

### <a name="example"></a>Exemplo

Exemplo seguinte utiliza estes perfis de técnicas de validação:

1. O primeiro perfil técnico de validação verifica as credenciais de utilizador e não continua se ocorrer um erro, como o nome de utilizador inválido ou a palavra-passe incorreta.
2. O perfil técnico validação seguinte, não é executado se a afirmação userType não existir, ou se o valor do userType é `Partner`. O perfil técnico de validação tenta ler o perfil de utilizador da base de dados dos clientes internos e continue se ocorrer um erro, como o serviço de REST API não está disponível, ou qualquer erro interno.
3. O perfil técnico validação última, não é executado se a afirmação userType não existe ou se o valor do userType é `Customer`. O perfil técnico de validação tenta ler o perfil de utilizador da base de dados interna do parceiro e continua se ocorrer um erro, como o serviço de REST API não está disponível, ou qualquer erro interno.

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
