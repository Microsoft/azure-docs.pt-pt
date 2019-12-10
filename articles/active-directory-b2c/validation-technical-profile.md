---
title: Definir um perfil técnico de validação em uma política personalizada
titleSuffix: Azure AD B2C
description: Valide as declarações usando um perfil técnico de validação em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: facef1e1288f2a64872efbf37a9a31fa05244a7e
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950804"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico de validação em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico de validação é um perfil técnico comum de qualquer protocolo, como [Azure Active Directory](active-directory-technical-profile.md) ou uma [API REST](restful-technical-profile.md). O perfil técnico de validação retorna declarações de saída ou retorna uma mensagem de erro HTTP 409 (código de status de resposta de conflito), com os seguintes dados:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

As declarações que são retornadas de um perfil técnico de validação são adicionadas de volta ao recipiente de declarações. Você pode usar essas declarações na próxima validação de perfis técnicos.

Os perfis técnicos de validação são executados na sequência que aparecem no elemento **ValidationTechnicalProfiles** . Você pode configurar o em um perfil técnico de validação se a execução de quaisquer perfis técnicos de validação subsequentes deve continuar se o perfil técnico de validação gerar um erro ou for bem-sucedido.

Um perfil técnico de validação pode ser executado condicionalmente com base nas pré-condições definidas no elemento **ValidationTechnicalProfile** . Por exemplo, você pode verificar se há declarações específicas ou se uma declaração é igual ou não ao valor especificado.

Um perfil técnico autodeclarado pode definir um perfil técnico de validação a ser usado para validar algumas ou todas as suas declarações de saída. Todas as declarações de entrada do perfil técnico referenciado devem aparecer nas declarações de saída do perfil técnico de validação de referência.

> [!NOTE]
> Somente perfis técnicos autodeclarados podem usar perfis técnicos de validação. Se você precisar validar as declarações de saída de perfis técnicos não autodeclarados, considere o uso de uma etapa de orquestração adicional no percurso do usuário para acomodar o perfil técnico responsável pela validação.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

O elemento **ValidationTechnicalProfiles** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Um perfil técnico a ser usado para validar algumas ou todas as declarações de saída do perfil técnico de referência. |

O elemento **ValidationTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido na política ou diretiva pai. |
|ContinueOnError|Não| Indicando se a validação de quaisquer perfis técnicos de validação subsequentes deve continuar se esse perfil técnico de validação gerar um erro. Valores possíveis: `true` ou `false` (padrão, o processamento de mais perfis de validação será interrompido e um erro retornará). |
|ContinueOnSuccess | Não | Indicando se a validação de quaisquer perfis de validação subsequentes deve continuar se esse perfil técnico de validação tiver sucesso. Valores possíveis: `true` ou `false`. O padrão é `true`, o que significa que o processamento de perfis de validação adicionais continuará. |

O elemento **ValidationTechnicalProfile** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Pré-condições | 0:1 | Uma lista de pré-condições que devem ser satisfeitas para que o perfil técnico de validação seja executado. |

O elemento de **pré-condição** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta a ser executada para a pré-condição. Qualquer um `ClaimsExist` é especificado para garantir que as ações sejam executadas se as declarações especificadas existirem no conjunto de declarações atual do usuário ou `ClaimEquals` for especificado que as ações devem ser executadas se a declaração especificada existir e seu valor for igual ao valor especificado. |
| `ExecuteActionsIf` | Sim | Indica se as ações na pré-condição devem ser executadas se o teste for verdadeiro ou falso. |

O elemento de **pré-condição** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Valor | 1: n | Os dados que são usados pela verificação. Se o tipo dessa verificação for `ClaimsExist`, esse campo especificará um ClaimTypeReferenceId a ser consultado. Se o tipo de verificação for `ClaimEquals`, esse campo especificará um ClaimTypeReferenceId a ser consultado. Enquanto outro elemento Value contém o valor a ser verificado.|
| Ação | 1:1 | A ação que deve ser executada se a verificação de pré-condição dentro de uma etapa de orquestração for verdadeira. O valor da **ação** é definido como `SkipThisValidationTechnicalProfile`. Especifica que o perfil técnico de validação associado não deve ser executado. |

### <a name="example"></a>Exemplo

O exemplo a seguir usa estes perfis técnicos de validação:

1. O primeiro perfil técnico de validação verifica as credenciais do usuário e não continua se ocorrer um erro, como nome de usuário inválido ou senha inválida.
2. O próximo perfil técnico de validação, não será executado se a declaração UserType não existir, ou se o valor do UserType for `Partner`. O perfil técnico de validação tenta ler o perfil do usuário do banco de dados interno do cliente e continuar se ocorrer um erro, como o serviço de API REST não disponível ou qualquer erro interno.
3. O último perfil técnico de validação, não será executado se a declaração UserType não existir, ou se o valor de UserType for `Customer`. O perfil técnico de validação tenta ler o perfil do usuário do banco de dados do parceiro interno e continua se ocorrer um erro, como o serviço de API REST não disponível ou qualquer erro interno.

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
