---
title: Verificar reclamações com controlos de exibição
titleSuffix: Azure AD B2C
description: Saiba como utilizar os controlos de exibição Azure AD B2C para verificar as reclamações nas viagens de utilizador fornecidas pelas suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188788"
---
# <a name="verification-display-control"></a>Controlo de visualização de verificação

Utilize um controlo de [visualização](display-controls.md) de verificação para verificar uma reclamação, por exemplo, um endereço de e-mail ou número de telefone, com um código de verificação enviado ao utilizador.

## <a name="verificationcontrol-actions"></a>Ações de Controlo de Verificação

O controlo do visor de verificação consiste em duas etapas (ações):

1. Solicite um destino ao utilizador, como um endereço de e-mail ou número de telefone, para o qual o código de verificação deve ser enviado. Quando o utilizador seleciona o botão **'Código de envio',** executa-se a ação do código de **envio** do comando de visualização. A **Ação SendCode** gera um código, constrói o conteúdo a enviar e envia-o para o utilizador. O valor do endereço pode ser pré-povoado e servir como uma autenticação de segundo fator.

    ![Página de exemplo para enviar ação de código](media/display-control-verification/display-control-verification-email-action-01.png)

1. Depois de o código ter sido enviado, o utilizador lê a mensagem, introduz o código de verificação no controlo fornecido pelo comando de exibição e seleciona **Verificar código**. Ao selecionar o **Código de Verificação,** a **Ação CheckCode** é executada para verificar o código associado ao endereço. Se o utilizador selecionar **Enviar Novo Código,** a primeira ação é executada novamente.

    ![Página de exemplo para verificar a ação do código](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificaçãoElementos necessários

O Controlo de **Verificação** deve conter os seguintes elementos:

- O tipo `DisplayControl` do `VerificationControl`é.
- `DisplayClaims`
  - **Enviar para** - Uma ou mais reclamações especificando para onde enviar o código de verificação. Por exemplo, *e-mail* ou *código do país* e número de *telefone*.
  - Código de **verificação** - O código de verificação afirma que o utilizador fornece após o envio do código. Esta reclamação deve ser definida `ControlClaimType` conforme necessário, `VerificationCode`e a definição deve ser definida para .
- Pedido de saída (opcional) a ser devolvido à página autoafirmada após o utilizador concluir o processo de verificação. Por exemplo, *e-mail* ou *código do país* e número de *telefone*. O perfil técnico autoafirmado utiliza as alegações para persistir os dados ou borbulhar as reivindicações de saída para o próximo passo de orquestração.
- Dois `Action`s com seguintes nomes:
  - **Enviar Código** - Envia um código ao utilizador. Esta ação geralmente contém dois perfis técnicos de validação, para gerar um código e enviá-lo.
  - **CheckCode** - Verifica o código. Esta ação geralmente contém um único perfil técnico de validação.

No exemplo abaixo, é apresentada uma caixa de texto por **e-mail** na página. Quando o utilizador introduz o seu endereço de e-mail e seleciona o **Envio Código,** a ação **SendCode** é ativada na extremidade traseira do Azure AD B2C.

Em seguida, o utilizador introduz o Código de **verificação** e seleciona **CheckCode** para acionar a ação **CheckCode** na parte de trás. Se todas as validações passarem, o Controlo de **Verificação** é considerado completo e o utilizador pode continuar até ao passo seguinte.

```XML
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
