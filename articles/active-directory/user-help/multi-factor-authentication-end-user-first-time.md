---
title: O que é a página Verificação adicional? - Azure AD
description: Como chegar à página adicional de verificação de segurança para verificação de dois fatores
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e5b07f8f7ae766d110c87a495a3e1623b815e526
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88795988"
---
# <a name="what-is-the-additional-verification-page"></a>O que é a página Verificação adicional?

A sua organização está a tomar medidas adicionais para garantir que é quem diz ser quando se inscreve. Esta verificação de segurança extra também é conhecida como verificação de dois fatores. Consiste numa combinação do seu nome de utilizador, palavra-passe e um dispositivo móvel ou telefone. Se tudo o que pretende fazer é desativar a verificação de dois fatores para uma conta microsoft, alain@outlook.com como, por exemplo, utilizar as instruções para ligar ou desligar a [verificação de dois fatores para a sua conta Microsoft.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

<center>

![Imagem de métodos de autenticação conceptual](../authentication/media/concept-mfa-howitworks/methods.png)</center>

A verificação de dois fatores é mais segura do que apenas uma palavra-passe porque se baseia em duas formas de autenticação:

- Algo que sabe, como a sua senha.
- Algo que tens, como um telefone ou outro dispositivo que carregas.

A verificação de dois fatores pode ajudar a impedir que hackers maliciosos finjam ser vocês. Mesmo que tenham a sua senha, as probabilidades são de que eles não tenham o seu dispositivo também.

>[!Important]
>Se é um administrador à procura de informações sobre como ativar a verificação de dois fatores para os seus colaboradores ou outros utilizadores, consulte a documentação de autenticação do [Diretório Ativo Azure](../authentication/index.yml). Este artigo destina-se a utilizadores que tentem utilizar a verificação de dois fatores com uma conta de trabalho ou escola alain@contoso.com (como).

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se usa esta funcionalidade?

Quem decide se utiliza a verificação de dois fatores depende do tipo de conta que tem:

- **Trabalho ou conta escolar.** Se estiver a usar uma conta de trabalho ou escola alain@contoso.com (como), cabe à sua organização decidir se utiliza a verificação de dois fatores, juntamente com os métodos específicos de verificação. Como a sua organização decidiu que deve usar esta funcionalidade, não há como desligá-la individualmente.

- **Conta pessoal da Microsoft.** Pode optar por configurar uma verificação de dois fatores para as suas contas pessoais da Microsoft (tais alain@outlook.com como). Pode ligá-lo ou desligá-lo sempre que quiser, utilizando as instruções simples para ligar ou desligar a [verificação de dois fatores para a sua conta Microsoft.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

    >[!Note]
    >Se estiver a ter outros problemas com a verificação de dois fatores e uma das suas contas pessoais da Microsoft, existem mais sugestões em Como utilizar a verificação em [duas etapas com a sua conta Microsoft.](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification)

## <a name="open-the-additional-security-verification-page"></a>Abra a página adicional de verificação de segurança

Depois de a sua organização iniciar a verificação de dois fatores, sempre que assinar, receberá uma solicitação a dizer-lhe para fornecer mais informações para ajudar a manter a sua conta segura.

![Mais informações necessárias](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Para aceder à página adicional de verificação de segurança

1. Selecione **Seguinte** a partir da **informação Mais necessária.**

    Aparece a página **adicional de verificação de segurança.**

2. Na página **de verificação de segurança adicional,** selecione o método de verificação de dois fatores para verificar se é quem diz ser quando se inscreve na sua conta de trabalho ou escola. Pode selecionar:

    | Método de contato | Description |
    | --- | --- |
    | Aplicação móvel | <ul><li>**Receber notificações para verificação.** Esta opção empurra uma notificação para a aplicação autenticadora no seu smartphone ou tablet. Ver a notificação e, se for legítimo, **selecione Authenticate** na aplicação. O seu trabalho ou escola pode exigir que introduza um PIN antes de autenticar.</li><li>**Use o código de verificação.** Neste modo, a aplicação gera um código de verificação que atualiza a cada 30 segundos. Introduza o código de verificação mais atual no ecrã de entrada.<br>A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS.](https://go.microsoft.com/fwlink/?linkid=866594)</li></ul> |
    | Telefone de autenticação | <ul><li>**A chamada telefónica** coloca uma chamada de voz automatizada para o número de telefone que fornece. Atende a chamada e prima a tecla da libra (#) no teclado do telefone para autenticar.</li><li>**A mensagem de** texto termina uma mensagem de texto contendo um código de verificação. Seguindo o pedido no texto, responda à mensagem de texto ou introduza o código de verificação fornecido na interface de entrada.</li></ul> |
    | Telefone do escritório | Coloca uma chamada de voz automatizada para o número de telefone que fornece. Atende a chamada e prima a tecla da libra (#) no teclado do telefone para autenticar. |

## <a name="next-steps"></a>Passos seguintes

Depois de ter selecionado um método de verificação de dois fatores na página **de verificação de segurança adicional,** deve configurar:

- [Configurar o seu dispositivo móvel como o seu método de verificação](multi-factor-authentication-setup-phone-number.md)

- [Configurar o seu telefone de escritório como o seu método de verificação](multi-factor-authentication-setup-office-phone.md)

- [Configurar a app Microsoft Authenticator como o seu método de verificação](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Recursos relacionados

- [Iniciar s-in utilizando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obtenha ajuda com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)