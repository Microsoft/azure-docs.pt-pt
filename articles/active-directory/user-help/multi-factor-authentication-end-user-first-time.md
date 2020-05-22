---
title: O que é a página Verificação adicional? - Azure AD
description: Como chegar à página adicional de verificação de segurança para verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 47220bc721092005c7e4d65a00eb933cd5c49a8c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746770"
---
# <a name="what-is-the-additional-verification-page"></a>O que é a página Verificação adicional?

Recebeu um e-mail de uma pessoa do departamento de TI ou do seu chefe a dizer que a organização acrescentou uma verificação de segurança adicional à sua conta. O que é que isso significa? Significa que a organização está a tomar medidas extra para garantir que os utilizadores são quem dizem ser quando iniciam sessão. Esta verificação extra, também conhecida como verificação de dois fatores, é feita através de uma combinação do seu nome de utilizador, da sua palavra-passe e de um dispositivo móvel ou telemóvel.

A verificação de dois fatores é mais segura do que apenas uma palavra-passe porque se baseia em duas formas de autenticação: algo que sabe, e algo que tem consigo. O algo que os utilizadores sabem é a palavra-passe. O algo que têm é um telefone ou dispositivo que costumam utilizar. A verificação de dois fatores pode ajudar a impedir que hackers maliciosos finjam ser você, porque mesmo que eles tenham a sua senha, as probabilidades são de que eles não têm o seu dispositivo também.

>[!Important]
>Este artigo destina-se a utilizadores que tentem utilizar a verificação de dois fatores com uma conta de trabalho ou de escola (por exemplo, alain@contoso.com ). Se for um administrador à procura de informações sobre como ativar a verificação de dois fatores para os seus colaboradores ou outros utilizadores, consulte a documentação de autenticação do [Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se usa este recurso?

Dependendo do tipo de conta, a sua organização pode decidir que deve usar a verificação de dois fatores, ou poderá decidir por si mesmo.

- **Trabalho ou conta escolar.** Se está a usar uma conta de trabalho ou de escola (por exemplo), cabe à sua organização utilizar a alain@contoso.com verificação de dois fatores, juntamente com os métodos de verificação específicos. Como a sua organização decidiu que deve usar esta funcionalidade, não há como desligá-la individualmente.

- **Conta pessoal da Microsoft.** Pode optar por configurar uma verificação de dois fatores para as suas contas pessoais da Microsoft (por exemplo, alain@outlook.com ). Se tiver problemas com a verificação de dois fatores e a sua conta pessoal da Microsoft, consulte [a verificação de dois fatores ligado ou desligado para](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)a sua conta Microsoft . Como escolhe se utiliza esta funcionalidade, pode ligá-la e desligá-la sempre que quiser.

    >[!Note]
    >Se estiver a ter problemas com a verificação de dois fatores e uma das suas contas pessoais da Microsoft (por exemplo), danielle@outlook.com pode experimentar as sugestões sobre como utilizar [a verificação em duas etapas com](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification)a sua conta Microsoft .

## <a name="access-the-additional-security-verification-page"></a>Aceda à página adicional de verificação de segurança

Depois da sua organização se excitar e configurar a verificação de dois fatores, receberá uma solicitação a dizer-lhe para fornecer mais informações para ajudar a manter a sua conta segura.

![Mais informações necessárias pronta](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Para aceder à página adicional de verificação de segurança

1. Selecione **Seguinte** a partir do **pedido mais solicitado.**

    Aparece a página adicional de verificação de **segurança.**

2. A partir da página adicional de verificação de **segurança,** você deve decidir qual o método de verificação de dois fatores a usar para verificar se é quem diz ser depois de assinar no seu trabalho ou conta escolar. Pode selecionar:

    | Método de contacto | Descrição |
    | --- | --- |
    | Aplicação móvel | <ul><li>**Receba notificações para verificação.** Esta opção empurra uma notificação para a aplicação autenticadora no seu smartphone ou tablet. Ver a notificação e, se for legítimo, **selecione Authenticate** na aplicação. O seu trabalho ou escola pode exigir que introduza um PIN antes de autenticar.</li><li>**Use o código de verificação.** Neste modo, a aplicação autenticadora gera um código de verificação que atualiza a cada 30 segundos. Introduza o código de verificação mais atual no ecrã de entrada.<br>A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS.](https://go.microsoft.com/fwlink/?linkid=866594)</li></ul> |
    | Telefone de autenticação | <ul><li>**O telefonema** coloca uma chamada de voz automatizada para o número de telefone que fornece. Atenda a chamada e pressione a tecla libra (#) no teclado do telefone para autenticar.</li><li>**A mensagem de** texto termina uma mensagem de texto contendo um código de verificação. Seguindo o pedido no texto, ou responde à mensagem de texto ou introduza o código de verificação fornecido na interface de entrada.</li></ul> |
    | Telefone do escritório | Coloca uma chamada de voz automatizada para o número de telefone que fornece. Atenda a chamada e pressione a tecla libra (#) no teclado do telefone para autenticar. |

## <a name="next-steps"></a>Passos seguintes

Depois de aceder à página adicional de verificação de **segurança,** deve selecionar e configurar o seu método de verificação de dois fatores:

- [Configurar o seu dispositivo móvel como o seu método de verificação](multi-factor-authentication-setup-phone-number.md)

- [Configura risa o seu telefone de escritório como o seu método de verificação](multi-factor-authentication-setup-office-phone.md)

- [Configurar a aplicação Microsoft Authenticator como o seu método de verificação](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Recursos relacionados

- [Gerencie as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md)

- [Iniciar sessão utilizando verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obtenha ajuda com verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md) 
