---
title: Verificar reclamações com controlos de exibição
titleSuffix: Azure AD B2C
description: Saiba como utilizar os controlos de exibição Azure AD B2C para verificar as alegações nas viagens do utilizador fornecidas pelas suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e42c889277f937a33e72eaf57819385166d6a409
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202319"
---
# <a name="verification-display-control"></a>Controlo de visualização de verificação

Utilize um [controlo de visualização](display-controls.md) de verificação para verificar uma reclamação, por exemplo, um endereço de e-mail ou número de telefone, com um código de verificação enviado ao utilizador.

## <a name="verificationcontrol-actions"></a>Ações de VerificaçãoControl

O controlo do visor de verificação consiste em duas etapas (ações):

1. Solicite um destino ao utilizador, como um endereço de e-mail ou número de telefone, para o qual o código de verificação deve ser enviado. Quando o utilizador seleciona o botão **'Enviar Código',** executa-se a **Ação SendCode** do controlo do visor de verificação. A **Ação SendCode** gera um código, constrói o conteúdo a enviar e envia-o para o utilizador. O valor do endereço pode ser pré-povoado e servir como uma autenticação de segundo fator.

    ![Página de exemplo para enviar ação de código](media/display-control-verification/display-control-verification-email-action-01.png)

1. Depois de o código ter sido enviado, o utilizador lê a mensagem, introduz o código de verificação no controlo fornecido pelo controlo do visor e seleciona **Código de Verificação**. Ao selecionar **Código de Verificação,** a **Ação CheckCode** é executada para verificar o código associado ao endereço. Se o utilizador selecionar **Enviar Novo Código,** a primeira ação é executada novamente.

    ![Página de exemplo para verificar ação de código](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificaçãoControlar elementos necessários

O **Controlo de Verificação** deve conter os seguintes elementos:

- O tipo de `DisplayControl` `VerificationControl` é.
- `DisplayClaims`
  - **Enviar para** - Uma ou mais reclamações especificando para onde enviar o código de verificação para. Por exemplo, *e-mail* ou *código de país* e número de *telefone*.
  - **Código de verificação** - O código de verificação alega que o utilizador fornece após o envio do código. Esta reclamação deve ser definida conforme necessário, e `ControlClaimType` a deve ser definida para `VerificationCode` .
- Pedido de saída (opcional) a ser devolvido à página autoafirmada após o utilizador concluir o processo de verificação. Por exemplo, *e-mail* ou *código de país* e número de *telefone*. O perfil técnico autoafirmado utiliza as alegações para persistir os dados ou borbulhar as reivindicações de saída para o próximo passo de orquestração.
- Dois `Action` s com os seguintes nomes:
  - **Enviar Código** - Envia um código ao utilizador. Esta ação geralmente contém dois perfis técnicos de validação, para gerar um código e enviá-lo.
  - **Verificar Código** - Verifique o código. Esta ação contém geralmente um único perfil técnico de validação.

No exemplo abaixo, é apresentada uma caixa de texto **de e-mail** na página. Quando o utilizador introduz o seu endereço de e-mail e seleciona **o SendCode,** a ação **SendCode** é ativada na extremidade traseira Azure AD B2C.

Em seguida, o utilizador introduz o **Código de Verificação** e seleciona **Verificação de Código** para ativar a ação **CheckCode** na parte de trás. Se todas as validações passarem, o **Controlo de Verificação** é considerado completo e o utilizador pode continuar até ao passo seguinte.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
