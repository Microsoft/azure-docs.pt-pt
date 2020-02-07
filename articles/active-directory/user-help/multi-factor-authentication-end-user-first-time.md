---
title: O que é a página Verificação adicional? -Azure AD
description: Como acessar a página de verificação de segurança adicional para verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 5a7f0e10b23bf1a541fe83c3112962c38f7e1331
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062562"
---
# <a name="what-is-the-additional-verification-page"></a>O que é a página Verificação adicional?

Recebeu um e-mail de uma pessoa do departamento de TI ou do seu chefe a dizer que a organização acrescentou uma verificação de segurança adicional à sua conta. O que é que isso significa? Significa que a organização está a tomar medidas extra para garantir que os utilizadores são quem dizem ser quando iniciam sessão. Essa verificação extra, também conhecida como verificação de dois fatores, é feita por meio de uma combinação de seu nome de usuário, sua senha e um dispositivo móvel ou telefone.

A verificação de dois fatores é mais segura do que apenas uma senha, pois depende de duas formas de autenticação: algo que você conhece e algo que você tem com você. O algo que os utilizadores sabem é a palavra-passe. O algo que têm é um telefone ou dispositivo que costumam utilizar. A verificação de dois fatores pode ajudar a impedir que hackers mal-intencionados se sobrepassem a você, porque mesmo que tenham sua senha, é provável que eles não tenham seu dispositivo também.

>[!Important]
>Este artigo destina-se a utilizadores que tentem utilizar a verificação de dois fatores com uma conta de trabalho ou escola (como, alain@contoso.com). Se for um administrador à procura de informações sobre como ativar a verificação de dois fatores para os seus colaboradores ou outros utilizadores, consulte a documentação de autenticação do [Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se você usa esse recurso?

Dependendo de seu tipo de conta, sua organização pode decidir que você deve usar a verificação de dois fatores ou pode decidir por conta própria.

- **Trabalho ou conta escolar.** Se estiver a usar uma conta de trabalho ou de escola (por exemplo, alain@contoso.com), cabe à sua organização utilizar a verificação de dois fatores, juntamente com os métodos de verificação específicos. Como sua organização decidiu que você deve usar esse recurso, não há como desativá-lo individualmente.

- **Conta pessoal da Microsoft.** Pode optar por configurar uma verificação de dois fatores para as suas contas pessoais da Microsoft (por exemplo, alain@outlook.com). Se tiver problemas com a verificação de dois fatores e a sua conta pessoal da Microsoft, consulte [a verificação de dois fatores ligado ou desligado para](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)a sua conta Microsoft . Como você escolhe se deseja usar esse recurso, você pode ativá-lo e desligá-lo sempre que desejar.

    >[!Note]
    >Se estiver a ter problemas com a verificação de dois fatores e uma das suas contas pessoais da Microsoft (por exemplo, danielle@outlook.com), pode experimentar as sugestões sobre como [utilizar a verificação em duas etapas com](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification)a sua conta Microsoft .

## <a name="access-the-additional-security-verification-page"></a>Acessar a página de verificação de segurança adicional

Depois que sua organização ativar e configurar a verificação de dois fatores, você receberá um prompt informando para fornecer mais informações para ajudar a manter sua conta segura.

![Aviso de mais informações necessárias](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Para acessar a página de verificação de segurança adicional

1. Selecione **Seguinte** a partir do **pedido mais solicitado.**

    Aparece a página adicional de verificação de **segurança.**

2. A partir da página adicional de verificação de **segurança,** você deve decidir qual o método de verificação de dois fatores a usar para verificar se é quem diz ser depois de assinar no seu trabalho ou conta escolar. Pode selecionar:

    | Método de contacto | Descrição |
    | --- | --- |
    | Aplicação móvel | <ul><li>**Receba notificações para verificação.** Essa opção envia uma notificação por push para o aplicativo autenticador em seu smartphone ou Tablet. Ver a notificação e, se for legítimo, **selecione Authenticate** na aplicação. Seu trabalho ou escola pode exigir que você insira um PIN antes de autenticar.</li><li>**Use o código de verificação.** Nesse modo, o aplicativo autenticador gera um código de verificação que é atualizado a cada 30 segundos. Insira o código de verificação mais atual na tela de entrada.<br>A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS.](https://go.microsoft.com/fwlink/?linkid=866594)</li></ul> |
    | Telefone de autenticação | <ul><li>**O telefonema** coloca uma chamada de voz automatizada para o número de telefone que fornece. Responda à chamada e pressione a tecla tralha (#) no teclado do telefone para autenticar.</li><li>**A mensagem de** texto termina uma mensagem de texto contendo um código de verificação. Após o prompt no texto, responda à mensagem de texto ou insira o código de verificação fornecido na interface de entrada.</li></ul> |
    | Telefone do escritório | Faz uma chamada de voz automática para o número de telefone que você fornece. Responda à chamada e pressione a tecla tralha (#) no teclado do telefone para autenticar. |

## <a name="next-steps"></a>Passos seguintes

Depois de aceder à página adicional de verificação de **segurança,** deve selecionar e configurar o seu método de verificação de dois fatores:

- [Configurar o seu dispositivo móvel como o seu método de verificação](multi-factor-authentication-setup-phone-number.md)

- [Configura risa o seu telefone de escritório como o seu método de verificação](multi-factor-authentication-setup-office-phone.md)

- [Configurar a aplicação Microsoft Authenticator como o seu método de verificação](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Recursos relacionados

- [Gerencie as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe das aplicações](multi-factor-authentication-end-user-app-passwords.md)

- [Iniciar sessão utilizando verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obtenha ajuda com verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md) 
