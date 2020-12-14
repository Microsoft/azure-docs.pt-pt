---
title: Referência de controlo de exibição
titleSuffix: Azure AD B2C
description: Referência para os comandos de visualização Azure AD B2C. Utilize controlos de exibição para personalizar viagens de utilizador definidas nas suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387059"
---
# <a name="display-controls"></a>Controlos de exibição

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um **controlo de ecrã** é um elemento de interface do utilizador que tem uma funcionalidade especial e interage com o serviço de back-end Azure Ative Directory B2C (Azure AD B2C). Permite ao utilizador executar ações na página que invocam um [perfil técnico de validação](validation-technical-profile.md) na parte de trás. Os controlos do visor são apresentados na página e são referenciados por um [perfil técnico autoafirmado](self-asserted-technical-profile.md).

A imagem a seguir ilustra uma página de inscrição autoafirmada com dois controlos de exibição que validam um endereço de e-mail primário e secundário.

![Exemplo de controlo de exibição prestado](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Pré-requisitos

 Na secção [metadados](self-asserted-technical-profile.md#metadata) de um [perfil técnico autoafirmado,](self-asserted-technical-profile.md)a [definição](contentdefinitions.md) de conteúdo referenciada tem de ter `DataUri` definido para a versão 2.0.0 ou superior do contrato de página. Por exemplo:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definição de controlos de exibição

O elemento **DisplayControl** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para o controlo de exibição. Pode ser [referenciado.](#referencing-display-controls) |
| UserInterfaceControlType | Sim | O tipo de controlo de visualização. Atualmente suportado é [Controle de Verificação](display-control-verification.md) |

O elemento **DisplayControl** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** são utilizados para pré-povoar o valor das reclamações a recolher junto do utilizador. Para mais informações, consulte o elemento [InputClaims.](technicalprofiles.md#input-claims) |
| DisplayClaims | 0:1 | **DisplayClaims** são utilizados para representar alegações a serem recolhidas junto do utilizador. Para mais informações, consulte o elemento [DisplayClaim.](technicalprofiles.md#displayclaim)|
| Resultados | 0:1 | **OutputClaims** são utilizados para representar alegações que serão guardadas temporariamente para este **DisplayControl**. Para mais informações, consulte o elemento [OutputClaims.](technicalprofiles.md#output-claims)|
| Ações | 0:1 | **As ações** são usadas para listar os perfis técnicos de validação para invocar para as ações do utilizador que ocorram na parte frontal. |

### <a name="input-claims"></a>Reclamações de entrada

Num controlo de visualização, pode utilizar **elementos InputClaims** para pré-povoar o valor das reclamações a recolher do utilizador na página. Quaisquer **InputClaimsTransformations** podem ser definidas no perfil técnico autoafirmado que faz referência a este controlo de visualização.

O exemplo seguinte préopula o endereço de e-mail a ser verificado com o endereço já presente.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Apresentar reclamações

Cada tipo de controlo de exibição requer um conjunto diferente de reclamações de exibição, [reivindicações de saída](#output-claims)e ações a serem [executadas.](#display-control-actions)

À semelhança das alegações do **visor definidas** num [perfil técnico autoafirmado,](self-asserted-technical-profile.md#display-claims)as alegações do visor representam as alegações a recolher do utilizador dentro do controlo do visor. O elemento **ClaimType** referenciado precisa de especificar o elemento **UserInputType** para um tipo de entrada de utilizador suportado por Azure AD B2C, tal como `TextBox` ou `DropdownSingleSelect` . Se for exigido um valor de reclamação de visualização por uma **Ação,** desabrisse o atributo **exigido** `true` para forçar o utilizador a fornecer um valor para essa alegação específica do visor.

São necessárias determinadas alegações de visualização para certos tipos de controlo de visualização. Por exemplo, o **Código de Verificação** é necessário para o controlo do visor do tipo Controlo de **Verificação**. Utilize o **atributo ControlClaimType** para especificar qual o DisplayClaim designado para a reclamação necessária. Por exemplo:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Reclamações de saída

As **alegações** de saída de um controlo de exibição não são enviadas para o próximo passo de orquestração. São guardadas temporariamente apenas para a sessão de controlo do visor atual. Estas alegações temporárias podem ser partilhadas entre as diferentes ações do mesmo controlo de exibição.

Para borbulhar as reivindicações de saída para o próximo passo de orquestração, utilize os **OutputClaims** do perfil técnico autoafirmado que faz referência a este controlo de exibição.

### <a name="display-control-actions"></a>Ações de controlo de exibição

As **Ações** de um controlo de exibição são procedimentos que ocorrem no azure AD B2C final quando um utilizador realiza uma determinada ação do lado do cliente (o navegador). Por exemplo, as validações a executar quando o utilizador seleciona um botão na página.

Uma ação define uma lista de perfis técnicos de **validação.** São utilizados para validar algumas ou todas as reivindicações do ecrã do controlo do visor. O perfil técnico de validação valida a entrada do utilizador pode devolver um erro ao utilizador. Pode utilizar **ContinueOnError**, **ContinueOnSuccess** e **Pré-condições** no controlo de ecrã Ação semelhante à forma como são utilizados em [perfis técnicos de validação](validation-technical-profile.md) num perfil técnico autoafirmado.

#### <a name="actions"></a>Ações

O elemento **Ações** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Ação | 1:n | Lista de ações a executar. |

#### <a name="action"></a>Ação

O elemento **ação** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | O tipo de operação. Valores possíveis: `SendCode` ou `VerifyCode` . O `SendCode` valor envia um código ao utilizador. Esta ação pode conter dois perfis técnicos de validação: um para gerar um código e outro para o enviar. O `VerifyCode` valor verifica o código que o utilizador escreveu na caixa de texto de entrada. |

O elemento **ação** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidaçãoClaimsExchange | 1:1 | Os identificadores de perfis técnicos que são utilizados para validar algumas ou todas as alegações de exibição do perfil técnico de referência. Todas as alegações de entrada do perfil técnico referenciado devem figurar nas alegações de visualização do perfil técnico de referência. |

#### <a name="validationclaimsexchange"></a>ValidaçãoClaimsExchange

O elemento **ValidationClaimsExchange** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidaçãoTechnicalProfile | 1:n | Um perfil técnico a utilizar para validar algumas ou todas as reivindicações do perfil técnico de referência. |

O elemento **ValidationTechnicalProfile** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido na política ou na política dos pais. |
|ContinueOnError|Não| Indica se a validação de quaisquer perfis técnicos de validação subsequentes deve continuar se este perfil técnico de validação levantar um erro. Valores possíveis: `true` ou `false` (por defeito, o processamento de perfis de validação adicionais irá parar e um erro será devolvido). |
|ContinueOnSuccess | Não | Indica se a validação de quaisquer perfis de validação subsequentes deve continuar se este perfil técnico de validação for bem sucedido. Valores possíveis: `true` ou `false` . O padrão é `true` , o que significa que o processamento de perfis de validação adicionais continuará. |

O elemento **ValidationTechnicalProfile** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Condições prévias | 0:1 | Uma lista de pré-condições que devem ser satisfeitas para que o perfil técnico de validação seja executado. |

O **elemento pré-condição** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta a efetuar para a pré-condição. Valores possíveis: `ClaimsExist` ou `ClaimEquals` . `ClaimsExist` especifica que as ações devem ser executadas se as alegações especificadas existirem no conjunto de reclamações atuais do utilizador. `ClaimEquals` especifica que as ações devem ser executadas se a reclamação especificada existir e o seu valor for igual ao valor especificado. |
| `ExecuteActionsIf` | Sim | Indica se as ações na condição prévia devem ser executadas se o teste for verdadeiro ou falso. |

O **elemento pré-condição** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Valor | 1:n | Os dados que são utilizados pelo cheque. Se o tipo desta verificação `ClaimsExist` for, este campo especifica uma ClaimTypeReferenceId para consulta. Se o tipo de verificação `ClaimEquals` for, este campo especifica uma ClaimTypeReferenceId para consulta. Especifique o valor a verificar noutro elemento de valor.|
| Ação | 1:1 | A ação que deve ser tomada se o controlo de pré-condição dentro de um passo de orquestração for verdadeiro. O valor da **Ação** é definido para `SkipThisValidationTechnicalProfile` , que especifica que o perfil técnico de validação associado não deve ser executado. |

O exemplo a seguir envia e verifica o endereço de e-mail utilizando o [perfil técnico da Azure AD SSPR](aad-sspr-technical-profile.md).

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

O exemplo a seguir envia um código em e-mail ou SMS com base na seleção do utilizador da reclamação **do mfaType** com pré-condições.

```xml
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

## <a name="referencing-display-controls"></a>Ver referências aos controlos de exibição

Os controlos de exibição são referenciados nas alegações de [visualização](self-asserted-technical-profile.md#display-claims) do [perfil técnico autoafirmado](self-asserted-technical-profile.md).

Por exemplo:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Próximos passos

Para obter amostras de controlo de visualização, consulte: 

- [Verificação personalizada de e-mail com Mailjet](custom-email-mailjet.md)
- [Verificação personalizada de e-mail com SendGrid](custom-email-sendgrid.md)
