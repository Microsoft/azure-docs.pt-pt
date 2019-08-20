---
title: Configurar sua visão geral dos métodos de verificação de dois fatores-Azure Active Directory | Microsoft Docs
description: Visão geral sobre como configurar seus métodos de verificação de dois fatores para verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616180"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>Configurar a visão geral dos métodos de verificação de dois fatores

Sua organização ativou a verificação de dois fatores, o que significa que sua entrada de conta corporativa ou de estudante agora requer uma combinação de seu nome de usuário, sua senha e um dispositivo móvel ou telefone. Sua organização ativou essa verificação extra porque é mais segura do que apenas uma senha, contando com duas formas de autenticação: algo que você conhece e algo que você tem com você. A verificação de dois fatores pode ajudar a impedir que hackers mal-intencionados se fingim de ser você, porque mesmo que tenham sua senha, é provável que eles não tenham seu dispositivo também.

>[!Important]
>Este conteúdo destina-se aos utilizadores. Se for um administrador, pode encontrar mais informações sobre como configurar e gerir o ambiente do Azure Active Directory (Azure AD) na [Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se você usa esse recurso?

Dependendo de seu tipo de conta, sua organização pode decidir que você deve usar a verificação de dois fatores ou pode decidir por conta própria.

- **Conta escolar ou profissional.** Se você estiver usando uma conta corporativa ou de estudante (por exemplo alain@contoso.com,), cabe à sua organização se você deve usar a verificação de dois fatores, juntamente com os métodos de verificação específicos. Como sua organização decidiu que você deve usar esse recurso, não há como desativá-lo individualmente.

- **Conta Microsoft pessoais.** Você pode optar por configurar a verificação de dois fatores para suas contas pessoais da Microsoft (por exemplo alain@outlook.com,). Se você estiver tendo problemas com a verificação de dois fatores e seus conta Microsoft pessoais, consulte [Ativar ou desativar a verificação de dois fatores para seu conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Como você escolhe se deseja usar esse recurso, você pode ativá-lo e desligá-lo sempre que desejar.

## <a name="access-the-additional-security-verification-page"></a>Acessar a página de verificação de segurança adicional

Depois que sua organização ativar e configurar a verificação de dois fatores, você receberá um prompt informando para fornecer mais informações para ajudar a manter sua conta segura.

![Aviso de mais informações necessárias](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Para acessar a página de verificação de segurança adicional

1. Selecione **Avançar** no prompt **mais informações necessárias** .

    A página **verificação de segurança adicional** é exibida.

2. Na página de **verificação de segurança adicional** , você deve decidir qual método de verificação de dois fatores usar para verificar se você é quem diz que está depois de entrar em sua conta corporativa ou de estudante. Você pode selecionar:

    | Método de contacto | Descrição |
    | --- | --- |
    | Aplicação móvel | <ul><li>**Receber notificações para verificação.** Essa opção envia uma notificação por push para o aplicativo autenticador em seu smartphone ou Tablet. Exiba a notificação e, se for legítima, selecione **autenticar** no aplicativo. Seu trabalho ou escola pode exigir que você insira um PIN antes de autenticar.</li><li>**Use o código de verificação.** Nesse modo, o aplicativo autenticador gera um código de verificação que é atualizado a cada 30 segundos. Insira o código de verificação mais atual na tela de entrada.<br>O aplicativo Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [Ios](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefone de autenticação | <ul><li>**Chamada telefônica** coloca uma chamada de voz automática para o número de telefone que você fornecer. Responda à chamada e pressione a tecla tralha (#) no teclado do telefone para autenticar.</li><li>**Mensagem de texto** encerra uma mensagem de texto que contém um código de verificação. Após o prompt no texto, responda à mensagem de texto ou insira o código de verificação fornecido na interface de entrada.</li></ul> |
    | Telefone do escritório | Faz uma chamada de voz automática para o número de telefone que você fornece. Responda à chamada e pressione a tecla tralha (#) no teclado do telefone para autenticar. |

## <a name="next-steps"></a>Passos Seguintes

Depois de acessar a página de **verificação de segurança adicional** , você deve selecionar e configurar o método de verificação de dois fatores:

- [Configurar seu dispositivo móvel como seu método de verificação](multi-factor-authentication-setup-phone-number.md)

- [Configurar seu telefone comercial como seu método de verificação](multi-factor-authentication-setup-office-phone.md)

- [Configurar o aplicativo Microsoft Authenticator como seu método de verificação](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Recursos relacionados

- [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe das aplicações](multi-factor-authentication-end-user-app-passwords.md)

- [Entrar usando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obter ajuda com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md) 
