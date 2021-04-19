---
title: Perfis técnicos de acesso condicional em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para perfis técnicos de acesso condicional em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721891"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de acesso condicional numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Ative Directy (Azure AD) Conditional Access é a ferramenta utilizada pelo Azure AD B2C para reunir sinais, tomar decisões e impor políticas organizacionais. Automatizar a avaliação dos riscos com as condições políticas significa que os sinais de risco são imediatamente identificados e remediados ou bloqueados.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O **atributo handler** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo que é utilizado pela Azure AD B2C:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico de acesso condicional:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Avaliação de Acesso Condicional

Para cada sing-in, o Azure AD B2C avalia todas as políticas e garante que todos os requisitos são cumpridos antes de conceder o acesso ao utilizador. O "bloqueio de acesso" substitui todas as outras definições de configuração. O modo de **Avaliação** do perfil técnico de Acesso Condicional avalia os sinais recolhidos pelo Azure AD B2C durante a entrada com uma conta local. O resultado do perfil técnico de Acesso Condicional é um conjunto de reclamações que resultam da avaliação do Acesso Condicional. A política Azure AD B2C utiliza estas alegações numa próxima etapa de orquestração para tomar uma ação, como bloquear o utilizador ou desafiar o utilizador com a autenticação multi-factor. As seguintes opções podem ser configuradas para este modo.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Tipo de Operação | Yes | Deve ser **avaliação.**  |

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para Acesso Condicional. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico de Acesso Condicional.

| ReclamaçãoReferênciaId | Necessário | Tipo de Dados | Descrição |
| --------- | -------- | ----------- |----------- |
| IDUtilizador | Yes | string | O identificador do utilizador que se inscreve. |
| Autenticação MehodsUsed | Yes |stringCollection | A lista de métodos que o utilizador usou para iniciar sinscrevi-se. Valores possíveis: `Password` e `OneTimePasscode` . |
| IsFederated | Yes |boolean | Indica se um utilizador assinou ou não com uma conta federada. O valor deve `false` ser. |
| IsMfaRegistered | Yes |boolean | Indica se o utilizador já inscreveu um número de telefone para autenticação multi-factor. |


O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de **entradaClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novas antes de enviá-las para o serviço de Acesso Condicional.

### <a name="output-claims"></a>Reclamações de saída

O elemento **OutputClaims** contém uma lista de reclamações geradas pelo ConditionalAccessProtocolProvider. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReclamaçãoReferênciaId | Necessário | Tipo de Dados | Descrição |
| --------- | -------- | ----------- |----------- |
| Desafios | Yes |stringCollection | Lista de ações para remediar a ameaça identificada. Valores possíveis: `block` |
| MultiConditionalAccessStatus | Yes | stringCollection |  |

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="example-evaluation"></a>Exemplo: Avaliação

O exemplo a seguir mostra um perfil técnico de Acesso Condicional que é utilizado para avaliar a ameaça de inscrição.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Remediação

O modo de **remediação** do perfil técnico de Acesso Condicional informa a Azure AD B2C que a ameaça identificada de inscrição é remediada. As seguintes opções podem ser configuradas para o modo de remediação.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Tipo de Operação | Yes | Deve ser **remediação.**  |

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para Acesso Condicional. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico de Acesso Condicional.

| ReclamaçãoReferênciaId | Necessário | Tipo de Dados | Descrição |
| --------- | -------- | ----------- |----------- |
| DesafiosSSatisficados | Yes | stringCollection| A lista de desafios satisfeitos para remediar a ameaça identificada como retorno do modo de avaliação, desafia a reivindicação.|


O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de **entradaClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novas antes de ligar para o serviço de Acesso Condicional.

### <a name="output-claims"></a>Reclamações de saída

O fornecedor de protocolo de acesso condicional não devolve nenhuma **OutputClaims,** pelo que não há necessidade de especificar reclamações de saída. Pode, no entanto, incluir reclamações que não sejam devolvidas pelo fornecedor de protocolo de Acesso Condicional, desde que desemque o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="example-remediation"></a>Exemplo: Remediação

O exemplo a seguir mostra um perfil técnico de acesso condicional que é utilizado para remediar a ameaça de inscrição.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba como [adicionar acesso condicional aos fluxos de utilizador no Azure Ative Directory B2C](conditional-access-user-flow.md).
