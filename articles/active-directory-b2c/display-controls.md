---
title: Referência de controlo de exibição
titleSuffix: Azure AD B2C
description: Referência para os comandos de exibição Azure AD B2C. Utilize controlos de exibição para personalizar as viagens de utilizador definidas nas suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188737"
---
# <a name="display-controls"></a>Controlos de exibição

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um **controlo de ecrã** é um elemento de interface de utilizador que possui uma funcionalidade especial e interage com o serviço de back-end do Azure Ative Directory B2C (Azure AD B2C). Permite ao utilizador realizar ações na página que invocam um perfil técnico de [validação](validation-technical-profile.md) na parte de trás. Os controlos de exibição são apresentados na página e são referenciados por um [perfil técnico autoafirmado](self-asserted-technical-profile.md).

A imagem que se segue ilustra uma página de inscrição autoafirmada com dois controlos de exibição que validam um endereço de e-mail primário e secundário.

![Controlo de exibição renderizado por exemplo](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

 Na secção [metadados](self-asserted-technical-profile.md#metadata) de um [perfil técnico autoafirmado,](self-asserted-technical-profile.md)o `DataUri` Conteúdo [Definido](contentdefinitions.md) referenciado precisa de ter definido para a versão de contrato de página 2.0.0 ou superior. Por exemplo:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definição de controlos de exibição

O elemento **DisplayControl** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para o controlo de exibição. Pode ser [referenciado.](#referencing-display-controls) |
| UserInterfaceControlType | Sim | O tipo de controlo de exibição. Atualmente suportado é [Control de Verificação](display-control-verification.md) |

O elemento **DisplayControl** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Créditos de entrada | 0:1 | **As InputClaims** são utilizadas para pré-povoar o valor das reclamações a recolher junto do utilizador. |
| DisplayClaims | 0:1 | **Os DisplayClaims** são utilizados para representar alegações a recolher junto do utilizador. |
| OutputClaims | 0:1 | **OutputClaims** são utilizados para representar alegações que serão guardadas temporariamente para este **DisplayControl**. |
| Ações | 0:1 | **As ações** são usadas para listar os perfis técnicos de validação para invocar as ações do utilizador que acontecem na parte frontal. |

### <a name="input-claims"></a>Reclamações de entrada

Num controlo de exibição, pode utilizar elementos **InputClaims** para pré-povoar o valor das reclamações a recolher do utilizador na página. Quaisquer **Transformações inputClaims** podem ser definidas no perfil técnico autoafirmado que refere este controlo de exibição.

O exemplo que se segue prepovoa o endereço de e-mail a ser verificado com o endereço já presente.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Apresentar reclamações

Cada tipo de controlo de exibição requer um conjunto diferente de reclamações de exibição, [alegações](#output-claims)de saída e [ações](#display-control-actions) a realizar.

Semelhante às **alegações** de exibição definidas num [perfil técnico autoafirmado,](self-asserted-technical-profile.md#display-claims)as alegações de exibição representam as alegações a recolher junto do utilizador no controlo do ecrã. O elemento **ClaimType** referenciado precisa de especificar o elemento **UserInputType** para um tipo de entrada `TextBox` `DropdownSingleSelect`do utilizador suportado pelo Azure AD B2C, tal como ou . Se for exigido um valor de reclamação **Required** de visualização `true` por uma **Ação,** detete o atributo necessário para forçar o utilizador a fornecer um valor para essa reclamação específica.

São necessárias certas alegações de exibição para certos tipos de controlo de visualização. Por exemplo, o Código de **Verificação** é necessário para o controlo do ecrã do tipo Controlo de **Verificação**. Utilize o atributo **ControlClaimClaimType** para especificar qual o DisplayClaim designado para a reclamação necessária. Por exemplo:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Reclamações de produção

As **alegações** de saída de um controlo de exibição não são enviadas para o próximo passo de orquestração. São guardados temporariamente apenas para a atual sessão de controlo de exibição. Estas alegações temporárias podem ser partilhadas entre as diferentes ações do mesmo controlo de exibição.

Para borbulhar as alegações de saída para o próximo passo de orquestração, utilize as **OutputClaims** do perfil técnico autoafirmado real que faz referência a este controlo de exibição.

### <a name="display-control-actions"></a>Ações de controlo de exibição

As **Ações** de um controlo de ecrã são procedimentos que ocorrem na extremidade traseira do Azure AD B2C quando um utilizador realiza uma determinada ação do lado do cliente (o navegador). Por exemplo, as validações a realizar quando o utilizador seleciona um botão na página.

Uma ação define uma lista de perfis técnicos de **validação.** São utilizados para validar algumas ou todas as alegações de exibição do controlo de exibição. O perfil técnico de validação valida a entrada do utilizador e pode devolver um erro ao utilizador. Pode utilizar **o ContinueOnError**, **ContinueOnSuccess**e **as Preconditionss** no controlo do ecrã Ação semelhante à forma como são utilizados em perfis técnicos de [validação](validation-technical-profile.md) num perfil técnico autoafirmado.

O exemplo seguinte envia um código quer em e-mail quer em SMS com base na seleção do utilizador da alegação **mfaType.**

```XML
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Referenciação de controlos de exibição

Os controlos de [exibição](self-asserted-technical-profile.md#display-claims) são referenciados nas alegações de exibição do [perfil técnico autoafirmado](self-asserted-technical-profile.md).

Por exemplo:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
